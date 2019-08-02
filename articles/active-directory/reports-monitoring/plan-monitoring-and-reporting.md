---
title: Planejar uma Azure Active Directory a implantação de relatórios e monitoramento
description: Descreve como planejar e executar implmentation de relatórios e monitoramento.
services: active-directory
documentationcenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: plan
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f68b3a312ff7681fde65154542a66767c5195ff
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406451"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Planejar uma Azure Active Directory a implantação de relatórios e monitoramento

Sua solução de relatório e monitoramento do Azure Active Directory (AD do Azure) depende dos seus requisitos legais, de segurança e operacionais e de seus processos e ambiente existentes. Este artigo apresenta as várias opções de design e orienta você para a estratégia de implantação certa.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Benefícios dos relatórios e monitoramento do Azure AD

Os relatórios do Azure AD fornecem uma exibição e logs abrangentes da atividade do Azure AD em seu ambiente, incluindo eventos de entrada, eventos de auditoria e alterações no diretório.

Os dados fornecidos permitem a você:

* Determine como seus aplicativos e serviços são usados.

* detecte possíveis riscos que afetem a integridade do seu ambiente.

* solucionar problemas que impedem seus usuários de realizar seu trabalho.

* obter informações observando eventos de auditoria de alterações no seu diretório do Azure AD.

> [!IMPORTANT]
> O monitoramento do AD do Azure permite que você encaminhe seus logs gerados pelo Azure AD Reporting para sistemas de destino diferentes. Você pode mantê-lo para uso de longo prazo ou integrá-lo com ferramentas de gerenciamento de eventos e informações de segurança (SIEM) de terceiros para obter informações sobre seu ambiente.

Com o monitoramento do AD do Azure, você pode rotear logs para:

* uma conta de armazenamento do Azure para fins de arquivamento.
* Azure Monitor logs, anteriormente conhecidos como espaço de trabalho do Azure Log Analytics, em que você pode analisar os dados, criar painéis e alertar sobre eventos específicos.
* um hub de eventos do Azure em que você pode se integrar com suas ferramentas SIEM existentes, como Splunk, Sumologic ou QRadar.

> [!NOTE]
Recentemente, começamos a usar o termo Azure Monitor logs em vez de Log Analytics. Os dados de log ainda são armazenados em um espaço de trabalho do Log Analytics e ainda são coletados e analisados pelo mesmo serviço do Log Analytics. Estamos atualizando a terminologia para refletir melhor a função dos [logs no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection). Confira as [alterações de terminologia do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand) para obter detalhes.

[Saiba mais sobre as políticas de retenção de relatório](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention).

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licenciamento e pré-requisitos para relatórios e monitoramento do Azure AD

Você precisará de uma licença do Azure AD Premium para acessar os logs de logon do Azure AD.

