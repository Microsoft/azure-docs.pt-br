---
title: Controle de segurança do Azure-registro em log e monitoramento
description: Monitoramento e registro em log do controle de segurança do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ccfbb585ccf50366721925b0b31b17088fd36371
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612415"
---
# <a name="security-control-logging-and-monitoring"></a>Controle de segurança: registro em log e monitoramento

O monitoramento e o log de segurança se concentram em atividades relacionadas à habilitação, à aquisição e ao armazenamento de logs de auditoria para os serviços do Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

No entanto, a Microsoft mantém fontes de tempo para recursos do Azure. você tem a opção de gerenciar as configurações de sincronização de tempo para seus recursos de computação.

- [Como configurar a sincronização de horário para recursos de computação do Windows do Azure](../../virtual-machines/windows/time-sync.md)

- [Como configurar a sincronização de horário para recursos de computação Linux do Azure](../../virtual-machines/linux/time-sync.md)

## <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.2 | 6,5, 6,6 | Cliente |

Os logs de ingestão por meio de Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto de extremidade, recursos de rede e outros sistemas de segurança. No Azure Monitor, use o Workspace do Log Analytics para consultar e realizar análises, e use contas de Armazenamento do Microsoft Azure para armazenamento de longo prazo/arquivamento.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros. 

- [Como integrar o Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Como coletar logs e métricas de plataforma com Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Como coletar logs de host interno da máquina virtual do Azure com Azure Monitor](../../azure-monitor/vm/quick-collect-azurevm.md)

- [Introdução à integração do Azure Monitor e ao SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.3 | 6,2, 6,3 | Cliente |

Habilite as configurações de diagnóstico nos recursos do Azure para acessar logs de auditoria, segurança e diagnóstico. Logs de atividade, que estão automaticamente disponíveis, incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Entender o registro em log e diferentes tipos de log no Azure](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.4 | 6,2, 6,3 | Cliente |

Se o recurso de computação pertence à Microsoft, a Microsoft é responsável por monitorá-lo. Se o recurso de computação pertence à sua organização, é sua responsabilidade monitorá-lo. Você pode usar a central de segurança do Azure para monitorar o sistema operacional. Os dados coletados pela central de segurança do sistema operacional incluem o tipo e a versão do so, sistema operacional (logs de eventos do Windows), processos em execução, nome da máquina, endereços IP e usuário conectado. O agente de Log Analytics também coleta arquivos de despejo de memória.

- [Como coletar logs de host interno da máquina virtual do Azure com Azure Monitor](../../azure-monitor/vm/quick-collect-azurevm.md)

- [Entender a coleta de dados da central de segurança do Azure](../../security-center/security-center-enable-data-collection.md)

## <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.5 | 6.4 | Cliente |

Em Azure Monitor, defina seu período de retenção de espaço de trabalho de Log Analytics de acordo com os regulamentos de conformidade de sua organização. Use contas de Armazenamento do Microsoft Azure para armazenamentos de longo prazo/arquivamento.

- [Alterar o período de retenção de dados em Log Analytics](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Como configurar a política de retenção para logs de conta de armazenamento do Azure](../../storage/common/manage-storage-analytics-logs.md#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.6 | 6.7 | Cliente |

Analise e monitore logs de comportamento anormal e Examine regularmente os resultados. Use o espaço de trabalho Log Analytics do Azure Monitor para examinar os logs e executar consultas nos dados de log.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros. 

- [Como integrar o Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Compreender o workspace do Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../../azure-monitor/logs/get-started-queries.md)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.7 | 6,8 | Cliente |

Use a central de segurança do Azure com Log Analytics espaço de trabalho para monitoramento e alertas em atividade anômala encontrada em eventos e logs de segurança.

Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel.

- [Como integrar o Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Como gerenciar alertas na central de segurança do Azure](../../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar sobre dados de log do log Analytics](../../azure-monitor/alerts/tutorial-response.md)

## <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.8 | 8.6 | Cliente |

Habilite a coleta de eventos antimalware para máquinas virtuais e serviços de nuvem do Azure.

- [Como configurar o Microsoft antimalware para máquinas virtuais](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Como configurar o Microsoft antimalware para serviços de nuvem](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension)

- [Entender o Microsoft Antimalware](../fundamentals/antimalware.md)

## <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2,9 | 8.7 | Cliente |

Implemente uma solução de terceiros do Azure Marketplace para a solução de registro em log DNS de acordo com as necessidades de suas organizações.  

## <a name="210-enable-command-line-audit-logging"></a>2.10: habilitar o registro em log de auditoria de linha de comando

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.10 | 8.8 | Cliente |

Use Microsoft Monitoring Agent em todas as máquinas virtuais Windows do Azure com suporte para registrar o evento de criação de processo e o campo CommandLine.   Para máquinas virtuais Linux do Azure com suporte, você pode configurar manualmente o log do console por nó e usar o syslog para armazenar os dados.  Além disso, use o espaço de trabalho Log Analytics do Azure Monitor para examinar os logs e executar consultas em dados registrados de máquinas virtuais do Azure. 

- [Coleta de dados na Central de Segurança do Azure](../../security-center/security-center-enable-data-collection.md#data-collection-tier)

- [Como realizar consultas personalizadas no Azure Monitor](../../azure-monitor/logs/get-started-queries.md)

- [Fontes de dados do Syslog no Azure Monitor](../../azure-monitor/agents/data-sources-syslog.md)


## <a name="next-steps"></a>Próximas etapas

- Consulte o próximo controle de segurança: [identidade e controle de acesso](security-control-identity-access-control.md)