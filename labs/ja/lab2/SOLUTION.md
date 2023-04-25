# Lab2 - アーティファクトの活用

## .gitlab-ci.yml

```yaml
variables:
  MAVEN_OPTS: "-Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository"

stages:
  - pre build
  - build

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
```

[Lab3](../lab3/README.md)