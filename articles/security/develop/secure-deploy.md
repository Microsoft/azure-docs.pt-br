---
title: Implantar aplicativos seguros no Microsoft Azure
description: Este artigo aborda as práticas recomendadas a serem consideradas durante as fases de lançamento e resposta do seu projeto de aplicativo Web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 4c71ddbf1d2b435697b2707acf0b1262f2c5dc31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517182"
---
# <a name="deploy-secure-applications-on-azure"></a>Implantar aplicativos seguros no Azure
Neste artigo, apresentamos as atividades de segurança e os controles a serem considerados ao implantar aplicativos para a nuvem. As perguntas de segurança e os conceitos a serem considerados durante as fases de lançamento e resposta do [SDL (Microsoft Security Development Lifecycle)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) são abordados. O objetivo é ajudá-lo a definir atividades e serviços do Azure que você pode usar para implantar um aplicativo mais seguro.

As seguintes fases do SDL são abordadas neste artigo:

- Versão
- Resposta

## <a name="release"></a>Versão
O foco da fase de lançamento está preparando um projeto para a versão pública.
Isso inclui formas de planejamento para executar efetivamente tarefas de manutenção de lançamento e resolver vulnerabilidades de segurança que podem ocorrer posteriormente.

### <a name="check-your-applications-performance-before-you-launch"></a>Verifique o desempenho do aplicativo antes de iniciar

Verifique o desempenho do aplicativo antes de iniciá-lo ou implantar atualizações na produção. Execute [testes de carga](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) baseados em nuvem usando o Visual Studio para encontrar problemas de desempenho em seu aplicativo, melhorar a qualidade da implantação, garantir que seu aplicativo esteja sempre ativo ou disponível e que seu aplicativo possa lidar com o tráfego para seu lançamento.

### <a name="install-a-web-application-firewall"></a>Instalar um firewall do aplicativo Web

Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. O comum entre essas explorações são ataques de injeção de SQL e ataques de script entre sites. Impedir esses ataques no código do aplicativo pode ser desafiador. Ele pode exigir manutenção rigorosa, aplicação de patches e monitoramento em muitas camadas da topologia do aplicativo. Um WAF centralizado ajuda a tornar o gerenciamento de segurança mais simples. Uma solução WAF também pode reagir a uma ameaça de segurança por meio da aplicação de patch em uma vulnerabilidade conhecida em um local central em relação à proteção de cada aplicativo Web individual.

O [WAF Gateway aplicativo Azure](../../web-application-firewall/ag/ag-overview.md) fornece proteção centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns. O WAF é baseado em regras dos [conjuntos de regras principais do OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,0 ou 2.2.9.

### <a name="create-an-incident-response-plan"></a>Criar um plano de resposta a incidentes

Preparar um plano de resposta a incidentes é crucial para ajudá-lo a resolver novas ameaças que podem surgir ao longo do tempo. Preparar um plano de resposta a incidentes inclui a identificação de contatos de emergência de segurança apropriados e o estabelecimento de planos de serviço de segurança para o código herdado de outros grupos na organização e para código de terceiros licenciado.

### <a name="conduct-a-final-security-review"></a>Conduzir uma revisão de segurança final

Revisar deliberadamente todas as atividades de segurança que foram executadas ajudam a garantir a prontidão para o aplicativo ou a versão do software. A FSR (análise de segurança final) geralmente inclui examinar modelos de ameaças, saídas de ferramentas e desempenho em relação às Gates de qualidade e barras de bugs que foram definidas na fase de requisitos.

### <a name="certify-release-and-archive"></a>Certificar liberação e arquivo morto

A certificação de software antes de uma versão ajuda a garantir que os requisitos de segurança e privacidade sejam atendidos. O arquivamento de todos os dados pertinentes é essencial para executar tarefas de manutenção de lançamento. O arquivamento também ajuda a reduzir os custos de longo prazo associados à engenharia de software sustentada.

## <a name="response"></a>Resposta
A fase pós-lançamento da resposta na equipe de desenvolvimento está sendo capaz e disponível para responder adequadamente a quaisquer relatórios de vulnerabilidades e ameaças de software emergentes.

### <a name="execute-the-incident-response-plan"></a>Executar o plano de resposta a incidentes

Ser capaz de implementar o plano de resposta a incidentes instituido na fase de lançamento é essencial para ajudar a proteger os clientes contra a segurança de software ou vulnerabilidades de privacidade que surgiram.

### <a name="monitor-application-performance"></a>Monitorar desempenho do aplicativo

O monitoramento contínuo de seu aplicativo depois de implantado pode ajudar a detectar problemas de desempenho, bem como vulnerabilidades de segurança.
Os serviços do Azure que auxiliam no monitoramento de aplicativos são:

  - Azure Application Insights
  - Central de Segurança do Azure

#### <a name="application-insights"></a>Application Insights

O [Application insights](../../azure-monitor/app/app-insights-overview.md) é um serviço de gerenciamento de desempenho de aplicativos (APM) extensível para desenvolvedores da Web em várias plataformas. Use-o para monitorar seu aplicativo Web online. Application Insights detecta automaticamente anomalias de desempenho. Ele inclui ferramentas de análise poderosas para ajudá-lo a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo. Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo.

#### <a name="azure-security-center"></a>Central de Segurança do Azure

A [central de segurança do Azure](../../security-center/security-center-introduction.md) ajuda você a prevenir, detectar e responder a ameaças com maior visibilidade no (e controle sobre) a segurança de seus recursos do Azure, incluindo aplicativos Web. A central de segurança do Azure ajuda a detectar ameaças que poderiam passar despercebidas. Ele funciona com várias soluções de segurança.

A camada gratuita da Central de Segurança oferece segurança limitada somente para seus recursos do Azure. A [camada Standard da central de segurança](../../security-center/security-center-get-started.md) estende esses recursos para recursos locais e outras nuvens.
A central de segurança Standard ajuda você a:

  - Encontre e corrija vulnerabilidades de segurança.
  - Aplicar controles de acesso e de aplicativo para bloquear atividades mal-intencionadas.
  - Detecte ameaças usando análise e inteligência.
  - Responda rapidamente quando estiver sob ataque.

## <a name="next-steps"></a>Próximas etapas
Nos artigos a seguir, recomendamos os controles de segurança e as atividades que podem ajudá-lo a projetar e desenvolver aplicativos seguros.

- [Criar aplicativos seguros](secure-design.md)
- [Desenvolver aplicativos e segurança](secure-develop.md)