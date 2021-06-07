---
title: Personalizar um modelo de linguagem com a API Video Indexer
titlesuffix: Azure Media Services
description: Saiba como personalizar um modelo de linguagem com a API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: kumud
ms.openlocfilehash: f373afae03357ffb65eb459f806fe441e29b21b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87047079"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Personalizar um modelo de linguagem com a API de Video Indexer

O Video Indexer permite criar modelos de Linguagem personalizados para personalizar o reconhecimento de fala, carregando o texto de adaptação, ou seja, o texto do domínio a cujo vocabulário você deseja que o mecanismo se adapte. Depois de treinar seu modelo, novas palavras que apareçam no texto de adaptação serão reconhecidas.

Para obter uma visão geral detalhada e as melhores práticas para modelos de Linguagem personalizados, confira [Personalizar um modelo de Linguagem com o Video Indexer](customize-language-model-overview.md).

Use as APIs do Video Indexer para criar e editar modelos de Linguagem personalizados em sua conta, conforme descrito neste tópico. Use também o site, conforme descrito em [Personalizar o modelo de Linguagem usando o site do Video Indexer](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Criar um modelo de Linguagem

A API [criar um modelo de linguagem](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) cria um novo modelo de linguagem personalizado na conta especificada. Você pode carregar arquivos para o modelo de Linguagem nessa chamada. Como alternativa, você pode criar o modelo de Linguagem aqui e carregar arquivos para o modelo mais tarde, atualizando o modelo de Linguagem.

> [!NOTE]
> Você ainda deverá treinar o modelo com seus arquivos habilitados para o modelo para aprender o conteúdo dos arquivos. As instruções sobre como treinar uma linguagem se encontram na próxima seção.

Para carregar arquivos a serem adicionados ao modelo de linguagem, você deve carregar arquivos no corpo usando FormData, além de fornecer valores para os parâmetros necessários acima. Há duas maneiras de realizar essa tarefa:

* A chave será o nome do arquivo e o valor será o arquivo txt.
* A chave será o nome do arquivo e o valor será uma URL para o arquivo txt.

### <a name="response"></a>Resposta

A resposta fornece metadados sobre o modelo de linguagem criado recentemente junto com os metadados em cada um dos arquivos do modelo, seguindo o formato deste exemplo de saída JSON:

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

A API [treinar um modelo de linguagem](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) treina um modelo de linguagem personalizado na conta especificada com o conteúdo nos arquivos que foram carregados e habilitados no modelo de linguagem.

> [!NOTE]
> Primeiro é necessário criar o modelo de Linguagem e carregar seus arquivos. Você pode carregar arquivos ao criar o modelo de idioma ou ao atualizar o modelo de linguagem.

### <a name="response"></a>Resposta

A resposta fornece metadados sobre o modelo de linguagem recentemente treinado junto com metadados em cada um dos arquivos do modelo, seguindo o formato deste exemplo de saída JSON:

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

O retornado `id` é uma ID exclusiva usada para distinguir entre modelos de linguagem, enquanto `languageModelId` é usado para [carregar um vídeo para indexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) e [reindexar uma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API de vídeo (também conhecido como `linguisticModelId` em video indexer carregar/reindexar APIs).

## <a name="delete-a-language-model"></a>Excluir um modelo de Linguagem

A API [excluir um modelo de linguagem](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) exclui um modelo de idioma personalizado da conta especificada. Qualquer vídeo que estava usando o modelo de linguagem excluído manterá o mesmo índice até que você reindexe o vídeo. Se você reindexar o vídeo, poderá atribuir um novo modelo de linguagem ao vídeo. Caso contrário, Video Indexer usará seu modelo padrão para reindexar o vídeo.

### <a name="response"></a>Resposta

Não há nenhum conteúdo retornado quando o modelo de linguagem é excluído com êxito.

## <a name="update-a-language-model"></a>Atualizar um modelo de Linguagem

A API [atualizar um modelo de linguagem](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) atualiza um modelo de pessoa de idioma personalizado na conta especificada.

> [!NOTE]
> Você já precisa ter criado um modelo de Linguagem. Use essa chamada para habilitar ou desabilitar todos os arquivos no modelo, atualizar o nome do modelo de Linguagem e carregar os arquivos a serem adicionados ao modelo de linguagem.

Para carregar arquivos a serem adicionados ao modelo de linguagem, você deve carregar arquivos no corpo usando FormData, além de fornecer valores para os parâmetros necessários acima. Há duas maneiras de realizar essa tarefa:

* A chave será o nome do arquivo e o valor será o arquivo txt.
* A chave será o nome do arquivo e o valor será uma URL para o arquivo txt.

### <a name="response"></a>Resposta

A resposta fornece metadados sobre o modelo de linguagem recentemente treinado junto com metadados em cada um dos arquivos do modelo, seguindo o formato deste exemplo de saída JSON:

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

Use o `id` dos arquivos retornados na resposta para baixar o conteúdo do arquivo.

## <a name="update-a-file-from-a-language-model"></a>Atualizar um arquivo com base em um modelo de Linguagem

A [atualização de um arquivo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) permite que você atualize o nome e o `enable` estado de um arquivo em um modelo de idioma personalizado na conta especificada.

### <a name="response"></a>Resposta

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

Use o `id` do arquivo retornado na resposta para baixar o conteúdo do arquivo.

## <a name="get-a-specific-language-model"></a>Obter um modelo de Linguagem específico

A API [Get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) retorna informações sobre o modelo de idioma especificado na conta especificada, como idioma e os arquivos que estão no modelo de linguagem.

### <a name="response"></a>Resposta

A resposta fornece metadados sobre o modelo de linguagem especificado junto com os metadados em cada um dos arquivos do modelo, seguindo o formato desta saída JSON de exemplo:

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

Use o `id` do arquivo retornado na resposta para baixar o conteúdo do arquivo.

## <a name="get-all-the-language-models"></a>Obter todos os modelos de Linguagem

A API [obter tudo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) retorna todos os modelos de idioma personalizados na conta especificada em uma lista.

### <a name="response"></a>Resposta

A resposta fornece uma lista de todos os modelos de linguagem em sua conta e de cada um de seus metadados e arquivos seguindo o formato desta saída JSON de exemplo:

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

A API de [exclusão](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) exclui o arquivo especificado do modelo de idioma especificado na conta especificada.

### <a name="response"></a>Resposta

Não há nenhum conteúdo retornado quando o arquivo é excluído do modelo de idioma com êxito.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Obter os metadados sobre um arquivo de um modelo de Linguagem

Os [metadados Get de uma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) API de arquivo retornam o conteúdo e os metadados do arquivo especificado do modelo de linguagem escolhido em sua conta.

### <a name="response"></a>Resposta

A resposta fornece o conteúdo e os metadados do arquivo no formato JSON, semelhante a este exemplo:

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

## <a name="download-a-file-from-a-language-model"></a>Baixar um arquivo de um modelo de idioma

O [download de uma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) API de arquivo baixa um arquivo de texto que contém o conteúdo do arquivo especificado do modelo de idioma especificado na conta especificada. Esse arquivo de texto deve corresponder ao conteúdo do arquivo de texto que foi originalmente carregado.

### <a name="response"></a>Resposta

A resposta será o download de um arquivo de texto com o conteúdo do arquivo no formato JSON.

## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de Linguagem usando o site](customize-language-model-with-website.md)
