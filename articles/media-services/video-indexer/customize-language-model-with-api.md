---
title: Usar as APIs do Video Indexer para personalizar um modelo de Linguagem – Azure
titlesuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo de Linguagem com as APIs do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: ca1e66d20b19c1a5b85a4f4ff1c433331116bee7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553735"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>Personalizar um modelo de Linguagem com as APIs do Video Indexer

O Video Indexer permite criar modelos de Linguagem personalizados para personalizar o reconhecimento de fala, carregando o texto de adaptação, ou seja, o texto do domínio a cujo vocabulário você deseja que o mecanismo se adapte. Depois de treinar seu modelo, novas palavras que apareçam no texto de adaptação serão reconhecidas. 

Para obter uma visão geral detalhada e as melhores práticas para modelos de Linguagem personalizados, confira [Personalizar um modelo de Linguagem com o Video Indexer](customize-language-model-overview.md).

Use as APIs do Video Indexer para criar e editar modelos de Linguagem personalizados em sua conta, conforme descrito neste tópico. Use também o site, conforme descrito em [Personalizar o modelo de Linguagem usando o site do Video Indexer](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Criar um modelo de Linguagem

O comando a seguir cria um modelo de Linguagem personalizado na conta especificada. Você pode carregar arquivos para o modelo de Linguagem nessa chamada. Como alternativa, você pode criar o modelo de Linguagem aqui e carregar arquivos para o modelo mais tarde, atualizando o modelo de Linguagem.

> [!NOTE]
> Você ainda deverá treinar o modelo com seus arquivos habilitados para o modelo para aprender o conteúdo dos arquivos. As instruções sobre como treinar uma linguagem se encontram na próxima seção.

### <a name="request-url"></a>URL de Solicitação

Essa é uma solicitação POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Confira abaixo a solicitação em cURL.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}&modelName={modelName}&language={language}"

--data-ascii "{body}" 
```

[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>Parâmetros da solicitação

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountId|string|Sim|Identificador global exclusivo para a conta|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|
|modelName|string|Sim|O nome do modelo de Linguagem|
|Linguagem|string|Sim|O idioma do modelo de Linguagem. <br/>O parâmetro **language** precisa receber a linguagem no formato BCP-47 'tag do idioma-região' (por exemplo: 'en-US'). Os idiomas compatíveis são inglês (en-US), alemão (de-DE), espanhol (es-SP), árabe (ar-EG), francês (fr-FR), hindi (hi-HI), italiano (it-IT), japonês (ja-JP), português (pt-BR), russo (ru-RU) e chinês (zh-CN).  |

### <a name="request-body"></a>Corpo da solicitação

Para carregar os arquivos a serem adicionados ao modelo de Linguagem, é necessário carregar os arquivos no corpo usando dados de formulário, além de fornecer valores para os parâmetros necessários acima. Há duas maneiras de fazer isso: 

1. A chave será o nome de arquivo e o valor será o arquivo .txt
2. A chave será o nome de arquivo e o valor será uma URL para o arquivo .txt

### <a name="response"></a>Response

A resposta fornece metadados sobre o modelo de Linguagem recém-criado, junto com metadados sobre cada um dos arquivos do modelo seguindo o formato da saída JSON de exemplo.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Treinar um modelo de Linguagem

O comando a seguir treina um modelo de Linguagem personalizado na conta especificada com o conteúdo dos arquivos que foram carregados e habilitados no modelo de Linguagem. 

> [!NOTE]
> Primeiro é necessário criar o modelo de Linguagem e carregar seus arquivos. Você pode carregar arquivos ao criar o modelo de Linguagem ou atualizá-lo. 

### <a name="request-url"></a>URL de Solicitação

Essa é uma solicitação PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}
```

