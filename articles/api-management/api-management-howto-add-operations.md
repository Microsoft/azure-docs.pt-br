---
title: "Como adicionar operações a uma API no Gerenciamento de API do Azure | Microsoft Docs"
description: "Saiba como adicionar operações a uma API no Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 1158a023-1913-4e9c-93de-9164b672f9b3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 105fc51c2d1152a40a5757985da47330e0b7b8cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>Como adicionar operações a uma API no Gerenciamento de API do Azure
Antes que uma API no Gerenciamento de API possa ser usada, as operações devem ser adicionadas. Este guia mostra como adicionar e configurar tipos diferentes de operações para uma API em Gerenciamento de API.

## <a name="add-operation"> </a>Adicionar uma operação
Operações são adicionadas e configuradas em uma API no Portal do editor. Para acessar o portal do editor, clique em **Portal do editor** no Portal do Azure para acessar o serviço Gerenciamento de API.

![Portal do editor][api-management-management-console]

> Se ainda não criou uma instância de serviço de Gerenciamento de API, confira [Criar uma instância de serviço de Gerenciamento de API][Create an API Management service instance] no tutorial [Introdução ao Gerenciamento de API do Azure][Get started with Azure API Management].
> 
> 

Selecione a API desejada no Portal do editor e então selecione a guia **Operações** . 

![Operações][api-management-operations]

Clique em **Adicionar operação** para adicionar uma nova operação. A janela **Nova operação** será exibida e a guia **Assinatura** será selecionada por padrão.

![Adicionar operação][api-management-add-operation]

Especifique o **verbo HTTP** escolhendo na lista suspensa.

![Método HTTP][api-management-http-method]

<a name="url-template"></a>

Defina o modelo do URL digitando um fragmento de URL consistindo de um ou mais segmentos de caminho URL e nenhum ou mais parâmetros de cadeia de consulta. O modelo do URL, anexado ao URL base da API, identifica uma única operação HTTP. Ele pode conter uma ou mais partes variáveis nomeadas que são identificadas por chaves. Essas partes variáveis se chamam parâmetros de modelo, que são valores atribuídos dinamicamente e extraídos do URL da solicitação quando a solicitação está sendo processada pela plataforma de Gerenciamento de API.

> O modelo de URL pode incluir padrões curingas. Por exemplo, especificar `/*` encaminha todas as solicitações para esse método HTTP para o serviço de back-end.

![Modelo do URL][api-management-url-template]

<a name="rewrite-url-template"></a>

Se desejar, especifique **Reescrever modelo de URL**. Isso permite que você use o modelo de URL padrão para processar solicitações de entrada no front-end, enquanto chama o back-end por meio de um URL convertido de acordo com o modelo reescrito. Os parâmetros de modelo do modelo de URL devem ser usados no modelo reescrito. O exemplo a seguir mostra como o tipo de conteúdo codificado como um segmento de caminho no serviço Web do exemplo anterior pode ser fornecido como parâmetro de consulta na API publicada por meio da plataforma de Gerenciamento de API usando os modelos de URL.

![Modelo de URL reescrito][api-management-url-template-rewrite]

Quem chamar a operação utilizará o formato `/customers?customerid=ALFKI` e ele será mapeado para `/Customers('ALFKI')` quando o serviço de back-end for invocado.

O Nome de **exibição** e a **Descrição** oferecem uma descrição da operação e são usados para fornecer documentação aos desenvolvedores que utilizam essa API no portal do desenvolvedor.

![Descrição][api-management-description]

A descrição da operação pode ser especificada como texto sem formatação ou HTML na caixa de texto **Descrição** .

## <a name="operation-caching"> </a>Cache da operação
O cache de respostas reduz a latência percebida pelos consumidores da API, reduz a largura de banda e diminui a carga no serviço Web HTTP que está implementando a API. 

Para habilitar o cache para a operação de modo fácil e rápido, selecione a guia **Caching** e marque a caixa de seleção **Habilitar**.

![Cache][api-management-caching-tab]

