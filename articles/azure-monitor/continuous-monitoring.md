---
title: Monitoramento contínuo com o Azure Monitor | Microsoft Docs
description: Descreve as etapas específicas de uso do Azure Monitor para ativar o monitoramento contínuo em todos os fluxos de trabalho.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: 2fe9d7e4da8950cee2dcb2fbcfb26355ace7285d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044010"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Monitoramento contínuo com o Azure Monitor

Monitoramento contínuo refere-se ao processo e à tecnologia necessários para incorporar o monitoramento em cada fase dos ciclos de vida de operações de TI e DevOps. Ele ajuda a garantir de maneira contínua a integridade, o desempenho e a confiabilidade do aplicativo e da infraestrutura à medida que passa do desenvolvimento à produção. O monitoramento contínuo se baseia nos conceitos de Integração Contínua e Implantação Contínua (CI/CD), que ajudam você a desenvolver e fornecer software de maneira mais rápida e confiável, para oferecer vantagens contínuas aos usuários.

O [Azure Monitor](overview.md) é a solução de monitoramento unificada do Azure que oferece a capacidade de observação de pilha completa entre aplicativos e infraestrutura local ou na nuvem. Ele funciona perfeitamente com [Visual Studio e o Visual Studio Code](https://visualstudio.microsoft.com/), durante o desenvolvimento e teste, e é integrado ao [Azure DevOps](/azure/devops/user-guide/index) para gerenciamento de versão e de itens de trabalho, durante a implantação e as operações. Ele oferece ainda integração entre as ferramentas ITSM e SIEM de sua escolha para acompanhar incidentes em seus processos de TI existentes.

Este artigo descreve as etapas específicas de uso do Azure Monitor para ativar o monitoramento contínuo em todos os fluxos de trabalho. Ele inclui links para documentação adicional que oferece detalhes sobre a implementação de diferentes recursos.


## <a name="enable-monitoring-for-all-your-applications"></a>Habilite o monitoramento de todos os aplicativos
Para ganhar capacidade de observação em todo o ambiente, você precisa ativar o monitoramento em todos os aplicativos e serviços da web. Isso permitirá que você visualize facilmente transações e conexões de ponta a ponta em todos os componentes.

- O [Azure DevOps Projects](../devops-project/overview.md) oferece uma experiência simplificada com seu código e repositório Git existentes ou escolhe um dos aplicativos de exemplo para criar um pipeline de CI (integração contínua) e CD (entrega contínua) para o Azure.
- [O monitoramento contínuo do pipeline de lançamento do DevOps](./app/continuous-monitoring.md) permite que você bloqueie ou remova a implantação com base em dados de monitoramento.
- O [Status Monitor](./app/monitor-performance-live-website-now.md) permite que você instrumentalize um aplicativo .NET ativo no Windows com o Azure Application Insights, sem precisar modificar ou reimplantar o código.
- Se você tiver acesso ao código para seu aplicativo, habilite o monitoramento completo com [Application insights](./app/app-insights-overview.md) instalando o SDK do Application Insights do Azure monitor para [.net](./app/asp-net.md), [.net Core](./app/asp-net-core.md), [Java](./app/java-get-started.md), [Node.js](./app/nodejs-quick-start.md)ou [qualquer outra linguagem de programação](./app/platforms.md). Isso permite que você especifique eventos personalizados, métricas ou exibições de página relevantes para o aplicativo e a empresa.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Habilite o monitoramento de toda a infraestrutura
Os aplicativos só são confiáveis se a infraestrutura subjacente é confiável. Habilitar o monitoramento em toda a infraestrutura ajudará você a obter capacidade de observação total, facilitando a descoberta de uma possível causa raiz em caso de falha. O Azure Monitor ajuda a monitorar a integridade e o desempenho de toda a infraestrutura híbrida, incluindo recursos como VMs, contêineres, armazenamento e rede.

- Você recebe automaticamente [métricas da plataforma, logs de atividades e logs de diagnósticos](agents/data-sources.md) da maioria dos recursos do Azure sem configuração.
- Habilite o monitoramento mais profundo para VMs com o [insights de VM](vm/vminsights-overview.md).
-  Habilite o monitoramento detalhado para clusters AKS com o [Azure Monitor para contêineres](containers/container-insights-overview.md).
- Adicione [soluções de monitoramento](./monitor-reference.md) a diferentes aplicativos e serviços em seu ambiente.


A [infraestrutura como código ](/azure/devops/learn/what-is-infrastructure-as-code) é o gerenciamento da infraestrutura em um modelo descritivo com o mesmo controle de versão que as equipes do DevOps usam para o código-fonte. Ela assegura mais confiabilidade e escalabilidade ao ambiente e permite que você aproveite processos semelhantes usados para gerenciar os aplicativos.

-  Use os [modelos do Gerenciador de Recursos](./logs/resource-manager-workspace.md) para habilitar o monitoramento e configurar alertas em um grande conjunto de recursos.
- Use o [Azure Policy](../governance/policy/overview.md) para aplicar diferentes regras aos recursos. Isso garante que eles permaneçam compatíveis com seus padrões empresariais e contratos de nível de serviço. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Combine recursos em grupos de recursos do Azure
Atualmente, um aplicativo típico do Azure inclui vários recursos, como VMs e Serviços de Aplicativos, ou microsserviços hospedados em Serviços de Nuvem, clusters AKS ou Service Fabric. Esses aplicativos usam com frequência dependências como Hubs de Eventos, Armazenamento, SQL e Barramento de Serviço.

- Combine recursos nos Grupos de Recursos do Azure para obter capacidade de observação total de todos os recursos que compõem seus diferentes aplicativos. O [Azure Monitor para grupos de recursos](./insights/resource-group-insights.md) oferece uma maneira simples de acompanhar a integridade e o desempenho de todo o aplicativo de pilha completa e permite pesquisar os respectivos componentes para qualquer investigação ou depuração.

## <a name="ensure-quality-through-continuous-deployment"></a>Garanta a qualidade por meio da implantação contínua
A Integração Contínua/Implantação Contínua permite integrar e implantar automaticamente alterações de código no aplicativo com base nos resultados de testes automatizados. Ela agiliza o processo de implantação e garante a qualidade das alterações antes de passar para a produção.


- Use o [Azure Pipelines](/azure/devops/pipelines) para implementar a Implantação Contínua e automatizar todo o processo, desde a confirmação de código até a produção com base nos testes de CI/CD.
- Use o [Quality Gates](/azure/devops/pipelines/release/approvals/gates) para integrar o monitoramento na pré-implantação ou pós-implantação. Isso garante que você esteja alcançando as principais métricas de integridade/desempenho (KPIs) à medida que os aplicativos passam do desenvolvimento à produção. E também assegura que as diferenças no ambiente de infraestrutura ou escala não estejam impactando negativamente os KPIs.
- [Mantenha instâncias de monitoramento separadas](./app/separate-resources.md) entre seus diferentes ambientes de implantação como Dev, Test, Canary e Prod. Isso garante que os dados coletados sejam relevantes entre os aplicativos e a infraestrutura associados. Se você precisar correlacionar dados entre ambientes, poderá usar [gráficos com vários recursos no Metrics Explorer](./essentials/metrics-charts.md) ou criar [consultas entre recursos no Azure Monitor](logs/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Crie alertas acionáveis com ações
Uma das principais vantagens do monitoramento é que os administradores podem ser notificados de maneira proativa quanto a problemas atuais ou previstos. 

- Crie [alertas no Azure Monitor](./alerts/alerts-overview.md) com base em logs e métricas para identificar os estados de falha previsíveis. Você deve ter uma meta de deixar todos os alertas acionáveis, o que significa que eles representam condições críticas reais e buscam reduzir os falsos positivos. Use os [Limites Dinâmicos](alerts/alerts-dynamic-thresholds.md) para calcular automaticamente linhas de base em dados de métricas, em vez de definir os próprios limites estáticos. 
- Defina ações para que os alertas usem os meios mais eficazes para notificar os administradores. [Ações disponíveis para notificação ](alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal) são SMS, email, notificações por push ou chamadas de voz.
- Use ações mais avançadas de [conexão à ferramenta ITSM](alerts/itsmc-overview.md) ou a outros sistemas de gerenciamento de alertas por meio de [ webhooks ](alerts/activity-log-alerts-webhook.md).
- Corrija também as situações identificadas nos alertas com os [runbooks de Automação do Azure](../automation/automation-webhooks.md) ou [Aplicativos Lógicos](/connectors/custom-connectors/create-webhook-trigger) que podem ser inicializados com alertas usando o webhooks. 
- Use o [dimensionamento automático](./autoscale/tutorial-autoscale-performance-schedule.md) para aumentar e diminuir dinamicamente seus recursos de computação com base nas métricas coletadas.

## <a name="prepare-dashboards-and-workbooks"></a>Prepare pastas de trabalho e painéis
Garantir que o desenvolvimento e as operações tenham acesso à mesma telemetria e ferramentas permite que eles visualizem padrões em todo o ambiente e minimizem o tempo médio de detecção (MTTD) e o tempo médio de restauração (MTTR).

- Prepare [painéis personalizados](./app/tutorial-app-dashboards.md) com base em métricas e logs comuns para diferentes funções na sua organização. Os painéis podem combinar dados de todos os recursos do Azure.
- Prepare [Pastas de trabalho ](./visualize/workbooks-overview.md) para garantir o compartilhamento de conhecimento entre o desenvolvimento e as operações. Eles podem ser preparados como relatórios dinâmicos, com gráficos de métricas e consultas de log ou até mesmo como guias de solução de problemas preparados por desenvolvedores para oferecer suporte ao cliente ou às operações para lidar com problemas básicos.

## <a name="continuously-optimize"></a>Otimize de forma contínua
 O monitoramento é um dos principais aspectos da popular filosofia Build-Measure-Learn (Construir, Medir, Aprender), que recomenda acompanhar continuamente os KPIs e as métricas de comportamento do usuário e depois se esforçar para otimizá-los por meio de iterações de planejamento. O Azure Monitor ajuda a coletar métricas e logs relevantes para sua empresa e adicionar novos pontos de dados na próxima implantação, conforme necessário.

- Use as ferramentas do Application Insights para [acompanhar o comportamento e o envolvimento do usuário final](./app/tutorial-users.md).
- Use a [Análise de impacto](./app/usage-impact.md) para ajudar você a priorizar em quais áreas se concentrar para gerar importantes KPIs.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os diferentes componentes do [Azure Monitor](overview.md).
- [Adicione monitoramento contínuo](./app/continuous-monitoring.md) ao seu pipeline de lançamento.