---
title: Visão geral do Serviço de Aplicativo – Azure | Microsoft Docs
description: Saiba como o Serviço de Aplicativo do Azure o ajuda a desenvolver e hospedar aplicativos Web
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5ce8abd08f82a2a8b69cc996bfd8f7f060bb8c23
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205043"
---
# <a name="app-service-overview"></a>Visão geral do Serviço de Aplicativo

O *Serviço de Aplicativo do Azure* é um serviço com base em HTTP para hospedagem de aplicativos Web, APIs REST e back-ends móveis. Você pode desenvolver usando sua linguagem favorita, seja .NET, .NET Core, Java, Ruby, Node.js, PHP ou Python. Os aplicativos são executados e dimensionados com facilidade em ambientes baseados no Windows e no Linux. Para ambientes baseados em Linux, confira [Serviço de Aplicativo do Azure](containers/app-service-linux-intro.md). 

O Serviço de Aplicativo não agrega apenas o poder do Microsoft Azure ao seu aplicativo, como segurança, balanceamento de carga, dimensionamento automático e gerenciamento automatizado. Você pode também aproveitar seus recursos de DevOps, como implantação contínua desde o Azure DevOps, GitHub, Hub do Docker e outras fontes, gerenciamento de pacote, ambientes de preparo, domínio personalizado e certificados SSL. 

Com o Serviço de Aplicativo, você paga pelos recursos de computação do Azure que usar. Os recursos de computação usados são determinados pelo _Plano do Serviço de Aplicativo_ no qual os aplicativos são executados. Para obter mais informações, confira [Visão geral dos Planos do Serviço de Aplicativo do Azure](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Por que usar o Serviço de Aplicativo?

Veja alguns dos principais recursos do Serviço de Aplicativo:

* **Variedade de linguagens e estruturas** – O Serviço de Aplicativo têm suporte de primeira classe para ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP ou Python. Você também pode executar o [PowerShell e outros scripts ou executáveis](webjobs-create.md) como serviços em segundo plano.
* **Otimização de DevOps** – configure a [integração e implantação contínuas](deploy-continuous-deployment.md) com o Azure DevOps, o GitHub, o BitBucket, o Hub do Docker ou o Registro de Contêiner do Azure. Promova atualizações por meio de [ambientes de preparo e teste](deploy-staging-slots.md). Gerencie aplicativos no Serviço de Aplicativo usando o [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou a [CLI (interface de linha de comando de plataforma cruzada)](/cli/azure/install-azure-cli).
* **Escala global com alta disponibilidade** - escale [verticalmente](web-sites-scale.md) ou [horizontalmente](../monitoring-and-diagnostics/insights-how-to-scale.md) de forma manual ou automática. Hospede os aplicativos em qualquer lugar na infraestrutura de datacenter global da Microsoft, e o [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) do Serviço de Aplicativo promete alta disponibilidade.
* **Conexões com plataformas SaaS e dados locais** - escolha entre mais de 50 [conectores](../connectors/apis-list.md) para sistemas corporativos (como SAP), serviços de SaaS (como Salesforce) e serviços de Internet (como Facebook). Acesse dados locais usando [Conexões Híbridas](app-service-hybrid-connections.md) e [Redes Virtuais do Azure](web-sites-integrate-with-vnet.md).
* **Segurança e conformidade** - o Serviço de Aplicativo está em [conformidade com ISO, SOC e PCI](https://www.microsoft.com/en-us/trustcenter). Autentique usuários com o [Azure Active Directory](configure-authentication-provider-aad.md) ou com logon social ([Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) e [ Microsoft](configure-authentication-provider-microsoft.md)). Crie [Restrições de endereço IP](app-service-ip-restrictions.md) e [gerencie identidades de serviço](overview-managed-identity.md).
* **Modelos de aplicativos** - escolha dentre uma lista abrangente de modelos de aplicativos no [Azure Marketplace](https://azure.microsoft.com/marketplace/), como WordPress, Joomla e Drupal.
* **Integração do visual Studio** -ferramentas dedicadas no Visual Studio simplificam o trabalho de criar, implantar, depurar e gerenciar.
* **Recursos móveis e de API** – O Serviço de Aplicativo fornece suporte pronto para uso ao CORS para cenários de API RESTful e simplifica os cenários de aplicativos móveis permitindo autenticação, sincronização de dados offline, notificações por push e muito mais.
* **Código sem servidor** - Execute um snippet de código ou um script sob demanda sem a necessidade de provisionar explicitamente ou gerenciar a infraestrutura, e pague somente pelo tempo de computação usado pelo seu código (consulte [Azure Functions](/azure/azure-functions/)).

Além do Serviço de Aplicativo, o Azure oferece outros serviços que podem ser usados para hospedar sites e aplicativos Web. Para a maioria dos cenários, o Serviço de Aplicativo é a melhor opção.  Para arquitetura de microsserviço, considere o [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Se você precisar de mais controle sobre as VMs nas quais seu código é executado, considere as [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Para saber mais sobre como escolher entre esses serviços do Azure, confira [Comparação entre o Serviço de Aplicativo do Azure, Máquinas Virtuais, Service Fabric e Serviços de Nuvem do Azure](overview-compare.md).

## <a name="next-steps"></a>Próximas etapas

Crie seu primeiro aplicativo Web.

> [!div class="nextstepaction"]
> [ASP.NET Core](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Ruby (no Linux)](containers/quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python (no Linux)](containers/quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
