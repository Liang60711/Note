## 配置

 * 分成 develop 和 main 分支，依照 `tags` 去區分要去觸發哪一個 gitlab-runner。

 * 如果有權限管理上的問題，需要在 gitlab-runner 上的 vm 上設定 gitlab-runner 這個用戶的 sudo 權限。

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

* 配置，後端使用 springboot / java 11

    ```yml
    stages:
    - build
    - deploy

    before_script:
    - echo "Start hpiweb_be cicd process..."

    build_main:
    tags:
        - prod-hpiweb
    cache:
        paths:
        - .m2/repository
        key: "$CI_BUILD_REF_NAME"  # keep cache across branch
    variables:
        MAVEN_OPTS: "-Djava.awt.headless=true -Dmaven.repo.local=.m2/repository"
        MAVEN_CLI_OPTS: "--batch-mode --errors --fail-at-end --show-version"

    stage: build
    script:
        - mvn clean package -DskipTests
    artifacts:
        paths:
        - target/web.jar
        expire_in: 3 days
    only:
        - main

    build_develop:
    tags:
        - uat
    cache:
        paths:
        - .m2/repository
        key: "$CI_BUILD_REF_NAME"  # keep cache across branch
    variables:
        MAVEN_OPTS: "-Djava.awt.headless=true -Dmaven.repo.local=.m2/repository"
        MAVEN_CLI_OPTS: "--batch-mode --errors --fail-at-end --show-version"

    stage: build
    script:
        - mvn clean package -DskipTests
    artifacts:
        paths:
        - target/web.jar
        expire_in: 3 days
    only:
        - develop

    deploy_main:
    tags:
        - prod-hpiweb
    stage: deploy
    script:
        - cp target/web.jar /tmp
        - sudo /opt/web/update.sh /tmp/web.jar
    only:
        - main

    deploy_develop:
    tags:
        - uat
    stage: deploy
    script:
        - sudo cp target/web.jar /tmp
        - sudo /opt/web/update.sh /tmp/web.jar
    only:
        - develop
    ```

* 配置 前端使用 vue / nvm (切換 node 版本的工具)

    ```yml
    stages:
    - build
    - deploy

    before_script:
    - echo "Start hpiweb_fe cicd process..."
    - export NVM_DIR="$HOME/.nvm" && [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
    - nvm use v12.22.12

    build_master:
    tags:
    - prod-hpiweb
    cache:
        paths:
        - node_modules/
    stage: build
    script:
        - which npm
        - npm install
        - npm run build
    artifacts:
        paths:
        - dist/
        expire_in: 1 days
    only:
        - master

    build_develop:
    tags:
    - uat
    cache:
        paths:
        - node_modules/
    stage: build
    script:
        - which npm
        - npm install
        - npm run build
    artifacts:
        paths:
        - dist/
        expire_in: 1 days
    only:
        - develop

    deploy_master:
    tags:
    - prod-hpiweb
    stage: deploy
    script:
        - cp -rf dist/ /tmp/ && cd /opt/openresty/nginx/html
        - sudo /opt/openresty/nginx/update-hpiweb-prod.sh /tmp/dist
    only:
        - master

    deploy_develop:
    tags:
    - uat
    stage: deploy
    script:
        - sudo cp -rf dist/ /tmp/ && cd /usr/share/nginx/html
        - sudo /usr/share/nginx/html/update-hpiweb.sh /tmp/dist
    only:
        - develop
    ```