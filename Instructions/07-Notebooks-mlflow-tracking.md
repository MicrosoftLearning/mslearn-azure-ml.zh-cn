---
lab:
  title: 使用 MLflow 跟踪笔记本中的模型训练
---

# 使用 MLflow 跟踪笔记本中的模型训练

通常，你将通过试验和训练多个模型来启动新的数据科学项目。 若要跟踪你的工作并保持对所训练模型及其表现的概要了解，可使用 MLflow 跟踪。

在本练习中，在计算实例上运行的笔记本中进行 MLflow 以记录模型训练。

## 准备工作

需要一个你在其中具有管理级权限的 [Azure 订阅](https://azure.microsoft.com/free)。

## 预配 Azure 机器学习工作区

借助 Azure 机器学习工作区，可集中管理训练和管理模型所需的所有资源和资产。 可以通过工作室、Python SDK 和 Azure CLI 与 Azure 机器学习工作区进行交互。

使用 Azure CLI 预配工作区和必要的计算，并使用 Python SDK 通过自动化机器学习来训练分类模型。

### 创建工作区和计算资源

若要创建 Azure 机器学习工作区和计算实例，请使用 Azure CLI。 所有必需的命令都被分组到一个 Shell 脚本中，以便于执行。
1. 在浏览器中，打开 Azure 门户 (`https://portal.azure.com/`)，并登录 Microsoft 帐户。
1. 选择页面顶部搜索框右侧的 \[>_] (Cloud Shell) 按钮。 这会打开门户底部的 Cloud Shell 窗格。
1. 如果系统询问，请选择“Bash”。 首次打开 Cloud Shell 时，系统可能会要求你选择要使用的 shell 类型（Bash 或 PowerShell） 。
1. 如果系统要求你为 Cloud Shell 创建存储，请确认已指定正确的订阅，然后选择“创建存储”。 等待存储创建完成。
1. 在终端，输入以下命令以克隆此存储库：

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > 使用 `SHIFT + INSERT` 将复制的代码粘贴到 Cloud Shell 中。 

1. 克隆存储库后，输入以下命令以更改为此实验室的文件夹，然后运行其中包含的 setup.sh 脚本：

    ```azurecli
    cd azure-ml-labs/Labs/07
    ./setup.sh
    ```

    > 忽略任何指示扩展未安装的（错误）消息。

1. 等待脚本完成 - 这通常需要大约 5-10 分钟。

## 克隆实验室材料

创建工作区和必要的计算资源后，可打开 Azure 机器学习工作室并将实验室材料克隆到工作区中。

1. 在 Azure 门户，导航到名为“mlw-dp100-...”的 Azure 机器学习工作区。
1. 选择 Azure 机器学习工作区，并在其“概述”页中选择“启动工作室” 。 将在浏览器中打开另一个选项卡以打开 Azure 机器学习工作室。
1. 关闭工作室中显示的任何弹出窗口。
1. 在 Azure 机器学习工作室中，导航到“计算”页，并验证在上一部分中创建的计算实例是否存在。 该计算实例应该正在运行。
1. 在“计算实例”选项卡中，找到计算实例，然后选择“终端”应用程序 。
1. 在终端中，通过在终端中运行以下命令来在计算实例上安装 Python SDK 和 MLflow 库：

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    pip install mlflow
    ```

    > 忽略任何指示无法找到包和卸载包的（错误）消息。

1. 运行以下命令，将包含笔记本、数据和其他文件的 Git 存储库克隆到工作区：

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. 命令完成后，在“文件”窗格中，单击“&#8635;”刷新视图，并验证是否已创建新的 /users/your-user-name/azure-ml-labs 文件夹  。

## 使用 MLflow 跟踪模型训练

现在，你已拥有所有必要的资源，可在笔记本中训练模型时运行笔记本以配置和使用 MLflow。

1. 打开“实验室/07/使用 MLflow 跟踪模型训练.ipynb”笔记本。

    > 如果出现要求进行身份验证的通知，选择“验证”并按照必要的步骤进行操作。

1. 验证笔记本是否使用 Python 3.8 - AzureML 内核。
1. 运行笔记本中的所有单元。
1. 查看每次训练模型时创建的新作业。

## 删除 Azure 资源

当你完成对 Azure 机器学习的探索时，应删除已创建的资源，以避免产生不必要的 Azure 成本。

1. 关闭“Azure 机器学习工作室”选项卡并返回到 Azure 门户。
1. 在 Azure 门户的**主页**上，选择“资源组”。
1. 选择“rg-dp100-...”资源组。
1. 在资源组的“概述”页的顶部，选择“删除资源组”。
1. 输入资源组名称以确认要删除该资源组，然后选择“删除”。
