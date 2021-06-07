---
title: Pastas de trabalho do Azure Sentinel usadas com frequência | Microsoft Docs
description: Saiba mais sobre as pastas de trabalho usadas com mais frequência para usar recursos populares e internos do Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/07/2021
ms.author: bagol
ms.openlocfilehash: a37501498a9222025860702a7f29dccc9abfc8f7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102450055"
---
# <a name="commonly-used-azure-sentinel-workbooks"></a>Pastas de trabalho comumente usadas do Azure Sentinel

A tabela a seguir lista as pastas de trabalho internas do Azure Sentinel usadas com mais frequência.

Acesse pastas de trabalho no Azure Sentinel em pastas de trabalho de **Gerenciamento de ameaças** à  >   esquerda e procure a pasta de trabalho que você deseja usar. Para obter mais informações, consulte [tutorial: Visualizar e monitorar seus dados](tutorial-monitor-your-data.md).

|Nome da pasta de trabalho  |Descrição  |
|---------|---------|
|**Eficiência da análise**     |  Fornece informações sobre a eficácia de suas regras de análise para ajudá-lo a obter um melhor desempenho do SOC. <br><br>Para obter mais informações, consulte [o kit de ferramentas para Data-Driven Socs](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152).|
|**Atividades do Azure**     |     Fornece informações abrangentes sobre a atividade do Azure da sua organização analisando e correlacionando todas as operações e eventos do usuário. <br><br>Para obter mais informações, consulte [auditoria com logs de atividades do Azure](audit-sentinel-data.md#auditing-with-azure-activity-logs).    |
|**Logs de auditoria do Azure AD**     |  Usa Azure Active Directory logs de auditoria para fornecer informações sobre cenários do Azure AD. <br><br>Para obter mais informações, consulte  [início rápido: introdução ao Azure Sentinel](quickstart-get-visibility.md).     |
|**Logs de auditoria, atividade e entrada do Azure AD**     |   Fornece informações sobre Azure Active Directory auditoria, atividade e dados de entrada com uma pasta de trabalho. Essa pasta de trabalho pode ser usada por administradores de segurança e do Azure.      |
|**Logs de entrada do Azure AD**     | Usa os logs de entrada do Azure AD para fornecer informações sobre cenários do Azure AD.        |
|**Certificação de modelo de maturidade segurança cibernética (CMMC)**     |   Fornece um mecanismo para exibir consultas de log alinhadas aos controles CMMC no portfólio da Microsoft, incluindo ofertas de segurança da Microsoft, Office 365, Teams, Intune, área de trabalho virtual do Windows e assim por diante. <br><br>Para obter mais informações, consulte [pasta de trabalho CMMC (certificação do modelo de maturidade segurança cibernética) em visualização pública](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cybersecurity-maturity-model-certification-cmmc/ba-p/2111184).|
|**Monitoramento de integridade da coleta de dados**     |   Fornece informações sobre o status de ingestão de dados do espaço de trabalho. Exibir monitores e detectar anomalias para ajudá-lo a determinar a integridade da coleta de dados de espaços de trabalho.  <br><br>Para obter mais informações, consulte [monitorar a integridade dos conectores de dados com esta pasta de trabalho do Azure Sentinel](monitor-data-connector-health.md).    |
|**Analisador de eventos**     |  Permite explorar, auditar e acelerar a análise do log de eventos do Windows, incluindo todos os detalhes e atributos do evento, como segurança, aplicativo, sistema, instalação, serviço de diretório, DNS e assim por diante.       |
|**Exchange Online**     |Fornece informações sobre o Microsoft Exchange Online rastreando e analisando todas as operações e atividades do usuário do Exchange.         |
|**Identidade e acesso**     |   Fornece informações sobre as operações de identidade e acesso no uso do produto da Microsoft, por meio de logs de segurança que incluem logs de auditoria e de entrada.     |
|**Visão geral do incidente**     |   Projetado para ajudar na triagem e investigação, fornecendo informações detalhadas sobre um incidente, incluindo informações gerais, dados de entidade, tempo de triagem, tempo de mitigação e comentários. <br><br>Para obter mais informações, consulte [o kit de ferramentas para Data-Driven Socs](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152).      |
|**Análises de investigação**     | Fornece analistas com informações sobre incidentes, indicadores e dados de entidade. Consultas comuns e visualizações detalhadas podem ajudar os analistas a investigar atividades suspeitas.       |
|**Logs de descoberta de Microsoft Cloud App Security**     |   Fornece detalhes sobre os aplicativos de nuvem que são usados em sua organização e informações sobre tendências de uso e dados de busca detalhada para usuários e aplicativos específicos.  <br><br>Para obter mais informações, consulte [conectar dados de Microsoft Cloud app Security](connect-cloud-app-security.md).|
|**Pasta de trabalho MITRE ATT&CK**     |   Fornece detalhes sobre a cobertura do MITRE ATT&CK para o Azure Sentinel.      |
|**Office 365**     |  Fornece informações sobre o Office 365 rastreando e analisando todas as operações e atividades. Faça uma busca detalhada nos dados do SharePoint, do OneDrive e do Exchange.       |
|**Alertas de segurança**     |  Fornece um painel de alertas de segurança para alertas em seu ambiente do Azure Sentinel. <br><br>Para obter mais informações, consulte [criar incidentes automaticamente de alertas de segurança da Microsoft](create-incidents-from-alerts.md).      |
|**Eficiência das operações de segurança**     |  Destinado aos gerentes do SOC (centro de operações de segurança) para exibir as métricas e medidas de eficiência geral referentes ao desempenho de sua equipe. <br><br>Para obter mais informações, consulte [gerenciar o SOC melhor com métricas de incidentes](manage-soc-with-incident-metrics.md).  |
|**Inteligência contra ameaças**     | Fornece informações sobre indicadores de ameaças, incluindo tipo e gravidade de ameaças, atividade de ameaças ao longo do tempo e correlação com outras fontes de dados, incluindo o Office 365 e firewalls.  <br><br>Para obter mais informações, consulte [importar inteligência contra ameaças no Azure Sentinel](import-threat-intelligence.md).      |
|**Auditoria de espaço de trabalho**     |  Fornece um relatório de auditoria de espaço de trabalho que permite que você entenda as execuções de consulta em seu espaço de trabalho.   <br><br>Para obter mais informações, consulte [auditar consultas e atividades do Azure Sentinel](audit-sentinel-data.md).  |
|     |         |

