---
title: Monitoramento no Microsoft Azure | Microsoft Docs
description: "Opções para monitorar qualquer coisa no Microsoft Azure. Azure Monitor, Application Insights e Log Analytics"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: robb
ms.openlocfilehash: e164cbd910ccc38610c7aef37d25ff1b4413038d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-monitoring-in-microsoft-azure"></a>Visão Geral do Monitoramento no Microsoft Azure
Este artigo fornece uma visão geral das ferramentas e serviços envolvidos no monitoramento global do Microsoft Azure. Aplicável a:
- Usar os serviços do Azure para monitorar a infraestrutura e os aplicativos do Azure
- Usar os serviços do Azure para monitorar a infraestrutura e os aplicativos não Azure
- Usar serviços não Azure para monitorar a infraestrutura e os aplicativos do Azure

Ele aborda os vários produtos e serviços disponíveis e como eles funcionam juntos. Ele pode ajudar a determinar quais ferramentas são as mais apropriadas para você em quais casos.  

## <a name="why-use-azures-monitoring-services"></a>Por que usar os serviços de monitoramento do Azure?

Problemas de desempenho em seu aplicativo de nuvem podem afetar seus negócios. Com diversos componentes interconectados e frequentes lançamentos, degradações podem ocorrer a qualquer momento. E se você estiver desenvolvendo um aplicativo, seus usuários normalmente descobrirão problemas que você não encontrou durante os testes. Você precisa tomar conhecimento dessas questões imediatamente e precisa ter ferramentas para diagnosticar e corrigir problemas. Além disso, os problemas no seu aplicativo frequentemente são resultado da infraestrutura subjacente em que esses aplicativos são executados, então, ter uma visão holística do seu aplicativo e da infraestrutura é fundamental para monitorar o ambiente do Azure. O Microsoft Azure tem uma variedade de ferramentas para identificar e resolver esses problemas.

## <a name="how-do-i-monitor-my-azure-environment"></a>Como monitorar o ambiente do Azure?

Há uma variedade de ferramentas para monitorar o ambiente do Azure, do código do aplicativo em execução no Azure aos serviços e a infraestrutura que executam esse código. Essas ferramentas funcionam juntas para oferecer monitoramento abrangente de nuvem e incluem:

-   **Azure Monitor**: serviço do Azure que funciona como um pipeline consolidado para todos os dados de monitoramento dos serviços do Azure. Dá acesso às métricas de desempenho e aos eventos que descrevem a operação de infraestrutura do Azure e quaisquer serviços do Azure que você estiver usando. O Azure Monitor é um pipeline de dados de monitoramento do ambiente do Azure e oferece esses dados diretamente para o Log Analytics, bem como ferramentas de terceiros em que é possível obter insights desses dados e combiná-los com os dados de recursos locais ou outros recursos de nuvem.

-   **Application Insights**: serviço do Azure que oferece monitoramento do desempenho de aplicativos e análise de usuário. Monitora o código gravado e aplicativos implantados no Azure ou local/em outras nuvens. Por meio da instrumentação do aplicativo com o SDK do Application Insights, é possível obter acesso a uma variedade de dados, incluindo tempos de resposta de dependências, rastreamentos de exceção, depuração de instantâneos e perfis de execução. Oferece ferramentas avançadas para analisar a telemetria do aplicativo enquanto ele é desenvolvido e operado. Integra-se profundamente com o Visual Studio para que seja possível abordar diretamente a(s) linha(s) de código com problema e corrigi-las e também oferece a análise de uso dos seus aplicativos pelo cliente a gerentes de produto.

-   **Log Analytics**: anteriormente conhecido como OMS Log Analytics, é um serviço do Azure que ingere os dados de log e métrica dos serviços do Azure (por meio do Azure Monitor), VMs do Azure e infraestruturas locais/em outras nuvens e oferece uma pesquisa de logs flexíveis e análises integradas sobre esses dados. Fornece ferramentas mais sofisticadas para analisar dados entre origens, permite consultas complexas em todos os logs e pode alertar proativamente sobre condições especificadas.  Você pode até mesmo coletar dados personalizados em seu repositório central para que possa consultar e visualizá-los. Também é possível tirar proveito das soluções internas do Log Analytics para obter imediatamente insights sobre a segurança e a funcionalidade da sua infraestrutura.

## <a name="accessing-monitoring-in-the-azure-portal"></a>Acessando o monitoramento no Portal do Azure
Agora, todos os serviços de monitoramento do Azure estão disponíveis em um único painel de interface do usuário. Para obter mais informações sobre como acessar essa área, consulte [Introdução ao Azure Monitor](monitoring-get-started.md). 

Você também pode acessar as funções de monitoramento de recursos específicos realçando esses recursos e fazendo uma busca detalhada em suas opções de monitoramento. 

