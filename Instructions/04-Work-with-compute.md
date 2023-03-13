---
lab:
  title: 使用 Azure 机器学习中的计算资源
---

# 使用 Azure 机器学习中的计算资源

云的主要优点之一是，可以使用可缩放的、按需计算资源来经济高效地处理大型数据。

在本练习中，你将学习如何使用 Azure 机器学习中的云计算来大规模地运行实验和生产环境代码。

## 准备工作

需要一个你在其中具有管理级权限的 [Azure 订阅](https://azure.microsoft.com/free?azure-portal=true)。

## 预配 Azure 机器学习工作区

借助 Azure 机器学习工作区，可集中管理训练和管理模型所需的所有资源和资产。 可以通过工作室、Python SDK 和 Azure CLI 与 Azure 机器学习工作区进行交互。

若要创建 Azure 机器学习工作区，请使用 Azure CLI。 所有必需的命令都被分组到一个 Shell 脚本中，以便于执行。

1. 在浏览器中，打开 Azure 门户 (`https://portal.azure.com/`)，并使用 Microsoft 帐户登录。
1. 选择页面顶部搜索框右侧的 \[>_] (Cloud Shell) 按钮。 这会打开门户底部的 Cloud Shell 窗格。
1. 如果系统询问，请选择“Bash”。 首次打开 Cloud Shell 时，系统可能会要求你选择要使用的 shell 类型（Bash 或 PowerShell） 。 
1. 如果系统要求你为 Cloud Shell 创建存储，请确认已指定正确的订阅，然后选择“创建存储”。 等待存储创建完成。
1. 若要避免与以前的版本发生任何冲突，请通过在终端中运行以下命令来删除任何 ML CLI 扩展（版本 1 和 2）：

    ```azurecli
    az extension remove -n azure-cli-ml
    az extension remove -n ml
    ```

    > 使用 `SHIFT + INSERT` 将复制的代码粘贴到 Cloud Shell 中。 

    > 忽略任何指示扩展未安装的（错误）消息。 

1. 使用以下命令安装 Azure 机器学习 (v2) 扩展：
    
    ```azurecli
    az extension add -n ml -y
    ```

1. 创建资源组。 选择靠近你的位置。

    ```azurecli
    az group create --name "rg-dp100-labs" --location "eastus"
    ```

1. 创建工作区：

    ```azurecli
    az ml workspace create --name "mlw-dp100-labs" -g "rg-dp100-labs"
    ```

1. 等待命令完成 - 这通常需要大约 5-10 分钟。 

## 创建计算安装脚本

若要在 Azure 机器学习工作区内运行笔记本，将需要一个计算实例。 可以使用安装脚本在创建时配置计算实例。

1. 在 Azure 门户，导航到名为 mlw-dp100-labs 的 Azure 机器学习工作区。
1. 选择 Azure 机器学习工作区，并在其“概述”页中选择“启动工作室” 。 将在浏览器中打开另一标签页，以打开 Azure 机器学习工作室。
1. 关闭工作室中显示的任何弹出窗口。
1. 在 Azure 机器学习工作室中，导航到 Notebooks 页。
1. 在“文件”窗格中，选择“&#10753;”图标以添加文件 。 
1. 选择“创建新文件”****。
1. 验证文件位置是否为 Users/your-user-name。
1. 将文件类型更改为 Bash (*.sh)。
1. 将文件名更改为 `compute-setup.sh`。
1. 打开新创建的 compute-setup.sh 文件，并将以下内容粘贴到其内容中：

    ```azurecli
    #!/bin/bash

    # clone repository
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. 保存 compute-setup.sh 文件。

## 创建计算实例

若要创建计算实例，可以使用工作室、Python SDK 或 Azure CLI。 你将通过刚创建的安装脚本使用工作室创建计算实例。

1. 使用左侧的菜单导航到“计算”页。
1. 在“计算实例”选项卡中，选择“新建” 。
1. 使用以下设置配置（先不要创建）计算实例： 
    - 计算名称：输入唯一名称
    - **虚拟机类型**：CPU
    - **虚拟机大小**：Standard_DS11_v2
1. 在完成时选择“下一步:高级设置”。
1. 选择“添加计划”，并将计划配置为在每天 18:00 或下午 6:00 停止计算实例   。 
1. 选择“使用安装脚本进行预配”的切换开关。 
1. 选择之前创建的 compute-setup.sh 脚本。
1. 查看其他高级设置，但不要选择它们：
    - **启用 SSH 访问**：可使用此设置来实现使用 SSH 客户端直接访问虚拟机。
    - **启用虚拟网络**：通常会在企业环境中使用此设置来增强网络安全。
    - **分配给其他用户**：可使用此设置将计算实例分配给其他数据科学家。
1. 创建计算实例，等待它启动以及状态更改为“正在运行” 。
1. 当计算实例正在运行时，导航到 Notebooks 页。 在“文件”窗格中，单击“&#8635;”刷新视图，并验证是否已创建新的 /users/your-user-name/dp100-azure-ml-labs 文件夹  。 

## 配置计算实例

创建计算实例后，可以在该实例上运行笔记本。 可能需要安装某些包才能运行所需的代码。 可以在安装脚本中包含包，也可以使用终端进行安装。

1. 在“计算实例”选项卡中，找到计算实例，然后选择“终端”应用程序 。
1. 在终端中，通过在终端中运行以下命令，在计算实例上安装 Python SDK：

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > 忽略任何指示包未安装的（错误）消息。

1. 安装包后，可以关闭选项卡以终止终端。 

## 创建计算群集

Notebooks 非常适合在试验期间进行开发或迭代工作。 试验时，需要在计算实例上运行笔记本，以快速测试和查看代码。 迁移到生产环境时，建议在计算群集上运行脚本。 你将使用 Python SDK 创建计算群集，然后使用它以作业的形式运行脚本。

1. 打开 Labs/04/Work with compute.ipynb 笔记本。

    > 如果出现要求进行身份验证的通知，选择“验证”并按照必要的步骤进行操作。 

1. 验证笔记本是否使用 Python 3.8 - AzureML 内核。 
1. 运行笔记本中的所有单元。

## 删除 Azure 资源

当你完成对 Azure 机器学习的探索时，应删除已创建的资源，以避免产生不必要的 Azure 成本。

1. 关闭“Azure 机器学习工作室”选项卡并返回到 Azure 门户。
1. 在 Azure 门户的**主页**上，选择“资源组”。
1. 选择“rg-dp100-labs”资源组。
1. 在资源组的“概述”页的顶部，选择“删除资源组”。 
1. 输入资源组名称以确认要删除该资源组，然后选择“删除”。
