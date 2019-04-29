---
title: Cenário de log de erros e tratamento de exceção – Aplicativos Lógicos do Azure | Microsoft Docs
description: Aqui está um caso de uso real sobre o log de erros e o tratamento de exceção avançados nos Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: hedidin
ms.author: b-hoedid
ms.reviewer: estfan, LADocs
ms.assetid: 63b0b843-f6b0-4d9a-98d0-17500be17385
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: 58e59e4faa135e24124f494d90437b49caa30129
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599480"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Cenário: Tratamento de exceção e log de erros para aplicativos lógicos

Este cenário descreve como é possível estender um aplicativo lógico para dar melhor suporte ao tratamento de exceção. Usamos um caso de uso real para responder à pergunta: "Os Aplicativos Lógicos do Azure dão suporte ao tratamento de erro e exceção?"

> [!NOTE]
> O esquema atual dos Aplicativos Lógicos do Azure fornece um modelo padrão para respostas de ação. Esse modelo inclui a validação interna e as respostas de erro retornadas de um aplicativo de API.

## <a name="scenario-and-use-case-overview"></a>Visão geral do cenário e do caso de uso

Esta é a história do caso de uso para este cenário: 

Uma organização de saúde conhecida nos pediu para desenvolver uma solução do Azure que criasse um portal de paciente usando o Microsoft Dynamics CRM Online. Ela precisava enviar registros de consultas entre o portal de pacientes do Dynamics CRM Online e o Salesforce. Foi solicitado que usássemos o padrão [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) para todos os registros de pacientes.

O projeto tinha dois requisitos principais:  

* Um método para registrar em log os registros enviados do portal do Dynamics CRM Online
* Uma maneira de exibir os erros ocorridos no fluxo de trabalho

