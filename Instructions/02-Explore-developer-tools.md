---
lab:
  title: 了解用于工作区交互的开发人员工具
---

# 了解用于工作区交互的开发人员工具

可使用各种工具与 Azure 机器学习工作区进行交互。 根据需要执行的任务和对开发人员工具的偏好，可选择何时使用哪种工具。 本实验室旨在介绍通常用于工作区交互的开发人员工具。 若要更深入地了解如何使用特定工具，可探索其他实验室。

## 准备工作

需要一个你在其中具有管理级权限的 [Azure 订阅](https://azure.microsoft.com/free?azure-portal=true)。

用于与 Azure 机器学习工作区交互的常用开发人员工具包括：

- 带有 Azure 机器学习扩展的 Azure CLI：此命令行方法非常适合基础结构自动化。
- Azure 机器学习工作室：使用用户友好的 UI 探索工作区及其所有功能。
- 适用于 Azure 机器学习的 Python SDK：用于从 Jupyter 笔记本提交作业和管理模型，非常适合数据科学家。

你将针对通常使用该工具完成的任务探索其中的每一个工具。

## 使用 Azure CLI 预配基础结构

若要让数据科学家使用 Azure 机器学习训练机器学习模型，你需要设置必要的基础结构。 可使用带有 Azure 机器学习扩展的 Azure CLI，创建 Azure 机器学习工作区和资源，例如计算实例。

首先，打开 Azure Cloud Shell，安装 Azure 机器学习扩展并克隆 Git 存储库。

1. 在浏览器中，打开 Azure 门户 (`https://portal.azure.com/`)，并使用 Microsoft 帐户登录。
1. 选择页面顶部搜索框右侧的 \[>_] (Cloud Shell) 按钮。 这会打开门户底部的 Cloud Shell 窗格。
1. 如果系统询问，请选择“Bash”。 首次打开 Cloud Shell 时，系统可能会要求你选择要使用的 shell 类型（Bash 或 PowerShell） 。
1. 如果系统要求你为 Cloud Shell 创建存储，请确认已指定正确的订阅，然后选择“创建存储”。 等待存储创建完成。
1. 使用以下命令删除任何 ML CLI 扩展（版本 1 和 2），避免与以前的版本发生任何冲突：
    
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

1. 等待创建工作区及其关联资源 - 这通常需要大约 5 分钟。

## 使用 Azure CLI 创建计算实例

训练机器学习模型所需的基础结构的另一个重要部分是计算。 虽然可在本地训练模型，但使用云计算更具可伸缩性和成本效益。

当数据科学家在 Azure 机器学习工作区中开发机器学习模型时，他们希望使用可在其中运行 Jupyter 笔记本的虚拟机。 对于开发，计算实例是理想的选择。

创建 Azure 机器学习工作区后，还可使用 Azure CLI 创建计算实例。

在本练习中，使用以下设置创建计算实例：

- 计算名称：计算实例的名称。必须是唯一的且字符数少于 24 个。
- 虚拟机大小：STANDARD_DS11_V2
- 计算类型（实例或群集）：ComputeInstance
- Azure 机器学习工作区名称：mlw-dp100-labs
- 资源组：rg-dp100-labs

1. 使用以下命令在工作区中创建计算实例。 如果计算实例名称包含“XXXX”，请将其替换为随机数以创建唯一名称。

    ```azurecli
    az ml compute create --name "ciXXXX" --size STANDARD_DS11_V2 --type ComputeInstance -w mlw-dp100-labs -g rg-dp100-labs
    ```

    如果收到错误消息，指出具有该名称的计算实例已存在，请更改名称并重试命令。

## 使用 Azure CLI 创建计算群集

尽管计算实例非常适合开发，但当我们想要训练机器学习模型时，计算群集更适合。 仅当提交作业以使用计算群集时，才会将其大小调整为 0 个以上的节点并运行该作业。 不再需要计算群集后，它会自动重设回 0 个节点的大小，最大程度地降低成本。 

若要创建计算群集，可使用 Azure CLI，类似于创建计算实例。

使用以下设置创建计算群集：

- 计算名称：aml-cluster
- 虚拟机大小：STANDARD_DS11_V2
- 计算类型：AmlCompute（创建计算群集）
- 最大实例数：最大节点数
- Azure 机器学习工作区名称：mlw-dp100-labs
- 资源组：rg-dp100-labs

1. 使用以下命令在工作区中创建计算群集。
    
    ```azurecli
    az ml compute create --name "aml-cluster" --size STANDARD_DS11_V2 --max-instances 2 --type AmlCompute -w mlw-dp100-labs -g rg-dp100-labs
    ```

## 使用 Azure 机器学习工作室配置工作站

尽管 Azure CLI 非常适合自动化，但你可能想要查看所执行的命令的输出。 可使用 Azure 机器学习工作室检查是否已创建资源和资产，以及检查作业是否成功运行或查看作业失败的原因。 

1. 在 Azure 门户，导航到名为 mlw-dp100-labs 的 Azure 机器学习工作区。
1. 选择 Azure 机器学习工作区，并在其“概述”页中选择“启动工作室” 。 将在浏览器中打开另一个选项卡以打开 Azure 机器学习工作室。
1. 关闭工作室中显示的任何弹出窗口。
1. 在 Azure 机器学习工作室中，导航到“计算”页，并验证在上一部分中创建的计算实例和群集是否存在。 计算实例应正在运行，群集应处于空闲状态，且运行中的节点数为 0。

## 使用 Python SDK 训练模型

验证已创建必要的计算后，可使用 Python SDK 运行训练脚本。 在计算实例上安装并使用 Python SDK，并在计算群集上训练机器学习模型。

1. 选择计算实例的“终端”应用程序以启动终端 。
1. 在终端中，通过在终端中运行以下命令，在计算实例上安装 Python SDK：

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > 忽略任何指示包未安装的（错误）消息。

1. 运行以下命令，将包含笔记本、数据和其他文件的 Git 存储库克隆到工作区：

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. 命令完成后，在“文件”窗格中，选择“&#8635;”刷新视图，并验证是否已创建新的 /users/your-user-name/azure-ml-labs 文件夹  。
1. 打开 Labs/02/Run training script.ipynb 笔记本。

    > 如果出现要求进行身份验证的通知，选择“验证”并按照必要的步骤进行操作。

1. 验证笔记本是否使用“Python 3.8 - AzureML”内核。 每个内核都有自己的映像，并预安装了自己的一组包。
1. 运行笔记本中的所有单元。

将在 Azure 机器学习工作区中创建一个新作业。 作业跟踪作业配置中定义的输入、使用的代码及用于评估模型的指标等输出。

## 在 Azure 机器学习工作室中查看作业历史记录

将作业提交到 Azure 机器学习工作区时，可在Azure 机器学习工作室查看其状态。

1. 选择笔记本中作为输出提供的作业 URL，或导航到 Azure 机器学习工作室中的“作业”页。
1. 列出了一个名为 diabetes-training 的新实验。 选择最新的作业 diabetes-pythonv2-train。
1. 查看作业的属性。 注意作业状态：
    - 已排队：作业正在等待计算变得可用。
    - 正在准备：计算群集正在调整大小或正在计算目标上安装环境。
    - 正在运行：正在执行训练脚本。
    - 正在完成：训练脚本已运行，正在使用所有最终信息更新作业。
    - 已完成：作业已成功完成并已终止。
    - 失败：作业失败且已终止。
1. 在“**输出 + 日志**”下，可以在“**user_logs/std_log.txt**”中找到脚本的输出。 脚本中 **print** 语句的输出将在此处显示。 如果由于脚本问题而出现错误，也可在此处找到错误消息。
1. 在“代码”下，可找到作业配置中指定的文件夹。 此文件夹包括训练脚本和数据集。

## 删除 Azure 资源

当你完成对 Azure 机器学习的探索时，应删除已创建的资源，以避免产生不必要的 Azure 成本。

1. 关闭“Azure 机器学习工作室”选项卡并返回到 Azure 门户。
1. 在 Azure 门户的**主页**上，选择“资源组”。
1. 选择“rg-dp100-labs”资源组。
1. 在资源组的“概述”页的顶部，选择“删除资源组”。 
1. 输入资源组名称以确认要删除该资源组，然后选择“删除”。
