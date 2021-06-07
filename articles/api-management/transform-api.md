---
title: Tutorial – Transformar e proteger sua API no Gerenciamento de API do Azure | Microsoft Docs
description: Neste tutorial, você aprende a proteger sua API no Gerenciamento de API usando políticas de transformação e limitação (limitação de taxa).
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/28/2020
ms.author: apimpm
ms.openlocfilehash: 979bdaa1e0dac4f45a321abda2a208f46983f9cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010224"
---
# <a name="tutorial-transform-and-protect-your-api"></a>Tutorial: Transformar e proteger sua API

O tutorial mostra como transformar sua API para que ela não revele informações sobre o back-end privado. Por exemplo, você talvez queira ocultar as informações sobre a pilha de tecnologia em execução no back-end. Você também pode desejar ocultar URLs originais que aparecem no corpo da resposta HTTP da API e, em vez disso, redirecioná-las para o gateway de APIM.

Este tutorial também mostra como é fácil adicionar proteção à API de back-end configurando o limite de taxa com o Gerenciamento de API do Azure. Por exemplo, talvez você queira limitar a taxa de chamadas à API para que a API não seja usada em excesso pelos desenvolvedores. Para obter mais informações, confira [Políticas de Gerenciamento de API](api-management-policies.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> -   Transformar uma API para retirar os cabeçalhos de resposta
> -   Substituir as URLs originais no corpo da resposta da API por URLs de gateway de APIM
> -   Proteger uma API adicionando uma política de limite de taxa (limitação)
> -   Testar as transformações

:::image type="content" source="media/transform-api/api-management-management-console.png" alt-text="Políticas no portal":::

## <a name="prerequisites"></a>Pré-requisitos

-   Conheça a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md).
-   Compreenda o [conceito de políticas no Gerenciamento de API do Azure](api-management-howto-policies.md).
-   Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
-   Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Transformar uma API para retirar os cabeçalhos de resposta

Esta seção mostra como ocultar os cabeçalhos HTTP que você não deseja mostrar aos usuários. Este exemplo mostra como excluir os seguintes cabeçalhos na resposta HTTP:

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>Testar a resposta original

Para ver a resposta original:

1. Em sua instância de serviço do Gerenciamento de API, selecione **APIs**.
1. Selecione **API de Conferência de Demonstração** em sua lista de APIs.
1. Selecione a guia **Testar** na parte superior da tela.
1. Selecione a operação **GetSpeakers** e selecione **Enviar**.

A resposta original deve se parecer com a seguinte:

:::image type="content" source="media/transform-api/original-response.png" alt-text="Resposta da API original":::

Como você pode ver, a resposta inclui os cabeçalhos **X-AspNet-Version** e **X-Powered-By**.

### <a name="set-the-transformation-policy"></a>Definir a política de transformação

1. Selecione **API de Conferência de Demonstração** > **Design** > **Todas as operações**.
4. Na seção **Processamento de saída**, selecione o ícone do editor de código ( **</>** ).

   :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png" alt-text="Navegar para a política de saída" border="false":::

1. Posicione o cursor dentro do elemento de **&lt;saída&gt;** e selecione **Mostrar snippets** no canto superior direito.
1. Na janela à direita, em **Políticas de transformação**, selecione **Definir cabeçalho HTTP** duas vezes (para inserir dois snippets de política).

   :::image type="content" source="media/transform-api/transform-api.png" alt-text="Definir política de cabeçalho HTTP":::

1. Modifique seu código **\<outbound>** para ter a seguinte aparência:

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   :::image type="content" source="media/transform-api/set-policy.png" alt-text="Definir cabeçalho HTTP":::

1. Clique em **Salvar**.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Substituir as URLs originais no corpo da resposta da API por URLs de gateway de APIM

Esta seção mostra como ocultar as URLs originais que aparecem no corpo da resposta HTTP da API e, em vez disso, redirecioná-las para o gateway de APIM.

### <a name="test-the-original-response"></a>Testar a resposta original

