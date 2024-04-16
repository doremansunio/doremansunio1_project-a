# Lab7 - Web アプリケーションのデプロイ
## .gitlab-ci.yml

```yaml
variables:
  MAVEN_OPTS: "-Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository"
  ROLLOUT_RESOURCE_TYPE: deployment

stages:
  - pre build
  - build
  - test
  - post build
  - release
  - deploy
  - cleanup

download-m2cache:
  stage: pre build
  image: alpine:3.14.1
  script:
    - apk --no-cache add curl
    - 'curl -o $CI_PROJECT_DIR/m2.zip "https://gitlab.com/api/v4/projects/45304220/packages/generic/m2_local_cache/latest/m2.zip"'
    - if [ -e ./m2.zip ]; then unzip m2.zip; fi
  artifacts:
    paths:
      - $CI_PROJECT_DIR/.m2/

mvn-package:
  stage: build
  image: maven:3.8.6-openjdk-8-slim
  script:
   - ./mvnw package -Dmaven.test.skip=true
  artifacts:
    paths:
      - target/*.jar

unit-test:
  stage: test
  image: maven:3.8.6-openjdk-8-slim
  script:
    - ./mvnw test
  artifacts:
    when: always
    paths:
      - target/surefire-reports/TEST-*.xml
    reports: 
        junit:
          - target/surefire-reports/TEST-*.xml
  needs: 
    - download-m2cache

mvn-deploy:
  stage: post build
  image: maven:3.8.6-openjdk-8-slim
  before_script:
    - apt update && apt install git -y
  script:
    - ./mvnw deploy -s ci_settings.xml -Dmaven.test.skip=true
  rules:
    - if: '$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH || $CI_COMMIT_TAG'

release:
  stage: release
  image: registry.gitlab.com/gitlab-org/release-cli:latest
  script:
    - echo running release_job for '$CI_COMMIT_TAG'
  release:
    name: 'Release $CI_COMMIT_TAG'
    description: 'Created using the release-cli for $CI_COMMIT_TAG'
    tag_name: '$CI_COMMIT_TAG'
    milestones:
      - $CI_COMMIT_TAG
    assets:
      links:
        - name: 'spring-petclinic jar'
          url: '$CI_API_V4_URL/projects/$CI_PROJECT_ID/packages/maven/org/springframework/samples/spring-petclinic/$CI_COMMIT_TAG/spring-petclinic-$CI_COMMIT_TAG.jar'
        - name: 'spring-petclinic pom'
          url: '$CI_API_V4_URL/projects/$CI_PROJECT_ID/packages/maven/org/springframework/samples/spring-petclinic/$CI_COMMIT_TAG/spring-petclinic-$CI_COMMIT_TAG.pom'
  rules:
    - if: $CI_COMMIT_TAG

docker-build:
  stage: post build
  image: docker:20.10.6
  variables:
    DOCKER_TLS_CERTDIR: ''
  services:
    - name: 'docker:20.10.6-dind'
      command: ['--tls=false', '--host=tcp://0.0.0.0:2375']  
  before_script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
  script:
    - docker build -t $CI_REGISTRY_IMAGE/$CI_COMMIT_REF_SLUG:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE/$CI_COMMIT_REF_SLUG:$CI_COMMIT_SHA
  rules:
    - if: '$CI_COMMIT_TAG || $CI_COMMIT_BRANCH'
  needs:
    - mvn-package
    - unit-test

.auto-deploy:
  image: "registry.gitlab.com/gitlab-org/cluster-integration/auto-deploy-image:v2.12.0"
  dependencies: []

deploy:
  extends: .auto-deploy
  stage: deploy
  script:
    - auto-deploy check_kube_domain
    - auto-deploy download_chart
    - auto-deploy ensure_namespace
    - auto-deploy initialize_tiller
    - auto-deploy create_secret
    - auto-deploy deploy
    - auto-deploy persist_environment_url
  environment:
    name: review/$CI_COMMIT_REF_NAME
    url: http://$CI_PROJECT_ID-$CI_ENVIRONMENT_SLUG.$KUBE_INGRESS_BASE_DOMAIN
    on_stop: stop-deploy
  artifacts:
    paths: [environment_url.txt, tiller.log]
    when: always
  rules:
    - if: '$CI_KUBERNETES_ACTIVE == null || $CI_KUBERNETES_ACTIVE == ""'
      when: never
    - if: $CI_COMMIT_BRANCH
  needs:
    - docker-build

stop-deploy:
  extends: .auto-deploy
  stage: cleanup
  variables:
    GIT_STRATEGY: none
  script:
    - auto-deploy initialize_tiller
    - auto-deploy delete
  environment:
    name: review/$CI_COMMIT_REF_NAME
    action: stop
  allow_failure: true
  rules:
    - if: '$CI_KUBERNETES_ACTIVE == null || $CI_KUBERNETES_ACTIVE == ""'
      when: never
    - if: $CI_COMMIT_BRANCH
      when: manual

include:
  - template: Jobs/Code-Quality.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Jobs/Code-Quality.gitlab-ci.yml
  - template: Security/Dependency-Scanning.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Security/Dependency-Scanning.gitlab-ci.yml
  - template: Security/SAST.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Security/SAST.gitlab-ci.yml
  - template: Security/SAST-IaC.gitlab-ci.yml # https://gitlab.com/gitlab-org/gitlab/-/blob/master/lib/gitlab/ci/templates/Security/SAST-IaC.gitlab-ci.yml
  - template: Security/Secret-Detection.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Security/Secret-Detection.gitlab-ci.yml
#  - template: Security/Container-Scanning.gitlab-ci.yml  # https://gitlab.com/gitlab-org/gitlab/blob/master/lib/gitlab/ci/templates/Security/Container-Scanning.gitlab-ci.yml
```

## Dockerfile
```Dockerfile
FROM maven:3.8.6-openjdk-8-slim

WORKDIR /usr/src/app
COPY target/*.jar /usr/src/app/
ENV PORT 5000
EXPOSE $PORT

CMD java -jar /usr/src/app/*.jar --server.port=$PORT
```

[Lab8](../lab8/README.md)