Confira abaixo a solicitação em cURL.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}"
```
 
[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train).

### <a name="request-parameters"></a>Parâmetros da solicitação

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountID|string|Sim|Identificador global exclusivo para a conta|
|modelId|string|Sim|A ID do modelo de Linguagem (gerada quando o modelo de Linguagem é criado)|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

A resposta fornece metadados sobre o modelo de Linguagem recém-treinado, junto com metadados sobre cada um dos arquivos do modelo seguindo o formato da saída JSON de exemplo.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Em seguida, você deverá usar o valor **id** do modelo de linguagem para o parâmetro **linguisticModelId** ao [carregar um vídeo no índice](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) e para o parâmetro **languageModelId** ao [reindexar um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-language-model"></a>Excluir um modelo de Linguagem

O comando a seguir exclui um modelo de Linguagem personalizado da conta especificada. Qualquer vídeo que estava usando o modelo de Linguagem excluído manterá o mesmo índice até que você reindexe o vídeo. Se você reindexar o vídeo, poderá atribuir um novo modelo de Linguagem a ele. Caso contrário, o Video Indexer usará seu modelo padrão para reindexar o vídeo.

### <a name="request-url"></a>URL de Solicitação

Essa é uma solicitação DELETE.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Confira abaixo a solicitação em cURL.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete).

### <a name="request-parameters"></a>Parâmetros da solicitação 

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountID|string|Sim|Identificador global exclusivo para a conta|
|modelId|string|Sim|A ID do modelo de Linguagem (gerada quando o modelo de Linguagem é criado)|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

Nenhum conteúdo é retornado quando o modelo de Linguagem é excluído com êxito.

## <a name="update-a-language-model"></a>Atualizar um modelo de Linguagem

O comando a seguir atualiza um modelo de Linguagem personalizado de pessoa na conta especificada.

> [!NOTE]
> Você já precisa ter criado um modelo de Linguagem. Use essa chamada para habilitar ou desabilitar todos os arquivos no modelo, atualizar o nome do modelo de Linguagem e carregar os arquivos a serem adicionados ao modelo de linguagem.

### <a name="request-url"></a>URL de Solicitação

Essa é uma solicitação PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}[&modelName][&enable]
```

Confira abaixo a solicitação em cURL.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}?modelName={string}&enable={string}"

--data-ascii "{body}" 
```
 
[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update).

### <a name="request-parameters"></a>Parâmetros da solicitação 

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountID|string|Sim|Identificador global exclusivo para a conta|
|modelId|string|Sim|A ID do modelo de Linguagem (gerada quando o modelo de Linguagem é criado)|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|
|modelName|string|Não |Novo nome que pode ser dado ao modelo|
|enable|boolean|Não |Escolha se todos os arquivos desse modelo estão habilitados (true) ou desabilitados (false)|

### <a name="request-body"></a>Corpo da solicitação

Para carregar os arquivos a serem adicionados ao modelo de Linguagem, é necessário carregar os arquivos no corpo usando dados de formulário, além de fornecer valores para os parâmetros necessários acima. Há duas maneiras de fazer isso: 

1. A chave será o nome de arquivo e o valor será o arquivo .txt
2. A chave será o nome de arquivo e o valor será uma URL para o arquivo .txt

### <a name="response"></a>Response

A resposta fornece metadados sobre o modelo de Linguagem recém-treinado, junto com metadados sobre cada um dos arquivos do modelo seguindo o formato da saída JSON de exemplo.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```
Use a **ID** dos arquivos retornada aqui para baixar o conteúdo do arquivo.

## <a name="update-a-file-from-a-language-model"></a>Atualizar um arquivo com base em um modelo de Linguagem

O comando a seguir permite atualizar o nome e **habilitar** o estado de um arquivo em um modelo de Linguagem personalizado na conta especificada.

### <a name="request-url"></a>URL de Solicitação

Essa é uma solicitação PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}[&fileName][&enable]
```

Confira abaixo a solicitação em cURL.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}?fileName={string}&enable={string}"
```
 
[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update).

### <a name="request-parameters"></a>Parâmetros da solicitação 

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountId|string|Sim|Identificador global exclusivo para a conta|
|modelId|string|Sim|ID do modelo de Linguagem que contém o arquivo (gerada quando o modelo de Linguagem é criado)|
|fileId|string|Sim|ID do arquivo que está sendo atualizado (gerada quando o arquivo é carregado na criação ou na atualização do modelo de Linguagem)|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|
|fileName|string|Não |Nome para o qual atualizar o nome de arquivo|
|enable|boolean|Não |Atualiza se esse arquivo está habilitado (true) ou desabilitado (false) no modelo de linguagem|

### <a name="request-body"></a>Corpo da solicitação

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

A resposta fornece metadados sobre o arquivo que você atualizou seguindo o formato da saída JSON de exemplo abaixo.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
Use a **ID** do arquivo retornada aqui para baixar o conteúdo do arquivo.

## <a name="get-a-specific-language-model"></a>Obter um modelo de Linguagem específico

O comando a seguir retorna informações sobre o modelo de Linguagem especificado na conta especificada, como o idioma e os arquivos que estão no modelo de Linguagem. 

### <a name="request-url"></a>URL de Solicitação

Essa é uma solicitação GET.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Confira abaixo a solicitação em cURL.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get).

### <a name="request-parameters-and-request-body"></a>Parâmetros de solicitação e corpo da solicitação

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountID|string|Sim|Identificador global exclusivo para a conta|
|modelId|string|Sim|A ID do modelo de Linguagem (gerada quando o modelo de Linguagem é criado)|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

