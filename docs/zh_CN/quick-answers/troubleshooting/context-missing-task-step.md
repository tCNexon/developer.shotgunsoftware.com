---
layout: default
title: 为什么我的上下文缺少任务/工序，但它是文件名的一部分？
pagename: context-missing-task-step
lang: zh_CN
---

# 为什么我的上下文缺少任务/工序，但它是文件名的一部分？

通过 Toolkit 创建文件夹时，它会根据实体[注册路径](../administering/what-is-path-cache.md)，以便执行查找。这意味着如果指定了路径，就可以确定正确的上下文。Toolkit 将仅为从数据结构生成的文件夹创建注册表，因此不考虑仅在 `templates.yml` 文件中定义的文件名或文件夹等因素。如果您的数据结构中没有 `Task` 文件夹，则可能会遇到 Toolkit 需要了解文件的任务，但无法仅从路径完成任务的情况。

**示例**

以下面的默认数据结构为例，在文件夹创建过程中将注册 `Asset` 和 `Step` 文件夹：

![默认资产数据结构](./images/asset-schema.png)

如果使用模板生成如下所示的文件路径：

    assets/{sg_asset_type}/{Asset}/{Step}/work/maya/{task_name}_{name}.v{version}.{maya_extension}`

然后尝试从生成的路径确定上下文，这仅可以构建 `Asset` 和 `Step`，而不是**** `Task`，不论文件路径中的任务名称是什么。

**解决方案**

对于大多数工作流，数据结构中可以具有 `Step` 文件夹，也可以没有 `Task` 文件夹。通常，您会通过选择要处理的任务，然后选择文件，以使用 Workfiles 应用打开场景文件。然后使用您在 UI 中选择的任务来驱动上下文，而不是尝试从打开的文件的路径确定上下文。

但是，在某些情况下，能够从路径获得上下文这一点可能很重要，例如：

- 使用我们的自动上下文切换功能；该功能允许 Toolkit 检测您何时在软件的本地打开对话框中打开文件（而不是通过 Workfiles 应用）并相应地切换当前上下文。
- 在需要为给定文件确定上下文的独立过程中使用 API。

针对这些情况的解决方案是将 `Task` 文件夹引入到您的数据结构中，或者不使用自动上下文切换，对于 API 脚本，请确保您的过程已具有所需的上下文信息，而无需执行此查找操作。