---
title: Personalizar um modelo de marcas com a API Video Indexer
titleSuffix: Azure Media Services
description: Saiba como personalizar um modelo de marcas com a API de Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: c3056415d3432dbe64dd1f2bcf974a676bbf6c6b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586050"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Personalizar um modelo de marcas com a API do Video Indexer

O Video Indexer dá suporte à detecção de marca de fala e texto visual durante a indexação e a reindexação de conteúdo de áudio e de vídeo. O recurso de detecção de marca identifica menções a produtos, serviços e empresas sugeridos pelo banco de dados de marcas do Bing. Por exemplo, se a Microsoft for mencionada em conteúdo de vídeo ou áudio ou se aparecer no texto visual em um vídeo, Video Indexer o detectará como uma marca no conteúdo. Um modelo de marcas personalizado permite que você exclua algumas marcas da lista daquelas a serem detectadas, além de incluir marcas que devem ser parte de seu modelo e que podem não estar no banco de dados de marcas do Bing. Para obter mais informações, consulte [visão geral](customize-brands-model-overview.md).

> [!NOTE]
> Se o vídeo tiver sido indexado antes de adicionar uma marca, você precisará reindexá-la.

Você pode usar a API do Video Indexer para criar, usar e editar modelos personalizados de marcas detectados em um vídeo, conforme descrito neste tópico. Você também pode usar o site do Video Indexer, conforme descrito em [Personalizar o modelo de marcas usando o site do Video Indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Criar uma marca

A API [criar uma marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) cria uma nova marca personalizada e a adiciona ao modelo de marcas personalizadas para a conta especificada.

> [!NOTE]
> A configuração `enabled` (no corpo) como true coloca a marca na lista de *inclusão* para video indexer detectar. A configuração `enabled` como false coloca a marca na lista de *exclusões* , portanto Video indexer não a detectará.

Alguns outros parâmetros que podem ser definidos no corpo:

* O `referenceUrl` valor pode ser qualquer site de referência para a marca, como um link para sua página da Wikipédia.
* O `tags` valor é uma lista de marcas para a marca. Essa marca aparece no campo *categoria* da marca no site video indexer. Por exemplo, a marca "Azure" pode ser marcada ou categorizada como "Nuvem".

### <a name="response"></a>Resposta

A resposta fornece informações sobre a marca que você acabou de criar de acordo com o formato do exemplo a seguir.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Excluir uma marca

A API [excluir uma marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) remove uma marca do modelo de marcas personalizadas da conta especificada. A conta é especificada no `accountId` parâmetro. Quando chamada com êxito, a marca não estará mais nas listas de marcas *Inclusões* ou *Exclusões*.

### <a name="response"></a>Resposta

Não há nenhum conteúdo retornado quando a marca é excluída com êxito.

## <a name="get-a-specific-brand"></a>Obter uma marca específica

A API [obter uma marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) permite pesquisar os detalhes de uma marca no modelo de marcas personalizadas para a conta especificada usando a ID da marca.

### <a name="response"></a>Resposta

A resposta fornece informações sobre a marca que você pesquisou (usando a ID da marca) de acordo com o formato do exemplo a seguir.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> `enabled` sendo definido como `true` significa que a marca está na lista de *inclusão* para video indexer detectar e `enabled` ser false significa que a marca está na lista de *exclusões* , portanto, Video indexer não a detectará.

## <a name="update-a-specific-brand"></a>Atualiza uma marca específica

A API [atualizar uma marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) permite pesquisar os detalhes de uma marca no modelo de marcas personalizadas para a conta especificada usando a ID da marca.

### <a name="response"></a>Resposta

A resposta fornece as informações atualizadas sobre a marca que você acabou de criar de acordo com o formato do exemplo a seguir.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Obter todas as marcas

A API [obter todas as marcas](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) retorna todas as marcas no modelo de marcas personalizadas para a conta especificada, independentemente de a marca estar destinada a estar na lista *incluir* ou *excluir* marcas.

### <a name="response"></a>Resposta

A resposta fornece uma lista de todas as marcas na sua conta e cada um dos seus detalhes, seguindo o formato do exemplo a seguir.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> A marca denominada *example* está na lista *include* de video indexer a serem detectadas e a marca chamada *example2* está na lista *Exclude* , de modo que Video indexer não a detectará.

## <a name="get-brands-model-settings"></a>Obter configurações de modelo de marcas

A API [obter configurações de marcas](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) retorna as configurações do modelo de marcas na conta especificada. As configurações de modelo de marcas representam o estado habilitado ou desabilitado da detecção do banco de dados de marcas do Bing. Se as marcas do Bing não estiverem habilitadas, Video Indexer detectará apenas marcas do modelo de marcas personalizadas da conta especificada.

### <a name="response"></a>Resposta

A resposta mostra se as marcas do Bing estão habilitadas seguindo o formato do exemplo a seguir.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn` ser definido como true representa que as marcas do Bing estão habilitadas. Se `useBuiltin` for false, as marcas do Bing serão desabilitadas. O `state` valor pode ser ignorado porque foi preterido.

## <a name="update-brands-model-settings"></a>Atualizar configurações de modelo de marcas

A API [Atualizar marcas](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) atualiza as configurações do modelo de marcas na conta especificada. As configurações de modelo de marcas representam o estado habilitado ou desabilitado da detecção do banco de dados de marcas do Bing. Se as marcas do Bing não estiverem habilitadas, Video Indexer detectará apenas marcas do modelo de marcas personalizadas da conta especificada.

O `useBuiltIn` sinalizador definido como verdadeiro significa que as marcas do Bing estão habilitadas. Se `useBuiltin` for false, as marcas do Bing serão desabilitadas.

### <a name="response"></a>Resposta

Não há nenhum conteúdo retornado quando a configuração do modelo de marcas é atualizada com êxito.

## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de Marcas usando o site](customize-brands-model-with-website.md)
