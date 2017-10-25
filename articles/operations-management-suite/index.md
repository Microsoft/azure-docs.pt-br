---
title: "Documentação do (OMS) Azure Operations Management Suite - Tutoriais | Microsoft Docs"
description: "O OMS (Microsoft Operations Management Suite) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda a gerenciar e proteger sua infraestrutura local e de nuvem. Este artigo identifica os diferentes serviços incluídos no OMS e fornece links para o conteúdo detalhado."
services: operations-management-suite
author: czeumault
manager: carolz
layout: LandingPage
ms.assetid: 
ms.service: operations-management-suite
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
ms.openlocfilehash: 12f959376d4923e4e2481e37108ade632ac14902
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-operations-management-suite-oms"></a>O que é Operations Management Suite (OMS)?
O OMS (Microsoft Operations Management Suite) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda a gerenciar e proteger sua infraestrutura local e de nuvem.  Como o OMS é implementado como um serviço baseado em nuvem, é possível colocá-lo em funcionamento com investimentos mínimos em serviços de infraestrutura.  Novos recursos são entregues automaticamente, representando uma economia em manutenção contínua e em custos de atualização.

Além de fornecer serviços importantes por conta própria, o OMS pode integrar a componentes do System Center, como o System Center Operations Manager, para estender seus investimentos de gerenciamento existentes para a nuvem.  O System Center e o OMS podem trabalhar juntos para proporcionar uma experiência completa de gerenciamento híbrido.

As seções a seguir fornecem uma descrição de alto nível das áreas diferentes de valor do OMS, bem como os serviços responsáveis por sua implementação.  Consulte a arquitetura do OMS para obter uma visão geral dos diferentes componentes do OMS antes de examinar a documentação detalhada de cada um deles.

## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Insight and Analytics](media/operations-management-suite-overview/icon-insight-analytics.png) Insight and Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) o ajuda a coletar, correlacionar, pesquisar e agir em relação a dados de desempenho e log gerados por sistemas operacionais e aplicativos. Ela fornece análises operacionais em tempo real usando pesquisa integrada e painéis personalizados, a fim de analisar prontamente milhões de registros em todas as suas cargas de trabalho e servidores, independentemente de sua localização física.

Você pode adicionar soluções que definem os dados a serem coletados e a lógica da análise – tudo de maneira fácil ao Log Analytics.  As soluções podem incluir funcionalidade adicional que é fornecida automaticamente para os agentes com pouca ou nenhuma configuração.  Além de usar as ferramentas de análise fornecidas pelas soluções individuais, é possível executar pesquisas personalizadas em todo o conjunto de dados para correlacionar dados entre sistemas e aplicativos.  

## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![Automação e Controle](media/operations-management-suite-overview/icon-automation-control.png) Automação e Controle
A Automação do Azure automatiza processos administrativos com [runbooks](../automation/automation-runbook-types.md) que se baseiam no PowerShell e que são executados na nuvem do Azure.  Os runbooks podem acessar qualquer produto ou serviço que pode ser gerenciado com o PowerShell, incluindo recursos em outras nuvens como o AWS (Amazon Web Services).  Eles também podem ser executados em um servidor em seu data center local para gerenciar os recursos locais.

A Automação do Azure fornece o gerenciamento de configuração com o [DSC do PowerShell](../automation/automation-dsc-overview.md).  Você pode criar e gerenciar recursos DSC hospedados no Azure e aplicá-los aos sistemas na nuvem e locais para definir e impor automaticamente suas configurações.

## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![Proteção e recuperação](media/operations-management-suite-overview/icon-protection-recovery.png) Proteção e recuperação de desastre
[Backup do Azure](http://azure.microsoft.com/documentation/services/backup) protege seus dados de aplicativos e os retém por vários anos, sem nenhum investimento de capital e custos operacionais mínimos.  Ele pode fazer backup de dados de servidores físicos e virtuais do Windows, além de cargas de trabalho de aplicativos, como SQL Server e SharePoint.  Também pode ser usado pelo DPM (System Center Data Protection Manager) para replicar dados protegidos no Azure para redundância e armazenamento de longo prazo.

[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) contribui para sua estratégia de BCDR (continuidade dos negócios e recuperação de desastre) orquestrando a replicação, o failover e a recuperação de máquinas virtuais Hyper-V locais, máquinas virtuais VMware e servidores físicos Windows/Linux. É possível replicar computadores em um data center secundário ou estender seu data center replicando-os no Azure. A Recuperação de Site também fornece failover e recuperação simples para cargas de trabalho. Ela integra mecanismos de recuperação de desastre, como o SQL Server AlwaysOn, e fornece planos de recuperação para failover fácil de cargas de trabalho que são organizadas em camadas em vários computadores.

## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![Segurança e Conformidade do OMS](media/operations-management-suite-overview/icon-security-compliance.png) Segurança e Conformidade
A Segurança e Conformidade ajuda a identificar, avaliar e atenuar os riscos de segurança à sua infraestrutura.  Esses recursos do OMS são implementados por meio de várias soluções do Log Analytics que analisam os dados de log e a configuração dos sistemas de agente para ajudá-lo a garantir a segurança contínua de seu ambiente.

* A [solução de Segurança e Auditoria](oms-security-getting-started.md) coleta e analisa eventos de segurança nos sistemas gerenciados para identificar atividades suspeitas.
* A [solução de Antimalware](../log-analytics/log-analytics-malware.md) relata o status de proteção antimalware nos sistemas gerenciados.  
* A solução de Atualizações do Sistema executa uma análise das atualizações de segurança e outras atualizações nos sistemas gerenciados, para que você identifique facilmente os sistemas que necessitam de patches.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Saiba mais sobre a [Automação do Azure](../automation/automation-intro.md).
* Saiba mais sobre o [Backup do Azure](http://azure.microsoft.com/documentation/services/backup).
* Saiba mais sobre o [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).