Para ver a resposta original:

1. Selecione **API de Conferência de Demonstração** > **Testar**.
1. Selecione a operação **GetSpeakers** e selecione **Enviar**.

    Como você pode ver, a resposta inclui as URLs de back-end originais:

    :::image type="content" source="media/transform-api/original-response2.png" alt-text="URLs originais na resposta":::


### <a name="set-the-transformation-policy"></a>Definir a política de transformação

1.  Selecione **API de Conferência de Demonstração** > **Todas as operações** > **Design**.
1.  Na seção **Processamento de saída**, selecione o ícone do editor de código ( **</>** ).
1.  Posicione o cursor dentro do elemento de **&lt;saída&gt;** e selecione **Mostrar snippets** no canto superior direito.
1.  Na janela à direita, em **Políticas de transformação**, selecione **Mascarar URLs no conteúdo**. 
1.  Clique em **Salvar**.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Proteger uma API adicionando a política de limite de taxa (limitação)

Esta seção mostra como adicionar a proteção para a API de back-end configurando limites de taxa. Por exemplo, talvez você queira limitar a taxa de chamadas à API para que a API não seja usada em excesso pelos desenvolvedores. Neste exemplo, o limite é definido como 3 chamadas por 15 segundos para cada ID de assinatura. Depois de 15 segundos, um desenvolvedor pode repetir a chamada à API.

1.  Selecione **API de Conferência de Demonstração** > **Todas as operações** > **Design**.
1.  Na seção **Processamento de entrada**, selecione o ícone do editor de código ( **</>** ).
1.  Posicione o cursor dentro do elemento de **&lt;entrada&gt;** e selecione **Mostrar snippets** no canto superior direito.

    :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png" alt-text="Definir política de entrada" border="false":::

1.  Na janela à direita, em **Políticas de restrição de acesso**, clique em **+ Limitar taxa de chamada por chave**.
1.  Modifique o código **rate-limit-by-key** (no elemento **\<inbound\>** ) para o seguinte código:

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>Testar as transformações

Nesse ponto, se você examinar o código no editor de códigos, suas políticas terão esta aparência:

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <redirect-content-urls />
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

O restante desta seção testa as transformações de política que você definir neste artigo.

### <a name="test-the-stripped-response-headers"></a>Testar os cabeçalhos de resposta retirados

1. Selecione **API de Conferência de Demonstração** > **Testar**.
1. Selecione a operação **GetSpeakers** e selecione **Enviar**.

    Como você pode ver, os cabeçalhos foram retirados:

    :::image type="content" source="media/transform-api/final-response1.png" alt-text="Cabeçalhos de resposta retirados":::

### <a name="test-the-replaced-url"></a>Testar a URL substituída

1. Selecione **API de Conferência de Demonstração** > **Testar**.
1. Selecione a operação **GetSpeakers** e selecione **Enviar**.

    Como você pode ver, a URL foi substituída.

    :::image type="content" source="media/transform-api/final-response2.png" alt-text="URL substituída":::

### <a name="test-the-rate-limit-throttling"></a>Testar o limite de taxa (limitação)

1. Selecione **API de Conferência de Demonstração** > **Testar**.
1. Selecione a operação **GetSpeakers**. Selecione **Enviar** três vezes seguidas.

    Depois de enviar a solicitação três vezes, você obtém a resposta **429 Muitas solicitações**.

    :::image type="content" source="media/transform-api/test-throttling.png" alt-text="Número excessivo de solicitações":::

1. Aguarde 15 segundos e selecione **Enviar** novamente. Neste momento, você deve obter uma resposta **200 OK**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
>
> -   Transformar uma API para retirar os cabeçalhos de resposta
> -   Substituir as URLs originais no corpo da resposta da API por URLs de gateway de APIM
> -   Proteger uma API adicionando a política de limite de taxa (limitação)
> -   Testar as transformações

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Monitorar sua API](api-management-howto-use-azure-monitor.md)
