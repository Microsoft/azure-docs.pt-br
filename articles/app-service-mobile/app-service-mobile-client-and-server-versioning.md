---
title: Controle de versão de cliente e servidor SDK em Aplicativos Móveis e Serviços Móveis | Microsoft Docs
description: Lista dos SDKs clientes e compatibilidade com versões do SDK do servidor para os Serviços Móveis e Aplicativos Móveis do Azure
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 56c5e0582afe55dcd63aa056817898d3d4942419
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859066"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Controle de versão de cliente e servidor em Aplicativos Móveis e Serviços Móveis
A versão mais recente dos Serviços Móveis do Azure é o recurso **Aplicativos Móveis** do Serviço de Aplicativo do Azure.

Os SDKs de cliente e servidor de Aplicativos Móveis são baseados originalmente nos Serviços Móveis, mas *não* são compatíveis entre si.
Ou seja, você deve usar um SDK de cliente de *Aplicativos Móveis* com SDK de servidor de *Aplicativos Móveis* e da mesma forma para *Serviços Móveis*. Esse contrato é imposto por meio de um valor de cabeçalho especial usado pelos SDKs de cliente e servidor, `ZUMO-API-VERSION`.

Observação: sempre que este documento se refere a um back-end de *Serviços Móveis* , ele não necessariamente precisa estar hospedados nos Serviços Móveis. Agora é possível migrar um serviço móvel para ser executado em um Serviço de Aplicativo sem qualquer alteração de código, mas o serviço ainda estaria usando versões de SDK de *Serviços Móveis*.

Para saber mais sobre a migração para o Serviço de Aplicativo sem qualquer alteração de código, consulte o artigo [Migrar um Serviço Móvel para o Serviço de Aplicativo do Azure].

## <a name="header-specification"></a>Especificação de cabeçalho
A chave `ZUMO-API-VERSION` pode ser especificada no cabeçalho HTTP ou na cadeia de consulta. O valor é uma cadeia de caracteres de versão no formulário **x.y.z**.

Por exemplo: 

GET https://service.azurewebsites.net/tables/TodoItem

CABEÇALHOS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Recusando a verificação de versão
Você pode recusar a verificação de versão definindo um valor **verdadeiro** para a configuração do aplicativo **MS_SkipVersionCheck**. Especifique isso no seu web.config ou na seção Configurações do Aplicativo do Portal do Azure.

> [!NOTE]
> Há algumas alterações de comportamento entre os Serviços Móveis e os Aplicativos Móveis, especialmente nas áreas de sincronização offline, autenticação e notificações por push. Você deve recusar verificação somente após o teste completo para garantir que essas alterações de comportamento não interrompam a funcionalidade do aplicativo.
>
>

## <a name="summary-of-compatibility-for-all-versions"></a>Resumo de compatibilidade para todas as versões
A tabela abaixo mostra a compatibilidade entre todos os tipos de cliente e de servidor. Um back-end é classificado como **Serviços** Móveis ou **Aplicativos** Móveis com base no SDK de servidor que ele usa.

|  | **Serviços Móveis**  | **Aplicativos Móveis**  |
| --- | --- | --- |
| [Clientes de Serviços Móveis] |OK |Erro\* |
| [Clientes de Aplicativos Móveis] |Erro\* |OK |

\*Isso pode ser controlado especificando**MS_SkipVersionCheck**.

<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is https://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Servidor e cliente de Serviços Móveis
Os SDKs de cliente na tabela a seguir são compatíveis com **Serviços Móveis**.

Observação: os SDKs do cliente dos Serviços Móveis *não* enviam um valor de cabeçalho para `ZUMO-API-VERSION`. Se o serviço receber esse cabeçalho ou o valor de cadeia de consulta, um erro será retornado, a menos que você tenha recusado expressamente conforme descrito acima.

### <a name="MobileServicesClients"></a> SDKs de clientes de *Serviços* Móveis
| Plataforma cliente | Version | Valor de cabeçalho de versão |
| --- | --- | --- |
| Cliente gerenciado (Windows, Xamarin) |[1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) |n/d |
| iOS |[2.2.2](https://aka.ms/gc6fex) |n/d |
| Android |[2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) |n/d |
| HTML |[1.2.7](https://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) |n/d |

### <a name="mobile-services-server-sdks"></a>SDKs de servidor de *Serviços* Móveis
| Plataforma servidor | Version | Cabeçalho de versão aceito |
| --- | --- | --- |
| .NET |[WindowsAzure.MobileServices.Backend.* versão 1.0. x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) |**Nenhum cabeçalho de versão** |
| Node.js |(em breve) |**Nenhum cabeçalho de versão** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Comportamento dos back-ends de Serviços Móveis
| ZUMO-API-VERSION | Valor de MS_SkipVersionCheck | Response |
| --- | --- | --- |
| Não especificado |Qualquer |200 - OK |
| Qualquer valor |True |200 - OK |
| Qualquer valor |Falso/não especificado |400 - solicitação inválida |

## <a name="2.0.0"></a>Servidor e cliente de Aplicativos Móveis do Azure
### <a name="MobileAppsClients"></a> SDKs de cliente de *Aplicativos* Móveis
A verificação de versão foi introduzida começando com as seguintes versões do SDK do cliente para **Aplicativos Móveis do Azure**:

| Plataforma cliente | Version | Valor de cabeçalho de versão |
| --- | --- | --- |
| Cliente gerenciado (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>SDKs de servidor dos *Aplicativos* Móveis
A verificação de versão está incluída nas seguintes versões do SDK do servidor:

| Plataforma servidor | . | Cabeçalho de versão aceito |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportamento dos back-ends de Aplicativos Móveis
| ZUMO-API-VERSION | Valor de MS_SkipVersionCheck | Response |
| --- | --- | --- |
| x.y.z ou Null |True |200 - OK |
| Nulo |Falso/não especificado |400 - solicitação inválida |
| 1.x.y |Falso/não especificado |400 - solicitação inválida |
| 2.0.0-2.x.y |Falso/não especificado |200 - OK |
| 3.0.0-3.x.y |Falso/não especificado |400 - solicitação inválida |

## <a name="next-steps"></a>Próximas etapas
* [Migrar um Serviço Móvel para o Serviço de Aplicativo do Azure]

[Clientes de Serviços Móveis]: #MobileServicesClients
[Clientes de Aplicativos Móveis]: #MobileAppsClients


[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
[Migrar um Serviço Móvel para o Serviço de Aplicativo do Azure]: app-service-mobile-migrating-from-mobile-services.md
