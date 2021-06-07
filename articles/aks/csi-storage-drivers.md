---
title: Habilitar drivers do CSI (interface de armazenamento de contêiner) no AKS (serviço kubernetes do Azure)
description: Saiba como habilitar os drivers da CSI (interface de armazenamento de contêiner) para discos do Azure e arquivos do Azure em um cluster do AKS (serviço de kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: fa40ab22f0c1ebf47bb490a50f782a848d1441e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182104"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>Habilitar drivers do CSI (interface de armazenamento de contêiner) para discos do Azure e arquivos do Azure no serviço kubernetes do Azure (AKS) (visualização)

A CSI (interface de armazenamento de contêiner) é um padrão para expor sistemas de blocos e de armazenamento de arquivos arbitrários para cargas de trabalho em contêineres no kubernetes. Ao adotar e usar o CSI, o AKS (serviço kubernetes do Azure) pode escrever, implantar e iterar plug-ins para expor novos ou aprimorar sistemas de armazenamento existentes no kubernetes sem precisar tocar no código de kubernetes principal e aguardar seus ciclos de liberação.

O suporte ao driver de armazenamento do CSI no AKS permite que você use nativamente:
- [*Discos do Azure*](azure-disk-csi.md), que podem ser usados para criar um recurso de dataKubernetes de *disco* . Os discos podem usar o armazenamento Premium do Azure, apoiado por SSDs de alto desempenho ou pelo armazenamento standard do Azure, apoiado por HDDs regulares ou SSDs padrão. Para a maioria das cargas de trabalho de desenvolvimento e produção, use o armazenamento Premium. Os discos do Azure são montados como *ReadWriteOnce*, portanto, estão disponíveis apenas para um único Pod. Para volumes de armazenamento que podem ser acessados por vários pods simultaneamente, use os arquivos do Azure.
- [*Arquivos do Azure*](azure-files-csi.md), que podem ser usados para montar um compartilhamento SMB 3,0 apoiado por uma conta de armazenamento do Azure para pods. Com os arquivos do Azure, você pode compartilhar dados em vários nós e pods. Os arquivos do Azure podem usar o armazenamento standard do Azure apoiado por HDDs regulares ou pelo armazenamento Premium do Azure apoiado por SSDs de alto desempenho.

> [!IMPORTANT]
> A partir do kubernetes versão 1,21, o kubernetes usará apenas os drivers do CSI e, por padrão. Esses drivers são o futuro do suporte de armazenamento no kubernetes.
>
> Os *drivers na árvore* referem-se aos drivers de armazenamento atuais que fazem parte do código principal do kubernetes versus os novos drivers do CSI, que são plug-ins.

## <a name="limitations"></a>Limitações

- Este recurso só pode ser definido no momento da criação do cluster.
- A versão secundária kubernetes mínima que dá suporte aos drivers do CSI é v 1.17.
- Durante a visualização, a classe de armazenamento padrão ainda será a [mesma classe de armazenamento na árvore](concepts-storage.md#storage-classes). Depois que esse recurso estiver disponível, a classe de armazenamento padrão será a `managed-csi` classe de armazenamento e as classes de armazenamento na árvore serão removidas.
- Durante a primeira fase de visualização, há suporte apenas para CLI do Azure.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>Registrar o `EnableAzureDiskFileCSIDriver` recurso de visualização

Para criar um cluster AKS que pode usar drivers do CSI para discos do Azure e arquivos do Azure, você deve habilitar o `EnableAzureDiskFileCSIDriver` sinalizador de recurso em sua assinatura.

Registre o `EnableAzureDiskFileCSIDriver` sinalizador de recurso usando o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

Demora alguns minutos para o status exibir *Registrado*. Verifique o status do registro usando o comando [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para criar um cluster AKS ou um pool de nós que possa usar os drivers de armazenamento do CSI, você precisará da extensão de CLI do Azure de versão mais recente do *AKs-Preview* . Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] . Ou instale todas as atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Criar um novo cluster que pode usar drivers de armazenamento do CSI

Crie um novo cluster que possa usar drivers de armazenamento do CSI para discos do Azure e arquivos do Azure usando os comandos da CLI a seguir. Use o `--aks-custom-headers` sinalizador para definir o `EnableAzureDiskFileCSIDriver` recurso.

Criar um grupo de recursos do Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Crie o cluster AKS com suporte para drivers de armazenamento do CSI:

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure -k 1.17.9 --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Se você quiser criar clusters em drivers de armazenamento em árvore em vez de drivers de armazenamento do CSI, poderá fazer isso omitindo o `--aks-custom-headers` parâmetro personalizado.


Verifique Quantos volumes baseados em disco do Azure você pode anexar a este nó executando:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Próximas etapas

- Para usar a unidade CSI para discos do Azure, confira [usar discos do Azure com drivers do CSI](azure-disk-csi.md).
- Para usar a unidade CSI para arquivos do Azure, confira [usar os arquivos do Azure com drivers do CSI](azure-files-csi.md).
- Para obter mais informações sobre as práticas recomendadas de armazenamento, consulte [práticas recomendadas para armazenamento e backups no serviço kubernetes do Azure][operator-best-practices-storage].

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

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