> [!TIP]
> Para assistir a um vídeo de alto nível sobre esse projeto, consulte [Grupo de usuários de integração](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Grupo de usuários de integração").

## <a name="how-we-solved-the-problem"></a>Como solucionamos o problema

Escolhemos o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") como um repositório dos registros de log e erros (o Cosmos DB refere-se aos registros como documentos). Como os Aplicativos Lógicos do Azure têm um modelo padrão para todas as respostas, não será necessário criar um esquema personalizado. Nós poderíamos criar um aplicativo de API para **Inserir** e **Consultar** registros de erro e de log. Também poderíamos definir um esquema para cada um deles no aplicativo de API.  

Outro requisito era eliminar registros após determinada data. O Cosmos DB tem uma propriedade chamada [TTL](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Time to Live") (vida útil), que nos permitiu definir um valor de **TTL** para cada registro ou coleção. Essa funcionalidade eliminou a necessidade de excluir manualmente os registros do Cosmos DB.

> [!IMPORTANT]
> Para concluir este tutorial, você precisa criar um banco de dados do Cosmos DB e duas coleções (Log e Erros).

## <a name="create-the-logic-app"></a>Criar o aplicativo lógico

A primeira etapa é criar o aplicativo lógico e abri-lo no Designer de Aplicativos Lógicos. Neste exemplo, estamos usando aplicativos lógicos pai-filho. Vamos supor que nós criamos o pai e vamos criar um aplicativo lógico filho.

Já que vamos registrar o registro obtido do Dynamics CRM Online, começaremos no início. Devemos usar um gatilho **Solicitação**, pois o aplicativo lógico pai dispara esse filho.

### <a name="logic-app-trigger"></a>Gatilho de aplicativo lógico

Estamos usando um gatilho **Solicitação**, conforme mostrado no seguinte exemplo:

``` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

```


## <a name="steps"></a>Etapas

Devemos registrar a origem (solicitação) do registro do paciente do portal do Dynamics CRM Online.

1. É necessário obter um novo registro de compromisso do Dynamics CRM Online.

   O gatilho proveniente do CRM fornece a **CRM PatentId**, o **tipo de registro**, o **Registro Novo ou Atualizado** (valor booliano novo ou atualizado) e a **SalesforceId**. O **SalesforceId** pode ser nulo porque é usado somente para uma atualização.
   Obtemos o registro do CRM usando a **PatientID** do CRM e o **Tipo de Registro**.

2. Em seguida, precisamos adicionar a operação **InsertLogEntry** do nosso aplicativo de API do SQL do Azure Cosmos DB, conforme mostrado aqui no Designer de Aplicativo Lógico.

   **Inserir entrada de log**

   ![Inserir Entrada de Log](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Inserir entrada de erro**

   ![Inserir Entrada de Log](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Verificar falha na criação de registro**

   ![Condição](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Código-fonte do aplicativo lógico

> [!NOTE]
> Os exemplos a seguir são apenas amostras. Como este tutorial se baseia em uma implementação atualmente em produção, o valor de um **Nó de Origem** pode não exibir as propriedades relacionadas ao agendamento de um compromisso.> 

### <a name="logging"></a>Registro em log

O exemplo de código de aplicativo lógico a seguir mostra como lidar com registros em log.

#### <a name="log-entry"></a>Entrada de log

Este é o código-fonte do aplicativo lógico para a inserção de uma entrada de log.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Solicitação de log

Esta é a mensagem de solicitação de log postada no aplicativo de API.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Resposta de log

Esta é a mensagem de resposta de log do aplicativo de API.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Agora vejamos as etapas de tratamento de erros.

### <a name="error-handling"></a>Tratamento de erros

O exemplo de código de aplicativo lógico a seguir mostra como é possível implementar o tratamento de erro.

#### <a name="create-error-record"></a>Criar registro de erro

Este é o código-fonte do aplicativo lógico para criar um registro de erro.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-cosmos-db--request"></a>Inserir erro na solicitação do Cosmos DB

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-cosmos-db--response"></a>Inserir erro na resposta do Cosmos DB

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Resposta de erro do Salesforce

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="return-the-response-back-to-parent-logic-app"></a>Retornar a resposta ao aplicativo lógico pai

Assim que você obtiver a resposta, poderá passá-la para o aplicativo lógico pai.

#### <a name="return-success-response-to-parent-logic-app"></a>Retornar resposta de êxito para aplicativo lógico pai

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-parent-logic-app"></a>Retornar resposta de erro para aplicativo lógico pai

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="cosmos-db-repository-and-portal"></a>Repositório e portal do Cosmos DB

Nossa solução adicionou funcionalidades ao [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db).

### <a name="error-management-portal"></a>Portal de gerenciamento de erros

Para exibir os erros, crie um aplicativo Web do MVC para exibir os registros de erro do Cosmos DB. As operações **Listar**, **Detalhes**, **Editar** e **Excluir** foram incluídas na versão atual.

> [!NOTE]
> Operação Editar: O Cosmos DB substitui o documento inteiro. Os registros mostrados nos modos de exibição de **Lista** e de **Detalhe** são apenas exemplos. Eles não são registros reais de consultas de pacientes.

Estes são exemplos de detalhes de nosso aplicativo MVC criados com a abordagem descrita anteriormente.

#### <a name="error-management-list"></a>Lista de gerenciamento de erros
![Lista de Erros](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Modo de exibição de detalhes do gerenciamento de erros
![Detalhes do Erro](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portal de gerenciamento de logs

Para exibir os logs, também criamos um aplicativo Web do MVC. Estes são exemplos de detalhes de nosso aplicativo MVC criados com a abordagem descrita anteriormente.

#### <a name="sample-log-detail-view"></a>Modo de exibição de detalhes de log de exemplo
![Modo de Exibição de Detalhes de Log](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Detalhes do aplicativo de API

#### <a name="logic-apps-exception-management-api"></a>API de gerenciamento de exceções de Aplicativos Lógicos

Nosso aplicativo de API de gerenciamento de exceções dos Aplicativos Lógicos do Azure de software livre fornece a funcionalidade conforme descrito a seguir - existem dois controladores:

* **ErrorController** insere um registro de erro (documento) em uma coleção do Azure Cosmos DB.
* **LogController** insere um registro de log (documento) em uma coleção do Azure Cosmos DB.

> [!TIP]
> Ambos os controladores usam operações `async Task<dynamic>`, permitindo que as operações sejam resolvidas em tempo de execução, para que possamos criar o esquema do Azure Cosmos DB no corpo da operação. 
> 

Todos os documentos do Azure Cosmos DB devem ter uma ID exclusiva. Estamos usando o `PatientId` e adicionando um carimbo de data/hora convertido em um valor de carimbo de data/hora do Unix (double). Truncamos o valor para remover o valor fracionário.

Você pode exibir o código-fonte da nossa API de controlador erro no [GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs).

Chamamos a API de um aplicativo lógico usando a seguinte sintaxe:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

A expressão no exemplo de código anterior verifica o status de *Create_NewPatientRecord*, que é **Com Falha**.

## <a name="summary"></a>Resumo

* Você pode implementar facilmente o tratamento de logs e de erros em um aplicativo lógico.
* Você pode usar o Azure Cosmos DB como o repositório de registros de log e de erros (documentos).
* Você pode usar o MVC para criar um portal para exibir os registros de log e de erros.

### <a name="source-code"></a>Código-fonte

O código-fonte para o aplicativo de API de gerenciamento de exceções de Aplicativos Lógicos está disponível neste [repositório GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API de Gerenciamento de Exceções de Aplicativos Lógicos").

## <a name="next-steps"></a>Próximas etapas

* [Exibir mais exemplos e cenários de aplicativos lógicos](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Saiba mais sobre como monitorar aplicativos lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Criar modelos de implantação automatizados para aplicativos lógicos](../logic-apps/logic-apps-create-deploy-template.md)
