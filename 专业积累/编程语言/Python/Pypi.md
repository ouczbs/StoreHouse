## 自定义打包

### 源代码打包

- Python 语言开发
- *sdist*
- .tar.gz

### 二进制发行版

- c/c++ 开发
- wheel

## 打包项目

- 根目录新建 setup.py 文件

  - ```python
    import setuptools
    
    #根目录新建 README.md 文件
    #with open("README.md", "r") as fh:
    #    long_description = fh.read()
    
    setuptools.setup(
        name="example-pkg-YOUR-USERNAME-HERE", # Replace with your own username
        version="0.0.1",
        author="Example Author",
        author_email="author@example.com",
        description="A small example package",
        long_description=long_description,
        long_description_content_type="text/markdown", # 对应前面的 README.md
        url="https://github.com/pypa/sampleproject",
        packages=setuptools.find_packages(),
        classifiers=[ # 定义 软件包 Python 版本
            "Programming Language :: Python :: 3",
            "License :: OSI Approved :: MIT License",
            "Operating System :: OS Independent",
        ],
        python_requires='>=3.6',
    )
    ```

  - 超链接

    -  [打包和分发项目](https://packaging.python.org/guides/distributing-packages-using-setuptools/)

  - 运行命令
    - ```
      python setup.py sdist bdist_wheel
      ```

  - 完成后应在`dist`目录中生成两个文件

    - ```shell
      example_pkg_YOUR_USERNAME_HERE-0.0.1-py3-none-any.whl
      example_pkg_YOUR_USERNAME_HERE-0.0.1.tar.gz
      ```

- 上传档案到Pypi

  - 需安装 twins

    - ```
      python -m pip install --user --upgrade twine
      ```

  - 上传档案

    - ```
      python -m twine upload --repository testpypi dist/*
      ```

- 安装到本地

  - ```
    python -m pip install --index-url https://test.pypi.org/simple/ --no-deps example-pkg-YOUR-USERNAME-HERE
    ```

### 注意事项

- setup.py 需要与模块放在同一级目录下 
- 项目一个版本只能提交一次
- 无法删除Pypi项目

