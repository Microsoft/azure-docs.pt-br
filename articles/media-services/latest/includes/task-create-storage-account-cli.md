---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: a6349188a2c6b4da68009df93fbea5fa6eabacf1
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244715"
---
<!-- ### Create a storage account -->

Ao criar uma conta dos Serviços de Mídia, você precisa fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia. Para saber mais sobre como as contas de armazenamento são usadas nos Serviços de Mídia, confira [Contas de armazenamento](../storage-account-concept.md).

Você deve ter uma conta de armazenamento **principal** e pode ter qualquer número de contas de armazenamento **secundárias** associadas à sua conta de serviços de mídia. Os Serviços de Mídia oferecem suporte a contas **v2 para fins gerais** (GPv2) ou contas **v1 para fins gerais** (GPv1). As contas somente blob não são permitidas como **Primárias**. Se quiser saber mais sobre as contas de armazenamento, confira [Opções de conta de Armazenamento do Azure](../../../storage/common/storage-account-overview.md). 

Neste exemplo, criamos uma conta de uso geral v2, LRS Padrão. Caso deseje fazer experimentos com contas de armazenamento, use `--sku Standard_LRS`. No entanto, ao escolher um SKU para produção, você deverá considerar `--sku Standard_RAGRS`, que fornece replicação geográfica para a continuidade dos negócios. Para obter mais informações, confira [Contas de armazenamento](/cli/azure/storage/account).

O comando a seguir cria uma conta de armazenamento que será associada à conta dos Serviços de Mídia. No script a seguir, você pode substituir `storageaccountforams` pelo seu valor. `amsResourceGroup` deve corresponder ao valor que você deu para o grupo de recursos na etapa anterior. O nome da conta de armazenamento deve ter comprimento menor que 24.

```azurecli
az storage account create --name storageaccountforams --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```
