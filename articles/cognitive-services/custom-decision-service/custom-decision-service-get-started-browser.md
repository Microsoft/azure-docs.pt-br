---
title: Chamar API de um navegador ‒ Serviço de decisão personalizada
titlesuffix: Azure Cognitive Services
description: Como otimizar uma página da Web por meio de chamadas de API diretamente de um navegador para o Serviço de Decisão Personalizada.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.openlocfilehash: 2b356e2f0fe9235d49dffa7417cd3894059f9caf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829150"
---
# <a name="call-api-from-a-browser"></a>Chamar a API de um navegador

Este artigo ajuda você a fazer chamadas às APIs do Serviço de Decisão Personalizada do Azure diretamente de um navegador.

Lembre-se de [Registrar seu aplicativo](custom-decision-service-get-started-register.md) primeiro.

Vamos começar. Seu aplicativo é modelado como tendo uma página frontal, que contém links para várias páginas de artigo. A página frontal usa o Serviço de Decisão Personalizada para especificar a ordenação das páginas de artigo. Insira o seguinte código no cabeçalho HTML da página frontal:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

O argumento `data` contém a classificação das URLs a serem renderizadas. Para obter mais informações, confira a [API](custom-decision-service-api-reference.md) de referência.

Para manipular um clique do usuário no primeiro artigo, chame o seguinte código na página frontal:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Aqui, `data` é o argumento para a função `callback()`. Um exemplo de implementação pode ser encontrado neste [tutorial](custom-decision-service-tutorial-news.md#use-the-apis).

Por fim, você precisa fornecer a API de Conjunto de Ações, que retorna a lista de artigos (ações) a serem considerados pelo Serviço de Decisão Personalizada. Implemente essa API como um RSS feed, conforme mostrado aqui:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Aqui, cada elemento `<item>` de nível superior descreve um artigo. O `<link>` é obrigatório e é usado como uma ID de ação pelo Serviço de Decisão Personalizada. Especifique `<date>` (em um formato padrão do RSS) se você tiver mais de 15 artigos. Os 15 artigos mais recentes são usados. O `<title>` é opcional e é usado para criar recursos relacionados ao texto para o artigo.

## <a name="next-steps"></a>Próximas etapas

* Acompanhe um [tutorial](custom-decision-service-tutorial-news.md) para obter um exemplo mais detalhado.
* Consulte a [API](custom-decision-service-api-reference.md) de referência para saber mais sobre a funcionalidade fornecida.
