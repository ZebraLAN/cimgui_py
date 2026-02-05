# cimgui_py

Dear ImGui 的 Python 绑定，基于 cimgui。代码从 cimgui 的 JSON 定义自动生成。

## 构建

依赖：Python 3.10+、Cython 3.0+、CMake、Visual Studio 2022、LuaJIT

```powershell
# 1. 构建 cimgui.dll（会自动下载 GLFW、运行 generator）
.\scripts\build_cimgui.ps1

# 2. 生成绑定代码
python codegen/compiler.py -o src

# 3. 安装
pip install -e .
```

LuaJIT 安装：`winget install DEVCOM.LuaJIT`

## 用法

```python
import cimgui_py as imgui
from cimgui_py import backend

# 初始化
imgui.create_context()
backend.glfw_init_for_open_gl(window, True)
backend.opengl3_init("#version 330")

# 渲染循环
backend.glfw_new_frame()
backend.opengl3_new_frame()
imgui.new_frame()

if imgui.begin("Test"):
    imgui.text("Hello")
    if imgui.button("Click"):
        print("clicked")
    imgui.end()

imgui.render()
backend.opengl3_render_draw_data(imgui.get_draw_data())
```

## 项目结构

```
codegen/
├── compiler.py              # 代码生成器主入口
├── config/overrides.json    # 需要特殊处理的函数（input_text、combo 等）
└── templates/               # Jinja2 模板
    ├── cimgui.pxd.jinja2    # Cython 声明
    ├── core.pyx.jinja2      # 核心模块实现
    ├── core.pyi.jinja2      # 核心模块类型提示
    ├── backend.pyx.jinja2   # 后端实现 (GLFW + OpenGL3)
    └── backend.pyi.jinja2   # 后端类型提示

src/cimgui_py/               # 生成的代码（git ignored）
vendor/cimgui/               # cimgui submodule
lib/                         # 构建产物（git ignored）
```

## 重新生成

更新 imgui 版本后：

```powershell
# 重新构建 dll
.\scripts\build_cimgui.ps1

# 重新生成绑定（会读取新的 definitions.json）
python codegen/compiler.py -o src
```

## 相关项目

- [Dear ImGui](https://github.com/ocornut/imgui)
- [cimgui](https://github.com/cimgui/cimgui)
