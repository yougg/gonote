#### **◆ Fork代码步骤**

1. 进入项目代码主线仓库, 点击`Fork`按钮,再点击用户创建新的Fork仓库.

2. 复制Fork的仓库路径(如: http://github.com/username/project.git)  
   在本地使用git客户端`clone`此分支代码.  
   如果当前已经clone了主线仓库的代码,则按如下方式切换到Fork的仓库.
    - 打开`Git Bash`进入本地代码仓库目录

        ```bash
        cd /home/cxy/go/src/myproject/
        ```
    - 切换本地仓库对应的远程路径为Fork仓库的路径

        ```bash
        git remote set-url origin http://github.com/username/project.git
        ```
    - 查看本地仓库对应的路径是否更新
    
        ```bash
        git remote -v
        ```

3. 切换本地仓库代码分支
    - 首先更新代码(确保本地所有代码已提交,没有未合并的分支)
    
        ```bash
        git fetch
        ```
    - 切换分支
        
        ```bash
        git checkout -b develop origin/develop
        ```

#### **◆ 从主线仓库更新代码到本地Fork仓库的分支步骤**

1. 增加主线代码仓库地址  
   打开`Git Bash`进入本地代码仓库目录执行
  ```bash
  git remote add trunk http://github.com/mygroup/project.git
  ```

2. 查看本地仓库对应的路径是否更新
  ```bash
  git remote -v
  ```
有类似如下输出:
>origin  http://github.com/username/project.git (fetch)  
origin  http://github.com/username/project.git (push)  
trunk   http://github.com/mygroup/project.git (fetch)  
trunk   http://github.com/mygroup/project.git (push)

2. 从主线仓库origin/develop分支
  更新代码到本地Fork仓库的origin/develop分支
    - 在`IDEA`中更新  
        1. 选中项目,点击右键`Git`菜单,选择`Repository` -> `Pull...`
        2. 选择`Remote`为新增的主线远程仓库路径
        3. 选择分支为`origin/develop`,然后点击`Pull`拉取代码进行更新.  
        4. 如果代码存在冲突,在提示合并窗口中进行合并操作.
    - 在命令行更新

        ```bash
        git pull http://github.com/mygroup/project.git trunk develop
        ```

#### **◆ 合并代码到主线仓库步骤**

1. 本地修改提交代码, 使用IDEA界面`Ctrl + k`提交或者命令`git commit`

2. 推送本地代码到Fork仓库,使用IDEA界面`Ctrl + Shift+ k`推送或者命令`git push`

3. 在git服务器上`Fork`的工程中新建`Merge Request`合并请求  
   `Source branch`选择用户Fork仓库下的`develop`分支  
   `Target branch`选择主线仓库下的`develop`分支  
   点击`Compare branches`进行对比,完善信息后提交合并请求

4. `Committer`接受合并请求,合入代码.

#### **◆ 主线代码仓库发布版本步骤**

1. `Committer`在git服务器上主线代码仓库新建`Merge Request`合并请求  
   `Source branch`选择主线仓库下的`develop`分支  
   `Target branch`选择主线仓库下的`master`分支  
   点击`Compare branches`进行对比,完善信息后提交合并请求

2. 在CI服务器更新最新版本代码
    - 编译daily版本

        > 切换代码分支到`develop`
    - 编译release版本

        > 切换代码分支到`master`
