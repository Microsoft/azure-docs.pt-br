---
title: Visão geral dos logs da plataforma Azure | Microsoft Docs
description: Visão geral dos logs em Azure Monitor que fornecem dados avançados e frequentes sobre a operação de um recurso do Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 6ea960e93dba634573ec1ef594f1d2c49be57ca9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84945300"
---
# <a name="overview-of-azure-platform-logs"></a>Visão geral dos logs de plataforma do Azure
Os logs de plataforma fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e a plataforma do Azure da qual dependem. Eles são gerados automaticamente, embora você precise configurar determinados logs da plataforma para serem encaminhados a um ou mais destinos a serem retidos. Este artigo fornece uma visão geral dos logs de plataforma, incluindo as informações que eles fornecem e como você pode configurá-los para coleta e análise.

## <a name="types-of-platform-logs"></a>Tipos de logs de plataforma
A tabela a seguir lista os logs de plataforma específicos que estão disponíveis em diferentes camadas do Azure.

| Log | Camada | Descrição |
|:---|:---|:---|
| [Logs de recursos](resource-logs.md) | Recursos do Azure | Forneça informações sobre as operações que foram executadas em um recurso do Azure (o *plano de dados*), por exemplo, obter um segredo de um Key Vault ou fazer uma solicitação para um banco de dado. O conteúdo dos logs de recursos varia de acordo com o serviço do Azure e o tipo de recurso.<br><br>*Os logs de recursos foram anteriormente referidos como logs de diagnóstico.*  |
| [Log de atividades](activity-log.md) | Assinatura do Azure | Fornece informações sobre as operações em cada recurso do Azure na assinatura de fora (*o plano de gerenciamento*), além de atualizações em eventos de integridade do serviço. Use o log de atividades para determinar o _que_, _quem_e _quando_ para qualquer operação de gravação (put, post, Delete) realizada nos recursos em sua assinatura. Há um único log de atividades para cada assinatura do Azure. |
| [Logs do Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) | Locatário do Azure |  Contém o histórico de atividade de entrada e a trilha de auditoria das alterações feitas no Azure Active Directory para um locatário específico.   |

> [!NOTE]
> O Log de Atividades do Azure é usado principalmente para atividades que ocorrem no Azure Resource Manager. Ele não controla os recursos usando o modelo Clássico/RDFE. Alguns tipos de recursos Clássicos têm um provedor de recursos de proxy no Azure Resource Manager (por exemplo, Microsoft.ClassicCompute). Se você interagir com um tipo de recurso Clássico por meio do Azure Resource Manager usando esses provedores de recursos de proxy, as operações aparecerão no Log de Atividades. Se você interagir com um tipo de recurso clássico fora dos proxies do Azure Resource Manager, suas ações somente serão registradas no Log de Operação. O Log de Operação pode ser pesquisado em uma seção separada do portal.

![Visão geral de logs de plataforma](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Exibindo logs da plataforma
Há diferentes opções para exibir e analisar os diferentes logs da plataforma Azure.

- Exiba o log de atividades no portal do Azure e acesse eventos do PowerShell e da CLI. Consulte [Exibir o log de atividades](activity-log.md#view-the-activity-log) para obter detalhes. 
- Exiba Azure Active Directory relatórios de segurança e atividade no portal do Azure. Confira [o que são Azure Active Directory relatórios?](../../active-directory/reports-monitoring/overview-reports.md)  para obter detalhes.
- Os logs de recursos são gerados automaticamente por recursos do Azure com suporte, mas não estão disponíveis para serem exibidos, a menos que você os envie para um [destino](#destinations). 

## <a name="destinations"></a>Destinos
Você pode enviar os logs da plataforma para um ou mais dos destinos na tabela a seguir, dependendo dos seus requisitos de monitoramento. Configure destinos para logs da plataforma [criando uma configuração de diagnóstico](diagnostic-settings.md).

| Destino | Descrição |
|:---|:---|
| Espaço de trabalho do Log Analytics | Analise os logs de todos os seus recursos do Azure juntos e aproveite todos os recursos disponíveis para [Azure monitor logs](data-platform-logs.md) , incluindo [consultas de log](../log-query/log-query-overview.md) e alertas de [log](alerts-log.md). Fixe os resultados de uma consulta de log em um painel do Azure ou inclua-os em uma pasta de trabalho como parte de um relatório interativo. |  |
| Hub de Eventos | Envie dados de log da plataforma fora do Azure, por exemplo, para um SIEM de terceiros ou plataforma de telemetria personalizada.
| Armazenamento do Azure | Arquive os logs para auditoria ou backup. |

- Para obter detalhes sobre como criar uma configuração de diagnóstico para log de atividades ou logs de recursos, consulte [criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes](diagnostic-settings.md). 
- Para obter detalhes sobre como criar uma configuração de diagnóstico para logs de Azure Active Directory, consulte os artigos a seguir.
  - [Integrar logs do Azure AD com logs de Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
  - [Tutorial: Transmitir logs do Azure Active Directory para um hub de eventos do Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
  - [Tutorial: arquivar logs do Azure AD em uma conta de armazenamento do Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)



## <a name="next-steps"></a>Próximas etapas

* [Leia mais detalhes sobre o log de atividades](activity-log.md)
* [Leia mais detalhes sobre os logs de recursos](resource-logs.md)

