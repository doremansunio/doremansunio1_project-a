# Lab1 - .gitlab-ci.yml とビルドジョブの追加
## .gitlab-ci.yml

```yaml
variables:
  MAVEN_OPTS: "-Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository -Dcheckstyle.skip=true"

stages:
  - build

mvn-package:
  stage: build
  image: maven:3.8.1-openjdk-17-slim
  script:
    - ./mvnw package -Dmaven.test.skip=true
```

[Lab2](../lab2/README.md)
