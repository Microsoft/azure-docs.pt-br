---
title: Linha de base de segurança do Azure para a central de segurança
description: A linha de base de segurança da central de segurança fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 330b466360cc5687f78db81fbb4545e1c3409098
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729871"
---
# <a name="azure-security-baseline-for-security-center"></a>Linha de base de segurança do Azure para a central de segurança

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure](../security/benchmarks/overview.md) para a central de segurança do Azure. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis à central de segurança do Azure. Os **controles** não aplicáveis à central de segurança do Azure foram excluídos. Para ver como a central de segurança do Azure é mapeada completamente para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança do Azure Security Center completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte o [benchmark de segurança do Azure: segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: a central de segurança do Azure é uma oferta principal do Azure. Não é possível associar uma rede virtual, sub-rede ou grupo de segurança de rede diretamente à central de segurança. Se você habilitar a coleta de dados para seus recursos de computação, a central de segurança armazenará os dados coletados por meio de um espaço de trabalho Log Analytics, você poderá configurar esse espaço de trabalho para usar o link privado para acessar os dados do espaço de trabalho por meio de um ponto de extremidade privado em sua rede virtual. Além disso, se o uso da central de segurança de coleta de dados depender do agente de Log Analytics que está sendo implantado nos servidores para coletar dados de segurança e fornecer proteção a esses recursos de computação. O agente de Log Analytics exige que portas e protocolos específicos sejam abertos para a operação adequada com a central de segurança. Bloqueie suas redes para permitir apenas essas portas e protocolos necessários e adicione apenas regras adicionais que seu aplicativo requer para operar por meio de grupos de segurança de rede.

- [Coleta de dados na Central de Segurança do Azure](security-center-enable-data-collection.md)

- [Tráfego de rede do Filer com um grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)

- [Requisitos de firewall para usar o agente de Log Analytics](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements)

- [Entender o link privado do Azure](../private-link/private-link-overview.md) 

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Orientação**: a oferta da central de segurança do Azure não se integra diretamente a uma rede virtual, mas pode coletar dados de servidores configurados com o agente de log Analytics que são implantados em suas redes. Os servidores configurados para enviar dados para a central de segurança exigem que determinadas portas e protocolos tenham permissão para se comunicarem corretamente. Defina e aplique configurações de segurança padrão para esses recursos de rede com Azure Policy.

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala empacotando artefatos de ambiente-chave, como modelos de Azure Resource Manager, atribuições de função e atribuições de Azure Policy, em uma única definição de Blueprint. Você pode aplicar o plano gráfico a novas assinaturas para implantar configurações da central de segurança e recursos de rede relacionados de forma consistente e segura.

- [Coleta de dados na Central de Segurança do Azure](security-center-enable-data-collection.md)

- [Requisitos de firewall para usar o agente de Log Analytics](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Exemplos de Azure Policy para rede](../governance/policy/samples/built-in-policies.md#network)

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: a oferta da central de segurança do Azure não se integra diretamente a uma rede virtual, mas pode coletar dados de servidores configurados com o agente de log Analytics que são implantados em suas redes. Os servidores configurados para enviar dados para a central de segurança exigem que determinadas portas e protocolos tenham permissão para se comunicarem corretamente. Defina e aplique configurações de segurança padrão para esses recursos de rede com Azure Policy.

Use marcas de recurso para grupos de segurança de rede e outros recursos, como servidores em suas redes que estão configuradas para enviar logs de segurança para a central de segurança do Azure. Para regras de grupo de segurança de rede individuais, use o campo "Descrição" para documentar as regras que permitem o tráfego de/para uma rede. 

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas. 

- [Coleta de dados na Central de Segurança do Azure](security-center-enable-data-collection.md)

- [Requisitos de firewall para usar o agente de Log Analytics](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md) 

- [Como criar uma rede virtual do Azure](../virtual-network/quick-create-portal.md) 

- [Como filtrar o tráfego de rede com regras de grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos e detectar alterações de recursos de rede relacionados à central de segurança do Azure. Crie alertas no Azure Monitor para notificá-lo quando ocorrerem alterações em recursos críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log) 

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: agregue dados de segurança gerados pela central de segurança do Azure e suas fontes conectadas usando um espaço de trabalho central de log Analytics. 

Configure a coleta de dados da central de segurança para enviar dados de segurança e eventos de seus recursos de computação do Azure conectados para um espaço de trabalho central de Log Analytics. Além da coleta de dados, use o recurso de exportação contínua para transmitir alertas de segurança e recomendações geradas pela central de segurança para seu espaço de trabalho central Log Analytics. No Azure Monitor, você pode consultar e executar análises sobre os dados de segurança gerados na central de segurança e seus recursos do Azure conectados. 

Como alternativa, você pode enviar dados produzidos pela central de segurança para o Azure Sentinel ou um SIEM de terceiros.

- [Exportar continuamente dados da Central de Segurança](continuous-export.md)

- [Coleta de dados na Central de Segurança do Azure](security-center-enable-data-collection.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Como coletar logs de host interno da máquina virtual do Azure com Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Introdução à integração do Azure Monitor e ao SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: os logs de atividades do Azure monitor estão disponíveis automaticamente, esses logs contêm todas as operações de gravação para o recurso, como a central de segurança do Azure, incluindo quais operações foram feitas, quem iniciou a operação e quando elas ocorreram. Envie seus logs de atividades do Azure para um espaço de trabalho Log Analytics para consolidação de log e maior retenção.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Entender o registro em log e diferentes tipos de log no Azure](../azure-monitor/essentials/platform-logs-overview.md)

- [Enviar logs de atividade para um espaço de trabalho Log Analytics](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: em Azure monitor, defina seu período de retenção de espaço de trabalho de log Analytics de acordo com os regulamentos de conformidade de sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento. 

- [Alterar o período de retenção de dados em Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) 

- [Como configurar a política de retenção para logs de conta de armazenamento do Azure](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Orientação**: analise e monitore os logs produzidos pela central de segurança do Azure e suas fontes conectadas para comportamento anormal e Examine regularmente os resultados. Use Azure Monitor e um espaço de trabalho Log Analytics para examinar os logs e executar consultas nos dados de log.

Como alternativa, você pode habilitar o e os dados integrados para o Azure Sentinel ou um SIEM de terceiros. 

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Introdução às consultas de Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) 

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: Configure Azure monitor alertas de log para consultar atividades indesejadas ou anormais que são registradas pelo seu log de atividades ou os dados produzidos pela central de segurança do Azure. Configure grupos de ações para que sua organização seja notificada e possa tomar medidas se um alerta de log for iniciado para atividade anômala. Use o recurso de automação de fluxo de trabalho da central de segurança para disparar aplicativos lógicos sobre alertas de segurança e recomendações. Os fluxos de trabalho da central de segurança podem ser usados para notificar os usuários sobre a resposta a incidentes ou tomar medidas para corrigir recursos com base nas informações do alerta.

Como alternativa, você pode habilitar e dados integrados relacionados ao e produzidos pela central de segurança do Azure para o Azure Sentinel. O Azure Sentinel dá suporte a guias estratégicos que permitem respostas de ameaças automatizadas a problemas relacionados à segurança.

- [Automação do fluxo de trabalho da central de segurança do Azure](workflow-automation.md)

- [Como gerenciar alertas na central de segurança do Azure](security-center-managing-and-responding-alerts.md) 

- [Como alertar sobre dados de log do log Analytics](../azure-monitor/alerts/tutorial-response.md)

- [Configurar respostas de ameaças automatizadas no Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

- [Alertas de log no Azure Monitor](../azure-monitor/alerts/alerts-unified-log.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: o Azure RBAC (controle de acesso baseado em função) do Azure permite que você gerencie o acesso aos recursos do Azure por meio de atribuições de função. Você pode atribuir essas funções a usuários, grupos de entidades de serviço e identidades gerenciadas. Há funções internas predefinidas para determinados recursos, e essas funções podem ser inventariadas ou consultadas por meio de ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure. A central de segurança do Azure tem funções internas para o ' leitor de segurança ' ou o ' admin de segurança ', que permite aos usuários ler ou atualizar políticas de segurança e ignorar alertas e recomendações.

- [Permissões na Central de Segurança do Azure](security-center-permissions.md)

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Diretrizes**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas para a plataforma Azure ou específicos para a oferta da central de segurança do Azure. Use o gerenciamento de acesso e identidade da central de segurança do Azure para monitorar o número de contas administrativas no Azure Active Directory. A central de segurança também tem funções internas para o ' admin de segurança ' que permite que os usuários atualizem políticas de segurança e descartam alertas e recomendações, certifique-se de examinar e reconciliar todos os usuários que têm essa atribuição de função regularmente.

Além disso, para ajudá-lo a controlar contas administrativas dedicadas, você pode seguir as recomendações da Central de Segurança do Azure ou as políticas internas do Azure, como:

- Deve haver mais de um proprietário atribuído à sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

- [Permissões na Central de Segurança do Azure](security-center-permissions.md)

- [Como usar a Central de Segurança do Azure para monitorar a identidade e o acesso (versão prévia)](security-center-identity-access.md)

- [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Diretrizes**: sempre que possível, use Azure Active Directory SSO em vez de configurar credenciais autônomas individuais por serviço. Use as recomendações de acesso e identidade da central de segurança do Azure.

- [Entender o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Orientação**: habilitar Azure Active Directory MFA para acessar a central de segurança do Azure e a Portal do Azure, siga qualquer identidade da central de segurança e recomendações de acesso. 

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Como monitorar identidade e acesso na Central de Segurança do Azure](security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use uma estação de trabalho segura e gerenciada pelo Azure (também conhecida como uma estação de trabalho com acesso privilegiado ou Paw) para tarefas administrativas que exigem privilégios elevados.

- [Entenda as estações de trabalho seguras e gerenciadas pelo Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar o Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use Azure Active Directory relatórios de segurança e monitoramento para detectar quando uma atividade suspeita ou insegura ocorre no ambiente. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md) 

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Orientação**: use locais nomeados do Azure ad para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões. 

- [Como configurar locais nomeados do Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central ao usar a central de segurança do Azure. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário. A central de segurança do Azure tem funções internas que podem ser atribuídas como ' admin de segurança ', que permite aos usuários atualizar políticas de segurança e ignorar alertas e recomendações.

- [Permissões na Central de Segurança do Azure](security-center-permissions.md)

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Revisar e reconciliar regularmente o acesso do usuário

**Diretriz**: O Azure Active Directory fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de identidade e acesso do Azure AD para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário relacionado à central de segurança do Azure pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo. 

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml) 

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Orientação**: você tem acesso à atividade de entrada do Azure AD, auditoria e fontes de log de eventos de risco, que permitem a integração com qualquer ferramenta Siem/Monitoring. 

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas desejados no espaço de trabalho Log Analytics.  

- [Como integrar os logs de atividades do Azure com o Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta 

**Diretrizes**: Use Azure ad Identity Protection recursos para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada. 

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md) 

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: manter um inventário de informações confidenciais

**Diretrizes**: use marcas para auxiliar no rastreamento de recursos do Azure, como o espaço de trabalho log Analytics, que armazena informações de segurança confidenciais da central de segurança do Azure.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente o isolamento usando assinaturas e grupos de gerenciamento separados para domínios de segurança individuais, como tipo de ambiente e nível de sensibilidade de dados. Você pode restringir o nível de acesso aos recursos do Azure que seus aplicativos e ambientes empresariais exigem. Você pode controlar o acesso aos recursos do Azure por meio do RBAC do Azure.

Por padrão, os dados da central de segurança do Azure são armazenados no serviço de back-end da central de segurança. Se sua organização tiver adicionado requisitos para armazenar esses dados em seus próprios recursos, você poderá configurar um espaço de trabalho Log Analytics para armazenar dados, alertas e recomendações da central de segurança. Ao usar seu próprio espaço de trabalho, você pode adicionar mais separação Configurando espaços de trabalho diferentes de acordo com o ambiente em que os dados foram originados.

- [Exportar continuamente dados da Central de Segurança](continuous-export.md)

- [Coleta de dados na Central de Segurança do Azure](security-center-enable-data-collection.md)

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md) 

- [Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md) 

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: criptografar todas as informações confidenciais em trânsito. Verifique se todos os clientes que se conectam aos recursos do Azure são capazes de negociar o TLS 1,2 ou superior. Todas as máquinas virtuais configuradas com o agente de Log Analytics e para enviar dados para a central de segurança do Azure devem ser configuradas para usar o TLS 1,2.

Siga as recomendações da central de segurança do Azure para criptografia em repouso e criptografia em trânsito, quando aplicável. 

- [Enviando dados com segurança para Log Analytics](../azure-monitor/logs/data-security.md#sending-data-securely-using-tls-12)

- [Entender a criptografia em trânsito com o Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos 

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para gerenciar o acesso aos dados e recursos relacionados à central de segurança do Azure. A central de segurança do Azure tem funções internas para o ' leitor de segurança ' ou o ' admin de segurança ', que permite aos usuários ler ou atualizar políticas de segurança e ignorar alertas e recomendações. O espaço de trabalho Log Analytics que armazena os dados coletados pela central de segurança também tem funções internas que podem ser atribuídas como ' Log Analytics Reader ', ' Log Analytics colaborador ' e outros. Atribua a função menos permissiva necessária para que os usuários concluam suas tarefas necessárias. Por exemplo, atribua a função Leitor aos usuários que precisam apenas exibir informações sobre a integridade da segurança de um recurso, mas que não precisam executar nenhuma ação, como aplicar recomendações ou editar políticas.

- [Permissões para o espaço de trabalho Log Analytics do Azure](../role-based-access-control/built-in-roles.md#log-analytics-reader)

- [Permissões na Central de Segurança do Azure](security-center-permissions.md)

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: a central de segurança do Azure usa um espaço de trabalho log Analytics configurado para armazenar os dados, alertas e recomendações que ele gera. Configure uma chave gerenciada pelo cliente (CMK) para o espaço de trabalho que você configurou para a coleta de dados da central de segurança. O CMK permite que todos os dados salvos ou enviados para o espaço de trabalho sejam criptografados com uma chave de Azure Key Vault criada e pertence a você. 

- [Chave do Azure Monitor gerenciada pelo cliente](../azure-monitor/logs/customer-managed-keys.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor para criar alertas quando as alterações ocorrerem para recursos críticos do Azure relacionados à central de segurança do Azure. Essas alterações podem incluir qualquer ação que modifique as configurações relacionadas à central de segurança, como a desabilitação de alertas ou recomendações, ou a atualização ou exclusão de armazenamentos de dados.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: Use um programa de Pontuação de risco comum (por exemplo, sistema comum de Pontuação de vulnerabilidade) ou as classificações de risco padrão fornecidas pela ferramenta de verificação de terceiros.

- [Publicação NIST – sistema de Pontuação de vulnerabilidade comum](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos relacionados à central de segurança do Azure em suas assinaturas. Verifique as permissões apropriadas (leitura) em seu locatário e enumere todas as assinaturas do Azure para descobrir os recursos da central de segurança. 

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretrizes**: use marcas para auxiliar no rastreamento de recursos do Azure, como o espaço de trabalho log Analytics, que armazena informações de segurança confidenciais da central de segurança do Azure.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar os recursos da central de segurança do Azure. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Além disso, use a política do Azure para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: Crie um inventário dos recursos aprovados do Azure e do software aprovado para recursos de computação de acordo com suas necessidades organizacionais.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas. 

Use o Azure Resource Graph para consultar e descobrir recursos dentro das assinaturas deles.  Verifique se todos os recursos do Azure presentes no ambiente foram aprovados. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Diretrizes**: Remova os recursos do Azure relacionados à central de segurança do Azure quando eles não forem mais necessários como parte do processo de inventário e revisão da sua organização.

- [Exclusão de recursos e grupo de recursos do Azure](../azure-resource-manager/management/delete-resource-group.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretriz**: Configure o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”.

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: definir e implementar configurações de segurança padrão para a central de segurança do Azure e seu espaço de trabalho conectado via Azure Policy. Use aliases de Azure Policy nos namespaces "Microsoft. OperationalInsights" e "Microsoft. Security" para criar definições de Azure Policy personalizadas para auditar ou impor a configuração da central de segurança e seu espaço de trabalho de Log Analytics.

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Orientação**: Use efeitos de Azure Policy para "negar" e "implantar se não existir" para impor configurações seguras em seus recursos do Azure. Além disso, você pode usar modelos de Azure Resource Manager para manter a configuração de segurança dos recursos do Azure exigidos por sua organização. 

- [Entender Azure Policy efeitos](../governance/policy/concepts/effects.md) 

- [Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md) 

- [Visão geral de modelos de Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Orientação**: Use o Azure DevOps para armazenar e gerenciar com segurança seu código, como definições de Azure Policy personalizadas, modelos de Azure Resource Manager e scripts de configuração de estado desejado. Para acessar os recursos que você gerencia no Azure DevOps, você pode conceder ou negar permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory (AD do Azure), se integrados ao Azure DevOps, ou Active Directory se integrado ao TFS. 

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: definir e implementar configurações de segurança padrão para recursos do Azure usando Azure Policy. Use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor a configuração dos recursos relacionados à central de segurança do Azure. Além disso, você pode usar a automação do Azure para implantar alterações de configuração. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como usar aliases](../governance/policy/concepts/definition-structure.md#aliases)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no "Microsoft. OperationalInsights" e "Microsoft. Os namespaces de segurança para criar políticas personalizadas para alertar, auditar e impor configurações de recursos do Azure. Use os efeitos de política do Azure "auditoria", "negar" e "implantar se não houver" para impor automaticamente as configurações para os recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: a central de segurança do Azure usa um espaço de trabalho log Analytics configurado para armazenar os dados, alertas e recomendações que ele gera. Configure uma chave gerenciada pelo cliente (CMK) para o espaço de trabalho que você configurou para a coleta de dados da central de segurança. O CMK permite que todos os dados salvos ou enviados para o espaço de trabalho sejam criptografados com uma chave de Azure Key Vault criada e pertence a você. 

- [Chave do Azure Monitor gerenciada pelo cliente](../azure-monitor/logs/customer-managed-keys.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: a central de segurança do Azure não se destina a armazenar ou processar arquivos. É sua responsabilidade verificar previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure, incluindo Log Analytics espaço de trabalho.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantir backups automatizados regulares 

**Diretrizes**: siga uma abordagem de IAC (infraestrutura como código) e use Azure Resource Manager para implantar os recursos relacionados à central de segurança do Azure em um modelo de JavaScript Object Notation (JSON) que pode ser usado como backup para configurações relacionadas a recursos.

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Modelos de Azure Resource Manager para recursos de segurança](/azure/templates/microsoft.security/allversions)

- [Sobre a Automação do Azure](../automation/automation-intro.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: a central de segurança do Azure usa um espaço de trabalho log Analytics para armazenar os dados, alertas e recomendações que ele gera. Você pode configurar Azure Monitor e o espaço de trabalho que a central de segurança usa para habilitar uma chave gerenciada pelo cliente. Se você estiver usando um Key Vault para armazenar as chaves gerenciadas pelo cliente, garanta backups automatizados regulares de suas chaves.

- [Como fazer backup de chaves de Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: garanta a capacidade de executar a restauração periodicamente usando Azure Resource Manager arquivos de modelo com suporte. Teste a restauração de chaves de backup gerenciadas pelo cliente.

- [Gerenciar Log Analytics espaço de trabalho usando modelos de Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md)

- [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: Use o Azure DevOps para armazenar e gerenciar com segurança seu código, como definições de Azure Policy personalizadas e modelos de Azure Resource Manager. Para proteger os recursos que você gerencia no Azure DevOps, você pode conceder ou negar permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory (AD do Azure), se integrados ao Azure DevOps, ou Active Directory se integrado ao TFS. Use o controle de acesso baseado em função do Azure para proteger chaves gerenciadas pelo cliente.

Além disso, habilite a proteção de Soft-Delete e limpeza no Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.  Se o armazenamento do Azure for usado para armazenar Azure Resource Manager backups de modelo, habilite a exclusão reversível para salvar e recuperar seus dados quando BLOBs ou instantâneos de blob forem excluídos. 

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Como habilitar a proteção de Soft-Delete e limpeza no Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) 

- [Exclusão reversível para blobs do Armazenamento do Azure ](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte o [benchmark de segurança do Azure: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretrizes**: desenvolver um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de manipulação e gerenciamento de incidentes, desde a detecção até a revisão após o incidente. 

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Use o guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a central de segurança do Azure atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque as assinaturas usando marcas e crie um sistema de nomeação para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na importância dos recursos do Azure e do ambiente em que o incidente ocorreu. 

- [Alertas na Central de Segurança do Azure](security-center-alerts-overview.md) 

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise seu plano de resposta conforme necessário. 

- [Publicação do NIST – guia para testar, treinar e exercitar programas para planos de ti e recursos](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos. 

- [Como definir o contato de segurança da Central de Segurança do Azure](security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua, continuamente. Você pode usar o conector de dados da central de segurança do Azure para transmitir os alertas para o Azure Sentinel. 

- [Como configurar a exportação contínua](continuous-export.md) 

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Orientação**: usar o recurso de automação de fluxo de trabalho central de segurança do Azure para disparar automaticamente respostas a alertas de segurança e recomendações para proteger os recursos do Azure. 

- [Como configurar a automação do fluxo de trabalho na central de segurança](workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de teste de penetração Microsoft Cloud do Engagement para garantir que seus testes de penetração não estejam violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft. 

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)