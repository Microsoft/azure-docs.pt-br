---
title: Conectar-se à API dos serviços de mídia do Azure v3-Python
description: Este artigo demonstra como se conectar à API dos serviços de mídia v3 com Python.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-python
ms.openlocfilehash: de78008a4645690cfc900f77670204bb892daf51
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105042961"
---
# <a name="connect-to-media-services-v3-api---python"></a>Conectar-se à API dos serviços de mídia v3-Python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra como se conectar ao SDK do Python do Azure Media Services V3 usando o método de entrada da entidade de serviço.

## <a name="prerequisites"></a>Pré-requisitos

- Baixar o Python do [Python.org](https://www.python.org/downloads/)
- Certifique-se de definir a `PATH` variável de ambiente
- [Crie uma conta de Serviços de Mídia](./create-account-howto.md). Lembre-se do nome do grupo de recursos e da conta dos Serviços de Mídia.
- Siga as etapas no tópico [APIs de acesso](./access-api-howto.md) , selecionando o método de autenticação da entidade de serviço. Registre a ID da assinatura ( `SubscriptionId` ), a ID do cliente do aplicativo ( `AadClientId` ), a chave de autenticação ( `AadSecret` ) e a ID do locatário ( `AadTenantId` ) que você precisa em etapas posteriores.

> [!IMPORTANT]
> Examine [Convenções de nomenclatura](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com os serviços de mídia do Azure usando o Python, você precisa instalar esses módulos.

* O `azure-mgmt-resource` módulo, que inclui módulos do Azure para Active Directory.
* O `azure-mgmt-media` módulo, que inclui as entidades dos serviços de mídia.

    Certifique-se de obter [a versão mais recente do SDK dos serviços de mídia para Python](https://pypi.org/project/azure-mgmt-media/).

Abra uma ferramenta de linha de comando e use os comandos a seguir para instalar os módulos.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==3.0.0
```

## <a name="connect-to-the-python-client"></a>Conectar-se ao cliente Python

1. Criar um arquivo com uma `.py` extensão
1. Abra o arquivo em seu editor favorito
1. Adicione o código a seguir ao arquivo. O código importa os módulos necessários e cria o objeto de credenciais de Active Directory que você precisa para se conectar aos serviços de mídia.

      Defina os valores das variáveis para os valores obtidos das [APIs de acesso](./access-api-howto.md). Atualize as `ACCOUNT_NAME` `RESOUCE_GROUP_NAME` variáveis e para o nome da conta dos serviços de mídia e os nomes do grupo de recursos usados ao criar esses recursos.

      ```
      import adal
      from azure.mgmt.media import AzureMediaServices
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD

      RESOURCE = "https://management.core.windows.net/"
      # Tenant ID for your Azure Subscription (AadTenantId)
      TENANT_ID = "00000000-0000-0000-000000000000"
      # Your Service Principal App ID (AadClientId)
      CLIENT = "00000000-0000-0000-000000000000"
      # Your Service Principal Password (AadSecret)
      KEY = "00000000-0000-0000-000000000000"
      # Your Azure Subscription ID (SubscriptionId)
      SUBSCRIPTION_ID = "00000000-0000-0000-000000000000"
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = "amsaccount"
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = "amsResourceGroup"

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + "/" + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials, RESOURCE, CLIENT, KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print(client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Executar o arquivo

## <a name="next-steps"></a>Próximas etapas

- Use o [SDK do Python](https://aka.ms/ams-v3-python-sdk).
- Examine a documentação de [referência do Python](/python/api/overview/azure/mediaservices/management) dos Serviços de Mídia.
