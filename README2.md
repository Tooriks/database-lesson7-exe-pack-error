markdown
# 灾难应急管理系统 开发、排错、打包完整记录
## 项目基础信息
- 技术栈：Python + pymysql + MySQL
- 项目结构：main.py(交互菜单层) / dao.py(数据操作层) / db.py(数据库连接层)
- 打包工具：uv + PyInstaller
- 虚拟环境：项目本地 .venv 隔离环境

## 一、初始问题：运行代码报错 ModuleNotFoundError: No module named 'pymysql'
### 报错原因
项目使用 `.venv` 虚拟环境运行代码，但 `pymysql` 仅安装在全局 Python(D:\Python)，虚拟环境内部未安装该依赖，环境隔离导致代码无法识别库。

### 踩坑过程
1. 直接执行 `pip install pymysql`，终端提示 Requirement already satisfied，但安装路径始终指向全局 Python，虚拟环境不生效；
2. 手动拼接虚拟环境 pip 路径执行命令，因目录层级错误，系统提示找不到可执行文件；
3. 旧虚拟环境文件损坏，调用 `.venv\Scripts\python.exe` 提示 No module named pip。

### 最终完整修复步骤
1. 切换到代码子目录
```powershell
cd express-station
删除损坏的旧虚拟环境
powershell
Remove-Item -Recurse -Force .\.venv
新建纯净虚拟环境
powershell
python -m venv .venv
放行 PowerShell 脚本执行权限并激活虚拟环境
powershell
Set-ExecutionPolicy RemoteSigned -Scope Process
.\.venv\Scripts\Activate.ps1
激活成功标识：终端前缀出现 (.venv)
5. 在虚拟环境内安装数据库依赖
powershell
pip install pymysql -i https://pypi.tuna.tsinghua.edu.cn/simple
运行 main.py 验证
VS Code 直接运行代码，系统菜单正常打印，模块缺失报错彻底解决。
二、第二个问题：打包报错 Script file 'main.py' does not exist
报错原因
终端工作目录停留在外层 PythonShujikuCode，main.py 存放在子文件夹 express-station，打包命令找不到入口脚本。
解决步骤
必须先进入代码所在目录
powershell
cd express-station
执行完整打包命令（最终成功版本）
powershell
uv run pyinstaller --onefile --name="灾难应急管理系统" main.py
三、打包命令逐段解析
powershell
uv run pyinstaller --onefile --name="灾难应急管理系统" main.py
uv run：加载当前项目虚拟环境，调用环境内的工具打包，仅打包虚拟环境内安装的依赖，减少 exe 体积；
pyinstaller：Python 打包核心程序，将解释器、源码、第三方库封装为 Windows exe；
--onefile：单文件模式，所有依赖整合为一个 exe，方便提交、拷贝；
--name="灾难应急管理系统"：自定义输出 exe 文件名；
main.py：程序入口启动文件。
打包后目录说明
build/：打包临时缓存文件，可直接删除；
dist/：最终成品 exe 存放目录，作业提交仅取用此文件夹内程序；
灾难应急管理系统.spec：打包配置脚本，可自定义图标、隐藏控制台等。
四、运行 exe 硬性限制（重要）
PyInstaller 不会打包 MySQL 数据库服务，目标电脑必须满足两个条件才能完整使用系统：
本地安装并启动 MySQL 服务；
提前执行建库建表 SQL，创建 disaster_db 数据库与配套数据表；
否则打开程序选择功能时会出现数据库连接失败。
