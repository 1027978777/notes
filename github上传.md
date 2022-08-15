# 一、本地文件上传github

1. 右上角 new repository

2. ![image-20220812164029164](https://raw.githubusercontent.com/1027978777/mybed/main/img/202208151012012.png)

3. 复制https连接

4. ![image-20220812164234183](https://raw.githubusercontent.com/1027978777/mybed/main/img/202208151013201.png)

5. 在本地下载：[git](https://git-scm.com/download/win)

6. 然后找到你要上传的文件夹项目，右键点击文件夹（注意：不能选单个文件或者压缩包）在选项里选择*Git Bash Here*

7. ![image-20220812164647576](https://raw.githubusercontent.com/1027978777/mybed/main/img/202208151014004.png)

8. 输入：**git clone https://github.com/xx/xx.git** (之前复制的https地址)

9. 进入新出现的项目目录：cd 文件夹名称

10. 输入： **git add .** (**注意**  **.** "不能省略，此操作是把文件夹下面新的文件或修改过的文件添加进来，如果有的文件之前已经添加了，它会自动省略)

11. 输入：**git commit  -m  "提交信息"** （***提交的信息是你的项目说明***）

12. 有些刚开使用的用户：**这时你要先全局配置好在git上的用户名和邮箱**

13. 输入：**git config --global user.name "用户名"**

14. 输入：**git config --global user.email 邮箱**

15. 输入：**git push -u origin main**

16. ![image-20220812170342302](https://raw.githubusercontent.com/1027978777/mybed/main/img/202208151015166.png)

17. Logon failed, use ctrl+c to cancel basic credential prompt.错误

18. ![image-20220812171823249](https://raw.githubusercontent.com/1027978777/mybed/main/img/202208151015146.png)

19. 如果 git 没有更新到最新版本，就会发生这种情况，请更新 git，要更新 git，只需根据您使用的操作系统类型执行以下命令：

20. **windows**: `git update-git-for-windows`

    **Linux/Unix**: `git update`

# 二、Typora+picgo+github图片上传

1. 首先在 github 新建一个仓库，放置自己的图片
2. ![image-20220812164029164](https://raw.githubusercontent.com/1027978777/mybed/main/img/202208151012012.png)
3. 设置
4. ![](https://raw.githubusercontent.com/1027978777/mybed/main/img/202208151023374.png)
5. ![image-20220815102443755](https://raw.githubusercontent.com/1027978777/mybed/main/img/202208151024787.png)
6. ![image-20220815102521472](https://raw.githubusercontent.com/1027978777/mybed/main/img/202208151025499.png)
7. ![image-20220815102604519](https://raw.githubusercontent.com/1027978777/mybed/main/img/202208151026550.png)
8. ![image-20220815102618165](https://raw.githubusercontent.com/1027978777/mybed/main/img/202208151026185.png)
9. 复制token 
10. 下载picgo
11. ![image-20220815102744829](https://raw.githubusercontent.com/1027978777/mybed/main/img/202208151027866.png)
12. 配置picgo
13. ![image-20220815103143273](https://raw.githubusercontent.com/1027978777/mybed/main/img/202208151031305.png)
14. ![image-20220815103258233](https://raw.githubusercontent.com/1027978777/mybed/main/img/202208151032264.png)
15.  typora 验证图片上传选项
16. ![image-20220815103544472](https://raw.githubusercontent.com/1027978777/mybed/main/img/202208151035493.png)
