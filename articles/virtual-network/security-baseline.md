---
title: Linha de base de segurança do Azure para rede virtual
description: A linha de base de segurança de rede virtual fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 420cd4a5b39f7c5864cf9802d02643e23c8ad24e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666560"
---
# <a name="azure-security-baseline-for-virtual-network"></a>Linha de base de segurança do Azure para rede virtual

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure versão 1,0](../security/benchmarks/overview-v1.md) para a rede virtual do Azure. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis à rede virtual do Azure. Os **controles** não aplicáveis à rede virtual do Azure foram excluídos.

Para ver como a rede virtual do Azure é mapeada completamente para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança de rede virtual do Azure completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte o [benchmark de segurança do Azure: segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Diretrizes**: Use a central de segurança e siga as recomendações de proteção de rede para ajudar a proteger seus recursos de rede no Azure. 

Envie logs de fluxo do grupo de segurança de rede para um Log Analytics espaço de trabalho e use Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. O Análise de Tráfego oferece a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar incorretas configurações de rede. 

Use os logs de Azure Monitor para fornecer informações sobre o seu ambiente. Um espaço de trabalho deve ser usado para agrupar e analisar os dados e pode ser integrado a outros serviços do Azure, como o Application Insights e a central de segurança. 

Escolha os logs de recursos para enviar a uma conta de armazenamento do Azure ou a um hub de eventos. Uma plataforma diferente também pode ser usada para analisar os logs. 

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Entender a segurança de rede fornecida pela central de segurança](../security-center/security-center-network-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: habilitar a proteção padrão de DDoS (negação de serviço distribuído) em sua rede virtual do Azure para proteger contra ataques de DDoS.

Implante o Firewall do Azure em cada um dos limites de rede da organização com a filtragem baseada em inteligência contra ameaças habilitada e configurada para "alertar e negar" para tráfego de rede mal-intencionado.

Use os recursos de proteção contra ameaças da central de segurança para detectar comunicações com endereços IP mal-intencionados conhecidos.

Aplique recomendações de proteção de rede adaptáveis da central de segurança para configurações do grupo de segurança de rede que limitam portas e IPs de origem com base no tráfego real e na inteligência contra ameaças. 

- [Gerenciar a Proteção contra DDoS do Azure Standard usando o portal do Azure](../ddos-protection/manage-ddos-protection.md)

- [Filtragem baseada em inteligência contra ameaças do Firewall do Azure](../firewall/threat-intel.md)

- [Proteção contra ameaças na central de segurança](../security-center/azure-defender.md)

- [Proteção de rede adaptável na central de segurança do Azure](../security-center/security-center-adaptive-network-hardening.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: Use a captura de pacotes do gateway de VPN, além das ferramentas de captura de pacote normalmente disponíveis para gravar pacotes de rede. 

Você também pode examinar soluções baseadas em agente ou NVA que fornecem o ponto de acesso de terminal (toque) ou a funcionalidade de visibilidade de rede por meio das soluções de parceiros do agente de pacotes disponíveis nas ofertas do Azure Marketplace.

- [Configurar capturas de pacote para gateways de VPN](../vpn-gateway/packet-capture.md) 

- [Parceiro de solução de virtualização de rede](https://azure.microsoft.com/solutions/network-appliances)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: Use um firewall do Azure implantado em sua rede virtual com inteligência contra ameaças habilitada. Use a filtragem baseada em inteligência de ameaças do firewall do Azure para alertar ou negar o tráfego de e para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. 

Você também pode selecionar uma oferta apropriada no Azure Marketplace que dá suporte à funcionalidade de IDS/IPS com recursos de inspeção de conteúdo.

Implante a solução de firewall de sua escolha em cada um dos limites de rede da sua organização para detectar e/ou negar tráfego mal-intencionado.

- [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alertas com o Firewall do Azure](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure. As marcas de serviço podem ser usadas no lugar de endereços IP específicos ao criar regras de segurança. Permita ou negue o tráfego para o serviço correspondente especificando o nome da marca de serviço (por exemplo, ApiManagement) no campo de origem ou de destino apropriado de uma regra. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Use grupos de segurança de aplicativo para ajudar a simplificar a configuração de segurança complexa. Os grupos de segurança de aplicativo permitem configurar a segurança de rede como uma extensão natural da estrutura de um aplicativo. Isso permite que você agrupe máquinas virtuais e defina políticas de segurança de rede com base nesses grupos.

- [Entender e usar marcas de serviço](service-tags-overview.md)

- [Entender e usar grupos de segurança de aplicativos](./network-security-groups-overview.md#application-security-groups)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede com Azure Policy e examine as definições de política de rede internas para implementação.

Consulte a política padrão para a central de segurança que contém as recomendações de segurança disponíveis relacionadas às suas redes virtuais.

Use plantas do Azure para simplificar implantações de grande escala do Azure empacotando artefatos de ambiente-chave, como modelos de Azure Resource Manager, atribuições do Azure RBAC (controle de acesso baseado em função) e políticas, em uma única definição de Blueprint. Azure Blueprint podem ser aplicadas a novas assinaturas para controle e gerenciamento ajustados por meio de versionamento. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exemplos de Azure Policy para rede](../governance/policy/samples/built-in-policies.md#network) 

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

- [Habilitar o monitoramento na central de segurança do Azure](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Security%20Center/AzureSecurityCenter.json)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Diretrizes**: use marcas para grupos de segurança de rede e outros recursos relacionados à segurança de rede e ao fluxo de tráfego. Use o campo "Descrição" para especificar a necessidade de negócios, a duração e outras informações para qualquer regra que permita o tráfego de/para uma rede para regras de grupo de segurança de rede individuais.
Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Escolha Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](tutorial-filter-network-traffic.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos e detectar alterações em sua rede virtual. Crie alertas dentro de Azure Monitor que serão disparados quando ocorrerem alterações em recursos críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: habilite o Azure monitor para acesso aos logs de auditoria e de atividades, que incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis. 

No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.
Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros. 

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: habilite o Azure monitor para acesso aos logs de auditoria e de atividades, que incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretriz**: No Azure Monitor, defina o período de retenção do workspace do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento de retenção de armazenamento de log de segurança.

- [Alterar o período de retenção de dados em Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Como configurar a política de retenção para logs de conta de armazenamento do Azure](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: analise e monitore logs de comportamento anormal e Examine regularmente os resultados. Use o espaço de trabalho Log Analytics do Azure Monitor para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento. 

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros. 

- [Compreender o workspace do Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Introdução à integração do Azure Monitor e ao SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: Use a central de segurança com log Analytics espaço de trabalho para monitoramento e alertas em atividade anômala encontrada em eventos e logs de segurança.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou um SIEM de terceiros para alertas.

- [Como gerenciar alertas na central de segurança](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar sobre dados de log do log Analytics](../azure-monitor/alerts/tutorial-response.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS

**Diretrizes**: implemente uma solução de terceiros do Azure Marketplace para a solução de log DNS de acordo com sua necessidade organizacional.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: use funções de administrador internas do Azure Active Directory (Azure AD) que podem ser explicitamente atribuídas e que possam ser consultadas. 

Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use o gerenciamento de identidade e acesso da central de segurança para monitorar o número de contas administrativas.

Habilite o acesso just-in-time/apenas o suficiente usando Azure AD Privileged Identity Management funções privilegiadas para serviços e Azure Resource Manager da Microsoft. 

- [Saiba mais sobre o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Orientação**: Use o SSO com o Azure Active Directory (Azure AD) em vez de configurar credenciais autônomas individuais por serviço. Use as recomendações de gerenciamento de identidade e acesso da central de segurança.

- [Logon único em aplicativos no Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md) 

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretrizes**: habilite a autenticação multifator (MFA) do Azure Active Directory (Azure AD) e siga as recomendações de gerenciamento de identidade e acesso da central de segurança.

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usar estações de trabalho seguras e gerenciadas pelo Azure para tarefas administrativas

**Diretrizes**: Use Paw (estações de trabalho com acesso privilegiado) com a MFA (autenticação multifator) configurada para fazer logon e acessar os recursos de rede do Azure.

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Utilize as detecções de risco do Azure Active Directory (Azure AD) para exibir alertas e relatórios sobre o comportamento de usuário arriscado. 

Ingerir alertas de detecção de riscos da central de segurança em Azure Monitor e configure alertas/notificações personalizados usando grupos de ação.

- [Noções básicas sobre as detecções de riscos da central de segurança (atividade suspeita)](../active-directory/identity-protection/overview-identity-protection.md) 

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

- [Como configurar grupos de ação para alertas e notificações personalizados](../azure-monitor/alerts/action-groups.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar locais nomeados no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use o Azure Active Directory (AD do Azure) como um sistema de autenticação e autorização central para seus serviços. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito e também Salts, hashes e armazena com segurança as credenciais do usuário.  

- [Como criar e configurar uma instância do Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: Use Azure Active Directory (Azure AD) para fornecer logs para ajudar a descobrir contas obsoletas. 

As revisões de acesso de identidade do Azure podem ser executadas para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário deve ser revisado regularmente para garantir que apenas os usuários ativos tenham acesso contínuo.

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: integre a atividade de entrada do Azure Active Directory (Azure AD), as fontes de log de eventos de auditoria e de risco, com qualquer ferramenta Siem ou de monitoramento baseada em seu acesso. 

Simplifique esse processo criando configurações de diagnóstico para Azure Active Directory contas de usuário e enviando os logs de auditoria e os logs de entrada para um espaço de trabalho Log Analytics. Todos os alertas desejados podem ser configurados no espaço de trabalho Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: Use os recursos de proteção de risco e identidade do Azure Active Directory (Azure AD) para configurar respostas automatizadas para detectar ações suspeitas relacionadas a identidades de usuário para sua rede virtual. Ingerir dados no Azure Sentinel para qualquer investigação adicional.

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: criptografar todas as informações confidenciais em trânsito. Verifique se os clientes que se conectam aos recursos do Azure em suas redes virtuais são capazes de negociar o TLS 1,2 ou superior. Siga as recomendações da central de segurança para criptografia em repouso e criptografia em trânsito. 

A Microsoft fornece várias opções que podem ser utilizadas pelos clientes para proteger os dados em trânsito internamente na rede do Azure e externamente pela Internet para o usuário final. Isso inclui a comunicação por meio de redes virtuais privadas (utilizando criptografia IPsec/IKE), TLS 1,2 ou posterior (por meio de componentes do Azure, como gateway de aplicativo ou porta frontal do Azure), protocolos diretamente nas máquinas virtuais do Azure (como o Windows IPsec ou SMB) e muito mais.

Além disso, "a criptografia por padrão" usando MACsec (um padrão IEEE na camada de vínculo de dados) está habilitada para todo o tráfego do Azure que viaja entre os data centers do Azure para garantir a confidencialidade e a integridade dos dados do cliente.

- [Entender a criptografia em trânsito com o Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usar o RBAC do Azure para gerenciar o acesso aos recursos 

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para gerenciar o acesso a dados e recursos. Caso contrário, use métodos de controle de acesso específicos do serviço. 

Escolha funções internas, como proprietário, colaborador ou colaborador de rede, e atribua a função ao escopo apropriado. Por exemplo, você pode atribuir um subconjunto de recursos de rede virtual com as permissões específicas necessárias para as redes virtuais a qualquer uma dessas funções. 

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

- [Planejar redes virtuais](virtual-network-vnet-plan-design-arm.md#permissions)

- [Examinar as funções internas do Azure](../role-based-access-control/built-in-roles.md#networking)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com os logs de atividades do Azure para criar alertas para quando as alterações ocorrerem para recursos críticos do Azure, como redes virtuais e grupos de segurança de rede.

- [Log de diagnóstico para um grupo de segurança de rede](virtual-network-nsg-manage-log.md)

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos de rede, como redes virtuais, sub-redes em suas assinaturas. Verifique se você tem permissões (leitura) apropriadas em seu locatário e é capaz de enumerar todas as assinaturas do Azure, bem como os recursos nas suas assinaturas.

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md) 

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretrizes**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar Marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e controlar a rede virtual e os recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md) 

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md) 

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Orientação**: você precisará criar um inventário de recursos aprovados do Azure e software aprovado para recursos de computação de acordo com suas necessidades organizacionais.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos 

- Tipos de recursos permitidos 

Consulte ou descubra recursos dentro das assinaturas com o grafo de recursos do Azure em ambientes baseados em alta segurança, como aqueles com contas de armazenamento do Azure. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Azure Policy de exemplo interno para rede virtual](./policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: impedir a criação ou o uso de recursos com Azure Policy conforme exigido pelas políticas da organização. Implemente processos para remover recursos não autorizados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos 

- Tipos de recursos permitidos 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

- [Azure Policy de exemplo interno para rede virtual](./policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: Use o acesso condicional do Azure para limitar a capacidade do usuário de interagir com Azure Resource Manager Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de seus recursos de rede do Azure e também usar definições de Azure Policy internas.

Exporte qualquer um dos seus modelos de compilação com Azure Resource Manager no formulário JavaScript Object Notation (JSON) e examine-o para garantir que as configurações atendam ou excedam os requisitos de segurança para sua organização.

Implemente as recomendações da central de segurança como uma linha de base de configuração segura para os recursos do Azure.

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy de exemplo interno para rede virtual](./policy-reference.md)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretrizes**: use modelos de Azure Resource Manager e políticas do Azure para configurar com segurança os recursos do Azure associados à rede virtual e aos recursos relacionados.  Os modelos de Azure Resource Manager são arquivos baseados em JSON (JavaScript Object Notation) usados para implantar máquinas virtuais junto com os recursos do Azure. A Microsoft realiza a manutenção nos modelos de base.  

Use os efeitos Azure Policy [Deny] e [implantar se não existir] para impor as configurações seguras em seus recursos do Azure.

- [Informações sobre como criar modelos de Azure Resource Manager](../virtual-machines/windows/ps-template.md) 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Compreendendo os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

- [Exemplos de modelo do Azure Resource Manager para rede virtual](template-samples.md)

- [Azure Policy de exemplo interno para rede virtual](./policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Diretrizes**: Use o Azure DevOps para armazenar e gerenciar com segurança seu código, como políticas personalizadas do Azure, modelos de Azure Resource Manager, scripts de configuração de estado desejado. e assim por diante.

Você deve ter permissões para acessar os recursos que deseja gerenciar no Azure DevOps, como seu código, compilações e acompanhamento de trabalho. A maioria das permissões é concedida por meio de grupos de segurança internos. Você pode conceder ou negar permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory (AD do Azure), se integrado ao Azure DevOps, ou Active Directory se integrado ao Team Foundation Server.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: definir e implementar configurações de segurança padrão para recursos do Azure usando Azure Policy. Use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos recursos do Azure e quaisquer definições de políticas internas relacionadas a recursos específicos. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como usar aliases](../governance/policy/concepts/definition-structure.md#aliases)

- [Azure Policy de exemplo interno para rede virtual](./policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: Use a central de segurança para executar verificações de linha de base para sua rede virtual do Azure e recursos relacionados. Use Azure Policy para alertar e auditar configurações de recursos do Azure.

- [Como corrigir recomendações na central de segurança](../security-center/security-center-remediate-recommendations.md)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy de exemplo interno para rede virtual](./policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: Use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos para seus recursos do Azure hospedados em uma rede virtual do Azure.

- [Como integrar com identidades gerenciadas do Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Como criar um Key Vault](../key-vault/secrets/quick-create-portal.md) 

- [Como fornecer Key Vault autenticação com uma identidade gerenciada](../key-vault/general/assign-access-policy-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares

**Diretrizes**: Use Azure Resource Manager para implantar uma rede virtual e recursos relacionados. Azure Resource Manager fornece a capacidade de exportar modelos que podem ser usados como backups para restaurar a rede virtual e os recursos relacionados.  Use a automação do Azure para chamar a API de exportação do modelo Azure Resource Manager regularmente.

- [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Exemplos de modelo do Azure Resource Manager para rede virtual](template-samples.md)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Grupos de recursos-exportar modelo](/rest/api/resources/resourcegroups/exporttemplate)

- [Introdução à automação do Azure](../automation/automation-intro.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: Use Azure Resource Manager para implantar uma rede virtual e recursos relacionados. Azure Resource Manager fornece a capacidade de exportar modelos que podem ser usados como backups para restaurar a rede virtual e os recursos relacionados. Use a automação do Azure para chamar a API de exportação do modelo Azure Resource Manager regularmente. Fazer backup de chaves gerenciadas pelo cliente dentro do Azure Key Vault.

- [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Exemplos de modelo do Azure Resource Manager para rede virtual](template-samples.md)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Grupos de recursos-exportar modelo](/rest/api/resources/resourcegroups/exporttemplate)

- [Introdução à automação do Azure](../automation/automation-intro.md)

- [Como fazer backup de chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: execute periodicamente a implantação de modelos de Azure Resource Manager em uma assinatura isolada e a restauração de teste de chaves de backup gerenciadas pelo cliente.

- [Implantar recursos com modelos ARM e portal do Azure](../azure-resource-manager/templates/deploy-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: Use o Azure DevOps para armazenar e gerenciar com segurança seu código, como definições de Azure Policy personalizadas e modelos de Azure Resource Manager. 

Conceda ou negue permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory (AD do Azure) se integrados ao Azure DevOps ou Active Directory se integrado ao Team Foundation Server.  

Use o Azure RBAC (controle de acesso baseado em função) para proteger as chaves gerenciadas pelo cliente.   

Habilite a proteção de Soft-Delete e limpeza no Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.  

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Como habilitar a proteção de Soft-Delete e limpeza no Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) 

- [Exclusão reversível para blobs do Armazenamento do Azure ](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte o [benchmark de segurança do Azure: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.  

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Aproveitar o Guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Marque claramente as assinaturas (por exemplo, produção ou não produção) usando marcas e crie um sistema de nomeação para identificar e categorizar claramente os recursos do Azure, especialmente aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas de segurança na central de segurança](../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Testar procedimentos de resposta de segurança

**Diretriz**: Conduza regularmente exercícios para testar os recursos de resposta a incidentes de seus sistemas para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Publicação do NIST - Guia para testar, treinar e exercitar programas para planos de TI e capacidades](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da central de segurança](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança usando o recurso de exportação contínua para ajudar a identificar os riscos para os recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. 

Você também pode usar o conector de dados da central de segurança para transmitir os alertas para o Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações para proteger os recursos do Azure.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não sejam violações das políticas da Microsoft. Use a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem, serviços e aplicativos gerenciados pela Microsoft.

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)