# github-action-integration-quickstart

要创建一个GitHub Action，让其他人可以集成你的程序到他们的仓库中，你首先需要定义这个Action的功能。假设你的程序是一个Python脚本，它执行一些代码分析或测试。以下是一个基本的GitHub Action示例，用于集成一个名为`my_script.py`的Python脚本。

首先，你需要在你的项目中创建一个`.github/workflows`目录，并在这个目录下创建一个YAML文件，例如`integration.yml`。这个YAML文件将定义Action的工作流程。

### `integration.yml` 示例

```yaml
name: Integration Workflow

on: 
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  run-my-script:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: '3.8'

    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        if [ -f requirements.txt ]; then pip install -r requirements.txt; fi

    - name: Run my script
      run: python my_script.py
```

### 说明

1. **触发条件**：这个工作流程在主分支上的推送和拉取请求时触发。
2. **作业和步骤**：
   - **运行环境**：这个作业在`ubuntu-latest`虚拟环境上执行。
   - **步骤**：
     - **检出代码**：使用`actions/checkout@v2`来检出仓库代码。
     - **设置Python环境**：使用`actions/setup-python@v2`来设置Python环境，这里使用Python 3.8。
     - **安装依赖**：如果存在`requirements.txt`，则安装必要的Python依赖。
     - **运行脚本**：执行`my_script.py`。

### 分发你的GitHub Action

如果你希望让其他人可以重用你的Action，你可以考虑将其打包成一个单独的GitHub仓库，并通过`uses`指令引用。例如：

- 创建一个新的GitHub仓库，包含你的脚本和一个`action.yml`文件，定义输入、输出和执行的主命令。
- 用户可以通过在他们的工作流程文件中添加如下代码来使用你的Action：

```yaml
- name: Run custom action
  uses: your-github-username/your-action-repo@v1
  with:
    some-input: value
```

这样，其他开发者就可以通过简单的`uses`语句将你的程序集成到他们的工作流中。


查看GitHub Actions工作流日志是跟踪和调试你的自动化过程的重要步骤。以下是如何查看工作流日志的具体步骤：

### 1. 打开你的GitHub仓库
首先，打开你想查看工作流日志的GitHub仓库。

### 2. 导航到"Actions"选项卡
在仓库的顶部菜单栏中，找到并点击“Actions”选项卡。这里展示了所有与你的仓库相关的GitHub Actions工作流。

### 3. 选择特定的工作流
在“Actions”页面，你会看到一个列出所有工作流运行历史的列表。这些工作流可以根据其名称、触发事件或运行状态来筛选和排序。

### 4. 查看工作流运行详情
点击你感兴趣的工作流运行条目。这将打开一个新页面，展示该工作流具体实例的详细信息，包括触发该工作流的提交或事件的信息。

### 5. 查看日志和结果
在工作流详情页面，你可以看到该工作流包含的各个作业。点击任何一个作业，如“build”或“test”，来查看更具体的步骤。

在作业页面，每个步骤（例如“Checkout code”，“Set up Python”等）旁边都有一个下拉箭头。点击这个箭头可以展开该步骤的输出日志。这些日志将显示该步骤的执行结果，包括任何命令的输出、错误信息以及其他诊断信息。

### 6. 下载或搜索日志（可选）
如果你需要更详细地分析日志，GitHub也提供了下载日志的选项。在工作流或具体步骤的页面上，通常有一个“Download logs”按钮，你可以用它来下载包含所有日志信息的ZIP文件。

此外，你可以使用页面上的搜索功能来查找特定的日志内容，这在处理长日志文件时尤其有用。

### 注意事项
- **权限**：确保你有足够的权限访问仓库的Actions日志。
- **日志保留策略**：GitHub Actions日志不会永久保留。根据GitHub的策略，日志存储有一定的期限（如90天），之后会被自动删除。

通过这些步骤，你可以有效地查看和分析GitHub Actions工作流的执行情况，从而帮助你理解和优化你的CI/CD流程。
