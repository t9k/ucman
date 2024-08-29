# 管理 PVC

本教程演示创建、扩容和删除 [PVC](../../api/storage/pvc.md)。

## 准备工作

* 了解 PVC 的<a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/zh/docs/concepts/storage/persistent-volumes/">概念</a>和 <a target="_blank" rel="noopener noreferrer" href="https://kubernetes.io/docs/reference/kubernetes-api/config-and-storage-resources/persistent-volume-claim-v1/">Kubernetes API</a>。

## 创建 PVC

在左侧导航菜单点击**存储 > 存储卷**进入 PVC 管理页面，这里展示了所有存在的 PVC。点击右上角的**创建 PVC**进入创建页面：

<figure class="screenshot">
  <img alt="create" src="../../assets/guide/manage-storage-network-and-auxiliary/pvc/create.png" />
</figure>

在 PVC 创建页面，填写名称和存储卷大小，选择存储卷访问模式和存储类，然后点击**创建**。

<figure class="screenshot">
  <img alt="create-details" src="../../assets/guide/manage-storage-network-and-auxiliary/pvc/create-details.png" />
</figure>

<aside class="note tip">
<div class="title">提示</div>

你可以点击左上角的**导入 PersistentVolumeClaim** 以加载当前存在的某个 PVC 的配置。

</aside>

## 扩容 PVC

在 PVC 管理页面，点击 PVC 右侧的 <span class="twemoji"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M12 16a2 2 0 0 1 2 2 2 2 0 0 1-2 2 2 2 0 0 1-2-2 2 2 0 0 1 2-2m0-6a2 2 0 0 1 2 2 2 2 0 0 1-2 2 2 2 0 0 1-2-2 2 2 0 0 1 2-2m0-6a2 2 0 0 1 2 2 2 2 0 0 1-2 2 2 2 0 0 1-2-2 2 2 0 0 1 2-2Z"></path></svg></span> **> 扩容**：

<figure class="screenshot">
  <img alt="create" src="../../assets/guide/manage-storage-network-and-auxiliary/pvc/extend.png" />
</figure>

填写新的存储大小（必须大于原大小），点击**确定**以扩容 PVC：

<figure class="screenshot">
  <img alt="create" src="../../assets/guide/manage-storage-network-and-auxiliary/pvc/extend-details.png" />
</figure>

<aside class="note">
<div class="title">注意</div>

部分存储类型不支持扩容。

</aside>

## 删除 PVC

在 PVC 管理页面，点击 PVC 右侧的 <span class="twemoji"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M12 16a2 2 0 0 1 2 2 2 2 0 0 1-2 2 2 2 0 0 1-2-2 2 2 0 0 1 2-2m0-6a2 2 0 0 1 2 2 2 2 0 0 1-2 2 2 2 0 0 1-2-2 2 2 0 0 1 2-2m0-6a2 2 0 0 1 2 2 2 2 0 0 1-2 2 2 2 0 0 1-2-2 2 2 0 0 1 2-2Z"></path></svg></span> **> 删除**，确认以删除 PVC：

<figure class="screenshot">
    <img alt="delete" src="../../assets/guide/manage-storage-network-and-auxiliary/pvc/delete.png" />
</figure>

<aside class="note warning">
<div class="title">警告</div>

删除 PVC 后，PVC 中的文件会全部丢失，无法找回。请确认你不再需要这个 PVC 中的任何文件后再删除 PVC。

</aside>

<aside class="note">
<div class="title">注意</div>

正在被挂载到一个或多个 Pod 上的 PVC 不能被删除。如果你试图删除这样的 PVC，它会进入 Terminating 阶段，继续存在但不能再被挂载；一旦挂载它的所有 Pod 都被删除，它也会被随之删除。

</aside>
