---
title: Adicionar cache para melhorar o desempenho no Gerenciamento de API do Azure | Microsoft Docs
description: "Saiba como aprimorar a latência, carregar o consumo de largura de banda e o serviço Web para chamadas de serviço de Gerenciamento de API."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 59c595f0d5ce849f44c46fdb6cab0b44d35fffa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Adicionar caching para melhorar o desempenho no Gerenciamento de API do Azure
É possível configurar as operações do Gerenciamento de API para cache das respostas. O cache das respostas pode reduzir significativamente a latência da API, o consumo da largura de banda e a carga de serviço Web para dados que não são alterados com frequência.

Este guia mostra como adicionar o caching das respostas para sua API e configurar políticas para as operações de API de Eco. Você pode chamar a operação por meio do portal do desenvolvedor para verificar o caching em ação.

> [!NOTE]
> Para saber mais sobre itens de cache por chave usando expressões de política, confira [Cache personalizado no Gerenciamento de API do Azure](api-management-sample-cache-by-key.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Antes de seguir as etapas neste guia, você deve ter uma instância do serviço de Gerenciamento de API com uma API e um produto configurado. Se ainda não criou uma instância de serviço de Gerenciamento de API, confira [Criar uma instância de serviço de Gerenciamento de API][Create an API Management service instance] no tutorial [Introdução ao Gerenciamento de API do Azure][Get started with Azure API Management].

## <a name="configure-caching"> </a>Configurar uma operação para caching
Nesta etapa, você examinará as configurações de cache da operação **Recurso GET (em cache)** do exemplo de API de Eco.

> [!NOTE]
> Cada instância de serviço de Gerenciamento de API vem pré-configurada com uma API de Eco que pode ser usada para experimentar e aprender sobre o Gerenciamento de API. Para obter mais informações, confira [Introdução ao Gerenciamento de API do Azure][Get started with Azure API Management].
> 
> 

Para começar, clique em **Portal do Editor** no Portal do Azure para acessar o serviço de Gerenciamento de API. Isso levará você ao portal do editor de Gerenciamento de API.

![Portal do editor][api-management-management-console]

Clique em **APIs** no menu **Gerenciamento de API** à esquerda e clique em **API de Eco**.

![API de Eco][api-management-echo-api]

Clique na guia **Operações** e na operação **Recurso GET (em cache)**, na lista **Operações**.

![Operações de API de ECO][api-management-echo-api-operations]

Clique na guia **Cache** para ver as configurações de cache para esta operação.

![Guia Cache][api-management-caching-tab]

Para habilitar o cache de uma operação, marque a caixa de seleção **Habilitar** . Neste exemplo, o caching está habilitado.

A resposta de cada operação tem uma chave baseada nos valores dos campos **Variar por parâmetros da cadeia de consulta** e **Variar por cabeçalhos**. Se quiser armazenar em cache várias respostas com base em cabeçalhos ou parâmetros de cadeias de consulta, você pode configurá-las nesses dois campos.

**Duração** especifica o intervalo de vencimento das respostas armazenadas em cache. Neste exemplo, o intervalo é de **3600** segundos, que equivale a uma hora.

Utilizando a configuração de cache neste exemplo, a primeira solicitação para a operação **Recurso GET (em cache)** retorna uma resposta do serviço de back-end. Esta resposta será armazenada em cache, com uma chave de acordo com os parâmetros de cadeia de consulta e cabeçalhos especificados. Chamadas subsequentes para a operação, com parâmetros correspondentes, retornarão respostas em cache até que o intervalo de duração de cache expire.

## <a name="caching-policies"> </a>Examinar as políticas de cache
Nesta etapa, você analisará as configurações de cache da operação **Recurso GET (em cache)** do exemplo de API de Echo.

Quando as configurações de cache são definidas para uma operação na guia **Cache** , políticas de cache são adicionadas à operação. Essas políticas podem ser vistas e editadas no editor de políticas.

Clique em **Políticas** no menu **Gerenciamento de API** à esquerda e selecione **API de Eco/Recurso GET (em cache)** na lista suspensa **Operação**.

![Operação de escopo da política][api-management-operation-dropdown]

Exibe as políticas para esta operação no editor de políticas.

![Editor de políticas de Gerenciamento de API][api-management-policy-editor]

A definição de política para esta operação inclui as políticas que definem a configuração de cache, que foram revisadas usando a guia **Cache** na etapa anterior.

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-header>Accept-Charset</vary-by-header>
        </cache-lookup>
        <rewrite-uri template="/resource" />
    </inbound>
    <outbound>
        <base />
        <cache-store caching-mode="cache-on" duration="3600" />
    </outbound>
</policies>
```

> [!NOTE]
> As alterações feitas nas políticas de cache no editor de políticas serão refletidas na guia **Cache** de uma operação e vice-versa.
> 
> 

## <a name="test-operation"> </a>Chamar uma operação e testar o cache
Para ver o cache em funcionamento, podemos chamar a operação por meio do portal do desenvolvedor. Clique em **Portal do desenvolvedor** no menu superior direito.

![Portal do desenvolvedor][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e selecione **API de Eco**.

![API de Eco][api-management-apis-echo-api]

> Se você tem apenas uma API configurada ou visível na conta, clicar em APIs levará você diretamente às operações dessa API.
> 
> 

Selecione a operação **Recurso GET (em cache)** e clique em **Abrir Console**.

![Abrir console][api-management-open-console]

O console permite que você invoque operações diretamente por meio do portal do desenvolvedor.

![Console][api-management-console]

Mantenha os valores padrão para **param1** e **param2**.

Selecione a chave desejada na lista suspensa **subscription-key** . Se a sua conta tiver somente uma assinatura, ela já estará selecionada.

Insira **sampleheader:value1** na caixa de texto **Cabeçalhos de solicitação**.

Clique em **HTTP Get** e anote os cabeçalhos de resposta.

Insira **sampleheader:value2** na caixa de texto **Cabeçalhos de solicitação** e clique em **HTTP Get**.

Observe que o valor de **sampleheader** ainda será **value1** na resposta. Teste alguns valores diferentes e observe que a resposta armazenada em cache da primeira chamada será retornada.

Insira **25** no campo **param2** e clique em **HTTP Get**.

Observe que agora o valor de **sampleheader** na resposta será **value2**. Como os resultados da operação têm uma chave de acordo com a cadeia de consulta, a resposta em cache anterior não foi retornada.

## <a name="next-steps"> </a>Próximas etapas
* Para saber mais sobre as políticas de cache, veja [Políticas de cache][Caching policies] na [Referência de política do Gerenciamento de API][API Management policy reference].
* Para saber mais sobre itens de cache por chave usando expressões de política, confira [Cache personalizado no Gerenciamento de API do Azure](api-management-sample-cache-by-key.md).

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


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
