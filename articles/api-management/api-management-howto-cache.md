---
title: Adicionar cache para melhorar o desempenho no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como aprimorar a latência, carregar o consumo de largura de banda e o serviço Web para chamadas de serviço de Gerenciamento de API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: apimpm
ms.openlocfilehash: 732abed830afdb759ed52fd933673edd8e5cade6
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638726"
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Adicionar caching para melhorar o desempenho no Gerenciamento de API do Azure

As APIs e as operações no gerenciamento de API podem ser configuradas com o cache de resposta. O cache de resposta pode reduzir significativamente a latência de chamadores de API e carga de back-end para provedores de API.

> [!IMPORTANT]
> O cache interno é volátil e é compartilhado por todas as unidades na mesma região no mesmo serviço de gerenciamento de API.

Para saber mais sobre o cache, veja [Políticas de armazenamento em cache do Gerenciamento de API](api-management-caching-policies.md) e [Armazenamento em cache personalizado no Gerenciamento de API do Azure](api-management-sample-cache-by-key.md).

![políticas de cache](media/api-management-howto-cache/cache-policies.png)

O que você aprenderá:

> [!div class="checklist"]
> * Adicionar o cache de resposta à sua API
> * Verificação do cache em ação

## <a name="availability"></a>Disponibilidade

> [!NOTE]
> Cache interno não está disponível na camada de **Consumo** do Gerenciamento de API do Azure. Você pode [usar um Cache do Azure para Redis externo](api-management-howto-cache-external.md) em vez disso.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

+ [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)
+ [Importar e publicar uma API](import-and-publish.md)

## <a name="add-the-caching-policies"></a><a name="caching-policies"> </a>Adicionar as políticas de cache

Com as políticas de armazenamento em cache mostradas neste exemplo, a primeira solicitação feita à operação **GetSpeakers** retorna uma resposta do serviço de back-end. Esta resposta será armazenada em cache, com uma chave de acordo com os parâmetros de cadeia de consulta e cabeçalhos especificados. Chamadas subsequentes para a operação, com parâmetros correspondentes, retornarão respostas em cache até que o intervalo de duração de cache expire.

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até sua instância de APIM.
3. Selecione a guia **API**.
4. Clique em **API de Conferência de Demonstração** na sua lista de APIs.
5. Selecione **GetSpeakers**.
6. Na parte superior da tela, selecione a guia **Design**.
7. Na seção **Processamento de entrada** , clique no ícone **</>** .

    ![editor de códigos](media/api-management-howto-cache/code-editor.png)

8. No elemento de **entrada** , adicione a seguinte política:

   ```
   <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
       <vary-by-header>Accept</vary-by-header>
       <vary-by-header>Accept-Charset</vary-by-header>
       <vary-by-header>Authorization</vary-by-header>
   </cache-lookup>
   ```

9. No elemento de **saída** , adicione a seguinte política:

   ```
   <cache-store duration="20" />
   ```

    **Duração** especifica o intervalo de vencimento das respostas armazenadas em cache. Neste exemplo, o intervalo restante é de **20** segundos.

> [!TIP]
> Se você estiver usando um cache externo, conforme descrito em [Uso de um Cache do Azure para Redis externo no Gerenciamento de API do Azure](api-management-howto-cache-external.md), poderá especificar o atributo `caching-type` das políticas de cache. Para obter mais detalhes, consulte [Políticas de caching do Gerenciamento de API](api-management-caching-policies.md).

## <a name="call-an-operation-and-test-the-caching"></a><a name="test-operation"> </a>Chamar uma operação e testar o cache
Para ver o cache em funcionamento, chame a operação por meio do portal do desenvolvedor.

1. No portal do Azure, navegue até sua instância do APIM.
2. Selecione a guia **APIs** .
3. Selecione a API para a qual você adicionou políticas de cache.
4. Selecione a operação **GetSpeakers**.
5. Clique na guia **Teste** no menu superior direito.
6. Pressione **Enviar**.

## <a name="next-steps"></a><a name="next-steps"> </a>Próximas etapas
* Para saber mais sobre as políticas de cache, veja [Políticas de cache][Caching policies] na [Referência de política do Gerenciamento de API][API Management policy reference].
* Para saber mais sobre itens de cache por chave usando expressões de política, confira [Cache personalizado no Gerenciamento de API do Azure](api-management-sample-cache-by-key.md).
* Para obter mais informações sobre como usar o Cache do Azure para Redis externo, confira [Usar um Cache do Azure para Redis externo no Gerenciamento de API](api-management-howto-cache-external.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
