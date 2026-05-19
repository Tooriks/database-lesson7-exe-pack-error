
```markdown
# Python 打包报错：ModuleNotFoundError: No module named 'pymysql'

## 问题描述
使用 PyInstaller 打包 Python 快递驿站系统为 exe 文件后，运行报错：
```text
ModuleNotFoundError: No module named 'pymysql'
```
<img width="867" height="347" alt="屏幕截图 2026-05-19 190748" src="https://github.com/user-attachments/assets/21352a4e-403a-4532-965b-146f4f87ec85" />

---

## 失败命令（踩坑记录）
```bash
uv run pyinstaller --onefile --name="快递驿站系统" main.py
```

### 失败原因
1.  `uv run` 使用独立虚拟环境，未安装 `pymysql`
2.  未添加隐藏依赖参数，PyInstaller 无法自动识别数据库依赖
3.  虚拟环境与系统环境隔离，导致依赖丢失

---

## 成功命令（最终解决方案）
```bash
pyinstaller -F --hidden-import=pymysql --name="快递驿站系统" main.py
```

### 成功关键说明
- 直接使用**系统 Python + pip**，不使用虚拟环境（uv/venv）干扰最小
- `--hidden-import=pymysql`：强制打包 `pymysql` 库，解决依赖缺失问题
- `-F`：打包为单个 exe 文件，方便分发
- `--name`：自定义 exe 文件名

---

## 核心总结
- 小项目/数据库项目打包：**不要用 `uv run`！**
- PyInstaller + 数据库依赖，**必须加 `--hidden-import=库名`**
- 直接使用系统 `pip` 安装依赖最稳定
```
