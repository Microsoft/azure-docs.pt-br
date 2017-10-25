---
title: "Visão geral de aplicativos Web | Microsoft Docs"
description: "Saiba como o Serviço de Aplicativo do Azure o ajuda a desenvolver e hospedar aplicativos Web"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: c1b3feb79328269f4692d744e7a473397d0328c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="web-apps-overview"></a>Visão geral de aplicativos Web
*Aplicativos Web do Serviço de Aplicativo* é uma plataforma de computação totalmente gerenciada que é otimizada para hospedagem de sites e aplicativos Web. Essa oferta de [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) (plataforma como serviço) do Microsoft Azure permite que você se concentre na lógica de negócios enquanto o Azure cuida da infraestrutura para executar e dimensionar os aplicativos.

O vídeo de 5 minutos a seguir apresenta os Aplicativos Web do Serviço de Aplicativo do Azure.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

## <a name="what-is-a-web-app-in-app-service"></a>O que é um aplicativo Web no Serviço de Aplicativo?
No Serviço de Aplicativo, um *aplicativo Web* consiste nos recursos de computação que o Azure fornece para hospedar um site ou aplicativo Web.  

Os recursos de computação podem estar em VMs (máquinas virtuais) compartilhadas ou dedicadas, dependendo da camada de preços que você escolher. O código do aplicativo é executado em uma VM gerenciada que é isolada de outros clientes.

O código pode estar em qualquer linguagem ou estrutura com suporte no Serviço de Aplicativo do Azure, como ASP.NET, Node.js, Java, PHP ou Python. Você também pode executar scripts que usam o [PowerShell e outras linguagens de script](web-sites-create-web-jobs.md#acceptablefiles) em um aplicativo Web.

Para obter exemplos de cenários típicos de aplicativos nos quais você pode usar aplicativos Web, veja [Cenários de aplicativos Web](https://azure.microsoft.com/documentation/scenarios/web-app/) e a seção **Cenários e recomendações** de [Comparação entre o Serviço de Aplicativo do Azure, as Máquinas Virtuais, o Service Fabric e os Serviços de Nuvem](choose-web-site-cloud-service-vm.md#scenarios).

## <a name="why-use-web-apps"></a>Por que usar aplicativos Web?
Veja alguns recursos importantes do Serviço de Aplicativo que se aplicam aos Aplicativos Web:

* **Várias linguagens e estruturas** -o Serviço de Aplicativo tem suporte de primeira classe para ASP.NET, Node.js, Java, PHP e Python. Você também pode executar [o PowerShell e outros scripts ou executáveis](web-sites-create-web-jobs.md) nas VMs do Serviço de Aplicativo.
* **Otimização de DevOps** - configure a [implantação e integração contínua](app-service-continuous-deployment.md) com o Visual Studio Team Services, o GitHub ou BitBucket. Promova atualizações por meio de [ambientes de preparo e teste](web-sites-staged-publishing.md). Gerencie aplicativos no Serviço de Aplicativo usando o [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou a [CLI (interface de linha de comando de plataforma cruzada)](../cli-install-nodejs.md).
* **Escala global com alta disponibilidade** - escale [verticalmente](web-sites-scale.md) ou [horizontalmente](../monitoring-and-diagnostics/insights-how-to-scale.md) de forma manual ou automática. Hospede os aplicativos em qualquer lugar na infraestrutura de datacenter global da Microsoft, e o [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) do Serviço de Aplicativo promete alta disponibilidade.
* **Conexões com plataformas SaaS e dados locais** - escolha entre mais de 50 [conectores](../connectors/apis-list.md) para sistemas corporativos (como SAP, Siebel e Oracle), serviços de SaaS (como Salesforce e Office 365) e serviços de Internet (como Facebook e Twitter). Acesse dados locais usando [Conexões Híbridas](../biztalk-services/integration-hybrid-connection-overview.md) e [Redes Virtuais do Azure](web-sites-integrate-with-vnet.md).
* **Segurança e conformidade** - o Serviço de Aplicativo está em [conformidade com ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/).
* **Modelos de aplicativos** - escolha dentre uma lista abrangente de modelos de aplicativos no [Azure Marketplace](https://azure.microsoft.com/marketplace/) que permitem usar um assistente para instalar vários tipos populares de software livre, como WordPress, Joomla e Drupal.
* **Integração do visual Studio** -ferramentas dedicadas no Visual Studio simplificam o trabalho de criar, implantar, depurar e gerenciar.

Além disso, um aplicativo Web pode tirar proveito dos recursos oferecidos por [Aplicativos de API](app-service-web-tutorial-rest-api.md) (como suporte a CORS) e [Aplicativos Móveis](../app-service-mobile/app-service-mobile-value-prop.md) (como notificações por push). 

Além dos Aplicativos Web no Serviço de Aplicativo, o Azure oferece outros serviços que podem ser usados para hospedar sites e aplicativos Web. Os Aplicativos Web são a melhor opção para a maioria dos cenários.  Para a arquitetura de microsserviço, considere o [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric), e se você precisar de mais controle sobre as VMs nas quais seu código é executado, considere as [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Para saber mais sobre como escolher entre esses serviços do Azure, confira [Comparação entre o Serviço de Aplicativo do Azure, Máquinas Virtuais, Service Fabric e Serviços de Nuvem do Azure](choose-web-site-cloud-service-vm.md).

## <a name="getting-started"></a>Introdução
Para começar com a implantação do código de exemplo em um novo aplicativo Web no Serviço de Aplicativo, siga um dos tutoriais na caixa suspensa a seguir. Você precisará de uma conta gratuita do Azure.

> [!div class="op_single_selector"]
> * [Implantar seu primeiro aplicativo Web ASP.NET no Azure em cinco minutos](app-service-web-get-started-dotnet.md)
> * [Implantar seu primeiro aplicativo Web PHP no Azure em cinco minutos](app-service-web-get-started-php.md)
> * [Implantar seu primeiro aplicativo Web Node.js no Azure em cinco minutos](app-service-web-get-started-nodejs.md)
> * [Implantar seu primeiro aplicativo Web Java no Azure em cinco minutos](app-service-web-get-started-java.md)
> * [Implantar seu primeiro aplicativo Web Python no Azure em cinco minutos](app-service-web-get-started-python.md)
> * [Implantar seu primeiro site HTML no Azure em cinco minutos](app-service-web-get-started-html.md)
> 
> 

> [!NOTE]
> Você pode [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/) sem uma conta do Azure. Crie um aplicativo inicial e brinque com ele por até uma hora: não é necessário cartão de crédito ou compromissos.
> 
> 
