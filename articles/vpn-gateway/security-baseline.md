---
title: Linha de base de segurança do Azure para gateway de VPN
description: A linha de base de segurança do gateway de VPN fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0f1dcb82c3a8d3819e8d316ef8efe2e3256e3db4
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218988"
---
# <a name="azure-security-baseline-for-vpn-gateway"></a>Linha de base de segurança do Azure para gateway de VPN

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure versão 1,0](../security/benchmarks/overview-v1.md) para gateway de VPN. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao gateway de VPN. Os **controles** não aplicáveis ao gateway de VPN foram excluídos.

Para ver como o gateway de VPN é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base do gateway de VPN completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte o [benchmark de segurança do Azure: segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Orientação**: ao trabalhar com sub-redes de gateway de VPN, evite associar um NSG (grupo de segurança de rede) à sub-rede de gateway. Associar um grupo de segurança de rede a essa sub-rede pode fazer com que seu gateway de VPN para parar de funcionar conforme o esperado.  No entanto, habilite grupos de segurança de rede para outras sub-redes de gateway não VPN em sua rede virtual.

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md) 

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md) 

- [Criar um gateway de VPN baseado em rotas usando o portal do Azure](./tutorial-create-gateway-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Diretrizes**: Use a central de segurança do Azure e siga as recomendações de proteção de rede para ajudar a proteger seus recursos de rede no Azure. 

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: habilitar a captura de pacotes de gateway de VPN no gateway ou em uma conexão específica, dependendo de seus requisitos.

- [Configurar capturas de pacote para gateways de VPN](packet-capture.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede com Azure Policy.

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala empacotando artefatos de ambiente-chave, como modelos de Azure Resource Manager, atribuições de RBAC do Azure e atribuições de Azure Policy, em uma única definição de Blueprint. Você pode aplicar o plano gráfico a assinaturas novas ou existentes, bem como ajustar o controle e o gerenciamento por meio da versão.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Exemplos de Azure Policy para rede](../governance/policy/samples/built-in-policies.md#network) 

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos e detectar alterações nos recursos da rede virtual. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos críticos relacionados ao seu gateway de VPN.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: ingestão de atividade e logs de diagnóstico por meio de Azure monitor para agregar dados de segurança gerados por recursos de rede, como seus recursos de gateway de VPN. Use Azure Monitor para consultar e executar análises nos dados de log, use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento desses logs. 

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

- [Configurar alertas em eventos de log de diagnóstico do gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Introdução à integração do Azure Monitor e ao SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: habilite as configurações de diagnóstico em seus recursos de gateway de VPN para acessar logs de auditoria, segurança e diagnóstico. Logs de atividade, que estão automaticamente disponíveis, incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis. 

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Entender o registro em log e diferentes tipos de log no Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: em Azure monitor, defina seu período de retenção de espaço de trabalho de log Analytics de acordo com os regulamentos de conformidade de sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento. 

- [Alterar o período de retenção de dados em Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) 

- [Como configurar a política de retenção para logs de conta de armazenamento do Azure](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: analise e monitore os logs em busca de comportamento anormal e Examine regularmente os resultados. Use Azure Monitor e um espaço de trabalho Log Analytics para examinar os logs e executar consultas nos dados de log. 

Como alternativa, você pode habilitar o e os dados integrados para o Azure Sentinel ou um SIEM de terceiros. 

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Introdução às consultas de Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) 

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Orientação**: Use a central de segurança do Azure com log Analytics espaço de trabalho para monitoramento e alertas em atividade anômala encontrada em eventos e logs de segurança.

Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel.

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como gerenciar alertas na central de segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar sobre dados de log do log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS

**Diretrizes**: implemente uma solução de terceiros do Azure Marketplace para a solução de registro em log DNS de acordo com seus requisitos organizacionais.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: o Azure RBAC (controle de acesso baseado em função) do Azure permite que você gerencie o acesso aos recursos do Azure por meio de atribuições de função. Você pode atribuir essas funções a usuários, grupos de entidades de serviço e identidades gerenciadas. Há funções internas predefinidas para determinados recursos, e essas funções podem ser inventariadas ou consultadas por meio de ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure.

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. 

Você também pode habilitar um acesso just-in-time usando Azure AD Privileged Identity Management e Azure Resource Manager. 

- [Saiba mais sobre o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Diretrizes**: sempre que possível, use Azure Active Directory SSO em vez de configurar credenciais autônomas individuais por serviço. Use as recomendações de acesso e identidade da central de segurança do Azure. 

- [Entender o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Orientação**: habilitar a MFA do Azure AD e seguir as recomendações de acesso e identidade da central de segurança do Azure. 

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usar estações de trabalho seguras e gerenciadas pelo Azure para tarefas administrativas

**Diretrizes**: Use uma estação de trabalho segura e gerenciada pelo Azure (também conhecida como uma estação de trabalho com acesso privilegiado ou Paw) para tarefas administrativas que exigem privilégios elevados.

- [Entenda as estações de trabalho seguras e gerenciadas pelo Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar o Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use Azure Active Directory relatórios de segurança e monitoramento para detectar quando uma atividade suspeita ou insegura ocorre no ambiente. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Orientação**: use locais nomeados do Azure ad para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões. 

- [Como configurar locais nomeados do Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário. 

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (Azure AD) fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de identidade e acesso do Azure AD para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo. 

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml) 

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Orientação**: você tem acesso à atividade de entrada do Azure AD, auditoria e fontes de log de eventos de risco, que permitem a integração com qualquer ferramenta Siem/Monitoring. 

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas desejados no espaço de trabalho Log Analytics. 

- [Como integrar os logs de atividades do Azure com o Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Diretrizes**: Use Azure ad Identity Protection recursos para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada. 

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: os gateways de VPN têm instâncias de VM dedicadas para cada rede virtual do cliente. Implemente o isolamento usando redes virtuais, assinaturas e grupos de gerenciamento separados para domínios de segurança individuais, como tipo de ambiente e nível de sensibilidade de dados. Você pode restringir o nível de acesso aos recursos do Azure que seus aplicativos e ambientes empresariais exigem. Você pode controlar o acesso aos recursos do Azure por meio do Azure RBAC (controle de acesso baseado em função).

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: Use uma solução de terceiros do Azure Marketplace em perímetros de rede para monitorar a transferência não autorizada de informações confidenciais e bloquear essas transferências ao alertar os profissionais de segurança de informações. 

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e protege contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados. 

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: os gateways de VPN criptografam os pacotes do cliente entre os gateways de VPN do Azure e os dispositivos de VPN locais do cliente (S2S) ou clientes VPN (P2S). Os gateways de VPN também dão suporte à criptografia de VNet para VNet.

Siga as recomendações da central de segurança do Azure para criptografia em repouso e criptografia em trânsito, para recursos aplicáveis em sua rede virtual.

- [Sobre tipos de VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype)

- [Sobre dispositivos VPN e os parâmetros IPsec/IKE para conexões do Gateway de VPN Site a Site](vpn-gateway-about-vpn-devices.md#ipsec)

- [Sobre os requisitos criptográficos e os gateways de VPN do Azure](vpn-gateway-about-compliance-crypto.md)

- [Configurar a política de IPsec/IKE para conexões VPN S2S ou VNet para VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md)

- [Entender a criptografia em trânsito com o Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: Use uma ferramenta de descoberta ativa de terceiros para identificar todas as informações confidenciais armazenadas, processadas ou transmitidas pelos seus sistemas de tecnologia, incluindo aquelas localizadas no local ou em um provedor de serviços remoto e atualizar o inventário de informações confidenciais da organização.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para controlar o acesso a dados e recursos, caso contrário, use métodos de controle de acesso específicos de serviço. Use funções internas como proprietário, colaborador ou colaborador de rede e, em seguida, atribua a função ao escopo apropriado. Atribua permissões específicas para um subconjunto de recursos de rede virtual criando uma função personalizada e atribuindo as permissões específicas necessárias para redes virtuais, sub-redes, gateways de VPN, interfaces de rede, grupos de segurança de rede e tabelas de rotas à função.

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

- [Planejar redes virtuais](../virtual-network/virtual-network-vnet-plan-design-arm.md#permissions)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Configure Azure monitor alertas para disparar seus logs de atividades do Azure para quando as alterações ocorrerem para recursos críticos do Azure, como seus gateways de VPN. 

- [Configurar alertas em métricas de gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)

- [Configurar alertas em eventos de log de diagnóstico do gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

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

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos relacionados aos seus gateways de VPN em suas assinaturas. Verifique se você tem permissões apropriadas (leitura) em seu locatário e se é capaz de enumerar todos os recursos em suas assinaturas. Além disso, você também pode usar o CLI do Azure para enumerar seus recursos de gateway de VPN.

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

- [CLI do Azure para gateway de VPN](/cli/azure/network/vnet-gateway)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do gateway de VPN para organizá-los logicamente de acordo com uma taxonomia definida.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e controlar os recursos de gateway de VPN. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil. 

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md) 

- [Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md) 

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: Crie um inventário dos recursos aprovados do Azure e do software aprovado para recursos de computação de acordo com suas necessidades organizacionais.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use a política do Azure para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o grafo de recursos do Azure para consultar e descobrir recursos dentro das assinaturas. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Azure Policy de exemplo interno para rede virtual](../virtual-network/policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: os clientes podem impedir a criação ou o uso de recursos atribuindo definições de Azure Policy, conforme exigido pelos requisitos de segurança organizacional. No entanto, você deve implementar seu próprio processo para remover recursos não aprovados ou não autorizados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

- [Azure Policy de exemplo interno para rede virtual](../virtual-network/policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: Use o acesso condicional do Azure ad para limitar a capacidade dos usuários de interagir com o Gerenciador de recursos do Azure Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure". 

- [Como configurar o acesso condicional para bloquear o acesso ao Gerenciador de recursos do Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de seus recursos de rede do Azure, incluindo o gateway de VPN. Você também pode usar definições de Azure Policy internas.

Azure Resource Manager tem a capacidade de exportar o modelo em JSON (script Object Notation) do Java, que deve ser revisado para garantir que as configurações atendam ou ultrapassem os requisitos de segurança para sua organização.

Você também pode usar recomendações da central de segurança do Azure como uma linha de base de configuração segura para seus recursos do Azure.

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy de exemplo interno para rede virtual](../virtual-network/policy-reference.md)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretrizes**: Use Azure Resource Manager modelos e atribuições de Azure Policy para configurar com segurança os recursos do Azure associados ao gateway de VPN e aos recursos relacionados. Os modelos de Azure Resource Manager são arquivos baseados em JSON usados para implantar a máquina virtual junto com os recursos do Azure e o modelo personalizado precisarão ser mantidos. A Microsoft realiza a manutenção nos modelos de base.  Use Azure Policy nos modos [Deny] e [implantar se não existir] para impor as configurações seguras em seus recursos do Azure.

- [Informações sobre como criar modelos de Azure Resource Manager](../virtual-machines/windows/ps-template.md)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreendendo os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

- [Exemplos de modelo do Azure Resource Manager para rede virtual](../virtual-network/template-samples.md)

- [Azure Policy de exemplo interno para rede virtual](../virtual-network/policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Orientação**: Use o Azure DevOps para armazenar e gerenciar com segurança seu código, como definições de Azure Policy personalizadas, modelos de Azure Resource Manager e scripts de configuração de estado desejado. Para acessar os recursos que você gerencia no Azure DevOps, você pode conceder ou negar permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory (AD do Azure), se integrados ao Azure DevOps, ou Active Directory se integrado ao TFS. 

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: definir e implementar configurações de segurança padrão para recursos do Azure usando Azure Policy. Use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor as configurações dos recursos do Azure. Você também pode fazer uso de definições de política internas relacionadas aos seus recursos específicos. Além disso, você pode usar a automação do Azure para implantar alterações de configuração. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como usar aliases](../governance/policy/concepts/definition-structure.md#aliases)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: atribua definições de Azure Policy para medir as configurações de recursos relacionadas aos recursos de gateway de VPN. Use Azure Policy insights para auditar configurações de recursos e alertar sobre alterações de configuração críticas.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy de exemplo interno para rede virtual](../virtual-network/policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Orientação**: o serviço de gateway de VPN armazena internamente e transmite chaves pré-compartilhadas do cliente e certificados em formulários criptografados. Os clientes precisam proteger as chaves pré-compartilhadas ou os certificados em seus próprios sistemas.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretrizes**: a VPN P2S do Azure dá suporte a três métodos de autenticação:

- Baseado em certificados
- RAIO
- Active Directory do Azure (Azure AD)

O Azure AD é recomendado porque permite que você aproveite as identidades gerenciadas.

- [Configurar um locatário](openvpn-azure-ad-tenant.md)

- [Configurar um locatário com vários aplicativos cliente](openvpn-azure-ad-tenant-multi-app.md)

- [Configurar a autenticação multifator](openvpn-azure-ad-mfa.md)

- [Configurar um cliente VPN](openvpn-azure-ad-client.md)

- [Como configurar identidades gerenciadas](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares

**Diretrizes**: Use Azure Resource Manager para implantar recursos de gateway de VPN. Azure Resource Manager fornece a capacidade de exportar modelos que podem ser usados como backups para restaurar recursos de gateway de VPN. Use a automação do Azure para chamar a API de exportação do modelo Azure Resource Manager regularmente.

- [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Exemplos de modelo do Azure Resource Manager para rede virtual](../virtual-network/template-samples.md)

- [Grupos de recursos-exportar modelo](/rest/api/resources/resourcegroups/exporttemplate)

- [Introdução à automação do Azure](../automation/automation-intro.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: Use Azure Resource Manager para implantar recursos de gateway de VPN. Azure Resource Manager fornece a capacidade de exportar modelos que podem ser usados como backups para restaurar o gateway de VPN e os recursos relacionados. Use a automação do Azure para chamar a API de exportação do modelo Azure Resource Manager regularmente.

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Grupos de recursos-exportar modelo](/rest/api/resources/resourcegroups/exporttemplate)

- [Introdução à automação do Azure](../automation/automation-intro.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: garanta a capacidade de executar periodicamente a implantação de modelos de Azure Resource Manager regularmente para uma assinatura isolada, se necessário.

- [Implantar recursos com modelos ARM e portal do Azure](../azure-resource-manager/templates/deploy-portal.md)

- [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: Use o Azure DevOps para armazenar e gerenciar com segurança seu código, como definições de Azure Policy personalizadas e modelos de Azure Resource Manager. Para proteger os recursos que você gerencia no Azure DevOps, você pode conceder ou negar permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory (AD do Azure), se integrados ao Azure DevOps.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

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

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a central de segurança do Azure atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque as assinaturas usando marcas e crie um sistema de nomeação para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na importância dos recursos do Azure e do ambiente em que o incidente ocorreu. 

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md) 

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise seu plano de resposta conforme necessário. 

- [Publicação do NIST – guia para testar, treinar e exercitar programas para planos de ti e recursos](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos. 

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua, continuamente. Você pode usar o conector de dados da central de segurança do Azure para transmitir os alertas para o Azure Sentinel. 

- [Como configurar a exportação contínua](../security-center/continuous-export.md) 

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Orientação**: usar o recurso de automação de fluxo de trabalho central de segurança do Azure para disparar automaticamente respostas a alertas de segurança e recomendações para proteger os recursos do Azure. 

- [Como configurar a automação do fluxo de trabalho na central de segurança](../security-center/workflow-automation.md)

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