---
title: Personalizar um modelo Person com a API Video Indexer
titleSuffix: Azure Media Services
description: Saiba como personalizar um modelo Person com a API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 09366dea1a0d77052b6f99e9f5ab52c270e341b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87047021"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Personalizar um modelo de Pessoa com a API do Video Indexer

O Video Indexer dá suporte à detecção facial e ao reconhecimento de celebridades para conteúdo de vídeo. O recurso de reconhecimento de celebridade aborda cerca de 1 milhão rostos com base na fonte de dados solicitada comumente, como o IMDB, a Wikipédia e os principais influenciadores do LinkedIn. Os rostos que não são reconhecidos pelo recurso de reconhecimento de celebridade são detectados, mas deixados sem nome. Depois de carregar seu vídeo para Video Indexer e obter resultados de volta, você pode voltar e nomear as faces que não foram reconhecidas. Depois que você rotular um rosto com um nome, o rosto e o nome serão adicionados ao modelo de Pessoa de sua conta. Em seguida, o Video Indexer reconhecerá esse rosto em seus vídeos futuros e antigos.

Você pode usar a API do Video Indexer para editar os rostos detectados em um vídeo, conforme descrito neste tópico. Você também pode usar o site do Video Indexer, conforme descrito em [Personalizar o modelo de Pessoa usando o site do Video Indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Gerenciar vários modelos de Pessoa

O Video Indexer dá suporte a vários modelos de Pessoa por conta. Esse recurso está disponível atualmente apenas por meio de APIs do Video Indexer.

Se a conta lida com diferentes cenários de caso de uso, talvez seja conveniente que você crie vários modelos de Pessoa por conta. Por exemplo, se o conteúdo estiver relacionado a esportes, você poderá criar um modelo de pessoa separado para cada esporte (futebol, basquete, futebol e assim por diante).

Depois de criar um modelo, você pode usá-lo, fornecendo a ID do modelo de um modelo específico de Pessoa ao carregar/indexar ou reindexar um vídeo. Treinar um novo rosto para um vídeo atualiza o modelo personalizado específico com o qual o vídeo foi associado.

Cada conta tem um limite de 50 modelos de Pessoa. Se você não precisar do suporte ao modelo de várias pessoas, não atribua uma ID de modelo de pessoa ao seu vídeo ao carregar/indexar ou reindexar. Nesse caso, o Video Indexer usa o modelo de Pessoa personalizado padrão em sua conta.

## <a name="create-a-new-person-model"></a>Criar um novo modelo de Pessoa

Para criar um novo modelo de pessoa na conta especificada, use a API [criar um modelo Person](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?) .

A resposta fornece o nome e a ID de modelo gerada do modelo de Pessoa que você acabou de criar, seguindo o formato do exemplo a seguir.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Em seguida, você usa o valor de **ID** para o parâmetro **personModelId** ao [carregar um vídeo para indexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [reindexar um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Excluir um modelo de Pessoa

Para excluir um modelo de pessoa personalizada da conta especificada, use a API de [modelo excluir uma pessoa](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?) .

Depois que o modelo de Pessoa é excluído com êxito, o índice de seus vídeos atuais que estavam usando o modelo excluído permanecerá inalterado até você reindexá-los. Após a reindexação, as faces que foram nomeadas no modelo excluído não serão reconhecidas pelo Video Indexer em seus vídeos atuais que foram indexados usando esse modelo, mas as faces ainda serão detectadas. Seus vídeos atuais que foram indexados usando o modelo excluído usarão agora o modelo de Pessoa padrão da sua conta. Se os rostos do modelo excluído também forem nomeados no modelo padrão da sua conta, esses rostos continuarão a ser reconhecidos nos vídeos.

Não há nenhum conteúdo retornado quando o modelo Person é excluído com êxito.

## <a name="get-all-person-models"></a>Obter todos os modelos de Pessoa

Para obter todos os modelos de pessoa na conta especificada, use a API [obter um modelo Person](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?) .

A resposta fornece uma lista de todos os modelos de pessoa em sua conta (incluindo o modelo de pessoa padrão na conta especificada) e cada um de seus nomes e IDs seguindo o formato do exemplo abaixo.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

Você pode escolher qual modelo deseja usar para um vídeo usando o `id` valor do modelo Person para o `personModelId` parâmetro ao [carregar um vídeo para indexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [reindexar um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Atualizar um rosto

Esse comando permite que você atualize um rosto no vídeo com um nome usando a ID do vídeo e a ID do rosto. Em seguida, essa ação atualiza o modelo Person ao qual o vídeo foi associado no carregamento/indexação ou reindexação. Se nenhum modelo de Pessoa foi atribuído, ele atualiza o modelo de Pessoa padrão da conta.

Em seguida, o sistema reconhece as ocorrências da mesma face em seus outros vídeos atuais que compartilham o mesmo modelo de pessoa. O reconhecimento do rosto nos outros vídeos atuais pode levar algum tempo para entrar em vigor, pois esse é um processo em lote.

Atualize um rosto que o Video Indexer reconheceu como uma celebridade com um novo nome. O novo nome que você fornecer terá precedência sobre o reconhecimento de celebridades interno.

Para atualizar a face, use [atualizar uma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?) API de face de vídeo.

Os nomes são exclusivos para modelos de pessoa, portanto, se você fornecer duas faces diferentes no mesmo modelo de pessoa com o mesmo `name` valor de parâmetro, Video indexer exibirá as faces como a mesma pessoa e as convergirá quando você reindexar o vídeo.

## <a name="next-steps"></a>Próximas etapas

[Personalizar um modelo de Pessoa usando o site do Video Indexer](customize-person-model-with-website.md)
