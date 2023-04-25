# Lab3 - テストの実行
## .gitlab-ci.yml

```yaml
variables:
  MAVEN_OPTS: "-Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository"

stages:
  - pre build
  - build
  - test

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
```

[Lab4](../lab4/README.md)