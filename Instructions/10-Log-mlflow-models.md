---
lab:
  title: 使用 MLflow 记录和注册模型
---

# 使用 MLflow 记录和注册模型

MLflow 是用于管理端到端机器学习生命周期的开源平台。 使用 MLflow 记录模型时，可以轻松地在不同平台和工作负载之间移动模型。

在本练习中，你将使用 MLflow 记录机器学习模型。

## 准备工作

需要一个你在其中具有管理级权限的 [Azure 订阅](https://azure.microsoft.com/free?azure-portal=true)。

## 预配 Azure 机器学习工作区

借助 Azure 机器学习工作区，可集中管理训练和管理模型所需的所有资源和资产。 可以通过工作室、Python SDK 和 Azure CLI 与 Azure 机器学习工作区进行交互。

你将使用 Azure CLI 预配工作区和必要的计算，并使用 Python SDK 运行命令作业。

### 创建工作区和计算资源

若要创建 Azure 机器学习工作区、计算实例计算群集，请使用 Azure CLI。 所有必需的命令都被分组到一个 Shell 脚本中，以便于执行。

1. 在浏览器中，打开 Azure 门户 (`https://portal.azure.com/`)，并登录 Microsoft 帐户。
1. 选择页面顶部搜索框右侧的 \[>_] (Cloud Shell) 按钮。 这会打开门户底部的 Cloud Shell 窗格。
1. 如果系统询问，请选择“Bash”。 首次打开 Cloud Shell 时，系统可能会要求你选择要使用的 shell 类型（Bash 或 PowerShell） 。
1. 检查是否指定了正确的订阅以及是否选择了**不需要存储帐户**。 选择“应用”。
1. 在终端，输入以下命令以克隆此存储库：

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > 使用 `SHIFT + INSERT` 将复制的代码粘贴到 Cloud Shell 中。

1. 克隆存储库后，输入以下命令以更改为此实验室的文件夹，然后运行其中包含的 setup.ps1 脚本：

    ```azurecli
    cd azure-ml-labs/Labs/10
    ./setup.sh
    ```

    > 忽略表示扩展未安装的（错误）消息。

1. 等待脚本完成 - 这通常需要大约 5-10 分钟。

## 克隆实验室材料

创建工作区和必要的计算资源后，可以打开 Azure 机器学习工作室并将实验室材料克隆到工作区中。

1. 在 Azure 门户，导航到名为 **mlw-dp100-...** 的 Azure 机器学习工作区。
1. 选择 Azure 机器学习工作区，并在其“概述”页中选择“启动工作室” 。 将在浏览器中打开另一标签页，以打开 Azure 机器学习工作室。
1. 关闭工作室中显示的弹出窗口。
1. 在 Azure 机器学习工作室中，导航到“计算”页，并验证在上一部分中创建的计算实例和群集是否存在。 计算实例应正在运行，群集应处于空闲状态，且运行中的节点数为 0。
1. 在“计算实例”选项卡中，找到计算实例，然后选择“终端”应用程序 。
1. 在终端中，通过在终端中运行以下命令，在计算实例上安装 Python SDK：

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > 忽略任何指示无法找到包和卸载包的（错误）消息。

1. 运行以下命令，将包含笔记本、数据和其他文件的 Git 存储库克隆到工作区：

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. 命令完成后，在“文件”窗格中，单击 &#8635; 来刷新视图，并验证是否已创建新的 /users/your-user-name/azure-ml-labs 文件夹  。

## 从笔记本提交 MLflow 作业

现在，你已拥有所有必要的资源，可以运行笔记本来使用 MLflow 训练和记录模型。

1. 打开“Labs\10\Log models with MLflow.ipynb”笔记本。

    > 选择“身份验证”，如果出现要求进行身份验证的通知，请执行必要的步骤。

1. 验证笔记本是否使用“Python 3.8 - AzureML”内核。
1. 运行笔记本中的所有单元格。

## 删除 Azure 资源

当你完成对 Azure 机器学习的探索时，应删除已创建的资源，以避免产生不必要的 Azure 成本。

1. 关闭“Azure 机器学习工作室”选项卡并返回到 Azure 门户。
1. 在 Azure 门户的**主页**上，选择“资源组”。
1. 选择“rg-dp100-...”资源组。
1. 在资源组的“概述”页的顶部，选择“删除资源组”。
1. 输入资源组名称以确认要删除该资源组，然后选择“删除”。
