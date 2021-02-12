---
title: Benchmark de segurança do Azure v2-registro em log e detecção de ameaças
description: Registro em log e detecção de ameaças do Azure Security benchmark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: c5a855ffe06a17d8ec1bfe249dbe7bc41d1166af
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369065"
---
# <a name="security-control-v2-logging-and-threat-detection"></a>Controle de segurança v2: registro em log e detecção de ameaças

O registro em log e a detecção de ameaças abrangem controles para detectar ameaças no Azure e habilitar, coletar e armazenar logs de auditoria para serviços do Azure. Isso inclui a habilitação de processos de detecção, investigação e correção com controles para gerar alertas de alta qualidade com a detecção de ameaças nativas nos serviços do Azure; Ele também inclui a coleta de logs com Azure Monitor, centralização da análise de segurança com o Azure Sentinel, sincronização de tempo e retenção de log. 

## <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: habilitar a detecção de ameaças para recursos do Azure

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| LT-1 | 6.7 | AU-3, AU-6, AU-12, SI-4 |

Verifique se você está monitorando diferentes tipos de ativos do Azure para possíveis ameaças e anomalias. Concentre-se em obter alertas de alta qualidade para reduzir os falsos positivos para os analistas classificarem. Os alertas podem ser originados de dados de log, agentes ou outros dados.

Use o recurso de detecção de ameaças interna da central de segurança do Azure, que se baseia no monitoramento da telemetria de serviço do Azure e na análise de logs de serviço. Os dados são coletados usando o agente de Log Analytics, que lê várias configurações relacionadas à segurança e logs de eventos do sistema e copia os dados para o espaço de trabalho para análise. 

Além disso, use o Azure Sentinel para criar regras de análise, que são ameaças que correspondem a critérios específicos em seu ambiente. As regras geram incidentes quando os critérios são correspondidos, para que você possa investigar cada incidente. O Azure Sentinel também pode importar inteligência de ameaças de terceiros para aprimorar seu recurso de detecção de ameaças. 

- [Proteção contra ameaças na Central de Segurança do Azure](../../security-center/azure-defender.md)

- [Guia de referência de alertas de segurança da central de segurança do Azure](../../security-center/alerts-reference.md)

- [Criar regras de análise personalizadas para detectar ameaças](../../sentinel/tutorial-detect-threats-custom.md)

