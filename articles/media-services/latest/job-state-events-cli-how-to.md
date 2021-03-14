---
title: Monitorar eventos dos serviços de mídia do Azure com a grade de eventos
description: Este artigo mostra como assinar a grade de eventos para monitorar os eventos dos serviços de mídia do Azure usando CLI do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: e01083b8618e4751770ed69aa4b1c20f8a072d88
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092324"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Criar e monitorar eventos dos Serviços de Mídia com a Grade de Eventos usando a CLI do Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Grade de Eventos do Azure é um serviço de eventos para a nuvem. Esse serviço usa [assinaturas de evento](../../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para assinantes. Os eventos dos Serviços de Mídia contêm todas as informações que você precisa para responder às alterações em seus dados. Você pode identificar um evento de Serviços de Mídia porque a propriedade eventType começa com "Microsoft.Media". Para obter mais informações, confira [Esquemas de eventos dos Serviços de Mídia](media-services-event-schemas.md).

Neste artigo, você usará a CLI do Azure para assinar eventos da sua conta de Serviços de Mídia do Azure. Em seguida, você aciona eventos para exibir o resultado. Normalmente, você envia eventos para um ponto de extremidade que processa os dados de evento e realiza ações. Neste artigo, você enviará eventos para um aplicativo Web que coleta e exibe as mensagens.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- Instalar e usar a CLI localmente, este artigo requer a versão 2.0 ou posterior da CLI do Azure. Execute `az --version` descobrir a versão que você tem. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

    Atualmente, nem todos os comandos da [CLI V3 dos Serviços de Mídia](/cli/azure/ams) funcionam no Azure Cloud Shell. É recomendável usar a CLI localmente.

- [Crie uma conta de Serviços de Mídia](./create-account-howto.md).

    Lembre-se dos valores que você usou para o nome do grupo de recursos e o nome da conta de Serviços de Mídia.

## <a name="create-a-message-endpoint"></a>Criar um ponto de extremidade de mensagem

Antes de assinar os eventos da conta dos Serviços de Mídia, vamos criar o terminal para a mensagem do evento. Normalmente, o ponto de extremidade executa ações com base nos dados de evento. Neste artigo, você implanta uma [aplicativo web criado previamente](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens de evento. A solução implantada inclui um plano do Serviço de Aplicativo, um aplicativo Web do Aplicativo do Serviço de e o código-fonte do GitHub.

1. Selecione **Implantar no Azure** para implantar a solução na sua assinatura. No portal do Azure, forneça os valores para os parâmetros.

   [![Imagem mostrando um botão rotulado como "Implantar no Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. A implantação pode levar alguns minutos para ser concluída. Depois que a implantação for bem-sucedida, exiba seu aplicativo Web para garantir que ele esteja em execução. Em um navegador da Web, navegue até: `https://<your-site-name>.azurewebsites.net`

Se você alternar para o site "Visualizador de grade de eventos do Azure", verá que ele ainda não há eventos.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Definir a assinatura do Azure

No comando a seguir, forneça a ID de assinatura do Azure que você deseja usar para a conta de Serviços de Mídia. Veja uma lista das assinaturas as quais você tem acesso navegando até [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Assinar eventos de serviços de mídia

Você assina um artigo para informar à grade de eventos quais eventos você deseja rastrear. O exemplo a seguir assina a conta dos serviços de mídia que você criou e passa a URL do site que você criou como o ponto de extremidade para notificação de eventos. 

Substitua `<event_subscription_name>` por um nome exclusivo para a assinatura de evento. Para `<resource_group_name>` e `<ams_account_name>`, use os valores que você escolheu ao criar a conta de Serviços de Mídia. Para o `<endpoint_URL>`, forneça a URL do seu aplicativo Web e adicione `api/updates` à URL da página inicial. Ao especificar um ponto de extremidade durante a assinatura, a Grade de Eventos manipula o roteamento dos eventos para esse ponto de extremidade. 

1. Obter a ID do recurso

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Por exemplo:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Assinar os eventos

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Por exemplo:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Você poderá receber um aviso de validação de handshake. Aguarde alguns minutos e o handshake será validado.

Agora, vamos acionar eventos para ver como o Event Grid distribui a mensagem para o seu endpoint.

## <a name="send-an-event-to-your-endpoint"></a>Enviar um evento para o ponto de extremidade

Você pode acionar eventos para a conta do Media Services executando uma tarefa de codificação. Você pode seguir [este guia de início rápido](stream-files-dotnet-quickstart.md) para codificar um arquivo e iniciar o envio de eventos. 

Exiba novamente o seu aplicativo Web e observe que um evento de validação de assinatura foi enviado a ele. A Grade de Eventos envia o evento de validação de modo que o ponto de extremidade possa verificar se ele deseja receber os dados de evento. O ponto de extremidade deve definir `validationResponse` para `validationCode`. Para saber mais, confira [Event Grid security and authentication](../../event-grid/security-authentication.md) (Segurança e autenticação da Grade de Eventos). Você pode visualizar o código do aplicativo da Web para ver como ele valida a assinatura.

> [!TIP]
> Selecione o ícone de olho para expandir os dados de evento. Não atualize a página, se você quiser ver todos os eventos.

![Exibição do evento de assinatura](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Próximas etapas

[Carregar, codificar e transmitir](stream-files-tutorial-with-api.md)