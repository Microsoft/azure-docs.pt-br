---
title: Usar o Azure Video Indexer para personalizar o modelo de marcas
titlesuffix: Azure Media Services
description: Este artigo mostra como usar o Azure Video Indexer para personalizar marcas.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: df77a745ef6508b15b5a8bcde5eede0e06eb1afc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583716"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Personalizar um modelo de marcas com a API do Video Indexer

O Video Indexer dá suporte à detecção de marca de fala e texto visual durante a indexação e a reindexação de conteúdo de áudio e de vídeo. O recurso de detecção de marca identifica menções a produtos, serviços e empresas sugeridos pelo banco de dados de marcas do Bing. Por exemplo, se a Microsoft é mencionada em um conteúdo de áudio ou de vídeo ou se ela aparece no texto visual em um vídeo, o Video Indexer detecta-a como uma marca presente no conteúdo. Um modelo de marcas personalizado permite que você exclua algumas marcas da lista daquelas a serem detectadas, além de incluir marcas que devem ser parte de seu modelo e que podem não estar no banco de dados de marcas do Bing.

Para obter uma visão geral detalhada, confira [Visão geral](customize-brands-model-overview.md).

Você pode usar a API do Video Indexer para criar, usar e editar modelos personalizados de marcas detectados em um vídeo, conforme descrito neste tópico. Você também pode usar o site do Video Indexer, conforme descrito em [Personalizar o modelo de marcas usando o site do Video Indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Criar uma marca

Isso cria uma nova marca personalizada e adiciona-a ao modelo de marcas personalizado para a conta especificada.

### <a name="request-url"></a>URL de Solicitação

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand).

### <a name="request-parameters"></a>Parâmetros da solicitação

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountId|string|Sim|Identificador global exclusivo para a conta|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Além desses parâmetros, você precisa fornecer um objeto JSON de corpo da solicitação que fornece informações sobre a nova marca seguindo o formato do exemplo a seguir.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

Definir **enabled** como true coloca a marca na lista *Incluir*, para ser detectada pelo Video Indexer. Definir **enabled** como false coloca a marca na lista *Exclusões*, de modo que o Video Indexer não a detectará.

O valor **referenceUrl** pode ser qualquer site de referência para a marca, assim como um link para sua página da Wikipédia.

O valor **tags** é uma lista de marcas para a marca comercial. Isso é mostrado no campo *Categoria* da marca no site do Video Indexer. Por exemplo, a marca "Azure" pode ser marcada ou categorizada como "Nuvem".

### <a name="response"></a>Response

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

Remove uma marca do modelo de marcas personalizado para a conta especificada. A conta é especificada no parâmetro **accountId**. Quando chamada com êxito, a marca não estará mais nas listas de marcas *Inclusões* ou *Exclusões*.

### <a name="request-url"></a>URL de Solicitação

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?).

### <a name="request-parameters"></a>Parâmetros da solicitação

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountId|string|Sim|Identificador global exclusivo para a conta|
|ID|inteiro|Sim|A ID da marca (gerada quando a marca foi criada)|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

Não há nenhum conteúdo retornado quando a marca é excluída com êxito.

## <a name="get-a-specific-brand"></a>Obter uma marca específica

Isso permite que você pesquise os detalhes de uma marca no modelo de marcas personalizado para a conta especificada usando a ID da marca.

### <a name="request-url"></a>URL de Solicitação

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?).

### <a name="request-parameters"></a>Parâmetros da solicitação

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountId|string|Sim|Identificador global exclusivo para a conta|
|ID|inteiro|Sim|A ID da marca (gerada quando a marca foi criada)|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

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
> **enabled** sendo definido como **true** significa que a marca está na lista *Incluir* para o Video Indexer detectar, enquanto **enabled** sendo false significa que a marca está na lista *Excluir*, portanto, o Video Indexer não a detectará.

## <a name="update-a-specific-brand"></a>Atualiza uma marca específica

Isso permite que você pesquise os detalhes de uma marca no modelo de marcas personalizado para a conta especificada usando a ID da marca.

### <a name="request-url"></a>URL de Solicitação

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?).

### <a name="request-parameters"></a>Parâmetros da solicitação

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountId|string|Sim|Identificador global exclusivo para a conta|
|ID|inteiro|Sim|A ID da marca (gerada quando a marca foi criada)|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Além desses parâmetros, é preciso fornecer um objeto JSON de corpo da solicitação que forneça informações atualizadas sobre a nova marca que você deseja atualizar, seguindo o formato do exemplo a seguir.

```json
{
  "name": "Example",
  "enabled": false,
  "tags": ["Tag1", "NewTag2"],
  "description": "This is an update example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-28T15:52:22.3413983",
}
```

> [!NOTE]
> Neste exemplo, a tag criada no corpo da solicitação de exemplo na seção **Criar uma marca** está sendo atualizada aqui com uma nova tag e uma nova descrição. O valor **enabled** também foi alterado para false para colocá-lo na lista *Excluir*.

### <a name="response"></a>Response

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

Isso retorna todas as marcas no modelo de marcas personalizado para a conta especificada, independentemente de a marca ser destinada a estar na lista de marcas de *Inclusões* ou *Exclusões*.

### <a name="request-url"></a>URL de Solicitação

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?).

### <a name="request-parameters"></a>Parâmetros da solicitação

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountId|string|Sim|Identificador global exclusivo para a conta|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

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
> A marca nomeada *Exemplo* está na lista de *Inclusões* a serem detectadas pelo Video Indexer, enquanto a marca nomeada *Exemplo2* está na lista *Exclusões* e, portanto, não será detectada pelo Video Indexer.

## <a name="get-brands-model-settings"></a>Obter configurações de modelo de marcas

Isso retorna as configurações de modelo de marcas na conta especificada. As configurações de modelo de marcas representam o estado habilitado ou desabilitado da detecção do banco de dados de marcas do Bing. Se as marcas do Bing não estiverem habilitadas, o Video Indexer só detectará marcas do modelo de marcas personalizado da conta especificada.

### <a name="request-url"></a>URL de Solicitação

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands).

### <a name="request-parameters"></a>Parâmetros da solicitação

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountId|string|Sim|Identificador global exclusivo para a conta|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

A resposta mostra se as marcas do Bing estão habilitadas seguindo o formato do exemplo a seguir.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn** definido como true representa que as marcas do Bing estão habilitadas. Se *useBuiltin* for false, as marcas do Bing estarão desabilitadas. O valor **state** pode ser ignorado, pois ele foi preterido.

## <a name="update-brands-model-settings"></a>Atualizar configurações de modelo de marcas

Isso atualiza as configurações de modelo de marcas na conta especificada. As configurações de modelo de marcas representam o estado habilitado ou desabilitado da detecção do banco de dados de marcas do Bing. Se as marcas do Bing não estiverem habilitadas, o Video Indexer só detectará marcas do modelo de marcas personalizado da conta especificada.

### <a name="request-url"></a>URL da solicitação:
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```

[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?).

### <a name="request-parameters"></a>Parâmetros da solicitação

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountId|string|Sim|Identificador global exclusivo para a conta|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Além desses parâmetros, você precisa fornecer um objeto JSON de corpo da solicitação que fornece informações sobre a nova marca seguindo o formato do exemplo a seguir.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> **useBuiltIn** definido como true representa que as marcas do Bing estão habilitadas. Se *useBuiltin* for false, as marcas do Bing estarão desabilitadas.

### <a name="response"></a>Response

Não há nenhum conteúdo retornado quando a configuração de modelo de marca é atualizada com êxito.

## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de Marcas usando o site](customize-brands-model-with-website.md)