A resposta fornece metadados sobre o modelo de Linguagem especificado, junto com metadados sobre cada um dos arquivos do modelo seguindo o formato da saída JSON de exemplo abaixo.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Use a **ID** do arquivo retornada aqui para baixar o conteúdo do arquivo.

## <a name="get-all-the-language-models"></a>Obter todos os modelos de Linguagem

O comando a seguir retorna todos os modelos de Linguagem personalizados na conta especificada em uma lista.

### <a name="request-url"></a>URL de Solicitação

Essa é uma solicitação GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}
```

Confira abaixo a solicitação em cURL.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}"
```
 
[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get).

### <a name="request-parameters"></a>Parâmetros da solicitação

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountID|string|Sim|Identificador global exclusivo para a conta|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

A resposta fornece uma lista de todos os modelos de Linguagem em sua conta e cada um de seus metadados e arquivos, seguindo o formato da saída JSON de exemplo abaixo.

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Excluir um arquivo de um modelo de Linguagem

O comando a seguir exclui o arquivo especificado do modelo de Linguagem especificado na conta especificada. 

### <a name="request-url"></a>URL de Solicitação

Essa é uma solicitação DELETE.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}
```

Confira abaixo a solicitação em cURL.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete).

### <a name="request-parameters"></a>Parâmetros da solicitação 

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountID|string|Sim|Identificador global exclusivo para a conta|
|modelId|string|Sim|ID do modelo de Linguagem que contém o arquivo (gerada quando o modelo de Linguagem é criado)|
|fileId|string|Sim|ID do arquivo que está sendo atualizado (gerada quando o arquivo é carregado na criação ou na atualização do modelo de Linguagem)|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

Nenhum conteúdo é retornado quando o arquivo é excluído do modelo de Linguagem com êxito.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Obter os metadados sobre um arquivo de um modelo de Linguagem

Isso retorna o conteúdo e os metadados sobre o arquivo especificado do modelo de Linguagem escolhido em sua conta.

### <a name="request-url"></a>URL de Solicitação

Essa é uma solicitação GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Confira abaixo a solicitação em cURL.
```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model).

### <a name="request-parameters"></a>Parâmetros da solicitação 

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountID|string|Sim|Identificador global exclusivo para a conta|
|modelId|string|Sim|ID do modelo de linguagem que contém o arquivo (gerada quando o modelo de linguagem é criado)|
|fileId|string|Sim|ID do arquivo que está sendo atualizado (gerada quando o arquivo é carregado na criação ou na atualização do modelo de linguagem)|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

A resposta fornece o conteúdo e os metadados do arquivo no formato JSON, semelhante a este:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> O conteúdo desse arquivo de exemplo são as palavras "olá" e "mundo" em duas linhas separadas.

## <a name="download-a-file-from-a-language-model"></a>Baixar um arquivo de um modelo de Linguagem

O comando a seguir baixa um arquivo de texto que traz o conteúdo do arquivo especificado do modelo de Linguagem especificado na conta especificada. Esse arquivo de texto deve corresponder ao conteúdo do arquivo de texto que foi originalmente carregado.

### <a name="request-url"></a>URL de Solicitação
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}
```

Confira abaixo a solicitação em cURL.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}"
```
 
[Confira os parâmetros necessários e teste-os usando o portal do desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?).

### <a name="request-parameters"></a>Parâmetros da solicitação 

|**Nome**|**Tipo**|**Obrigatório**|**Descrição**|
|---|---|---|---|
|location|string|Sim|A região do Azure para a qual a chamada deve ser roteada. Para obter mais informações, confira [Regiões do Azure e Video Indexer](regions.md).|
|accountID|string|Sim|Identificador global exclusivo para a conta|
|modelId|string|Sim|ID do modelo de Linguagem que contém o arquivo (gerada quando o modelo de Linguagem é criado)|
|fileId|string|Sim|ID do arquivo que está sendo atualizado (gerada quando o arquivo é carregado na criação ou na atualização do modelo de Linguagem)|
|accessToken|string|Sim|Token de acesso (precisa ser do escopo [Token de Acesso da Conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) a ser autenticado na chamada. Os tokens de acesso expiram em 1 hora.|

### <a name="request-body"></a>Corpo da solicitação 

Nenhum outro corpo da solicitação é necessário para essa chamada.

### <a name="response"></a>Response

A resposta será o download de um arquivo de texto com o conteúdo do arquivo no formato JSON. 

## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de Linguagem usando o site](customize-language-model-with-website.md)
