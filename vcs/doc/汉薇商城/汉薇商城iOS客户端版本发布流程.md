# 汉薇商城iOS客户端版本发布流程

1. 在iTunes Connect网站上创建新的版本号，填写版本更新描述，如果需要的话更新App预览以及截图

   汉薇商城版本号为点号分隔的四位整数，每位版本号**不超过9**（例：1.1.1.9的下一个版本为1.1.2.0；1.1.9.9的下一个版本为1.2.0.0）

2. 将需要发布的所有特效分支合并至**develop**分支

3. 将**develop**分支的代码合并至相应的环境分支，版本发布使用**env/release**分支（测试时使用env/test分支）

4. 从**env/release**分支上切出发布分支，分支名称为**release/<此次发布的版本号>**，如汉薇商城**1.1.2.4**版本的发布分支名称为**release/v1.1.2.4**

5. 在发布分支(release/*)上修改版本号等元数据

   1. 手动修改Info.plist文件中的`sys-clientVersion`字段，值为四位数版本号，如发布版本为**1.1.2.4**时，`sys-clientVersion`的值也应该为**1.1.2.4**
   2. 手动修改Info.plist文件中的`Bundle versions string, short`字段，（改字段的值由于苹果的规范，必须为三位整数点号分隔的版本号），当`sys-clientVersion`的值为**a.b.c.d**时，`Bundle versions string, short`的值为**a.b.c*10 + d**，例如当发布版本**1.1.2.4**时`sys-clientVersion`的值为`1.1.2.4`，`Bundle versions string, short`的值应为**1.1.24**
   3. 如果是手动打包发布的话在工程路径下执行**agvtool next-version -all**自增工程版本号，以及Build号（绝对不要手动修改），如果是自动发布的话执行**fastlane release**命令自动发布版本不需要额外执行**agvtool**
   4. 创建一个git提交，提交信息中注明版本号以及当前的Build号即可，没有格式限制，例如`git commit -m "Version 1.1.2.4, Build 99"`

6. 上传版本之后，直至审核通过开放给用户正式使用之前，如果遇到需要撤销审核/发布的情况

   * 如果仅需要修改代码，少量的修改可以直接在**develop**分支上修改，如果和需发布特性相关程度很大的大量代码，需要在相应的特性分支上修改并合并到**develop**分支（无论哪一种情况，保证最终develop分支上应该包含需要发布的所有内容），将develop分支上的内容合并到**env/release**分支，再将**env/release**分支的内容合并到**release/\***发布分支，并回到**步骤5.3**。
   * 如果需要拆除部分特性分支，则需要将**develop**分支，**env/release**，重置回**步骤2**之前的状态，并删除相应的**release/\***发布分支，然后回到**步骤2**

7. 版本审核通过并正式开放给用户下载，之后才算完整走完该版本的发布周期，执行以下步骤：

   1. 将**releaes/\***发布分支合并到**master**分支，在**master**分支上打上版本号的tag（例如：git tag v1.1.2.4）
   2. 切换到**env/release**分支，将上一步骤中的tag，合并到**env/release** 分支
   3. 删除**release/\***发布分支，删除已发布的特性分支
   4. 推送各个分支（develop, env/release, master）的更新到远程仓库
   5. 执行**git push --tags origin**推送tag到远程仓库



> 注意事项：
>
> （标注：branchA -> branchB表示将branchA，合并到branchB分支）
>
> 1. 不建议直接将develop -> release/\*分支，应该将develop -> env/release -> release/\*
> 2. 版本开放给用户之后记得打上tag，推送代码
> 3. Build号一定不要手动更改