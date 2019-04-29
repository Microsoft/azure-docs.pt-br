---
title: Acessar a API dos Serviços de Mídia do Azure – CLI do Azure | Microsoft Docs
description: Execute as etapas desta instrução para acessar a API dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 8374e4c49012a2c49de41001be0fdb30f9151332
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733497"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Acessar a API dos Serviços de Mídia do Azure com a CLI do Azure
 
Para usar a autenticação de entidade de serviço do AD do Azure para conectar-se à API de serviços de mídia do Azure, seu aplicativo precisa solicitar um token do AD do Azure que tem os seguintes parâmetros:

* Ponto de extremidade de locatário do Azure AD
* URI de recursos dos Serviços de Mídia
* URI de recurso dos Serviços de Mídia REST
* Valores do aplicativo do Azure AD: a ID do cliente e o segredo do cliente

Para obter explicações detalhadas, consulte [acessando os serviços de mídia v3 APIs](media-services-apis-overview.md#accessing-the-azure-media-services-api).

Este artigo mostra como usar a CLI do Azure para criar um aplicativo e uma entidade de serviço do Azure AD e obter os valores necessários para acessar os recursos dos Serviços de Mídia do Azure.

## <a name="prerequisites"></a>Pré-requisitos 

[Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).

Lembre-se dos valores que você usou para o nome do grupo de recursos e o nome da conta de Serviços de Mídia.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Consulte também

- [Dimensionar as unidades reservadas para mídia – CLI](media-reserved-units-cli-how-to.md)
- [Criar uma conta dos Serviços de Mídia – CLI](./scripts/cli-create-account.md) 
- [Redefinir as credenciais da conta – CLI](./scripts/cli-reset-account-credentials.md)
- [Criar ativos – CLI](./scripts/cli-create-asset.md)
- [Carregar um arquivo – CLI](./scripts/cli-upload-file-asset.md)
- [Criar transformações – CLI](./scripts/cli-create-transform.md)
- [Criar trabalhos – CLI](./scripts/cli-create-jobs.md)
- [Criar uma EventGrid – CLI](./scripts/cli-create-event-grid.md)
- [Publicar um ativo – CLI](./scripts/cli-publish-asset.md)
- [Filtro – CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="next-steps"></a>Próximas etapas

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
