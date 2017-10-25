---
title: Rastrear chamadas com o Inspetor de API - Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como rastrear chamadas usando o Inspetor de API no Gerenciamento de API do Azure.
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 4b222327-c8a4-4f33-9a06-adff2a9834d9
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: a9d4d3be7f046af975f6dc25670070204848588c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-api-inspector-to-trace-calls-in-azure-api-management"></a>Como usar o Inspetor de API para rastrear chamadas no Gerenciamento de API do Azure
O Gerenciamento de API oferece uma ferramenta de Inspetor de API para ajudá-lo a depurar e solucionar problemas de suas APIs. O Inspetor de API pode ser usado de forma programática e pode também ser usado diretamente do portal do desenvolvedor. 

Além das operações de rastreamento, o Inspetor de API também rastreia as avaliações de [expressão de política](https://msdn.microsoft.com/library/azure/dn910913.aspx) . Para assistir a uma demonstração, confira o [Episódio 177 do Cloud Cover: mais recursos de Gerenciamento de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avance até 21:00.

Este guia fornece uma explicação da utilização do Inspetor de API.

> [!NOTE]
> Os rastreamentos de um Inspetor de API são gerados e disponibilizados somente para solicitações que contêm chaves de assinatura que pertencem à conta de [administrador](api-management-howto-create-groups.md) .
> 
> 

## <a name="trace-call"> </a> Usar o Inspetor de API para rastrear uma chamada
Para usar o Inspetor de API, adicione um cabeçalho de solicitação **ocp-apim-trace: true** à sua chamada de operação, baixe e inspecione o rastreamento utilizando a URL indicada pelo cabeçalho de resposta **ocp-apim-trace-location**. Isso pode ser feito de forma programática ou diretamente a partir do portal do desenvolvedor.

Este tutorial mostra como usar o Inspetor de API para operações de rastreamento usando a API de Calculadora Básica configurada no tutorial de introdução [Gerenciar sua primeira API](api-management-get-started.md) . Se você ainda não concluiu esse tutorial, leva apenas alguns minutos para importar a API de Calculadora Básica; outra opção é usar outra API de sua escolha, como a API de Eco. Cada instância de serviço de Gerenciamento de API vem pré-configurada com uma API de ECO que pode ser usada para experimentar e aprender sobre o Gerenciamento de API. A API de ECO retorna qualquer entrada enviada a ela. Para usá-la, você pode invocar qualquer verbo HTTP e o valor retornado será simplesmente o que você tiver enviado. 

Para começar, clique no **Portal do desenvolvedor** no Portal do Azure para seu serviço de Gerenciamento de API. As operações podem ser chamadas diretamente do portal do desenvolvedor, que fornece uma maneira conveniente de exibir e testar as operações de uma API.

> Se você ainda não criou uma instância do serviço de Gerenciamento de API, veja [Criar uma instância do serviço de Gerenciamento de API][Create an API Management service instance] no tutorial [Introdução ao Gerenciamento de API do Azure][Get started with Azure API Management].
> 
> 

![Portal do desenvolvedor do Gerenciamento da API][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e depois clique em **Calculadora Básica**.

![API de Eco][api-management-api]

Clique em **Experimentar** para experimentar a operação **Adicionar dois inteiros**.

![Experimente][api-management-open-console]

Mantenha os valores de parâmetros padrão e selecione a chave de assinatura do produto que deseja usar no menu suspenso **subscription-key** .

Por padrão, no portal do desenvolvedor, o cabeçalho **Ocp-Apim-Trace** já está definido como **true**. Esse cabeçalho define se um rastreamento é gerado ou não.

![Enviar][api-management-http-get]

Clique em **Enviar** para invocar a operação.

![Enviar][api-management-send-results]

Nos cabeçalhos de resposta, haverá um **ocp-apim-trace-location** com um valor semelhante ao exemplo a seguir.

```
ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742
```

O rastreamento pode ser baixado do local especificado e revisado, conforme demonstrado na etapa a seguir. Observe que apenas as últimas 100 entradas de log são armazenadas e os locais de log são reutilizados no esquema rotativo. Portanto, se você fizer mais de 100 chamadas com rastreamento habilitado, consequentemente iniciará a substituição dos primeiros rastreamentos em vigor.

## <a name="inspect-trace"> </a>Inspecionar o rastreamento
Para revisar os valores no rastreamento, baixe o arquivo de rastreamento da URL **ocp-apim-trace-location** . Trata-se de um texto no formato JSON que contém entradas semelhantes ao exemplo a seguir.

```json
{
    "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
    "traceEntries": {
        "inbound": [
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0725926",
                "data": {
                    "request": {
                        "method": "GET",
                        "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
                        "headers": [
                            {
                                "name": "Ocp-Apim-Subscription-Key",
                                "value": "5d7c41af64a44a68a2ea46580d271a59"
                            },
                            {
                                "name": "Connection",
                                "value": "Keep-Alive"
                            },
                            {
                                "name": "Host",
                                "value": "contoso5.azure-api.net"
                            }
                        ]
                    }
                }
            },
            {
                "source": "mapper",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0726213",
                "data": {
                    "configuration": {
                        "api": {
                            "from": "/calc",
                            "to": {
                                "scheme": "http",
                                "host": "calcapi.cloudapp.net",
                                "port": 80,
                                "path": "/api",
                                "queryString": "",
                                "query": {},
                                "isDefaultPort": true
                            }
                        },
                        "operation": {
                            "method": "GET",
                            "uriTemplate": "/add?a={a}&b={b}"
                        },
                        "user": {
                            "id": 1,
                            "groups": [
                                "Administrators",
                                "Developers"
                            ]
                        },
                        "product": {
                            "id": 1
                        }
                    }
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0727522",
                "data": {
                    "message": "Request is being forwarded to the backend service.",
                    "request": {
                        "method": "GET",
                        "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
                        "headers": [
                            {
                                "name": "Ocp-Apim-Subscription-Key",
                                "value": "5d7c41af64a44a68a2ea46580d271a59"
                            },
                            {
                                "name": "X-Forwarded-For",
                                "value": "33.52.215.35"
                            }
                        ]
                    }
                }
            }
        ],
        "outbound": [
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1960601",
                "data": {
                    "response": {
                        "status": {
                            "code": 200,
                            "reason": "OK"
                        },
                        "headers": [
                            {
                                "name": "Pragma",
                                "value": "no-cache"
                            },
                            {
                                "name": "Content-Length",
                                "value": "124"
                            },
                            {
                                "name": "Cache-Control",
                                "value": "no-cache"
                            },
                            {
                                "name": "Content-Type",
                                "value": "application/xml; charset=utf-8"
                            },
                            {
                                "name": "Date",
                                "value": "Tue, 23 Jun 2015 19:51:35 GMT"
                            },
                            {
                                "name": "Expires",
                                "value": "-1"
                            },
                            {
                                "name": "Server",
                                "value": "Microsoft-IIS/8.5"
                            },
                            {
                                "name": "X-AspNet-Version",
                                "value": "4.0.30319"
                            },
                            {
                                "name": "X-Powered-By",
                                "value": "ASP.NET"
                            }
                        ]
                    }
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1961112",
                "data": {
                    "message": "Response headers have been sent to the caller. Starting to stream the response body."
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1963155",
                "data": {
                    "message": "Response body streaming to the caller is complete."
                }
            }
        ]
    }
}
```

## <a name="next-steps"> </a>Próximas etapas
* Assista a uma demonstração das expressões da política de rastreamento no [Episódio 177 do Cloud Cover: mais recursos de Gerenciamento de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Avance até 21:00 para assistir à demonstração.

> [!VIDEO https://channel9.msdn.com/Shows/Cloud+Cover/Episode-177-More-API-Management-Features-with-Vlad-Vinogradsky/player]
> 
> 

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png