Para obter informações detalhadas sobre recursos e licenciamento no [Guia de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Para implantar o monitoramento e o relatório do Azure AD, você precisará de um usuário que seja um administrador global ou administrador de segurança para o locatário do Azure AD.

Dependendo do destino final dos seus dados de log, você precisará de um dos seguintes:

* Uma conta de armazenamento do Azure para a qual você tem permissões de ListKeys. Recomendamos que você use uma conta de armazenamento geral e não uma conta do Armazenamento de blobs. Para obter informações sobre preços de armazenamento, confira a [Calculadora de preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Um namespace de hubs de eventos do Azure para integração com soluções SIEM de terceiros.

* Um espaço de trabalho do Log Analytics do Azure para enviar logs aos logs do Azure Monitor.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Planejar um projeto de implantação de relatórios e monitoramento do Azure

Neste projeto, você definirá as audiências que consumirão e monitorarão relatórios e definirá sua arquitetura de monitoramento do Azure AD.

### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos

Quando os projetos de tecnologia falham, eles normalmente fazem isso devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar essas armadilhas, [Verifique se você está participando dos participantes certos](https://aka.ms/deploymentplans). Além disso, certifique-se de que as funções de Stakeholder no projeto sejam bem compreendidas documentando os participantes e suas entradas de projeto e responsabilidades.

### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique-se de forma proativa com seus usuários como sua experiência será alterada, quando ele será alterado e como obter suporte se eles tiverem problemas.

### <a name="document-your-current-infrastructure-and-policies"></a>Documente sua infraestrutura e políticas atuais

Sua infraestrutura e políticas atuais direcionarão seu design de relatórios e monitoramento. Verifique se você conhece

* Quais são as ferramentas SIEM que você está usando.

* Sua infraestrutura do Azure, incluindo contas de armazenamento e monitoramento existentes que estão sendo usados.

* Suas políticas de retenção organizacional para logs, incluindo todas as estruturas de conformidade aplicáveis necessárias. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Planejar uma implantação de relatórios e monitoramento do Azure AD

Relatórios e monitoramento são usados para atender às suas necessidades de negócios, obter informações sobre padrões de uso e não aumentar a postura de segurança de suas organizações.

### <a name="business-use-cases"></a>Casos de uso de negócios

* Necessário para a solução atender às necessidades dos negócios
* Bom para ter de atender às necessidades dos negócios
* Não aplicável

|Área |Descrição |
|-|-|
|Retenção| **Retenção de log de mais de 30 dias**. Devido aos requisitos legais ou comerciais, é necessário armazenar os logs de auditoria e os logs de logon do Azure AD por mais de 30 dias. |
|Análise| **Os logs precisam ser pesquisáveis**. Os logs armazenados precisam ser pesquisáveis com ferramentas analíticas. |
| Insights Operacionais| **Informações para várias equipes**. A necessidade de conceder acesso a diferentes usuários para obter informações operacionais, como uso do aplicativo, erros de entrada, uso de autoatendimento, tendências, etc. |
| Informações de segurança| **Informações para várias equipes**. A necessidade de conceder acesso a diferentes usuários para obter informações operacionais, como uso do aplicativo, erros de entrada, uso de autoatendimento, tendências, etc. |
| Integração em sistemas SIEM      | **Integração do Siem**. A necessidade de integrar e transmitir logs de logon do Azure AD e logs de auditoria para sistemas SIEM existentes. |

### <a name="choose-a-monitoring-solution-architecture"></a>Escolher uma arquitetura de solução de monitoramento

Com o monitoramento do AD do Azure, você pode rotear seus logs de atividade do Azure AD para um sistema que melhor atenda às suas necessidades de negócios. Você pode remantê-los para relatórios e análises de longo prazo para obter informações sobre seu ambiente e integrá-los a ferramentas SIEM.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sectionsmediareporting-deployment-plandeploy-reporting-flow-diagrampng"></a>Gráfico de fluxo de decisão![Uma imagem mostrando o que está descrito nas seções subsequentes](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Arquivar logs em uma conta de armazenamento

Ao rotear logs para uma conta de armazenamento do Azure, você pode mantê-los por mais tempo do que o período de retenção padrão descrito em nossas [políticas de retenção](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention). Use esse método se você precisar arquivar seus logs, mas não precisar integrá-los a um sistema SIEM e não precisar de consultas e análises em andamento. Você ainda pode fazer pesquisas sob demanda.

Saiba como [encaminhar os dados para sua conta de armazenamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account).

#### <a name="send-logs-to-azure-monitor-logs"></a>Enviar logs para os logs do Azure Monitor

[Os logs de Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) consolidam dados de monitoramento de fontes diferentes. Ele também fornece uma linguagem de consulta e um mecanismo de análise que fornece informações sobre a operação de seus aplicativos e o uso de recursos. Ao enviar logs de atividade do Azure AD para Azure Monitor logs, você pode recuperar, monitorar e alertar rapidamente os dados coletados. Use esse método quando você não tiver uma solução SIEM existente para a qual deseja enviar seus dados diretamente, mas quiser consultas e análises. Depois que os dados estiverem em logs de Azure Monitor, você poderá enviá-los para o Hub de eventos e daí para um SIEM, se desejar.

Saiba como [enviar dados para os logs do Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Você também pode instalar os modos de exibição pré-criados para logs de atividades do Azure AD para monitorar cenários comuns que envolvem eventos de entrada e auditoria.

Saiba como [instalar e usar as exibições do Log Analytics para logs de atividades do Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Transmitir logs para o Hub de eventos do Azure

Os logs de roteamento para um hub de eventos do Azure permitem a integração com ferramentas SIEM de terceiros. Essa integração permite combinar dados de log de atividades do Azure AD com outros dados gerenciados pelo seu SIEM para fornecer em informações mais avançadas sobre seu ambiente. 

Saiba como [enviar seus logs para um hub de eventos](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Planejar operações e segurança para relatórios e monitoramento do Azure AD

Os participantes precisam acessar os logs do Azure AD para obter informações operacionais. Os usuários provavelmente incluem membros da equipe de segurança, auditores internos ou externos e a equipe de operações de gerenciamento de identidade e acesso.

As funções do Azure AD permitem que você delegue a capacidade de configurar e exibir relatórios do Azure AD com base em sua função. Identifique quem em sua organização precisa de permissão para ler relatórios do Azure AD e qual função seria apropriada para eles. 

As funções a seguir podem ler os relatórios do Azure AD:

* Administrador Global

* Admin de Segurança

* Leitor de Segurança

* Leitor de relatórios

Saiba mais sobre as [funções administrativas do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

*Sempre aplique o conceito de privilégios mínimos para reduzir o risco de um comprometimento de conta*. Considere implementar [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) para proteger ainda mais sua organização.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Implantar relatórios e monitoramento do Azure AD

Dependendo das decisões feitas anteriormente usando as diretrizes de design acima, esta seção orientará você na documentação sobre as diferentes opções de implantação.

### <a name="consume-and-archive-azure-ad-logs"></a>Consumir e arquivar logs do Azure AD

[Localizar relatórios de atividade no Portal do Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[Usar o pacote de conteúdo do Power BI para o Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack)

[Arquivar logs do Azure AD em uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>Implementar monitoramento e análise

[Enviar logs para Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[Instalar e usar os modos de exibição do Log Analytics do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[Analisar logs de atividade do Azure AD com os logs do Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [Interpretar o esquema de logs de auditoria no Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [Interpretar o esquema de logs de entrada no Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [Transmitir logs do Azure AD para um hub de eventos do Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [Integrar logs do Azure AD com o Splunk usando o Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk)

* [Integrar logs do Azure AD ao SumoLogic usando o Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)

 

 

## <a name="next-steps"></a>Próximas etapas

Considere a implementação de [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 

Considere implementar o [RBAC (controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/overview)

 
