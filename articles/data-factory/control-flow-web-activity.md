---
title: Atividade da Web no Azure Data Factory
description: Saiba como você pode usar a atividade da Web, uma das atividades de fluxo de controle com suporte pelo Data Factory, para invocar um ponto de extremidade REST de um pipeline.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: e4578b41e5cbb62c8a1bfa0c48d4fd60d042a506
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361512"
---
# <a name="web-activity-in-azure-data-factory"></a>Atividade da Web no Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


A atividade da Web pode ser usada para chamar um ponto de extremidade REST personalizado de um pipeline do Data Factory. Você pode passar conjuntos de dados e serviços vinculados a serem consumidos e acessados pela atividade.

> [!NOTE]
> A atividade da Web também pode invocar URLs que são hospedadas em uma rede virtual privada aproveitando o runtime de integração auto-hospedada. O runtime de integração deve ter uma linha de visão para o ponto de extremidade da URL. 

> [!NOTE]
> O tamanho máximo do conteúdo de resposta de saída com suporte é de 4 MB.  

## <a name="syntax"></a>Sintaxe

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "connectVia": {
          "referenceName": "<integrationRuntimeName>",
          "type": "IntegrationRuntimeReference"
      }
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

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
name | Nome da atividade da Web | String | Sim
type | Deve ser definido como **WebActivity**. | String | Sim
method | Método da API REST para o ponto de extremidade de destino. | Cadeia de caracteres. <br/><br/>Tipos com suporte: "GET", "POST", "PUT" | Sim
url | Ponto de extremidade de destino e o caminho | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres). A atividade atingirá o tempo limite em 1 minuto com um erro se não receber uma resposta do ponto de extremidade. | Sim
headers | Cabeçalhos que são enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Sim, o cabeçalho Content-Type é necessário. `"headers":{ "Content-Type":"application/json"}`
body | Representa o conteúdo enviado para o ponto de extremidade.  | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres). <br/><br/>Consulte o esquema da carga de solicitação na seção [Esquema de carga de solicitação](#request-payload-schema). | Necessário para os métodos PUT/POST.
autenticação | Método de autenticação usado para chamar o ponto de extremidade. Os tipos com suporte são "Basic ou ClientCertificate." Para obter mais informações, consulte a seção [Autenticação](#authentication). Se a autenticação não for necessária, exclua essa propriedade. | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Não
conjuntos de dados | Lista de conjuntos de dados passados para o ponto de extremidade. | Matriz de referências do conjunto de dados. Pode ser uma matriz vazia. | Sim
linkedServices | Lista de serviços vinculados passado ao ponto de extremidade. | Matriz de referências de serviço vinculado. Pode ser uma matriz vazia. | Sim
connectVia | O [runtime de integração](./concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Use o runtime de integração do Azure ou o runtime de integração auto-hospedada (se o seu armazenamento de dados estiver em uma rede privada). Se essa propriedade não for especificada, o serviço usará o runtime de integração do Azure padrão. | A referência de runtime de integração. | Não 

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

## <a name="authentication"></a>Autenticação

Veja abaixo os tipos de autenticação compatíveis com a atividade da Web.

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

Especifique o URI do recurso para o qual o token de acesso será solicitado usando a identidade gerenciada para o data factory. Para chamar a API de Gerenciamento de Recursos do Azure, use `https://management.azure.com/`. Para obter mais informações sobre como identidades gerenciadas funcionam, confira a [página de visão geral de identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Se o seu data factory estiver configurado com um repositório Git, você precisará armazenar suas credenciais no Azure Key Vault para usar a autenticação básica ou de certificado do cliente. O Azure Data Factory não armazena senhas no Git.

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
Neste exemplo, a atividade da Web no pipeline chama um ponto de extremidade REST. Ele passa um serviço vinculado do SQL do Azure e um conjunto de dados SQL do Azure para o ponto de extremidade. O ponto de extremidade REST usa a cadeia de conexão do SQL do Azure para se conectar ao servidor SQL lógico e retorna o nome da instância do servidor SQL.

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
