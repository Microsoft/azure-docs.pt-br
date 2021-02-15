---
title: Monitorar eventos dos serviços de mídia com o portal da grade de eventos
description: Este artigo mostra como se inscrever no Event Grid para monitorar eventos do Azure Media Services.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: serviços de mídia do azure, transmissão, difusão, ao vivo, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 9f1a74ee1668594d2e28bed0ad36793bf47653fe
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955251"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Criar e monitorar eventos do Media Services com o Event Grid usando o portal do Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Grade de Eventos do Azure é um serviço de eventos para a nuvem. Esse serviço usa [assinaturas de evento](../../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para assinantes. Os eventos dos Serviços de Mídia contêm todas as informações que você precisa para responder às alterações em seus dados. Você pode identificar um evento de Serviços de Mídia porque a propriedade eventType começa com "Microsoft.Media". Para obter mais informações, confira [Esquemas de eventos dos Serviços de Mídia](media-services-event-schemas.md).

Neste artigo, você usa o portal do Azure para assinar eventos da sua conta dos Serviços de Mídia do Azure. Em seguida, você aciona eventos para exibir o resultado. Normalmente, você envia eventos para um ponto de extremidade que processa os dados de evento e realiza ações. No artigo, enviamos eventos para um aplicativo Web que coleta e exibe as mensagens.

Ao concluir, você verá que os dados do evento foram enviados para um aplicativo Web.

## <a name="prerequisites"></a>Pré-requisitos 

* Ter uma assinatura ativa do Azure.
* Crie uma nova conta dos Serviços de Mídia do Azure, conforme descrito [neste início rápido](./create-account-howto.md).

## <a name="create-a-message-endpoint"></a>Criar um ponto de extremidade de mensagem

Antes de assinar os eventos da conta dos Serviços de Mídia, vamos criar o terminal para a mensagem do evento. Normalmente, o ponto de extremidade executa ações com base nos dados de evento. Neste artigo, você implanta uma [aplicativo web criado previamente](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens de evento. A solução implantada inclui um plano do Serviço de Aplicativo, um aplicativo Web do Aplicativo do Serviço de e o código-fonte do GitHub.

1. Selecione **Implantar no Azure** para implantar a solução na sua assinatura. No portal do Azure, forneça os valores para os parâmetros.

   [![Imagem mostrando um botão rotulado como "Implantar no Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. A implantação pode levar alguns minutos para ser concluída. Depois que a implantação for bem-sucedida, exiba seu aplicativo Web para garantir que ele esteja em execução. Em um navegador da Web, navegue até: `https://<your-site-name>.azurewebsites.net`

Se você alternar para o site "Visualizador de grade de eventos do Azure", verá que ele ainda não há eventos.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Assinar eventos de serviços de mídia

Assine um tópico para indicar à Grade de Eventos quais eventos você deseja acompanhar e para onde enviar os eventos.

1. No portal, selecione sua conta de Serviços de Mídia e selecione **Eventos**.
1. Para enviar eventos para o seu aplicativo visualizador, use um web hook para o ponto de extremidade. 

   ![Selecionar web hook](./media/monitor-events-portal/select-web-hook.png)

1. A assinatura do evento é preenchida com valores para sua conta do Media Services. 
1. Selecione ' gancho da Web ' para o **tipo de ponto de extremidade**.
1. Neste tópico, deixamos a **inscrição para todos os tipos de eventos** marcada. No entanto, você pode desmarcar a ele e filtrar por tipos de evento específico. 
1. Clique no **selecione um ponto de extremidade** link.

    Para o ponto de extremidade do web hook, forneça a URL do seu aplicativo Web e adicione `api/updates` à URL da página inicial. 

1. Pressione **confirmar seleção**.
1. Pressione **Criar**.
1. Dê à sua assinatura um nome.

   ![Selecione os logs](./media/monitor-events-portal/create-subscription.png)

1. Exiba novamente o seu aplicativo Web e observe que um evento de validação de assinatura foi enviado a ele. 

    A Grade de Eventos envia o evento de validação de modo que o ponto de extremidade possa verificar se ele deseja receber os dados de evento. O ponto de extremidade deve definir `validationResponse` para `validationCode`. Para saber mais, confira [Event Grid security and authentication](../../event-grid/security-authentication.md) (Segurança e autenticação da Grade de Eventos). Você pode visualizar o código do aplicativo da Web para ver como ele valida a assinatura.

Agora, vamos acionar eventos para ver como o Event Grid distribui a mensagem para o seu endpoint.

## <a name="send-an-event-to-your-endpoint"></a>Enviar um evento para o ponto de extremidade

Você pode acionar eventos para a conta do Media Services executando uma tarefa de codificação. Você pode seguir [este guia de início rápido](stream-files-dotnet-quickstart.md) para codificar um arquivo e iniciar o envio de eventos. Se você se inscreveu em todos os eventos, você verá uma tela semelhante à seguinte:

> [!TIP]
> Selecione o ícone de olho para expandir os dados de evento. Não atualize a página, se você quiser ver todos os eventos.

![Exibição do evento de assinatura](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Próximas etapas

[Carregar, codificar e transmitir](stream-files-tutorial-with-api.md)
