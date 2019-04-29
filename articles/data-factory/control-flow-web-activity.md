---
title: Atividade da Web no Azure Data Factory | Microsoft Docs
description: Saiba como você pode usar a atividade da Web, uma das atividades de fluxo de controle com suporte pelo Data Factory, para invocar um ponto de extremidade REST de um pipeline.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: shlo
ms.openlocfilehash: 7edaa4c673c2cb94dc5bd0245ce66c9fe6a7dd3c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764281"
---
# <a name="web-activity-in-azure-data-factory"></a>Atividade da Web no Azure Data Factory
A atividade da Web pode ser usada para chamar um ponto de extremidade REST personalizado de um pipeline do Data Factory. Você pode passar conjuntos de dados e serviços vinculados a serem consumidos e acessados pela atividade.

## <a name="syntax"></a>Sintaxe

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{
         "Content-Type":"application/json"
      },
      "authentication":{
         "type":"ClientCertificate",
         "pfx":"****",
         "password":"****"
      },
      "datasets":[
         {
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{
               ...
            }
         }
      ],
      "linkedServices":[
         {
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
Nome | Nome da atividade da Web | Cadeia de caracteres | Sim
tipo | Deve ser definido como **WebActivity**. | Cadeia de caracteres | Sim
method | Método da API REST para o ponto de extremidade de destino. | Cadeia de caracteres. <br/><br/>Tipos com suporte: "GET", "POST", "PUT" | Sim
url | Ponto de extremidade de destino e o caminho | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres). A atividade atingirá o tempo limite em 1 minuto com um erro se não receber uma resposta do ponto de extremidade. | Sim
headers | Cabeçalhos que são enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Sim, o cabeçalho Content-Type é necessário. `"headers":{ "Content-Type":"application/json"}`
body | Representa o conteúdo enviado para o ponto de extremidade.  | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres). <br/><br/>Consulte o esquema da carga de solicitação na seção [Esquema de carga de solicitação](#request-payload-schema). | Necessário para os métodos PUT/POST.
Autenticação | Método de autenticação usado para chamar o ponto de extremidade. Os tipos com suporte são "Basic ou ClientCertificate." Para obter mais informações, consulte a seção [Autenticação](#authentication). Se a autenticação não for necessária, exclua essa propriedade. | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Não 
conjuntos de dados | Lista de conjuntos de dados passados para o ponto de extremidade. | Matriz de referências do conjunto de dados. Pode ser uma matriz vazia. | Sim
linkedServices | Lista de serviços vinculados passado ao ponto de extremidade. | Matriz de referências de serviço vinculado. Pode ser uma matriz vazia. | Sim

> [!NOTE]
> Os pontos de extremidade REST que invoca a atividade da Web invoca devem retornar para uma resposta do JSON de tipo. A atividade atingirá o tempo limite em 1 minuto com um erro se não receber uma resposta do ponto de extremidade.

A tabela a seguir mostra os requisitos para o conteúdo JSON:

| Tipo de valor | Corpo da solicitação | Corpo da resposta |
|---|---|---|
|Objeto JSON | Com suporte | Com suporte |
|Matriz JSON | Com suporte <br/>(No momento, matrizes JSON não funcionam como resultado de um bug. A correção está em andamento.) | Sem suporte |
| Valor JSON | Com suporte | Sem suporte |
| Tipo não-JSON | Sem suporte | Sem suporte |
||||

## <a name="authentication"></a>Authentication

### <a name="none"></a>Nenhum
Se a autenticação não for necessária, não inclua a propriedade "authentication".

### <a name="basic"></a>Basic
Especifique o nome de usuário e senha a serem usados com a autenticação básica.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificado do cliente
Especifique o conteúdo codificado em base64 de um arquivo PFX e a senha.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Identidade Gerenciada

Especifique o URI do recurso para o qual o token de acesso será solicitado usando a identidade gerenciada para o data factory. Para chamar a API de Gerenciamento de Recursos do Azure, use `https://management.azure.com/`. Para obter mais informações sobre como identidades gerenciadas funcionam, confira a [página de visão geral de identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

## <a name="request-payload-schema"></a>Solicitar esquema de carga
Quando você usa o método PUT/POST, a propriedade body representa a carga que é enviada para o ponto de extremidade. Você pode passar serviços vinculados e conjuntos de dados como parte da carga. Aqui está o esquema para a carga:

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
}
```

## <a name="example"></a>Exemplo
Neste exemplo, a atividade da Web no pipeline chama um ponto de extremidade REST. Ele passa um serviço vinculado do SQL do Azure e um conjunto de dados SQL do Azure para o ponto de extremidade. O ponto de extremidade REST usa a cadeia de conexão do SQL do Azure para se conectar ao SQL Server do Azure e retorna o nome da instância do SQL Server.

### <a name="pipeline-definition"></a>Definição de pipeline

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Valores de parâmetro do pipeline

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Código do ponto de extremidade de serviço Web

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Próximas etapas
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory:

- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
