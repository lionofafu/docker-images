参考文章 https://blog.csdn.net/weixin_46203834/article/details/140556611

# 1.首先，在workflow文件中定义触发器，例如每次推送到主分支时触发同步操作。
# 工作流名称
name: Sync Image to Aliyun  Example
 
# 怎样触发工作流
on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]
 
# 2.添加一个job来执行同步操作。在workflow文件中，添加一个job 并指定使用的操作系统环境。
# 工作流程任务（通常含有一个或多个步骤）
jobs:
  build:
    runs-on: ubuntu-latest
 
    # 在job中添加步骤来执行同步操作。在上述job中，添加步骤来执行skopeo命令，从docker.io仓库拉取镜像，并将其推送到阿里云个人仓库。
    steps:
    - name: Git pull
      uses: actions/checkout@v3
 
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v2.9.1
      
    # 设置项目Secrets以提供Docker Hub登录所需的用户名和密码。
    - name: Login to Docker Hub
      uses: docker/login-action@v2.2.0
      with:
        registry: registry.cn-guangzhou.aliyuncs.com
        username: ${{secrets.DOCKER_USERNAME}}
        password: ${{secrets.DOCKER_PASSWORD}}
        logout: false
 
    # 使用shell命令批量同步所需的镜像到阿里云个人仓库中
    - name: Use Skopeo Tools Sync Image to Docker Hub
      run: |
        # 使用 skopeo 工具将镜像同步到阿里云个人仓库中，使用时请自行源和目标修改仓库名称和镜像名称
         skopeo copy docker://docker.io/userxy2015/ngnix:latest docker://registry.cn-guangzhou.aliyuncs.com/gorgor1065/ngnix:latest
