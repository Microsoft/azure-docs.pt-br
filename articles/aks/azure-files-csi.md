---
title: Usar drivers do CSI (interface de armazenamento de contêiner) para arquivos do Azure no serviço kubernetes do Azure (AKS)
description: Saiba como usar os drivers da CSI (interface de armazenamento de contêiner) para arquivos do Azure em um cluster do AKS (serviço de kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 93f7f7a3c59beca362145ac16f7cf727df773f81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174054"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Usar drivers da interface de armazenamento de contêiner de arquivos do Azure (CSI) no serviço kubernetes do Azure (AKS) (visualização)

O driver CSI (interface de armazenamento de contêiner de arquivos do Azure) é um driver compatível com a [especificação CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md)usado pelo serviço de kubernetes do Azure (AKs) para gerenciar o ciclo de vida dos compartilhamentos de arquivos do Azure.

O CSI é um padrão para expor sistemas de blocos e de armazenamento de arquivos arbitrários a cargas de trabalho em contêineres no kubernetes. Ao adotar e usar o CSI, o AKS agora pode escrever, implantar e iterar plug-ins para expor novos ou aprimorar sistemas de armazenamento existentes no kubernetes sem precisar tocar no código de kubernetes principal e aguardar seus ciclos de liberação.

Para criar um cluster AKS com suporte ao driver do CSI, consulte [habilitar drivers do CSI para discos do Azure e arquivos do Azure no AKs](csi-storage-drivers.md).

>[!NOTE]
> Os *drivers na árvore* referem-se aos drivers de armazenamento atuais que fazem parte do código principal do kubernetes versus os novos drivers do CSI, que são plug-ins.

## <a name="use-a-persistent-volume-with-azure-files"></a>Usar um volume persistente com arquivos do Azure

Um [volume persistente (VP)](concepts-storage.md#persistent-volumes) representa um pedaço de armazenamento provisionado para uso com kubernetes pods. Um VP pode ser usado por um ou vários pods e pode ser provisionado de forma dinâmica ou estática. Se vários pods precisarem de acesso simultâneo ao mesmo volume de armazenamento, você poderá usar os arquivos do Azure para se conectar usando o [protocolo SMB][smb-overview]. Este artigo mostra como criar dinamicamente um compartilhamento de arquivos do Azure para uso por vários pods em um cluster AKS. Para provisionamento estático, consulte [criar e usar manualmente um volume com um compartilhamento de arquivos do Azure](azure-files-volume.md).

Para obter mais informações sobre o Kubernetes, veja [Opções de armazenamento para aplicativos no AKS][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>Criar dinamicamente arquivos do Azure PVs usando as classes de armazenamento internas

Uma classe de armazenamento é usada para definir como um compartilhamento de arquivos do Azure é criado. Uma conta de armazenamento é criada automaticamente no [grupo de recursos do nó][node-resource-group] para uso com a classe de armazenamento para manter os compartilhamentos dos arquivos do Azure. Escolha uma das seguintes [SKUs de redundância de armazenamento do Azure][storage-skus] para *skuName*:

* **Standard_LRS**: armazenamento com redundância local padrão
* **Standard_GRS**: armazenamento com redundância geográfica padrão
* **Standard_ZRS**: armazenamento com redundância de zona padrão
* **Standard_RAGRS**: armazenamento com redundância geográfica com acesso de leitura padrão
* **Premium_LRS**: armazenamento com redundância local Premium

> [!NOTE]
> Os arquivos do Azure dão suporte ao armazenamento Premium do Azure. O compartilhamento de arquivos Premium mínimo é de 100 GB.

Quando você usa os drivers de armazenamento do CSI em AKS, há dois outros internos `StorageClasses` que usam os drivers de armazenamento dos arquivos do Azure CSI. As classes adicionais de armazenamento do CSI são criadas com o cluster juntamente com as classes de armazenamento padrão na árvore.

- `azurefile-csi`: Usa o armazenamento standard do Azure para criar um compartilhamento de arquivos do Azure.
- `azurefile-csi-premium`: Usa o armazenamento Premium do Azure para criar um compartilhamento de arquivos do Azure.

A política de recuperação em ambas as classes de armazenamento garante que o compartilhamento de arquivos do Azure subjacente seja excluído quando o respectivo PV for excluído. As classes de armazenamento também configuram os compartilhamentos de arquivos a serem expansíveis, basta editar o PVC (declaração de volume persistente) com o novo tamanho.

Para usar essas classes de armazenamento, crie um [PVC](concepts-storage.md#persistent-volume-claims) e o respectivo Pod que os referencie e os utilize. Um PVC é usado para provisionar automaticamente o armazenamento com base em uma classe de armazenamento. Um PVC pode usar uma das classes de armazenamento criadas previamente ou uma classe de armazenamento definida pelo usuário para criar um compartilhamento de arquivos do Azure para o SKU e o tamanho desejados. Quando você cria uma definição de Pod, o PVC é especificado para solicitar o armazenamento desejado.

Crie um [PVC de exemplo e um pod que imprime a data `outfile` atual em um](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) com o comando [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Depois que o Pod estiver no estado executando, você poderá validar se o compartilhamento de arquivos está montado corretamente executando o comando a seguir e verificando se a saída contém `outfile` :

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Criar uma classe de armazenamento personalizada

As classes de armazenamento padrão se adaptam aos cenários mais comuns, mas não todas. Para alguns casos, talvez você queira ter sua própria classe de armazenamento personalizada com seus próprios parâmetros. Por exemplo, use o manifesto a seguir para configurar o `mountOptions` do compartilhamento de arquivos.

O valor padrão de *fileMode* e *dirMode* é *0777* para compartilhamentos de arquivos montados kubernetes. Você pode especificar as opções de montagem diferentes no objeto de classe de armazenamento.

Crie um arquivo chamado `azure-file-sc.yaml` e cole o seguinte exemplo de manifesto:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

Crie a classe de armazenamento com o comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

O driver CSI dos arquivos do Azure dá suporte à criação [de instantâneos de volumes persistentes](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) e aos compartilhamentos de arquivos subjacentes.

Crie uma [classe de instantâneo de volume](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) com o comando [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Crie um [instantâneo de volume](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) do PVC [que criamos dinamicamente no início deste tutorial](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes), `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Verifique se o instantâneo foi criado corretamente:

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume"></a>Redimensionar um volume persistente

Você pode solicitar um volume maior para um PVC. Edite o objeto PVC e especifique um tamanho maior. Essa alteração dispara a expansão do volume subjacente que faz o backup do VP.

> [!NOTE]
> Um novo VP nunca é criado para atender à declaração. Em vez disso, um volume existente é redimensionado.

No AKS, a `azurefile-csi` classe de armazenamento interna já dá suporte à expansão, portanto, use o [PVC criado anteriormente com essa classe de armazenamento](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes). O PVC solicitou um compartilhamento de arquivos do 100Gi. Podemos confirmar que, executando:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Expanda o PVC aumentando o `spec.resources.requests.storage` campo:

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Verifique se o PVC e o sistema de arquivos dentro do pod mostram o novo tamanho:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>Compartilhamentos de arquivos NFS
[Os arquivos do Azure agora têm suporte para o protocolo NFS v 4.1](../storage/files/storage-files-how-to-create-nfs-shares.md). O suporte a NFS 4,1 para arquivos do Azure fornece um sistema de arquivos NFS totalmente gerenciado como um serviço criado em uma plataforma de armazenamento resiliente distribuída altamente disponível e altamente durável.

 Essa opção é otimizada para cargas de trabalho de acesso aleatório com atualizações de dados in-loco e fornece suporte completo ao sistema de arquivos POSIX. Esta seção mostra como usar compartilhamentos NFS com o driver do Azure File CSI em um cluster AKS.

Certifique-se de verificar as [limitações](../storage/files/storage-files-compare-protocols.md#limitations) e a [disponibilidade da região](../storage/files/storage-files-compare-protocols.md#regional-availability) durante a fase de visualização.

### <a name="register-the-allownfsfileshares-preview-feature"></a>Registrar o `AllowNfsFileShares` recurso de visualização

Para criar um compartilhamento de arquivos que aproveita o NFS 4,1, você deve habilitar o `AllowNfsFileShares` sinalizador de recurso em sua assinatura.

Registre o `AllowNfsFileShares` sinalizador de recurso usando o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

Demora alguns minutos para o status exibir *Registrado*. Verifique o status do registro usando o comando [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft. Storage* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>Criar uma conta de armazenamento para o compartilhamento de arquivos NFS

[Criar um `Premium_LRS` Conta de armazenamento do Azure](../storage/files/storage-how-to-create-file-share.md) com as seguintes configurações para dar suporte a compartilhamentos NFS:
- tipo de conta: FileStorage
- transferência segura necessária (somente habilitar tráfego HTTPS): false
- Selecione a rede virtual de seus nós de agente em firewalls e redes virtuais-portanto, talvez você prefira criar a conta de armazenamento no grupo de recursos MC_.

### <a name="create-nfs-file-share-storage-class"></a>Criar classe de armazenamento de compartilhamento de arquivos NFS

Salve um `nfs-sc.yaml` arquivo com o manifesto abaixo editando os respectivos espaços reservados.

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile-csi-nfs
provisioner: file.csi.azure.com
parameters:
  resourceGroup: EXISTING_RESOURCE_GROUP_NAME  # optional, required only when storage account is not in the same resource group as your agent nodes
  storageAccount: EXISTING_STORAGE_ACCOUNT_NAME
  protocol: nfs
```

Depois de editar e salvar o arquivo, crie a classe de armazenamento com o comando [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>Criar uma implantação com um compartilhamento de arquivos com suporte NFS
Você pode implantar um [conjunto com estado](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) de exemplo que economiza carimbos de data/hora em um arquivo `data.txt` implantando o comando a seguir com o comando [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

Valide o conteúdo do volume executando:

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

>[!NOTE]
> Observe que, como o compartilhamento de arquivos NFS está em conta Premium, o tamanho mínimo do compartilhamento de arquivos é de 100 GB. Se você criar um PVC com um tamanho de armazenamento pequeno, poderá encontrar um erro "falha ao criar o compartilhamento de arquivos... tamanho (5)... ".


## <a name="windows-containers"></a>Contêineres do Windows

O driver CSI dos arquivos do Azure também dá suporte a nós e contêineres do Windows. Se você quiser usar contêineres do Windows, siga o [tutorial contêineres do Windows](windows-container-cli.md) para adicionar um pool de nós do Windows.

Depois de ter um pool de nós do Windows, use as classes de armazenamento internas como `azurefile-csi` ou crie aquelas personalizadas. Você pode implantar um [conjunto com estado baseado no Windows](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) de exemplo que economiza carimbos de data/hora em um arquivo `data.txt` implantando o comando a seguir com o comando [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

Valide o conteúdo do volume executando:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Próximas etapas

- Para saber como usar os drivers do CSI para discos do Azure, confira [usar discos do Azure com drivers do CSI](azure-disk-csi.md).
- Para obter mais informações sobre as práticas recomendadas de armazenamento, consulte [práticas recomendadas para armazenamento e backups no serviço kubernetes do Azure][operator-best-practices-storage].


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md