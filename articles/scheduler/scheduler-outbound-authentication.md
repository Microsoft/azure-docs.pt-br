---
title: "Autenticação de Saída do Agendador"
description: "Autenticação de Saída do Agendador"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/15/2016
ms.author: deli
ms.openlocfilehash: e345b2e22daae5b24c23645f7d2636f66df630ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scheduler-outbound-authentication"></a>Autenticação de Saída do Agendador
Os trabalhos do Agendador podem precisar chamar serviços que requerem autenticação. Dessa forma, um serviço chamado pode determinar se o trabalho do Agendador poderá acessar seus recursos. Alguns desses serviços incluem outros serviços do Azure, Salesforce.com, Facebook e sites seguros personalizados.

## <a name="adding-and-removing-authentication"></a>Adição e Remoção de Autenticação
Adicionar a autenticação para um trabalho do Agendador é simples: adicione um elemento filho JSON `authentication` para o elemento `request` ao criar ou atualizar um trabalho. Os segredos passados para o serviço do Agendador em uma solicitação PUT, PATCH ou POST, como parte do objeto `authentication` , nunca são retornados em respostas. Nas respostas, as informações secretas são definidas como nulo ou podem ter um token público que representa a entidade autenticada.

Para remover a autenticação, faça PUT ou PATCH do trabalho explicitamente, configurando o objeto `authentication` como nulo. Você não verá quaisquer propriedades de autenticação na resposta.

Atualmente, os únicos tipos de autenticação com suporte são o modelo `ClientCertificate` (para uso dos certificados de cliente SSL/TLS), o modelo `Basic` (para autenticação básica) e o modelo `ActiveDirectoryOAuth` (para autenticação OAuth do Active Directory.)

## <a name="request-body-for-clientcertificate-authentication"></a>Corpo da solicitação de autenticação ClientCertificate
Ao adicionar a autenticação usando o modelo `ClientCertificate` , especifique os seguintes elementos adicionais no corpo da solicitação.  

| Elemento | Descrição |
|:--- |:--- |
| *autenticação (elemento pai)* |Objeto de autenticação para usar um certificado de cliente SSL. |
| *tipo* |Obrigatório. Tipo de autenticação. Para certificados de cliente SSL, o valor deve ser `ClientCertificate`. |
| *pfx* |Obrigatório. Conteúdo codificado na Base64 do arquivo PFX. |
| *password* |Obrigatório. Senha para acessar o arquivo PFX. |

## <a name="response-body-for-clientcertificate-authentication"></a>Corpo da resposta para autenticação ClientCertificate
Quando uma solicitação é enviada com as informações de autenticação, a resposta contém os seguintes elementos de autenticação.

| Elemento | Descrição |
|:--- |:--- |
| *autenticação (elemento pai)* |Objeto de autenticação para usar um certificado de cliente SSL. |
| *tipo* |Tipo de autenticação. Para certificados de cliente SSL, o valor é `ClientCertificate`. |
| *certificateThumbprint* |A impressão digital do certificado. |
| *certificateSubjectName* |O nome distinto da entidade do certificado. |
| *certificateExpiration* |A data de validade do certificado. |

## <a name="sample-rest-request-for-clientcertificate-authentication"></a>Exemplo de solicitação REST para autenticação ClientCertificate
```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
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
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-clientcertificate-authentication"></a>Exemplo de resposta REST para autenticação ClientCertificate
```
HTTP/1.1 200 OK
Cache-Control: no-cache
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
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
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

## <a name="request-body-for-basic-authentication"></a>Corpo da solicitação de autenticação básica
Ao adicionar a autenticação usando o modelo `Basic` , especifique os seguintes elementos adicionais no corpo da solicitação.

| Elemento | Descrição |
|:--- |:--- |
| *autenticação (elemento pai)* |Objeto de autenticação para usar a autenticação básica. |
| *tipo* |Obrigatório. Tipo de autenticação. Para a autenticação básica, o valor deve ser `Basic`. |
| *username* |Obrigatório. Nome de usuário para autenticação. |
| *password* |Obrigatório. Senha para autenticação. |

## <a name="response-body-for-basic-authentication"></a>Corpo da resposta de autenticação básica
Quando uma solicitação é enviada com as informações de autenticação, a resposta contém os seguintes elementos de autenticação.

| Elemento | Descrição |
|:--- |:--- |
| *autenticação (elemento pai)* |Objeto de autenticação para usar a autenticação básica. |
| *tipo* |Tipo de autenticação. Para a autenticação básica, o valor é `Basic`. |
| *username* |O nome de usuário autenticado. |

## <a name="sample-rest-request-for-basic-authentication"></a>Exemplo de solicitação REST para autenticação Básica
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
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
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-basic-authentication"></a>Exemplo de resposta REST para autenticação Básica
```
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
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
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
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="request-body-for-activedirectoryoauth-authentication"></a>Corpo da solicitação de autenticação ActiveDirectoryOAuth
Ao adicionar a autenticação usando o modelo `ActiveDirectoryOAuth` , especifique os seguintes elementos adicionais no corpo da solicitação.

| Elemento | Descrição |
|:--- |:--- |
| *autenticação (elemento pai)* |Objeto de autenticação para usar a autenticação ActiveDirectoryOAuth. |
| *tipo* |Obrigatório. Tipo de autenticação. Para autenticação de ActiveDirectoryOAuth, o valor deve ser `ActiveDirectoryOAuth`. |
| *tenant* |Obrigatório. O identificador do locatário para o locatário do Azure AD. |
| *audience* |Obrigatório. Isso é definido como https://management.core.windows.net/. |
| *clientId* |Obrigatório. Forneça o identificador de cliente para o aplicativo do AD do Azure. |
| *secret* |Obrigatório. Segredo do cliente que está solicitando o token. |

### <a name="determining-your-tenant-identifier"></a>Determinando o identificador do locatário
Você pode encontrar o identificador do locatário para o locatário do Azure AD executando `Get-AzureAccount` no Azure PowerShell.

## <a name="response-body-for-activedirectoryoauth-authentication"></a>Corpo da resposta de autenticação ActiveDirectoryOAuth
Quando uma solicitação é enviada com as informações de autenticação, a resposta contém os seguintes elementos de autenticação.

| Elemento | Descrição |
|:--- |:--- |
| *autenticação (elemento pai)* |Objeto de autenticação para usar a autenticação ActiveDirectoryOAuth. |
| *tipo* |Tipo de autenticação. Para autenticação de ActiveDirectoryOAuth, o valor é `ActiveDirectoryOAuth`. |
| *tenant* |O identificador do locatário para o locatário do Azure AD. |
| *audience* |Isso é definido como https://management.core.windows.net/. |
| *clientId* |O identificador de cliente para o aplicativo do AD do Azure. |

## <a name="sample-rest-request-for-activedirectoryoauth-authentication"></a>Exemplo de solicitação REST para autenticação ActiveDirectoryOAuth
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
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
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-activedirectoryoauth-authentication"></a>Exemplo de resposta REST para autenticação ActiveDirectoryOAuth
```
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
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
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
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## <a name="see-also"></a>Consulte também
 [O que é o Agendador?](scheduler-intro.md)

 [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)

 [Introdução à utilização do Agendador no Portal do Azure](scheduler-get-started-portal.md)

 [Planos e Cobrança no Agendador do Azure](scheduler-plans-billing.md)

 [Referência da API REST do Agendador do Azure](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)

 [Alta disponibilidade e confiabilidade do Agendador do Azure](scheduler-high-availability-reliability.md)

 [Limites, padrões e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)

