---
title: Autenticação de saída
description: Saiba como configurar ou remover a autenticação de saída do Agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 0a8d79af9f45731971cb1be1f39fc193f9d0f0d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80878962"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Autenticação de saída para Agendador do Azure

> [!IMPORTANT]
> Os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) estão substituindo o Agendador do Azure, que está [sendo desativado](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Para continuar trabalhando com os trabalhos que você configurou no Agendador, [migre para o aplicativo lógico do Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) assim que possível. 
>
> O Agendador não está mais disponível no portal do Azure, mas a [API REST](/rest/api/scheduler) e os [cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md) permanecem disponíveis no momento para que você possa gerenciar seus trabalhos e suas coleções de trabalhos.

Os trabalhos do Agendador do Azure podem precisar chamar serviços que exigem autenticação, como outros serviços do Azure, Salesforce.com, Facebook e sites seguros personalizados. O serviço chamado pode determinar se o trabalho do Agendador poderá acessar seus recursos solicitados. 

O Agendador oferece suporte a esses modelos de autenticação: 

* Autenticação de *certificado de cliente* ao usar certificados de cliente SSL/TLS
* Autenticação *básica*
* *Active Directory Autenticação OAuth*

## <a name="add-or-remove-authentication"></a>Adicionar ou remover a autenticação

* Para adicionar autenticação a um trabalho do Agendador, quando você cria ou atualiza o trabalho, adicione a `authentication` elemento filho de notação JSON (JavaScript Object) para o `request` elemento. 

  Respostas nunca retornam segredos passados para o serviço do Agendador em uma solicitação PUT, PATCH ou POST, como parte do objeto `authentication`. 
  Respostas configuram informações secretas são definidas como nulo ou podem ter um token público que representa a entidade autenticada. 

* Para remover a autenticação de um trabalho do Agendador, explicitamente execute uma solicitação PUT ou PATCH no trabalho e defina o `authentication` objeto como null. A resposta não conterá as propriedades de autenticação.

## <a name="client-certificate"></a>Certificado do cliente

### <a name="request-body---client-certificate"></a>Corpo da solicitação – certificado do cliente

Ao adicionar a autenticação usando o modelo `ClientCertificate`, especifique estes elementos adicionais no corpo da solicitação.  

| Elemento | Obrigatório | Descrição |
|---------|----------|-------------|
| **autenticação (elemento pai)** | O objeto de autenticação para usar um certificado de cliente SSL/TLS |
| **type** | Sim | O tipo de autenticação. Para certificados de cliente SSL/TLS, o valor é `ClientCertificate` . |
| **pfx** | Sim | Conteúdo codificado na base64 do arquivo PFX |
| **password** | Sim | A senha para acessar o arquivo PFX |
||| 

### <a name="response-body---client-certificate"></a>Corpo da resposta – certificado do cliente 

Quando uma solicitação é enviada com as informações de autenticação, a resposta contém os elementos de autenticação.

| Elemento | Descrição | 
|---------|-------------| 
| **autenticação (elemento pai)** | O objeto de autenticação para usar um certificado de cliente SSL/TLS |
| **type** | O tipo de autenticação. Para certificados de cliente SSL/TLS, o valor é `ClientCertificate` . |
| **certificateThumbprint** |A impressão digital do certificado |
| **certificateSubjectName** |O nome distinto da entidade do certificado |
| **certificateExpiration** | A data de validade do certificado |
||| 

### <a name="sample-rest-request---client-certificate"></a>Exemplo de solicitação REST - certificado do cliente

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---client-certificate"></a>Exemplo de resposta REST - certificado do cliente

```json
HTTP/1.1 200 OKCache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="basic"></a>Basic

### <a name="request-body---basic"></a>Corpo da solicitação - Básico

Ao adicionar a autenticação usando o modelo `Basic`, especifique estes elementos adicionais no corpo da solicitação.

| Elemento | Obrigatório | Descrição |
|---------|----------|-------------|
| **autenticação (elemento pai)** | O objeto de autenticação para usar a autenticação básica | 
| **type** | Sim | O tipo de autenticação. Para a autenticação básica, o valor é `Basic`. | 
| **username** | Sim | Nome de usuário para autenticação | 
| **password** | Sim | Senha para autenticação |
|||| 

### <a name="response-body---basic"></a>Corpo da resposta - Básico

Quando uma solicitação é enviada com as informações de autenticação, a resposta contém os elementos de autenticação.

| Elemento | Descrição | 
|---------|-------------|
| **autenticação (elemento pai)** | O objeto de autenticação para usar a autenticação básica |
| **type** | O tipo de autenticação. Para a autenticação básica, o valor é `Basic`. |
| **username** | O nome de usuário autenticado |
||| 

### <a name="sample-rest-request---basic"></a>Exemplo de solicitação REST - Basic

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---basic"></a>Exemplo de resposta REST - Basic

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"Http"
      },
      "recurrence":{  
         "frequency":"Minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"Enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="active-directory-oauth"></a>Active Directory OAuth

### <a name="request-body---active-directory-oauth"></a>Corpo de solicitação - Active Directory OAuth 

Ao adicionar a autenticação usando o modelo `ActiveDirectoryOAuth`, especifique estes elementos adicionais no corpo da solicitação.

| Elemento | Obrigatório | Descrição |
|---------|----------|-------------|
| **autenticação (elemento pai)** | Sim | Objeto de autenticação para usar a autenticação ActiveDirectoryOAuth |
| **type** | Sim | O tipo de autenticação. Para autenticação de ActiveDirectoryOAuth, o valor é `ActiveDirectoryOAuth`. |
| **tenant** | Sim | O identificador do locatário para o locatário do Azure AD. Para encontrar o identificador do locatário para o locatário do Azure AD executando `Get-AzureAccount` no Azure PowerShell. |
| **platéia** | Sim | Esse valor é configurado para `https://management.core.windows.net/`. | 
| **clientId** | Sim | O identificador de cliente para o aplicativo do Azure AD | 
| **RADIUS** | Sim | O segredo para o cliente que está solicitando o token | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Corpo de resposta - Active Directory OAuth

Quando uma solicitação é enviada com as informações de autenticação, a resposta contém os elementos de autenticação.

| Elemento | Descrição |
|---------|-------------|
| **autenticação (elemento pai)** | Objeto de autenticação para usar a autenticação ActiveDirectoryOAuth |
| **type** | O tipo de autenticação. Para autenticação de ActiveDirectoryOAuth, o valor é `ActiveDirectoryOAuth`. | 
| **tenant** | O identificador do locatário para o locatário do Azure AD |
| **platéia** | Esse valor é configurado para `https://management.core.windows.net/`. |
| **clientId** | O identificador de cliente para o aplicativo do Azure AD |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Exemplo de solicitação REST - OAuth do Active Directory

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "Http"
    },
    "recurrence": {
      "frequency": "Minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "Enabled"
  }
}
```

### <a name="sample-rest-response---active-directory-oauth"></a>Exemplo de resposta REST - OAuth do Active Directory

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type": "Microsoft.Scheduler/jobCollections/jobs",
   "name": "southeastasiajc/httpjob",
   "properties": {
      "startTime": "2015-05-14T14:10:00Z",
      "action": {  
         "request": {
            "uri": "https://mywebserviceendpoint.com",
            "method": "GET",
            "headers": {  
               "x-ms-version": "2013-03-01"
            },
            "authentication": {  
               "tenant": "microsoft.onmicrosoft.com",
               "audience": "https://management.core.windows.net/",
               "clientId": "dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type": "ActiveDirectoryOAuth"
            }
         },
         "type": "Http"
      },
      "recurrence": {  
         "frequency": "minute",
         "endTime": "2016-04-10T08:00:00Z",
         "interval": 1
      },
      "state": "Enabled",
      "status": {  
         "lastExecutionTime": "2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime": "2016-03-16T19:11:00Z",
         "executionCount": 5,
         "failureCount": 5,
         "faultedCount": 1
      }
   }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)
* [Limites, padrões e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)
* [Referência da API REST do Agendador do Azure](/rest/api/scheduler)
* [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)