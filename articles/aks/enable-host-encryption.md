---
title: Habilitar a criptografia baseada em host no serviço kubernetes do Azure (AKS)
description: Saiba como configurar uma criptografia baseada em host em um cluster do AKS (serviço kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 01/27/2021
ms.openlocfilehash: ac28c698a766f1f3febaff582038906f658d58dd
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071843"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Criptografia baseada em host no serviço kubernetes do Azure (AKS) (visualização)

Com a criptografia baseada em host, os dados armazenados no host da VM de suas VMs dos nós do agente AKS são criptografados em repouso e fluem criptografados para o serviço de armazenamento. Isso significa que os discos temporários são criptografados em repouso com chaves gerenciadas pela plataforma. O cache do sistema operacional e dos discos de dados é criptografado em repouso com chaves gerenciadas por plataforma ou chaves gerenciadas pelo cliente, dependendo do tipo de criptografia definido nesses discos. Por padrão, ao usar AKS, o sistema operacional e OS discos de dados são criptografados em repouso com chaves gerenciadas pela plataforma, o que significa que os caches desses discos também são criptografados por padrão em repouso com chaves gerenciadas pela plataforma.  Você pode especificar suas próprias chaves gerenciadas seguindo [BYOK (Traga suas próprias chaves) com discos do Azure no serviço kubernetes do Azure](azure-disk-customer-managed-keys.md). O cache desses discos também será criptografado usando a chave que você especificar nesta etapa.


## <a name="before-you-begin"></a>Antes de começar

Esse recurso só pode ser definido na criação do cluster ou no momento da criação do pool de nós.

> [!NOTE]
> A criptografia baseada em host está disponível em [regiões do Azure][supported-regions] que dão suporte à criptografia do lado do servidor de Azure Managed disks e somente com [tamanhos de VM com suporte][supported-sizes]específicos.

### <a name="prerequisites"></a>Pré-requisitos

- Verifique se você tem a `aks-preview` extensão da CLI v 0.4.73 ou versão superior instalada.

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para criar um cluster AKS que tenha criptografia baseada em host, você precisa da extensão mais recente da CLI do *AKs-Preview* . Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] ou verifique se há atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limitações

- Só pode ser habilitado em novos pools de nós ou em novos clusters.
- Só pode ser habilitado em [regiões do Azure][supported-regions] que dão suporte à criptografia do lado do servidor de Azure Managed disks e somente com [tamanhos de VM com suporte][supported-sizes]específicos.
- Requer um cluster AKS e um pool de nós baseados em VMSS (conjuntos de dimensionamento de máquinas virtuais) como *tipo de conjunto de VMs*.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Usar a criptografia baseada em host em novos clusters (visualização)

Configure os nós do agente de cluster para usar a criptografia baseada em host quando o cluster for criado. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Se você quiser criar clusters sem criptografia baseada em host, poderá fazer isso omitindo o `--enable-encryption-at-host` parâmetro.

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Usar criptografia baseada em host em clusters existentes (visualização)

Você pode habilitar a criptografia baseada em host em clusters existentes adicionando um novo pool de nós ao cluster. Configure um novo pool de nós para usar a criptografia baseada em host usando o `--enable-encryption-at-host` parâmetro.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Se você quiser criar novos pools de nós sem o recurso de criptografia baseado em host, poderá fazer isso omitindo o `--enable-encryption-at-host` parâmetro.

## <a name="next-steps"></a>Próximas etapas

Examine [as práticas recomendadas para segurança de cluster AKs][best-practices-security] Leia mais sobre [criptografia baseada em host](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
