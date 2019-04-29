---
title: Usar a API do Video Indexer para personalizar um modelo de Pessoa – Azure
titlesuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo de Pessoa com a API do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: e5a34a75c73401c567a0e898a1ce9f85cde96586
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553701"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Personalizar um modelo de Pessoa com a API do Video Indexer

O Video Indexer dá suporte à detecção facial e ao reconhecimento de celebridades para conteúdo de vídeo. O recurso de reconhecimento de celebridades abrange aproximadamente um milhão de rostos baseados em fontes de dados geralmente solicitadas, como IMDB, Wikipédia e principais influenciadores do LinkedIn. Os rostos não reconhecidos pelo recurso de reconhecimento de celebridades são detectados; no entanto, eles são deixados sem nome. Depois de você carregar o vídeo no Video Indexer e obter os resultados, você pode voltar e nomear os rostos que não foram reconhecidos. Depois que você rotular um rosto com um nome, o rosto e o nome serão adicionados ao modelo de Pessoa de sua conta. Em seguida, o Video Indexer reconhecerá esse rosto em seus vídeos futuros e antigos.

Você pode usar a API do Video Indexer para editar os rostos detectados em um vídeo, conforme descrito neste tópico. Você também pode usar o site do Video Indexer, conforme descrito em [Personalizar o modelo de Pessoa usando o site do Video Indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Gerenciar vários modelos de Pessoa 

O Video Indexer dá suporte a vários modelos de Pessoa por conta. Esse recurso está disponível atualmente apenas por meio de APIs do Video Indexer.

Se a conta lida com diferentes cenários de caso de uso, talvez seja conveniente que você crie vários modelos de Pessoa por conta. Por exemplo, se o conteúdo está relacionado a esportes, você pode criar um modelo de Pessoa separado para cada esporte (futebol, basquete, futebol americano, etc.). 

Depois de criar um modelo, você pode usá-lo, fornecendo a ID do modelo de um modelo específico de Pessoa ao carregar/indexar ou reindexar um vídeo. Treinar um novo rosto para um vídeo atualiza o modelo personalizado específico com o qual o vídeo foi associado.

Cada conta tem um limite de 50 modelos de Pessoa. Se você não precisar de suporte ao modelo de várias pessoas, não atribua uma ID do modelo de Pessoa ao vídeo ao carregar/indexar ou reindexar. Nesse caso, o Video Indexer usa o modelo de Pessoa personalizado padrão em sua conta.

## <a name="create-a-new-person-model"></a>Criar um novo modelo de Pessoa

Crie um novo modelo de Pessoa na conta especificada. 

### <a name="request-url"></a>URL de Solicitação

Essa é uma solicitação POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Confira abaixo a solicitação em cURL.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>Parâmetros da solicitação 

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountId|string|Sim|Identificador global exclusivo para a conta|
|Nome|string|Sim|O nome do modelo de Pessoa|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

A resposta fornece o nome e a ID de modelo gerada do modelo de Pessoa que você acabou de criar, seguindo o formato do exemplo a seguir.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Em seguida, você deve usar o valor de **ID** para o parâmetro **personModelId** ao [Carregar um vídeo no índice](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [reindexar um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Excluir um modelo de Pessoa

Exclua um modelo de Pessoa personalizado da conta especificada. 

Depois que o modelo de Pessoa é excluído com êxito, o índice de seus vídeos atuais que estavam usando o modelo excluído permanecerá inalterado até você reindexá-los. Após a reindexação, os rostos que foram nomeados no modelo excluído não serão reconhecidos pelo Video Indexer em seus vídeos atuais que foram indexados usando esse modelo. No entanto, os rostos ainda serão detectados. Seus vídeos atuais que foram indexados usando o modelo excluído usarão agora o modelo de Pessoa padrão da sua conta. Se os rostos do modelo excluído também forem nomeados no modelo padrão da sua conta, esses rostos continuarão a ser reconhecidos nos vídeos.

### <a name="request-url"></a>URL de Solicitação

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

Confira abaixo a solicitação em cURL.
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?).

### <a name="request-parameters"></a>Parâmetros da solicitação

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountId|string|Sim|Identificador global exclusivo para a conta|
|ID|string|Sim|A ID do modelo de Pessoa (gerada quando o modelo de pessoa é criado)|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

Não há nenhum conteúdo retornado quando o modelo de Pessoa é excluído com êxito.

## <a name="get-all-person-models"></a>Obter todos os modelos de Pessoa

Obter todos os modelos de Pessoa na conta especificada. 

### <a name="request-call"></a>Chamada de solicitação

Essa é uma solicitação GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Confira abaixo a solicitação em cURL.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?).

### <a name="request-parameters"></a>Parâmetros da solicitação

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountId|string|Sim|Identificador global exclusivo para a conta|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

A resposta fornece uma lista de todos os modelos de Pessoa em sua conta (incluindo o modelo de Pessoa padrão na conta especificada) e cada um de seus nomes e IDs seguindo o formato do exemplo abaixo.

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

Você pode escolher qual modelo deseja usar para obter um vídeo usando o valor de **id** do modelo de Pessoa para o parâmetro **personModelId** ao [carregar um vídeo ao índice](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [reindexar um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Atualizar um rosto

Esse comando permite que você atualize um rosto no vídeo com um nome usando a ID do vídeo e a ID do rosto. Em seguida, isso atualiza o modelo de Pessoa que ao qual o vídeo foi associado ao carregar/indexar ou reindexar. Se nenhum modelo de Pessoa foi atribuído, ele atualiza o modelo de Pessoa padrão da conta. 

Quando isso acontece, ele reconhece as ocorrências do mesmo rosto em seus vídeos atuais que compartilham o mesmo modelo de Pessoa. O reconhecimento do rosto nos outros vídeos atuais pode levar algum tempo para entrar em vigor, pois esse é um processo em lote.

Atualize um rosto que o Video Indexer reconheceu como uma celebridade com um novo nome. O novo nome que você fornecer terá precedência sobre o reconhecimento de celebridades interno.

### <a name="request-call"></a>Chamada de solicitação

Essa é uma solicitação POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

Confira abaixo a solicitação em cURL.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?).

### <a name="request-parameters"></a>Parâmetros da solicitação

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountId|string|Sim|Identificador global exclusivo para a conta|
|videoId|string|Sim|ID do vídeo no qual o rosto que você deseja atualizar é exibido. Isso é criado quando o vídeo é carregado e indexado.|
|faceId|inteiro|Sim|ID do rosto que será atualizado. Você pode obter o faceId do índice do vídeo|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|
|Nome|string|Sim|O novo nome com o qual atualizar o rosto.|

Os nomes são exclusivos para modelos de Pessoa; portanto se você der o mesmo valor do parâmetro **name** a dois rostos diferentes no mesmo modelo de Pessoa, o Video Indexer exibirá os rostos como a mesma pessoa e os convergirá depois que você reindexar o vídeo. 

### <a name="request-body"></a>Corpo da solicitação

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

Nenhum conteúdo é retornado quando o rosto é atualizado com êxito.

## <a name="next-steps"></a>Próximas etapas

[Personalizar um modelo de Pessoa usando o site do Video Indexer](customize-person-model-with-website.md)
