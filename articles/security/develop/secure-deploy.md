---
title: Implantar aplicativos seguros no Microsoft Azure
description: Este artigo discute as práticas recomendadas a serem considerados durante as fases de lançamento e a resposta do seu projeto de aplicativo web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: e8249113ee65c28414c79f00c53d11596673434b
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144445"
---
# <a name="deploy-secure-applications-on-azure"></a>Implantar aplicativos seguros no Azure
Neste artigo, apresentamos as atividades de segurança e controles a serem considerados ao implantar aplicativos para a nuvem. Perguntas de segurança e conceitos a serem considerados durante as fases de lançamento e a resposta da Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) são abordados. O objetivo é ajudá-lo a definir as atividades e serviços do Azure que você pode usar para implantar um aplicativo mais seguro.

As seguintes fases SDL são abordadas neste artigo:

- Versão
- Response

## <a name="release"></a>Versão
O foco da fase de versão está preparando um projeto para a versão pública.
Isso inclui o planejamento de maneiras de executar tarefas de manutenção pós-lançamento efetivamente e resolver vulnerabilidades de segurança que podem ocorrer mais tarde.

### <a name="check-your-applications-performance-before-you-launch"></a>Verificar o desempenho do aplicativo antes de iniciar

Verifique o desempenho do aplicativo antes de iniciá-lo ou implantar atualizações em produção. Executar com base em nuvem [testes de carga](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) usando o Visual Studio para localizar problemas de desempenho em seu aplicativo, melhorar a qualidade de implantação, certifique-se de que seu aplicativo estará sempre disponível ou disponível e que seu aplicativo pode manipular o tráfego para o seu lançamento.

### <a name="install-a-web-application-firewall"></a>Instalar um firewall do aplicativo Web

Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. São comuns entre essas explorações ataques de injeção de SQL e ataques de script entre sites. Impedir esses ataques no código do aplicativo pode ser desafiador. Ele pode exigir manutenção rigorosa, aplicação de patches e monitoramento em várias camadas da topologia do aplicativo. Um WAF centralizado ajuda a simplificar o gerenciamento de segurança. Uma solução WAF também pode reagir a uma ameaça à segurança, ao aplicar um patch uma vulnerabilidade conhecida em um local central em vez de proteger todos os aplicativos web individuais.

O [WAF de Gateway de aplicativo do Azure](https://docs.microsoft.com/azure/application-gateway/waf-overview) fornece proteção centralizada de seus aplicativos web contra vulnerabilidades e explorações comuns. O WAF é baseado nas regras do [conjuntos de regras OWASP principais](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9.

### <a name="create-an-incident-response-plan"></a>Criar um plano de resposta a incidentes

Preparar um plano de resposta a incidentes é crucial para ajudar você a atender a novas ameaças que podem surgir ao longo do tempo. Preparar um plano de resposta a incidentes inclui a identificação de contatos de emergência de segurança apropriados e estabelecendo uma segurança para o código que é herdado de outros grupos da organização e para o código de terceiros licenciado de planos de manutenção.

### <a name="conduct-a-final-security-review"></a>Conduzir uma revisão final de segurança

Revisar deliberadamente todas as atividades de segurança que foram executadas ajuda a garantir a prontidão para seu aplicativo ou uma versão de software. A revisão final de segurança (FSR) geralmente inclui examinando os modelos de ameaças, saídas de ferramentas e desempenho em relação as restrições de qualidade e as barras de bugs que foram definidas na fase de requisitos.

### <a name="certify-release-and-archive"></a>Certificar versão e o arquivo morto

Certificação de software antes de uma versão ajuda a garantir que os requisitos de segurança e privacidade são atendidos. Arquivamento de todos os dados pertinentes é essencial para realizar tarefas de manutenção pós-lançamento. Arquivamento também ajuda a diminuir os custos de longo prazo associados com prolongada de engenharia de software.

## <a name="response"></a>Response
Centraliza a fase de pós-versão de resposta na equipe de desenvolvimento, sendo capaz de e para responder adequadamente a todos os relatórios de vulnerabilidades e ameaças emergentes de software está disponível.

### <a name="execute-the-incident-response-plan"></a>Executar o plano de resposta a incidentes

Ser capaz de implementar o plano de resposta a incidentes instituído na fase de versão é essencial para ajudar a proteger os clientes contra vulnerabilidades de segurança ou privacidade de software que surgem.

### <a name="monitor-application-performance"></a>Monitorar desempenho do aplicativo

Monitoramento contínuo de seu aplicativo após sua implantação potencialmente o ajuda a detectar problemas de desempenho, bem como vulnerabilidades de segurança.
Os serviços do Azure que ajudam com o monitoramento de aplicativo são:

  - Azure Application Insights
  - Central de Segurança do Azure

#### <a name="application-insights"></a>Application Insights

O [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço APM (Gerenciamento de Desempenho de Aplicativos) extensível para desenvolvedores da Web em várias plataformas. Use-o para monitorar seu aplicativo Web online. Application Insights detecta automaticamente anomalias de desempenho. Ele inclui ferramentas de análise avançadas para ajudá-lo a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo. Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo.

#### <a name="azure-security-center"></a>Central de Segurança do Azure

[A Central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) ajuda a prevenir, detectar e responder a ameaças com maior visibilidade (e controle sobre a) a segurança dos recursos do Azure, incluindo aplicativos web. A Central de segurança do Azure ajuda a detectar ameaças que poderiam passar despercebidas. Ele funciona com várias soluções de segurança.

A camada gratuita da Central de Segurança oferece segurança limitada somente para seus recursos do Azure. O [camada central de segurança Standard](https://docs.microsoft.com/azure/security-center/security-center-onboarding) estende esses recursos para recursos locais e em outras nuvens.
Central de segurança Standard ajuda você a:

  - Localize e corrija vulnerabilidades de segurança.
  - Aplique controles de acesso e de aplicativo para bloquear atividades mal-intencionadas.
  - Detecte ameaças usando análise e inteligência.
  - Responda rapidamente quando sob ataque.

## <a name="next-steps"></a>Próximas etapas
Os artigos a seguir, é recomendável que os controles de segurança e as atividades que podem ajudá-lo a projetar e desenvolvem aplicativos seguros.

- [Design de aplicativos seguros](secure-design.md)
- [Desenvolver aplicativos seguros](secure-develop.md)
