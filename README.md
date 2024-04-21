# github-action-integration-quickstart


This repository provides a GitHub Action for integration purposes.

## Usage

```yaml
steps:
  - name: Integration Step
    uses: zgimszhd61/github-action-integration-quickstart@v1
    with:
      myInput: 'Some input'



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


----

是的，将你的Python程序打包到Docker镜像中，并在GitHub Actions中仅下载并执行该镜像是一个常见的方法来隐藏源代码并简化部署过程。这种方法的好处在于增强了代码的安全性，因为源代码不直接暴露在GitHub仓库中，同时还利用了Docker的便携性和环境一致性。下面是如何实现这个过程的步骤：

### 1. 创建Dockerfile
首先，你需要创建一个`Dockerfile`，这个文件定义了如何构建你的Docker镜像，包括安装依赖、添加源代码、设置工作目录和指定运行时的命令。例如：

```dockerfile
# 使用官方Python镜像作为基础镜像
FROM python:3.8-slim

# 设置工作目录
WORKDIR /app

# 将依赖文件复制到容器中
COPY requirements.txt .

# 安装依赖
RUN pip install --no-cache-dir -r requirements.txt

# 将源代码复制到容器中
COPY . .

# 运行Python程序
CMD ["python", "my_script.py"]
```

### 2. 构建和上传镜像
在本地构建Docker镜像，并将其推送到一个Docker容器注册中心，如Docker Hub或GitHub Container Registry。例如：

```bash
docker build -t username/my-python-app:latest .
docker push username/my-python-app:latest
```

### 3. 配置GitHub Actions
接下来，修改你的GitHub Actions配置，让其使用Docker来运行你的程序。这可以通过使用`docker`命令直接在运行时拉取和运行你的镜像来实现。以下是在GitHub Actions中使用Docker镜像的`workflow.yml`示例：

```yaml
name: Run Docker App

on: [push, pull_request]

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout
      uses: actions/checkout@v2

    - name: Log in to Docker Hub
      uses: docker/login-action@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Pull and Run Docker Image
      run: |
        docker pull username/my-python-app:latest
        docker run username/my-python-app:latest
```

在这个配置中，`docker/login-action`步骤用于登录到Docker Hub（如果你的镜像是私有的）。然后，使用`docker pull`和`docker run`命令来运行你的镜像。

### 注意事项
- **保密性**：虽然这种方法可以隐藏源代码，但不可避免地会增加一定的复杂性，并且如果镜像被提取和反编译，代码还是可能被恢复。确保使用适当的镜像存储权限和安全措施。
- **维护性**：每次更改代码后，都需要重新构建和推送新的Docker镜像。
- **性能**：下载和启动Docker镜像可能会花费比直接运行脚本更多的时间。

这种方式非常适合于需要保护源代码同时享受Docker带来的环境一致性优势的场景。


-----

隐藏Python脚本中的敏感信息（如密钥、密码和API令牌）是保护应用安全的重要步骤。如果你已经将程序打包到Docker镜像中，并在GitHub Actions中使用，你还需要采取以下措施来进一步加固代码并确保敏感信息的安全：

### 1. 使用环境变量
环境变量是管理配置和敏感信息的一种安全方式。你可以在Docker容器中设置环境变量，并在Python脚本中通过`os.environ`来访问这些变量。

#### 在Dockerfile中设置环境变量：
你可以在Dockerfile中使用`ENV`命令来设置环境变量，但不建议这样做，因为它会将敏感信息写入镜像。相反，你应该在运行容器时动态传递这些变量：

```bash
docker run -e "API_KEY=your_api_key" username/my-python-app:latest
```

#### 在GitHub Actions中设置环境变量：
在GitHub Actions工作流中，你可以使用`secrets`来安全地处理敏感信息，并在运行容器时将这些信息作为环境变量传递：

```yaml
- name: Pull and Run Docker Image
  run: |
    docker pull username/my-python-app:latest
    docker run -e API_KEY=${{ secrets.API_KEY }} username/my-python-app:latest
```

### 2. 使用配置文件
另一种策略是将配置存储在文件中，而不是直接在代码中。这些配置文件不应该被提交到版本控制系统中。

#### 在Docker中使用配置文件：
你可以在本地创建配置文件，并在运行容器时将其挂载到容器内：

```bash
docker run -v $(pwd)/config.json:/app/config.json username/my-python-app:latest
```

在Python代码中，你可以使用标准的文件读取操作来访问这些配置信息：

```python
import json

with open('config.json') as config_file:
    config = json.load(config_file)
api_key = config['API_KEY']
```

### 3. 使用秘密管理工具
对于更复杂的应用，可以考虑使用专门的秘密管理工具，如HashiCorp Vault、AWS Secrets Manager或Azure Key Vault。这些工具专为安全存储和访问敏感信息设计。

### 4. 最小权限原则
无论在Docker容器还是在GitHub Actions中，都应遵循最小权限原则。只给予程序完成其任务所必需的权限，不多也不少。这有助于减少安全风险。

### 5. 安全审计和日志记录
确保你的应用有适当的日志记录和监控机制，以便在出现安全问题时迅速响应。同时，定期审计这些日志和你的安全配置。

通过这些措施，你可以增强Docker容器中Python应用的安全性，更有效地保护敏感信息不被泄露。


------

为了使你的Python脚本能够接收GitHub仓库地址作为参数，你需要在`action.yml`文件中定义输入参数，并在你的脚本中适当地处理这些参数。这样，当其他人使用你的Action时，他们可以提供特定的仓库地址，你的脚本则能够使用这个地址执行相关的任务。

### 步骤 1: 定义输入参数

在你的`action.yml`文件中，你可以添加一个输入字段来接收仓库地址。例如：

```yaml
name: 'My GitHub Action'
description: 'Performs operations using a repository URL.'
inputs:
  repository_url:
    description: 'URL of the repository to operate on.'
    required: true
runs:
  using: 'composite'
  steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Setup Python
      uses: actions/setup-python@v2
      with:
        python-version: '3.8'

    - name: Run script
      run: python your_script.py ${{ inputs.repository_url }}
      shell: bash
```

这里，我们定义了一个名为`repository_url`的输入参数，它是必需的，并且在运行Python脚本时作为命令行参数传递。

### 步骤 2: 修改Python脚本以接收参数

在你的Python脚本中，你需要处理命令行参数以接收仓库地址。这可以通过使用标准库中的`argparse`模块来实现。以下是一个简单的例子：

```python
import argparse

def main():
    parser = argparse.ArgumentParser(description='Process some integers.')
    parser.add_argument('repository_url', type=str, help='URL of the repository to operate on')
    
    args = parser.parse_args()
    
    # 使用参数
    print(f"Operating on repository at: {args.repository_url}")

if __name__ == "__main__":
    main()
```

这段代码创建了一个解析器对象，定义了一个位置参数`repository_url`，并从命令行读取该参数。当你的脚本被执行时，它会打印出提供的仓库地址。

### 步骤 3: 使用Action

当其他用户在他们的GitHub Actions工作流中引用你的Action时，他们可以这样指定仓库地址：

```yaml
steps:
  - name: Use My GitHub Action
    uses: your-username/your-action-repo@v1
    with:
      repository_url: 'https://github.com/example/repo'
```

这样，`repository_url`的值会被传递给你的Python脚本，并由脚本进行处理。

确保你的GitHub仓库中的文档清晰地说明了如何提供这些参数，这会帮助用户正确地使用你的Action。


