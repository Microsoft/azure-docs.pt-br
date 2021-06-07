---
title: Tutorial – Depurar APIs no Gerenciamento de API do Azure usando o rastreamento de solicitação
description: Siga as etapas deste tutorial para habilitar o rastreamento e inspecionar as etapas de processamento de solicitações no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 531e346569b85ababc382f997fd7764a92b3d05f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542307"
---
# <a name="tutorial-debug-your-apis-using-request-tracing"></a>Tutorial: Depure suas APIs usando o rastreamento de solicitação

Este tutorial descreve como inspecionar (rastrear) o processamento de solicitações no Gerenciamento de API do Azure para ajudar você a depurar sua API e solucionar problemas dela. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Rastrear um exemplo de chamada
> * Examinar as etapas de processamento de solicitações

:::image type="content" source="media/api-management-howto-api-inspector/api-inspector-001.png" alt-text="Inspetor de API":::

## <a name="prerequisites"></a>Pré-requisitos

+ Conheça a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md).
+ Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

## <a name="verify-allow-tracing-setting"></a>Verificar a configuração Permitir rastreamento 

A configuração **Permitir rastreamento** da assinatura usada para a API precisa estar habilitada. Se você estiver usando a assinatura interna que permite todo o acesso, ela estará habilitada por padrão. Para verificar isso no portal, navegue até a sua instância do Gerenciamento de API e selecione **Assinaturas**.

   :::image type="content" source="media/api-management-howto-api-inspector/allow-tracing.png" alt-text="Permitir rastreamento na assinatura":::

## <a name="trace-a-call"></a>Rastrear uma chamada

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até a sua instância do Gerenciamento de API.
1. Selecione **APIs**.
1. Selecione **API de Conferência de Demonstração** na lista de APIs.
1. Selecione a guia **Testar**.
1. Selecione a operação **GetSpeakers**.
1. Confirme se o cabeçalho de solicitação HTTP inclui **Ocp-Apim-Trace: True** e um valor válido para **Ocp-Apim-Subscription-Key**. Se não, selecione **+ Adicionar Cabeçalho** para adicionar o cabeçalho.
1. Escolha **Enviar** para fazer uma chamada à API.

  :::image type="content" source="media/api-management-howto-api-inspector/06-debug-your-apis-01-trace-call.png" alt-text="Configurar o rastreamento de API":::

> [!TIP]
> Se **Ocp-Apim-Subscription-Key** não for preenchida automaticamente na solicitação HTTP, recupere-a no portal. Selecione **Assinaturas** e abra o menu de contexto ( **...** ) da sua assinatura. Selecione **Mostrar/ocultar chaves**. Regenere também as chaves, se necessário. Em seguida, adicione uma chave ao cabeçalho.

## <a name="review-trace-information"></a>Examinar as informações de rastreamento

1. Depois que a chamada for concluída, acesse a guia **Rastreamento** na **Resposta HTTP**.
1. Selecione um dos seguintes links para ir para as informações detalhadas de rastreamento: **Entrada**, **Back-end** ou **Saída**.

     :::image type="content" source="media/api-management-howto-api-inspector/response-trace.png" alt-text="Examinar o rastreamento de resposta":::

    * **Entrada**: mostra a solicitação original recebida pelo Gerenciamento de API do chamador e as políticas aplicadas à solicitação. Por exemplo, se você adicionou políticas no [Tutorial: Transformar e proteger sua API](transform-api.md), elas são exibidas aqui.

    * **Back-end**: mostra as solicitações enviadas pelo Gerenciamento de API ao back-end de API e a resposta recebida.

    * **Saída**: mostra as políticas aplicadas à resposta antes do novo envio ao chamador.

    > [!TIP]
    > Cada etapa também mostra o tempo decorrido desde quando a solicitação foi recebida pelo Gerenciamento de API.

1. Na guia **Mensagem**, o cabeçalho **ocp-apim-trace-location** mostra a localização dos dados de rastreamento mantidos no Armazenamento de Blobs do Azure. Se necessário, acesse essa localização para recuperar o rastreamento.

     :::image type="content" source="media/api-management-howto-api-inspector/response-message.png" alt-text="Localização de rastreamento no Armazenamento do Azure":::
## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Rastrear um exemplo de chamada
> * Examinar as etapas de processamento de solicitações

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Usar revisões](api-management-get-started-revise-api.md)
