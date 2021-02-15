---
title: Benchmark de segurança do Azure v2-resposta a incidentes
description: Resposta a incidente do Azure Security benchmark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 043e365bfe27db516a42386a91bc0433e27e2068
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368827"
---
# <a name="security-control-v2-incident-response"></a>Controle de segurança v2: resposta a incidentes

A resposta a incidentes cobre controles no ciclo de vida de resposta a incidentes – preparação, detecção e análise, confinamento e atividades pós-incidente. Isso inclui o uso de serviços do Azure, como a central de segurança do Azure e o Sentinel para automatizar o processo de resposta a incidentes.

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: preparação – atualizar o processo de resposta a incidentes do Azure

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| IR-1 | 19 | IR-4, IR-8 |

Verifique se sua organização tem processos para responder a incidentes de segurança, atualizou esses processos para o Azure e está experimentando-os regularmente para garantir a preparação.

- [Implementar a segurança em todo o ambiente empresarial](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Guia de referência da resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: preparação – configurar a notificação de incidentes

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| IR-2 | 19,5 | IR-4, IR-5, IR-6, IR-8 |

Configure informações de contato de incidente de segurança na central de segurança do Azure. Essas informações de contato serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobrir que os seus dados foram acessados por uma pessoa não autorizada ou ilegal. Você também tem opções para personalizar o alerta de incidente e a notificação em diferentes serviços do Azure de acordo com as suas necessidades de resposta a incidentes. 

- [Como definir o contato de segurança da Central de Segurança do Azure](../../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detecção e análise – criar incidentes com base em alertas de alta qualidade

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| IR-3 | 19,6 | IR-4, IR-5 |

Verifique se você tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Isso permite que você aprenda as lições dos últimos incidentes e priorize os alertas para os analistas, de modo a não perderem tempo em falsos positivos. 

Alertas de alta qualidade podem ser criados com base na experiência dos últimos incidentes, fontes de comunidade validadas e ferramentas projetadas para gerar e limpar alertas pela combinação e pela correlação de fontes de sinal diversificadas. 

A Central de Segurança do Azure fornece alertas de alta qualidade em muitos ativos do Azure. Use o conector de dados da ASC para transmitir os alertas para o Azure Sentinel. Com o Azure Sentinel, você pode criar regras de alertas avançadas a fim de gerar incidentes automaticamente para uma investigação. 

Exporte alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação para ajudar a identificar os riscos para os recursos do Azure. Exporte os alertas e as recomendações de modo manual ou contínuo.

- [Como configurar a exportação](../../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detecção e análise – investigar um incidente

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

Verifique se os analistas podem consultar e usar diversas fontes de dados à medida que investigam incidentes potenciais, para criar uma visão completa do que aconteceu. Logs diversificados devem ser coletados para acompanhar as atividades de um invasor potencial na cadeia de eliminação para evitar pontos cegos.  Você também deve garantir que os insights e os aprendizados sejam capturados para outros analistas e para referência histórica futura.  

As fontes de dados para investigação incluem as fontes de log centralizadas que já estão sendo coletadas dos serviços no escopo e dos sistemas em execução, mas também podem incluir:

- Dados de rede – Use os logs de fluxo dos grupos de segurança de rede, o Observador de Rede do Azure e o Azure Monitor para capturar logs de fluxo de rede e outras informações de análise. 

- Instantâneos dos sistemas em execução: 

    - Use a funcionalidade de instantâneo da máquina virtual do Azure para criar um instantâneo do disco do sistema em execução. 

    - Use a funcionalidade de despejo de memória nativa do sistema operacional para criar um instantâneo da memória do sistema em execução.

    - Use o recurso de instantâneo dos serviços do Azure ou a funcionalidade do seu software para criar instantâneos dos sistemas em execução.

O Azure Sentinel fornece ampla análise de dados em praticamente qualquer origem de log e um portal de gerenciamento de casos para gerenciar o ciclo de vida completo de incidentes. As informações de inteligência coletadas durante uma investigação podem ser associadas a um incidente para fins de rastreamento e relatório. 

- [Criar um instantâneo de disco de um computador Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Criar um instantâneo de disco de um computador Linux](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informações de diagnóstico e coleta de despejo de memória do Suporte do Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigar incidentes com o Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detecção e análise – priorizar incidentes

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| IR-5 | 19,8 | CA-2, IR-4 |

Forneça contexto aos analistas nos quais os incidentes se concentram primeiro com base na severidade do alerta e na sensibilidade do ativo. 

A Central de Segurança do Azure atribui uma severidade a cada alerta para ajudar você a priorizar os alertas que devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na conclusão ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que gerou o alerta.

Além disso, marque os recursos usando marcas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](../../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: confinamento, erradicação e recuperação – automatizar o tratamento de incidentes

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| IR-6 | 19 | IR-4, IR-5, IR-6 |

Automatize tarefas repetitivas manuais para acelerar o tempo de resposta e reduzir a carga de analistas. As tarefas manuais demoram mais para serem executadas, atrasando cada incidente e reduzindo o número de incidentes que um analista pode processar. Elas também aumentam a fadiga do analista, o que aumenta o risco de erros humanos que causam atrasos, prejudicando a capacidade dos analistas de se concentrarem efetivamente em tarefas complexas. Use os recursos de automação de fluxo de trabalho da Central de Segurança do Azure e do Azure Sentinel para disparar ações automaticamente ou executar um guia estratégico para responder aos alertas de segurança recebidos. O guia estratégico executa ações, como enviar notificações, desabilitar contas e isolar redes problemáticas. 

- [Configurar a automação de fluxo de trabalho na Central de Segurança](../../security-center/workflow-automation.md)

- [Configurar respostas automatizadas a ameaças na Central de Segurança do Azure](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas de ameaças automatizadas no Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação para incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Inteligência contra ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)