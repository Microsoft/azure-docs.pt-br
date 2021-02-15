---
title: A API REST de conversão de ativos
description: Descreve como converter um ativo por meio da API REST
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 889a70005f1cbabaad525147b4661ea04886138a
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445601"
---
# <a name="use-the-model-conversion-rest-api"></a>Usar a API REST de conversão de modelo

O serviço de [conversão de modelo](model-conversion.md) é controlado por meio de uma [API REST](https://en.wikipedia.org/wiki/Representational_state_transfer). Este artigo descreve os detalhes da API do serviço de conversão.

## <a name="regions"></a>Regiões

Consulte a [lista de regiões disponíveis](../../reference/regions.md) para as URLs de base para as quais enviar as solicitações.

## <a name="common-headers"></a>Cabeçalhos comuns

### <a name="common-request-headers"></a>Cabeçalhos de solicitação comuns

Esses cabeçalhos devem ser especificados para todas as solicitações:

- O cabeçalho **Authorization** deve ter o valor de "portador [ *token* ]", onde [ *token* ] é um [token de acesso de serviço](../tokens.md).

### <a name="common-response-headers"></a>Cabeçalhos de resposta comuns

Todas as respostas contêm estes cabeçalhos:

- O cabeçalho **MS-CV** contém uma cadeia de caracteres exclusiva que pode ser usada para rastrear a chamada dentro do serviço.

## <a name="endpoints"></a>Pontos de extremidade

O serviço de conversão fornece três pontos de extremidade da API REST para:

- Inicie a conversão de modelo usando uma conta de armazenamento vinculada à sua conta de renderização remota do Azure. 
- Inicie a conversão de modelo usando as *assinaturas de acesso compartilhado (SAS)* fornecidas.
- consultar o status da conversão

### <a name="start-conversion-using-a-linked-storage-account"></a>Iniciar conversão usando uma conta de armazenamento vinculada
Sua conta de renderização remota do Azure precisa ter acesso à conta de armazenamento fornecida seguindo as etapas sobre como [vincular contas de armazenamento](../create-an-account.md#link-storage-accounts).

| Ponto de extremidade | Método |
|-----------|:-----------|
| /v1/accounts/ **AccountId** /Conversions/Create | POST |

Retorna a ID da conversão em andamento, encapsulada em um documento JSON. O nome do campo é "conversãoid".

#### <a name="request-body"></a>Corpo da solicitação

> [!NOTE]
> Tudo em `input.folderPath` será recuperado para executar a conversão no Azure. Se `input.folderPath` não for especificado, todo o conteúdo do contêiner será recuperado. Todos os BLOBs e pastas que são recuperados devem ter [nomes de arquivo do Windows válidos](/windows/win32/fileio/naming-a-file#naming-conventions).

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Iniciar conversão usando assinaturas de acesso compartilhado fornecidas
Se sua conta do ARR não estiver vinculada à sua conta de armazenamento, essa interface REST permitirá que você forneça acesso usando *SAS (assinaturas de acesso compartilhado)*.

| Ponto de extremidade | Método |
|-----------|:-----------|
| /v1/accounts/ **AccountId** /Conversions/createWithSharedAccessSignature | POST |

Retorna a ID da conversão em andamento, encapsulada em um documento JSON. O nome do campo é `conversionId` .

#### <a name="request-body"></a>Corpo da solicitação

O corpo da solicitação é o mesmo da chamada de criação REST acima, mas a entrada e a saída contêm *tokens de SAS (assinaturas de acesso compartilhado)*. Esses tokens fornecem acesso à conta de armazenamento para ler a entrada e gravar o resultado da conversão.

> [!NOTE]
> Esses tokens de URI de SAS são as cadeias de caracteres de consulta e não o URI completo. 

> [!NOTE]
> Tudo em `input.folderPath` será recuperado para executar a conversão no Azure. Se `input.folderPath` não for especificado, todo o conteúdo do contêiner será recuperado. Todos os BLOBs e pastas que são recuperados devem ter [nomes de arquivo do Windows válidos](/windows/win32/fileio/naming-a-file#naming-conventions).

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>Status da conversão de sondagem
O status de uma conversão em andamento iniciada com uma das chamadas REST acima pode ser consultado usando a seguinte interface:


| Ponto de extremidade | Método |
|-----------|:-----------|
| /v1/accounts/ **AccountId** /Conversions/ **conversionid** | GET |

Retorna um documento JSON com um campo "status" que pode ter os seguintes valores:

- Criação
- Executado
- "Success"
- Failure

Se o status for "Failure", haverá um campo "Error" adicional com um subcampo "Message" contendo informações de erro. Logs adicionais serão carregados no seu contêiner de saída.

## <a name="list-conversions"></a>Conversões de lista

Para obter uma lista de todas as conversões de uma conta, use a interface:

| Ponto de extremidade | Método |
|-----------|:-----------|
| /v1/accounts/ **AccountId** /Conversions? skiptoken = **skiptoken** | GET |

| Parâmetro | Obrigatório |
|-----------|:-----------|
| accountID | Yes |
| skiptoken | No |

Retorna um documento JSON que contém uma matriz de conversões e seus detalhes. Essa consulta retorna um máximo de 50 conversões por vez. Na situação em que há mais conversões a serem recuperadas, a resposta conterá uma propriedade **nextLink** contendo o skipToken que pode ser consultado para recuperar o próximo conjunto de resultados.

## <a name="next-steps"></a>Próximas etapas

- [Usar o armazenamento de Blobs do Azure para conversão de modelo](blob-storage.md)
- [Conversão de modelo](model-conversion.md)