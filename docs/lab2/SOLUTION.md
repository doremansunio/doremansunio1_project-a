# Lab2 - アーティファクトの活用

## .gitlab-ci.yml

```yaml
variables:
  MAVEN_OPTS: "-Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository -Dcheckstyle.skip=true"

stages:
  - pre build
  - build

download-m2cache:
  stage: pre build
  image: alpine:3.14.1
  script:
    - apk --no-cache add curl
    - curl -L -o m2.zip https://workshop.gitlabtraining.cloud/api/v4/projects/688/jobs/artifacts/main/download?job=mvn%20package
    - if [ -e ./m2.zip ]; then unzip m2.zip; fi
  artifacts:
    paths:
      - ./.m2/

mvn-package:
  stage: build
  image: maven:3.8.1-openjdk-17-slim
  script:
    - ./mvnw package -Dmaven.test.skip=true
```

[Lab3](../lab3/README.md)