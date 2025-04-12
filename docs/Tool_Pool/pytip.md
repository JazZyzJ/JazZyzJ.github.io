---
comment: true
---

# Python Tips

!!! abstract 
    This page is a collection of Python tips.

    Including embedded Python code and useful functions.


## Embed Python Code


### [pathlib](https://docs.python.org/3/library/pathlib.html)

> Introduce to the ```Path``` class in ```pathlib``` module.

!!! note "Intro"

    Being the replacement of ```os.path```, intended for simplifying the operations on files and file routes.

    - Unified the route operations on different platforms.
    - Linked call: Support consecutive operations like ```setup route -> check file -> read/write file```.

??? example "Usage"

    - Setup route obj:

    ```python
    from pathlib import Path

    # 从字符串创建
    p1 = Path("data/files")
    p2 = Path("/home/user/docs/report.txt")

    # 特殊路径快捷方式
    home_dir = Path.home()  # 用户主目录（如 /home/user）
    current_dir = Path.cwd()  # 当前工作目录
    ```

    - Route connection: use ```/``` to replace ```os.path.join```.
    
    ```python
    base = Path("data")
    file_path = base / "subfolder" / "file.txt"  # 自动处理分隔符
    ```

    - Check file:
    
    ```python
    path = Path("data/file.txt")

    if path.exists():
    print("文件存在！")

    if path.is_file():
    print("这是一个文件！")

    if path.is_dir():
    print("这是一个目录！")
    ```
    
    - Read/write file:

    ```python
    path = Path("data/file.txt")

    # 读取文本
    content = path.read_text(encoding="utf-8")

    # 写入文本
    path.write_text("Hello, Path!", encoding="utf-8")

    # 处理二进制文件（如图片）
    binary_data = path.read_bytes()
    ```

    - Iterate over directory:
    
    ```python
    dir_path = Path("data")

    # 遍历所有文件和子目录
    for item in dir_path.iterdir():
    print(item.name)

    # 递归遍历所有文件（含子目录）

    for file in dir_path.glob("**/*.txt"):
    print(file)

    ```

    - Route decomposition:

    ```python
    path = Path("/home/user/data/report.txt")

    # 获取文件名（带后缀）
    print(path.name)          # 输出: report.txt

    # 获取文件名（不带后缀）
    print(path.stem)          # 输出: report

    # 获取后缀
    print(path.suffix)        # 输出: .txt

    # 获取父目录
    print(path.parent)        # 输出: /home/user/data

    # 修改后缀
    new_path = path.with_suffix(".csv")
    print(new_path)           # 输出: /home/user/data/report.csv
    ```

    - Linked operations:
    
    ```python
    (Path.cwd() / "data")
    .mkdir(exist_ok=True)          # 创建目录（如果不存在）
    .with_name("backup")           # 重命名为 backup
    .mkdir(parents=True)           # 创建多级目录
    ```

    - Wildcard search:

    ```python
    # 查找当前目录下所有 .csv 文件
    for csv_file in Path(".").glob("*.csv"):
    print(csv_file)

    # 递归查找所有子目录中的 .png 文件
    for png_file in Path("images").rglob("*.png"):
    print(png_file)
    ```






