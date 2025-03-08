# 配置Git全局信息
---
- 作用：记录提交者信息，不影响实际同步
```bash
git config --global user.name "你的名字（如John）"
git config --global user.email "你的邮箱（如john@example.com）"
```
# 配置SSH密钥
---
- 作用：帮助PC获取github远程仓库的权限
![[Pasted image 20250307181711.png]]
- 打开 ​**Git Bash**，输入：
	```bash
	ssh-keygen -t ed25519 -C "你的邮箱"
	```
	- 提示保存路径时，直接按回车（默认路径）
	- 提示输入密码时，可直接回车（不设密码）
- 生成密钥后，输入以下命令查看公钥：
    ```bash
    cat ~/.ssh/id_ed25519.pub
    ```
	- 全选复制输出的内容（以 `ssh-ed25519 AAA...` 开头）
- 打开 GitHub SSH设置页面，点击 ​**New SSH Key**
    - ​**Title**: 输入任意名称（如My PC1）
    - ​**Key**: 粘贴复制的公钥
    - 点击 ​**Add SSH Key**


# 主机在Github上创建远程仓库
---
- 作用：首次上传需要，可以理解以主机版本为**远程仓库初版库**

```bash
# 进入你的Obsidian库目录（示例路径，需替换为你自己的路径）
cd D:\MyObsidianVault

# 初始化Git仓库
git init

# 关联远程仓库（替换为你的仓库地址）
git remote add origin git@github.com:你的用户名/obsidian-vault.git

# 首次拉取远程文件（如果仓库有README）
git pull origin main

# 将所有文件加入Git跟踪
git add .

# 提交更改
git commit -m "首次提交：初始化Obsidian库"

# 推送至远程仓库
git push -u origin main
```


# 从机从Github上克隆远程仓库
---
- 作用：首次下载需要，可以理解为从机**创建初版库**
```bash
# 进入你想存放库的目录（如D盘或Home目录）
cd D:\

# 克隆仓库（替换为你的仓库地址）
git clone git@github.com:你的用户名/obsidian-vault.git

# 完成后会生成同名文件夹，用Obsidian打开此文件夹即可
```

# 后续修改上传/下载同步
---
- 作用：维护Github后续数据同步
- git commit -m “此处编辑要呈现的改动信息，便于确认何时上传以及是否成功”
- git pull 为向远程仓库上传
- git push为从远程仓库下载
```bash
# Windows (sync.bat)
@echo off
cd /d "D:\MyObsidianVault"
git add .
git commit -m "AutoSync: %date% %time%"
git pull
git push
pause  # 可选：执行完毕后暂停窗口查看结果
```