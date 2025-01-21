## 配置

 * 分成 develop 和 main 分支，依照 `tags` 去區分要去觸發哪一個 gitlab-runner。

 * (選擇配置) 如果有權限管理上的問題，需要在 gitlab-runner 上的 vm 上設定 gitlab-runner 這個用戶的 sudo 權限。

    ```sh
    # 設置用戶的 sudo 權限，以免權限過大。
    visudo
    ```
    ```conf
    ## 使用 sudo，可以不用詢問密碼的指令
    ## gitlab-runner

    gitlab-runner ALL=(ALL) NOPASSWD: 
    /usr/bin/systemctl start web.service, 
    /usr/bin/systemctl stop web.service, 
    /usr/bin/systemctl restart web.service, 
    /usr/bin/systemctl status web.service, 
    /usr/bin/systemctl start nginx.service, 
    /usr/bin/systemctl stop nginx.service, 
    /usr/bin/systemctl restart nginx.service, 
    /usr/bin/systemctl status nginx.service, 
    /opt/web/update.sh, 
    /opt/openresty/nginx/update-hpiweb-prod.sh
    ```

<br/>

配置，後端使用 springboot / java 11

```conf
# build 階段，使用掛載的方式，將 maven 依賴持久化在 vm 主機上，否則每次 build 階段都會去下載依賴
[runners.docker]
  volumes = ["/cache", "/home/gitlab-runner/.m2/repository:/root/.m2/repository"]
```

```yml
stages: 
  - build
  - scan    # sonar-scanner 掃描原始碼
  - deploy

build:
  image: maven:3.9.6-eclipse-temurin-11
  tags:
    - shared-docker
  stage: build
  artifacts:
    paths:
      - target/eip.jar
    expire_in: 3 days
  script:
    - mvn clean package -DskipTests
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
    - if: '$CI_COMMIT_BRANCH == "develop"'

scan:
  image: sonarsource/sonar-scanner-cli:latest
  tags:
    - shared-docker
  stage: scan
  dependencies:
    - build
  variables:
    SONAR_USER_HOME: "${CI_PROJECT_DIR}/.sonar"
    GIT_DEPTH: "0"
  cache:
    key: "sonar-cache-$CI_COMMIT_REF_SLUG"
    paths:
      - "${SONAR_USER_HOME}/cache"
  script:
    - sonar-scanner
      -Dsonar.host.url=$SONAR_HOST_URL
      -Dsonar.projectKey=$EIP_BE_PROJECT_KEY
      -Dsonar.token=$EIP_BE_TOKEN
      -Dsonar.java.binaries=target
      -Dsonar.java.source=11
      -Dsonar.sources=src/main
      -Dsonar.sourceEncoding=UTF-8
      -Dsonar.exclusions=src/test/**
  allow_failure: true
  rules:
    - if: '$CI_COMMIT_BRANCH == "develop"'
    - if: '$CI_COMMIT_BRANCH == "main"'

# deploy 階段，將 build 階段建構的 artifacts，複製到
deploy:
  tags:
    - shared-shell
  stage: deploy
  dependencies:
    - build
  script:
    - cp -rf target/eip.jar /opt/ansible/artifacts/eip/eip.jar;
    - if [[ "$CI_COMMIT_BRANCH" == "develop" ]]; then
        ansible-playbook /opt/ansible/deploy/eip/uat-eip-be.yml;
      elif [[ "$CI_COMMIT_BRANCH" == "main" ]]; then
        ansible-playbook /opt/ansible/deploy/eip/prod-eip-be.yml;
      fi
  rules:
    - if: '$CI_COMMIT_BRANCH == "develop"'
    - if: '$CI_COMMIT_BRANCH == "main"'

```

配置 前端使用 vue + node 22

```yml
stages: 
  - scan
  - build
  - deploy

scan:
  image: sonarsource/sonar-scanner-cli:latest
  tags:
    - shared-docker
  stage: scan
  variables:
    SONAR_USER_HOME: "${CI_PROJECT_DIR}/.sonar"
    GIT_DEPTH: "0"
  cache:
    key: "sonar-cache-$CI_COMMIT_REF_SLUG"
    paths:
      - "${SONAR_USER_HOME}/cache"
  script:
    - sonar-scanner
      -Dsonar.host.url=$SONAR_HOST_URL
      -Dsonar.projectKey=$EIP_FE_PROJECT_KEY
      -Dsonar.token=$EIP_FE_TOKEN
      -Dsonar.sources=.
      -Dsonar.exclusions=**/node_modules/**,**/*.spec.js,**/__tests__/**
      -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
      -Dsonar.typescript.lcov.reportPaths=coverage/lcov.info
  allow_failure: true
  rules:
    - if: '$CI_COMMIT_BRANCH == "develop"'
    - if: '$CI_COMMIT_BRANCH == "master"'

build:
  image: node:22
  tags:
    - shared-docker
  stage: build
  cache:
    key: "$CI_BUILD_REF_NAME"
    paths:
      - node_modules/
      - dist/
  script:
    - npm install
    - npm run build:dev
    - ls
  artifacts:
    paths:
      - dist
    expire_in: 3 days
  rules:
    - if: '$CI_COMMIT_BRANCH == "develop"'
    - if: '$CI_COMMIT_BRANCH == "master"'

deploy:
  tags:
    - shared-shell
  stage: deploy
  dependencies:
    - build
  script:
    - cp -rf dist /opt/ansible/artifacts/eip/;
    - if [[ "$CI_COMMIT_BRANCH" == "develop" ]]; then 
        ansible-playbook /opt/ansible/deploy/eip/uat-eip-fe.yml; 
      elif [[ "$CI_COMMIT_BRANCH" == "master" ]]; then
        ansible-playbook /opt/ansible/deploy/eip/prod-eip-fe.yml;
      fi
  rules:
    - if: '$CI_COMMIT_BRANCH == "develop"'
    - if: '$CI_COMMIT_BRANCH == "master"'
```