## <a name="examples-of-when-to-use-which-tool"></a>Exemplos de quando usar qual ferramenta 

As seções a seguir mostram alguns cenários básicos e quais ferramentas devem ser usadas. 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>Cenário 1 – Corrigir erros em um aplicativo do Azure em desenvolvimento   

**A melhor opção é usar o Application Insights, o Azure Monitor e o Visual Studio juntos**

Agora, o Azure agora fornece toda a capacidade do depurador do Visual Studio na nuvem. Configure o Azure Monitor para enviar telemetria ao Application Insights. Habilite o Visual Studio para incluir o SDK do Application Insights em seu aplicativo. Uma vez no Application Insights, você pode usar o Mapa do aplicativo para descobrir visualmente quais partes do seu aplicativo em execução estão íntegras ou não. Para as partes que não estão íntegras, erros e exceções já estão disponíveis para exploração. Você pode usar as diferentes análises no Application Insights para obter mais detalhes. Se não tiver certeza quanto ao erro, você poderá usar o depurador do Visual Studio para rastrear o código e identificar o problema. 

Para obter mais informações, consulte [Monitorando Aplicativos Web](../application-insights/app-insights-azure-web-apps.md) e consulte o sumário à esquerda para obter instruções sobre diversos tipos de aplicativos e linguagens.  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>Cenário 2 – Depurar um aplicativo Web .NET do Azure para encontrar erros que aparecem somente na produção 

> [!NOTE]
> Esses recursos estão no modo de versão prévia. 

**A melhor opção é usar o Application Insights e, se possível, o Visual Studio para ter a experiência de depuração completa.**

Use o Depurador instantâneo do Application Insights para depurar seu aplicativo. Quando um determinado limite de erro ocorre com componentes de produção, o sistema captura automaticamente telemetria em janelas de tempo chamadas "instantâneos". O valor capturado é seguro para uma nuvem de produção porque é pequeno o suficiente para não afetar o desempenho, mas significativo o suficiente para permitir o rastreamento.  O sistema pode capturar vários instantâneos. Você pode examinar um ponto no tempo no Portal do Azure ou usar o Visual Studio para ter a experiência completa. Com o Visual Studio, os desenvolvedores podem percorrer o instantâneo como se estivessem depurando em tempo real. Quadros, memória, parâmetros e variáveis locais estão disponíveis. Os desenvolvedores devem obter acesso a esses dados de produção por meio de uma função RBAC.  

Para obter mais informações, consulte [Depuração instantânea](../application-insights/app-insights-snapshot-debugger.md). 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>Cenário 3 – Depurar um aplicativo do Azure que usa contêineres ou microsserviços 

**O mesmo que o cenário 1. Use o Application Insights, o Azure Monitor e o Visual Studio juntos** O Application Insights também dá suporte à coleta de telemetria de processos em execução dentro de contêineres e de microsserviços (Kubernetes, Docker, Service Fabric do Azure). Para obter mais informações, [assista a este vídeo sobre a depuração de contêineres e microsserviços](https://go.microsoft.com/fwlink/?linkid=848184). 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>Cenário 4 – Solucionar problemas de desempenho em seu aplicativo do Azure

O [Application Insights Profiler](../application-insights/app-insights-profiler.md) foi projetado para ajudar a solucionar esses tipos de problemas. Você pode identificar e solucionar problemas de desempenho para aplicativos em execução nos Serviços de Aplicativos (Aplicativos Web, Aplicativos Lógicos, Aplicativos Móveis, Aplicativos de API) e outros recursos de computação, como Máquinas Virtuais, VMSS (conjunto de dimensionamento de máquinas virtuais), Serviços de Nuvem e Service Fabric. 

> [!NOTE]
> A capacidade de criar perfis de Máquinas Virtuais, VMSS (conjunto de dimensionamento de máquinas virtuais), Serviços de Nuvem e Service Fabric está em versão prévia.   

Além disso, você será notificado proativamente por email sobre determinados tipos de erros, como lentidão para o carregamento de páginas, pela ferramenta de Detecção Inteligente.  Você não precisa fazer nenhuma configuração nessa ferramenta. Para obter mais informações, consulte [Detecção Inteligente – Anomalias de Desempenho](../application-insights/app-insights-proactive-performance-diagnostics.md).



## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre

* [Azure Monitor em um vídeo do Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Introdução ao Azure Monitor](monitoring-get-started.md)
* [Diagnóstico do Azure](../azure-diagnostics.md) se você estiver tentando diagnosticar problemas em seu Serviço de Nuvem, Máquina Virtual, conjunto de dimensionamento de máquinas virtuais ou aplicativo do Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) se você tiver problemas de diagnóstico em seu aplicativo Web do Serviço de Aplicativo.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) e a solução de monitoramento de produção [Operations Management Suite](https://www.microsoft.com/oms/)
