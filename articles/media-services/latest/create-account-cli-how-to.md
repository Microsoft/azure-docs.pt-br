---
title: Criar uma conta dos Serviços de Mídia com a CLI do Azure - Azure | Microsoft Docs
description: Execute as etapas neste início rápido para criar uma conta dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 02/15/2019
ms.author: juliako
ms.openlocfilehash: f4ce64599aad2b2eebbef6ca8d81acfca2a7a702
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733871"
---
# <a name="create-an-azure-media-services-account"></a>Criar uma conta de Serviços de Mídia do Azure

Para iniciar a criptografia, codificação, analise, gerenciamento e streaming de conteúdo de mídia no Azure, você precisa criar uma conta dos Serviços de Mídia. A conta dos Serviços de Mídia precisa estar associada a uma ou mais contas de armazenamento.

> [!NOTE]
> A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure. É altamente recomendável usar contas de armazenamento no mesmo local da conta de Serviços de Mídia para evitar custos de saída de dados e latência adicionais.

Este artigo descreve as etapas para criar uma nova conta dos Serviços de Mídia do Azure usando a CLI do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="set-the-azure-subscription"></a>Definir a assinatura do Azure

No comando a seguir, forneça a ID de assinatura do Azure que você deseja usar para a conta de Serviços de Mídia. Veja uma lista das assinaturas as quais você tem acesso navegando até [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Próximas etapas

[Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Consulte também

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

