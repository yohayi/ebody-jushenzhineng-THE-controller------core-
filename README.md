THE-controller — 桌面智能助手 (本地AI + 物理控制)
一个轻量级的桌面智能体，通过本地大模型（Ollama）理解自然语言，执行桌面自动化操作（点击、输入、快捷键等），并可扩展为树莓派物理控制终端。基于 Python + Tkinter 构建，极简高效。

目录结构
text
THE-controller/
├── .gitignore               # Git 忽略文件配置
├── README.md                # 项目说明文档
├── main.py                  # 程序入口（可选）
├── gui.py                   # 主界面：Tkinter 图形窗口
│
├── config/                  # 配置模块
│   └── settings.py          # Ollama 地址、模型名称、环境配置
│
├── llm/                     # 大语言模型交互
│   └── client.py            # 调用本地模型、解析 JSON、组合提示词（含 OCR）
│
├── perception/              # 感知模块
│   ├── screenshot.py        # 屏幕截图（PIL）
│   └── ocr.py               # OCR 文字识别（EasyOCR）
│
├── exec/                    # 执行模块
│   ├── executor.py          # 执行单个/多个动作（click, input, hotkey, wait, skill）
│   ├── skills.py            # 技能库注册与调用（如打开标签页、搜索等）
│   └── physical.py          # 树莓派物理控制（占位，后续扩展）
│
└── docs/                    # （可选）文档文件夹，可放截图等
文件功能说明
文件	功能
gui.py	图形界面，提供文本输入、日志输出、环境选择。用户在这里输入自然语言任务。
main.py	可选入口，直接启动 GUI。
config/settings.py	全局配置：Ollama API 地址、模型名称、预设环境（通用/Chrome/Rhino 等）。
llm/client.py	封装对本地模型的调用。接收任务和环境，结合屏幕 OCR 文字构建提示词，返回解析后的 JSON 动作。
perception/screenshot.py	截取当前屏幕，返回 PIL Image 对象或字节流。
perception/ocr.py	使用 EasyOCR 识别屏幕文字，供 AI 参考。
exec/executor.py	根据解析出的动作字典执行具体操作（如 click、input、hotkey、wait、skill）。
exec/skills.py	预定义可复用的技能（如打开 Chrome 标签页、关闭标签页、搜索）。支持注册机制，AI 可通过 skill 动作调用。
exec/physical.py	未来扩展：控制树莓派机械臂等物理设备的接口（需配合树莓派服务端）。
安装与依赖
1. 环境要求
Python 3.10+

本地已安装并运行 Ollama，且已拉取模型（例如 deepseek-coder:6.7b）

2. 安装 Python 依赖
bash
pip install requests pyautogui pillow easyocr
requests：调用 Ollama API

pyautogui：键盘鼠标控制

pillow：屏幕截图

easyocr：OCR 文字识别（首次运行会自动下载模型）

使用方法
确保 Ollama 服务在后台运行（默认端口 11434）。

运行 GUI：

bash
python gui.py
或直接双击 gui.py（需要 Python 关联）。

在输入框中输入自然语言任务，例如：

“打开记事本并输入 Hello World”

“在 Chrome 中打开百度并搜索天气”

选择当前操作环境（通用/Chrome/Rhino 等），有助于 AI 理解界面。

点击“执行任务”，系统将：

截图并 OCR 屏幕文字

结合任务和环境生成提示词

调用本地模型返回 JSON 动作

依次执行动作，并输出日志

##扩展指南
增加新技能
在 exec/skills.py 中编写函数，并用 register_skill() 注册：

python
def my_new_skill(param):
    # 实现功能
    return "执行结果"

register_skill("my_skill", my_new_skill, "技能描述")
AI 可以通过输出 {"type": "skill", "name": "my_skill", "args": {...}} 调用。

添加新环境
在 config/settings.py 的 ENVIRONMENTS 字典中添加新条目，定义 system_prompt 和可用技能列表。

接入树莓派
在 exec/physical.py 中实现 HTTP 请求树莓派服务。

在 executor.py 中增加 move_arm 等动作类型。

AI 可生成相应动作。

许可证
MIT License

未来规划
多步骤规划与自我反思

更丰富的视觉感知（目标检测）

语音输入支持

完善树莓派物理控制