- [Inteligência contra ameaças cibernéticos com o Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: habilitar a detecção de ameaças para o gerenciamento de identidades e acesso do Azure

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| LT-2 | 6,8 | AU-3, AU-6, AU-12, SI-4 |

O Azure AD fornece os seguintes logs de usuário que podem ser exibidos no relatório do Azure AD ou integrados com o Azure Monitor, o Azure Sentinel ou outras ferramentas de monitoramento/SIEM para casos de uso de monitoramento e análise mais sofisticados: 
-   Entradas – O relatório de entradas fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.

-   Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.

-   Entradas arriscadas - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário.

-   Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

A central de segurança do Azure também pode alertar sobre determinadas atividades suspeitas, como um número excessivo de tentativas de autenticação com falha e contas preteridas na assinatura. Além do monitoramento básico de higiene de segurança, o módulo de proteção contra ameaças da central de segurança do Azure também pode coletar alertas de segurança mais aprofundados de recursos de computação individuais do Azure (como máquinas virtuais, contêineres, serviço de aplicativo), recursos de dados (como banco de dado SQL e armazenamento) e camadas de serviço do Azure. Essa funcionalidade permite que você veja anomalias de conta dentro dos recursos individuais.

- [Relatórios de atividade de auditoria no Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Habilitar o Azure Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)

- [Proteção contra ameaças na Central de Segurança do Azure](../../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: habilitar o registro em log para atividades de rede do Azure

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| LT-3 | 9,3, 12,2, 12,5, 12,8 | AU-3, AU-6, AU-12, SI-4 |

Habilite e colete logs de recursos do NSG (grupo de segurança de rede), logs de fluxo do NSG, logs de firewall do Azure e logs de firewall do aplicativo Web (WAF) para análise de segurança para dar suporte a investigações de incidentes, busca de ameaças e geração de alertas de segurança. Você pode enviar os logs de fluxo para um espaço de trabalho Azure Monitor Log Analytics e, em seguida, usar Análise de Tráfego para fornecer informações. Verifique se você está coletando logs de consulta DNS para ajudar a correlacionar outros dados de rede.

- [Como habilitar logs de fluxo do grupo de segurança de rede](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Logs e métricas do Firewall do Azure](../../firewall/logs-and-metrics.md)

- [Como habilitar e usar Análise de Tráfego](../../network-watcher/traffic-analytics.md)

- [Monitoramento com o observador de rede](../../network-watcher/network-watcher-monitoring-overview.md)

- [Soluções de monitoramento de rede do Azure no Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md)

- [Reúna informações sobre sua infraestrutura de DNS com a solução de Análise de DNS](../../azure-monitor/insights/dns-analytics.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: habilitar o registro em log para recursos do Azure

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| LT-4 | 6,2, 6,3, 8,8 | AU-3, AU-12 |

Habilite o registro em log para recursos do Azure para atender aos requisitos de conformidade, detecção de ameaças, busca e investigação de incidentes. 

Você pode usar a central de segurança do Azure e Azure Policy para habilitar logs de recursos e dados de log coletados em recursos do Azure para acesso a logs de auditoria, segurança e recursos. Logs de atividade, que estão automaticamente disponíveis, incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis. 

- [Entender o registro em log e diferentes tipos de log no Azure](../../azure-monitor/platform/platform-logs-overview.md)

- [Entender a coleta de dados da central de segurança do Azure](../../security-center/security-center-enable-data-collection.md)

**Responsabilidade**: Compartilhado

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

Segurança de infraestrutura e ponto de extremidade 

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizar o gerenciamento e a análise do log de segurança

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| LT-5 | 6,5, 6,6 | AU-3, SI-4 |

Centralizar o armazenamento e a análise de log para habilitar a correlação. Para cada origem de log, verifique se você atribuiu um proprietário de dados, orientação de acesso, local de armazenamento, quais ferramentas são usadas para processar e acessar os dados e os requisitos de retenção de dados.

Verifique se você está integrando os logs de atividades do Azure ao seu registro central. Os logs de ingestão por meio de Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto de extremidade, recursos de rede e outros sistemas de segurança. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento.

Além disso, habilite e integre dados ao Azure Sentinel ou a um SIEM de terceiros.

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados com frequência e o armazenamento do Azure para dados "frios" que são usados com menos frequência. 

- [Como coletar logs e métricas de plataforma com Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md)

- [Como integrar o Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="lt-6-configure-log-storage-retention"></a>LT-6: configurar a retenção do armazenamento de log

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| LT-6 | 6.4 | AU-3, AU-11 |

Configure sua retenção de log de acordo com seus requisitos de conformidade, regulamentação e negócios. 

No Azure Monitor, você pode definir seu período de retenção de espaço de trabalho de Log Analytics de acordo com os regulamentos de conformidade de sua organização. Use o armazenamento do Azure, Data Lake ou Log Analytics contas de espaço de trabalho para armazenamento de longo prazo e arquivamento.

- [Alterar o período de retenção de dados em Log Analytics](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Como configurar a política de retenção para logs de conta de armazenamento do Azure](../../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Exportação de recomendações e alertas da central de segurança do Azure](../../security-center/continuous-export.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center) 

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: usar fontes de sincronização de tempo aprovadas

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| LT-7 | 6.1 | AU-8 |

A Microsoft mantém fontes de tempo para a maioria dos serviços de PaaS e SaaS do Azure. Para suas máquinas virtuais, use o servidor NTP padrão da Microsoft para sincronização de horário, a menos que você tenha um requisito específico.  Se você precisar criar seu próprio servidor NTP (Network Time Protocol), certifique-se de proteger a porta de serviço UDP 123.

Todos os logs gerados pelos recursos no Azure fornecem carimbos de data/hora com o fuso horário especificado por padrão.

- [Como configurar a sincronização de horário para recursos de computação do Windows do Azure](../../virtual-machines/windows/time-sync.md)

- [Como configurar a sincronização de horário para recursos de computação Linux do Azure](../../virtual-machines/linux/time-sync.md)

- [Como desabilitar o UDP de entrada para os serviços do Azure](https://support.microsoft.com/help/4558520/how-to-disable-inbound-udp-for-azure-services)

**Responsabilidade**: Compartilhado

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Política e padrões](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Segurança e DevOps de aplicativos](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Segurança de infraestrutura e ponto de extremidade](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)