**Duração** especifica o período de tempo durante o qual a resposta da operação permanece armazenada em cache. O valor padrão é 3.600 segundos, ou uma hora.

Chaves de cache são usadas para diferenciar as respostas, de modo que a resposta correspondente a cada chave de cache diferente tenha seu próprio valor em cache separado. Outra opção é inserir parâmetros de cadeia de consulta específicos e/ou cabeçalhos HTTP a serem utilizados para computar valores de chave de cache nas caixas de texto **Variar de acordo com os parâmetros da cadeia de consulta** e **Variar de acordo com cabeçalhos**. Quando nenhum é especificado, o URL de solicitação completo e os seguintes valores de cabeçalho HTTP são usados para gerar a chave de cache: **Accept** e **Accept-Charset**.

> Para obter mais informações sobre cache e políticas de cache, consulte [Como armazenar em cache os resultados de operações no Gerenciamento de API do Azure][How to cache operation results in Azure API Management].
> 
> 

## <a name="request-parameters"> </a>Parâmetros da solicitação
Os parâmetros da operação são gerenciados na guia Parâmetros. Parâmetros especificados no **Modelo do URL**, na guia **Assinatura**, são adicionados automaticamente e somente podem ser alterados editando o modelo do URL. Parâmetros adicionais podem ser inseridos manualmente.

Para adicionar um novo parâmetro de consulta, clique em **Adicionar parâmetro de consulta** e insira as informações a seguir:

* **Nome** - nome do parâmetro.
* **Descrição** - uma breve descrição do parâmetro (opcional).
* **Tipo** - tipo do parâmetro, selecionado no menu suspenso.
* **Valores** - valores que podem ser atribuídos a esse parâmetro. Um dos valores pode ser marcado como padrão (opcional).
* **Obrigatório** - faça com que o parâmetro seja obrigatório marcando a caixa de seleção. 

![Parâmetros da solicitação][api-management-request-parameters]

## <a name="request-body"> </a>Corpo da solicitação
Se a operação permitir (por exemplo, PUT, POST) e precisar de um corpo, você poderá fornecer um exemplo dele em todos os formatos de representação com suporte (por exemplo, json, XML). 

> O corpo da solicitação é usado somente para fins de documentação e não é validado.
> 
> 

Para inserir um corpo de solicitação, vá até a guia **Corpo** .

Clique em **Adicionar representação**, comece a digitar o nome do tipo de conteúdo desejado (por exemplo, aplicativo/json), selecione-o no menu suspenso e cole o exemplo de corpo de solicitação desejado, no formato selecionado, na caixa de texto. 

![Corpo da solicitação][api-management-request-body]

Além de representações, você também pode especificar um texto de descrição opcional na caixa de texto **Descrição** .

## <a name="responses"> </a>Respostas
É uma prática recomendável fornecer exemplos de respostas para todos os códigos de status que a operação possa produzir. Cada código de status pode ter mais de um exemplo de corpo de resposta, um para cada um dos tipos de conteúdos com suporte. 

Para adicionar uma resposta, clique em **Adicionar** e comece a digitar o código de status desejado. Neste exemplo, o código de status é **200 OK**. Quando o código for exibido no menu suspenso, selecione-o e o código de resposta será criado e adicionado à sua operação.

![Código de resposta][api-management-response-code]

Clique em **Adicionar representação**, comece a digitar o nome do tipo de conteúdo desejado (por exemplo, aplicativo/json) e selecione-o no menu suspenso.

![Tipo de conteúdo do corpo][api-management-response-body-content-type]

Copie o exemplo de corpo de resposta no formato selecionado na caixa de texto. 

![Corpo da resposta][api-management-response-body]

Se desejar, adicione uma descrição opcional na caixa de texto **Descrição** .

Após configurar a operação, clique em **Salvar**.

## <a name="next-steps"> </a>Próximas etapas
Após as operações serem adicionadas a uma API, a próxima etapa é associar a API a um produto e publicá-la para que os desenvolvedores possam chamar suas operações.

* [Como criar e publicar um produto][How to create and publish a product]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md
