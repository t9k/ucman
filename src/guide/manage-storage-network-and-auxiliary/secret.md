# 管理 Secret

本教程演示创建、查看、修改和删除 [Secret](../../api/auxiliary/secret.md)。

## 准备工作

* 了解 Secret 的<a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/zh/docs/concepts/configuration/secret/">概念</a>和 <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/reference/kubernetes-api/config-and-storage-resources/secret-v1/">Kubernetes API</a>。

## 创建 Secret

在左侧导航菜单点击**辅助 > Secret** 进入 Secret 管理页面，这里展示了所有存在的 Secret。点击右上角的**创建**进入创建页面：

<figure class="screenshot">
  <img alt="create" src="../../assets/guide/manage-storage-network-and-auxiliary/secret/create.png" />
</figure>

在 Secret 创建页面，填写名称和要存储的数据，然后点击**创建**：

<figure class="screenshot">
  <img alt="create-details" src="../../assets/guide/manage-storage-network-and-auxiliary/secret/create-details.png" />
</figure>

<aside class="note info">
<div class="title">Secret 模板</div>

Secret 创建页面提供多种模板，分别适用于存储不同类型的敏感数据，请根据 UI 和文本提示提供相应的数据。其中自定义（Custom）模板是一个通用的模板，其支持扩展以适应各种需求。

Secret 存储的数据类型是通过其标签进行识别的。

</aside>

<aside class="note tip">
<div class="title">提示</div>

你可以点击左上角的**导入 Secret** 以加载当前存在的某个 Secret 的配置。

</aside>

## 查看和修改 Secret 数据

在 Secret 管理页面，点击 Secret 右侧的 <span class="twemoji"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M12 16a2 2 0 0 1 2 2 2 2 0 0 1-2 2 2 2 0 0 1-2-2 2 2 0 0 1 2-2m0-6a2 2 0 0 1 2 2 2 2 0 0 1-2 2 2 2 0 0 1-2-2 2 2 0 0 1 2-2m0-6a2 2 0 0 1 2 2 2 2 0 0 1-2 2 2 2 0 0 1-2-2 2 2 0 0 1 2-2Z"></path></svg></span> **> 编辑**进入编辑页面：

<figure class="screenshot">
  <img alt="edit" src="../../assets/guide/manage-storage-network-and-auxiliary/secret/edit.png" />
</figure>

在 Secret 编辑页面可以查看和修改数据（但是不能重命名 Secret），修改完成后点击**编辑**以保存：

<figure class="screenshot">
  <img alt="edit-details" src="../../assets/guide/manage-storage-network-and-auxiliary/secret/edit-details.png" />
</figure>

## 删除 Secret

在 Secret 管理页面，点击 Secret 右侧的 <span class="twemoji"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M12 16a2 2 0 0 1 2 2 2 2 0 0 1-2 2 2 2 0 0 1-2-2 2 2 0 0 1 2-2m0-6a2 2 0 0 1 2 2 2 2 0 0 1-2 2 2 2 0 0 1-2-2 2 2 0 0 1 2-2m0-6a2 2 0 0 1 2 2 2 2 0 0 1-2 2 2 2 0 0 1-2-2 2 2 0 0 1 2-2Z"></path></svg></span> **> 删除**，确认以删除 Secret：

<figure class="screenshot">
    <img alt="delete" src="../../assets/guide/manage-storage-network-and-auxiliary/secret/delete.png" />
</figure>

<aside class="note tip">
<div class="title">提示</div>

许多 Apps 和 APIs 会自动创建所需的 Secret，例如上图中的 Secret sh.helm.release.v1.app-service-manager-dab483-6d.v1	 和 app-service-manager-1db583-6d 就是由 Service Manager App 自动创建。这种 Secret 的生命周期由创建它的 App 或 API 管理，也就是说，删除创建它的 App 或 API，它也会被随之删除，因此你无需管理这种 Secret。

</aside>
