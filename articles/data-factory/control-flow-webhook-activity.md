---
title: Atividade de Webhook no Azure Data Factory | Microsoft Docs
description: A atividade de Webhook não continuar a execução do pipeline até que ele valida o conjunto de dados anexado com determinados critérios especificados pelo usuário.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 6ec43b06ce266b9ceaddb5dd21cbf52f509d6596
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764298"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Atividade de Webhook no Azure Data Factory
Você pode usar uma atividade de gancho da web para controlar a execução de pipelines por meio de seu código personalizado. Usando a atividade de webhook, os clientes podem chamar um ponto de extremidade e passar uma URL de retorno de chamada. A execução de pipeline aguarda o retorno de chamada a ser invocado antes de prosseguir para a próxima atividade.

## <a name="syntax"></a>Sintaxe

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```


## <a name="type-properties"></a>Propriedades de tipo



Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
Nome | Nome da atividade de gancho da web | Cadeia de caracteres | Sim |
Tipo | Deve ser definido como **WebHook**. | Cadeia de caracteres | Sim |
method | Método da API REST para o ponto de extremidade de destino. | Cadeia de caracteres. Tipos com suporte: 'POST' | Sim |
url | Ponto de extremidade de destino e o caminho | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres). | Sim |
headers | Cabeçalhos que são enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: "cabeçalhos": {"Accept-Language": "en-us", "Content-Type": "application/json"}. | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Sim, o cabeçalho Content-Type é necessário. "headers":{ "Content-Type":"application/json"} |
body | Representa o conteúdo enviado para o ponto de extremidade. | O corpo passadas novamente para a retorno de chamada URI deve ser um JSON válido. Consulte o esquema da carga de solicitação na seção [Esquema de carga de solicitação](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0). | Sim |
Autenticação | Método de autenticação usado para chamar o ponto de extremidade. Tipos com suporte são "Básico" ou "ClientCertificate." Para obter mais informações, consulte a seção [Autenticação](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0). Se a autenticação não for necessária, exclua essa propriedade. | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Não  |
Tempo limite | Quanto tempo a atividade irá esperar para o &#39;o callBackUri&#39; a ser invocado. Quanto tempo a atividade aguardará o 'callBackUri' a ser invocado. Valor padrão é 10mins ("00:10:00"). O formato é o período de tempo ou seja, d.hh:mm:ss | Cadeia de caracteres | Não  |

## <a name="additional-notes"></a>Observações adicionais

O Azure Data Factory passará uma propriedade adicional "o callBackUri" no corpo para o ponto de extremidade de url e espera que esse uri a ser invocado antes do valor de tempo limite especificado. Se o uri não é chamado, a atividade falhará com o status 'TimedOut'.

A atividade de gancho da web em si não apenas quando a chamada para o ponto de extremidade personalizado falha. Qualquer mensagem de erro pode ser adicionada no corpo do retorno de chamada e usada em uma atividade subsequente.

## <a name="next-steps"></a>Próximas etapas
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory:

- [Atividade de Condição Se](control-flow-if-condition-activity.md)
- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
