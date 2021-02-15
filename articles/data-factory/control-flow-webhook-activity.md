---
title: Atividade de webhook no Azure Data Factory
description: A atividade de webhook não continua a execução do pipeline até que ele valide o conjunto de dado anexado com determinados critérios especificados pelo usuário.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 435cad4d1ef002261b194431dbdb787e072808f5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361478"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Atividade de webhook no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uma atividade de webhook pode controlar a execução de pipelines por meio de seu código personalizado. Com a atividade de webhook, o código dos clientes pode chamar um ponto de extremidade e passá-lo para uma URL de retorno de chamada. A execução do pipeline aguarda a invocação de retorno de chamada antes de prosseguir para a próxima atividade.

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

Propriedade | Descrição | Valores permitidos | Necessária
-------- | ----------- | -------------- | --------
**name** | O nome da atividade de webhook. | String | Sim |
**type** | Deve ser definido como "webhook". | String | Sim |
**forma** | O método da API REST para o ponto de extremidade de destino. | Cadeia de caracteres. O tipo com suporte é "POST". | Sim |
**url** | O ponto de extremidade e o caminho de destino. | Uma cadeia de caracteres ou uma expressão com o valor de **ResultType** de uma cadeia de caracteres. | Sim |
**conector** | Cabeçalhos que são enviados para a solicitação. Aqui está um exemplo que define o idioma e o tipo em uma solicitação: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` . | Uma cadeia de caracteres ou uma expressão com o valor de **ResultType** de uma cadeia de caracteres. | Sim. Um `Content-Type` cabeçalho como `"headers":{ "Content-Type":"application/json"}` é necessário. |
**body** | Representa o conteúdo enviado para o ponto de extremidade. | JSON válido ou uma expressão com o valor de **ResultType** de JSON. Consulte [esquema de carga de solicitação](./control-flow-web-activity.md#request-payload-schema) para o esquema da carga de solicitação. | Sim |
**Authentication** | O método de autenticação usado para chamar o ponto de extremidade. Os tipos com suporte são "básico" e "ClientCertificate". Para obter mais informações, consulte [Autenticação](./control-flow-web-activity.md#authentication). Se a autenticação não for necessária, exclua essa propriedade. | Uma cadeia de caracteres ou uma expressão com o valor de **ResultType** de uma cadeia de caracteres. | Não |
**timeout** | Por quanto tempo a atividade aguarda o retorno de chamada especificado por **callBackUri** ser invocado. O valor padrão é 10 minutos ("00:10:00"). Os valores têm o formato TimeSpan *d*. *hh*:*mm*:*SS*. | String | Não |
**Relatar status no retorno de chamada** | Permite que um usuário relate o status de falha de uma atividade de webhook. | Boolean | Não |

## <a name="authentication"></a>Autenticação

Uma atividade de webhook dá suporte aos seguintes tipos de autenticação.

### <a name="none"></a>Nenhum

Se a autenticação não for necessária, não inclua a propriedade de **autenticação** .

### <a name="basic"></a>Básico

Especifique o nome de usuário e a senha a serem usados com a autenticação básica.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificado do cliente

Especifique o conteúdo codificado em Base64 de um arquivo PFX e uma senha.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Identidade gerenciada

Use a identidade gerenciada do data factory para especificar o URI de recurso para o qual o token de acesso é solicitado. Para chamar a API de Gerenciamento de Recursos do Azure, use `https://management.azure.com/`. Para obter mais informações sobre como as identidades gerenciadas funcionam, consulte a [visão geral identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Se sua data factory estiver configurada com um repositório git, você deverá armazenar suas credenciais no Azure Key Vault para usar a autenticação básica ou de certificado do cliente. Azure Data Factory não armazena senhas no git.

## <a name="additional-notes"></a>Observações adicionais

Data Factory passa a propriedade adicional **callBackUri** no corpo enviado para o ponto de extremidade da URL. Data Factory espera que esse URI seja invocado antes do valor de tempo limite especificado. Se o URI não for invocado, a atividade falhará com o status "TimedOut".

A atividade de webhook falha quando a chamada para o ponto de extremidade personalizado falha. Qualquer mensagem de erro pode ser adicionada ao corpo do retorno de chamada e usada em uma atividade posterior.

Para cada chamada à API REST, o cliente atinge o tempo limite se o ponto de extremidade não responder dentro de um minuto. Esse comportamento é a prática recomendada de HTTP padrão. Para corrigir esse problema, implemente um padrão de 202. No caso atual, o ponto de extremidade retorna 202 (aceito) e o cliente pesquisa.

O tempo limite de um minuto na solicitação não tem nada a ver com o tempo limite da atividade. O último é usado para aguardar o retorno de chamada especificado por **callbackUri**.

O corpo passado de volta para o URI de retorno de chamada deve ser um JSON válido. Defina o cabeçalho `Content-Type` como `application/json`.

Ao usar o **status do relatório na propriedade de retorno de chamada** , você deve adicionar o seguinte código ao corpo ao fazer o retorno de chamada:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>Próximas etapas

Consulte as seguintes atividades de fluxo de controle com suporte pelo Data Factory:

- [Atividade Condição Se](control-flow-if-condition-activity.md)
- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)