---
title: Unity3D DevOps with Gitlab CI
date: 2019-05-09 11:12:32
tags: [Unity, Unity3D, CI ,GitLab]
description: Unity 配置 GitLab CI
---

## Gitlab-Runner

1. 安装 Gitlab-Runner

2. 获取 License

```shell

docker run --net host -it --rm -e "UNITY_USERNAME=your-user-name" -e "UNITY_PASSWORD=your-password" gableroux/unity3d:2017.4.10f1

xvfb-run --auto-servernum --server-args='-screen 0 640x480x24' /opt/Unity/Editor/Unity -logFile -batchmode -username "$UNITY_USERNAME" -password "$UNITY_PASSWORD"
```

3. `.gitlab-ci.yml`

```yaml
image: "gableroux/unity3d:2017.4.10f1"

stages:
- build
- deploy
- review

variables:
  BUILD_NAME: xdz_10
  
.unity_before_script: &unity_before_script
  before_script:
  - echo "$CI_PROJECT_NAMESPACE-$CI_PROJECT_NAME-$CI_COMMIT_REF_SLUG"
  - chmod +x ./ci/before_script.sh && ./ci/before_script.sh
  tags:
    - unity

.build: &build
  stage: build
  #tip: you can reuse same stage to do everything in parallel and reduce overall time
  # stage: test
  <<: *unity_before_script
  script:
    - chmod +x ./ci/build.sh && ./ci/build.sh
  artifacts:
    paths:
    - ./Builds/
  cache:
    key: "$CI_PROJECT_NAMESPACE-$CI_PROJECT_NAME-$CI_COMMIT_REF_SLUG-$BUILD_TARGET"
    paths:
      - Library/
#      - ./Builds/
  tags:
    - unity
#tip: uncomment following lines if you'd like to build only on master branch
#   only:
#     - master

# build-StandaloneLinux64:
#   <<: *build
#   variables:
#     BUILD_TARGET: StandaloneLinux64

# build-StandaloneOSX:
#   <<: *build
#   variables:
#     BUILD_TARGET: StandaloneOSX

#Note: build target names changed in recent versions, use this for versions < 2017.2:
# build-StandaloneOSXUniversal:
#   <<: *build
#   variables:
#     BUILD_TARGET: StandaloneOSXUniversal

build-StandaloneWindows64:
  <<: *build
  variables:
    BUILD_TARGET: StandaloneWindows64
  only:
    refs:
      - schedules
  tags:
    - unity

build-WebGL:
  <<: *build
  variables:
    BUILD_TARGET: WebGL
  after_script:
    - mv ./Builds/WebGL/$BUILD_NAME ./Builds/
    - rm -rf ./Builds/WebGL
    - mv ./Builds/$BUILD_NAME/ ./Builds/WebGL/
  only:
    refs:
      - schedules
  tags:
    - unity

pages:
  image: alpine:latest
  stage: deploy
  script:
    - mv ./Builds/WebGL/ public
  artifacts:
    paths:
    - public
  only:
    refs:
      - schedules
  tags:
    - unity

deploy_review:
  image: netroby/alpine-rsync:latest
  stage: review
  script:
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh
    - echo "$DOCKER_SSH_KEY" > ~/.ssh/id_rsa
    - chmod 600 ~/.ssh/id_rsa
    - eval $(ssh-agent -s)
    - ssh-add ~/.ssh/id_rsa
    - mkdir -p $CI_PROJECT_NAMESPACE/$CI_PROJECT_NAME/$CI_BUILD_REF_NAME
    - mv public/* "$CI_PROJECT_NAMESPACE/$CI_PROJECT_NAME/$CI_BUILD_REF_NAME/"
    - rsync -a --relative -drv --delete "$CI_PROJECT_NAMESPACE/$CI_PROJECT_NAME/$CI_BUILD_REF_NAME" -e "ssh -o StrictHostKeyChecking=no" $USER_NAME@gitlab.brainzero.top:"/var/www/gitlab-ci-pages/"
  environment:
    name: "review/$CI_BUILD_REF_NAME"
    url: "https://pages.brainzero.com/$CI_PROJECT_NAMESPACE/$CI_PROJECT_NAME/$CI_BUILD_REF_NAME"
    on_stop: stop_review
  dependencies:
    - pages
  only:
    refs:
      - schedules
  tags:
    - review

stop_review:
  stage: review
  script:
    - rm -rf "/var/www/gitlab-ci-pages/$CI_PROJECT_NAMESPACE/$CI_PROJECT_NAME/$CI_BUILD_REF_NAME"
  variables:
    GIT_STRATEGY: none
  when: manual
  environment:
    name: "review/$CI_BUILD_REF_NAME"
    action: stop
  only:
    refs:
      - schedules
  tags:
    - review

```

[1]: https://gitlab.com/gableroux/unity3d-gitlab-ci-example
