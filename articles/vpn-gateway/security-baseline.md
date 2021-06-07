---
title: Linha de base de segurança do Azure para o Gateway de VPN
description: A linha de base de segurança do Gateway de VPN oferece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0f1dcb82c3a8d3819e8d316ef8efe2e3256e3db4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218988"
---
# <a name="azure-security-baseline-for-vpn-gateway"></a>Linha de base de segurança do Azure para o Gateway de VPN

Essa linha de base de segurança aplica as diretrizes do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview-v1.md) ao Gateway de VPN. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo Azure Security Benchmark e pelas diretrizes relacionadas aplicáveis ao Gateway de VPN. Os **controles** não aplicáveis ao Gateway de VPN foram excluídos.

Para ver a relação completa entre o Gateway de VPN e o Azure Security Benchmark, confira o [arquivo de mapeamento de linha de base de segurança completo do Gateway de VPN](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Diretrizes**: ao trabalhar com sub-redes de gateway de VPN, evite a associação de um NSG (grupo de segurança de rede) à sub-rede de gateway. Associar um grupo de segurança de rede a essa sub-rede pode fazer com que seu gateway de VPN para parar de funcionar conforme o esperado.  No entanto, habilite grupos de segurança de rede para outras sub-redes de gateway que não sejam VPN na rede virtual.

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md) 

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md) 

- [Criar um gateway de VPN baseado em rotas usando o portal do Azure](./tutorial-create-gateway-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e adaptadores de rede

**Diretrizes**: use a Central de Segurança do Azure e siga as recomendações de proteção de rede para ajudar a proteger recursos de rede no Azure. 

- [Noções básicas sobre a segurança de rede fornecida pela Central de Segurança do Azure](../security-center/security-center-network-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1.5: Registrar os pacotes de rede

**Diretrizes**: habilite a captura de pacotes de gateway de VPN no gateway ou em uma conexão específica, dependendo de seus requisitos.

- [Configurar capturas de pacote para gateways de VPN](packet-capture.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede com o Azure Policy.

Você também pode usar o Azure Blueprints para simplificar as implantações do Azure de grande escala por meio do empacotamento de artefatos de ambiente importantes, como modelos do Azure Resource Manager, atribuições do RBAC (Controle de Acesso Baseado em Função) do Azure e atribuições do Azure Policy, em uma definição de blueprint. Você pode aplicar o blueprint a assinaturas novas ou atuais e ajustar o controle e o gerenciamento por meio do controle de versão.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Amostras do Azure Policy para rede](../governance/policy/samples/built-in-policies.md#network) 

- [Como criar um Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretrizes**: use o Log de Atividades do Azure para monitorar as configurações dos recursos e detectar alterações nos recursos de rede virtual. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos críticos relacionados ao gateway de VPN.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para obter mais informações, confira [Azure Security Benchmark: log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central de log de segurança

**Diretrizes**: realize a ingestão de logs de atividade e de diagnóstico por meio do Azure Monitor para agregar os dados de segurança gerados por recursos de rede, como recursos de gateway de VPN. Use o Azure Monitor para consultar e analisar os dados de log e use contas do Armazenamento do Microsoft Azure para armazenamento de longo prazo e arquivamento dos logs. 

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

- [Configurar alertas para eventos de log de diagnóstico do Gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como coletar logs e métricas de plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Introdução à integração do Azure Monitor e ao SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: habilite as configurações de diagnóstico nos recursos de gateway de VPN para acessar logs de auditoria, segurança e diagnóstico. Logs de atividade, que estão automaticamente disponíveis, incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis. 

- [Como coletar logs e métricas de plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Entender o registro em log e os diferentes tipos de log no Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: no Azure Monitor, defina o período de retenção do workspace do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Use contas do Armazenamento do Microsoft Azure para armazenamento de longo prazo e arquivamento. 

- [Alterar o período de retenção de dados no Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) 

- [Como configurar a política de retenção de logs de contas do Armazenamento do Microsoft Azure](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e examinar os logs

**Diretrizes**: analise e monitore os logs em busca de comportamentos anormais e examine os resultados regularmente. Use o Azure Monitor e um workspace do Log Analytics para examinar os logs e realizar consultas nos dados de log. 

Outra opção é habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros. 

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Introdução às consultas do Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) 

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Habilitar alertas sobre atividades anormais

**Diretrizes**: use a Central de Segurança do Azure com o workspace do Log Analytics para monitoramento e alertas de atividade anômala encontrada em eventos e logs de segurança.

Outra opção é habilitar e integrar dados no Azure Sentinel.

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como gerenciar alertas na Central de Segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar sobre dados de log do Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS

**Diretrizes**: implemente uma solução de terceiros do Azure Marketplace para registro em log de DNS de acordo com os requisitos da sua organização.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, confira [Azure Security Benchmark: controle de acesso e identidade](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Diretrizes**: o Azure RBAC (Controle de Acesso Baseado em Função) do Azure permite gerenciar o acesso aos recursos do Azure por meio de atribuições de função. Você pode atribuir essas funções a usuários, grupos, entidades de serviço e identidades gerenciadas. Há funções internas predefinidas para determinados recursos, e essas funções podem ser inventariadas ou consultadas por meio de ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure.

- [Como obter uma função do diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [Como obter membros de uma função do diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. 

Você também pode habilitar um acesso Just-In-Time usando o Privileged Identity Management e o Azure Resource Manager do Azure AD. 

- [Saiba mais sobre o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Usar o SSO (Logon Único) do Azure Active Directory

**Diretrizes**: sempre que possível, use o SSO do Azure Active Directory em vez de configurar credenciais autônomas individuais por serviço. Use as recomendações de identidade e acesso da Central de Segurança do Azure. 

- [Entender o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: habilite a MFA do Azure AD e siga as recomendações de acesso e identidade da Central de Segurança do Azure. 

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Usar as estações de trabalho seguras e gerenciadas pelo Azure para tarefas administrativas

**Diretrizes**: use uma estação de trabalho segura e gerenciada pelo Azure (também conhecida como estações de trabalho com acesso privilegiado ou PAW) para tarefas administrativas que exijam privilégios elevados.

- [Entender de que modo funcionam as estações de trabalho seguras e gerenciadas pelo Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a MFA do Azure AD](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrar atividades suspeitas em contas administrativas e alertar sobre elas

**Diretrizes**: use os relatórios de segurança e o monitoramento do Azure Active Directory para detectar a ocorrência de atividades suspeitas ou inseguras no ambiente. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: use as localizações nomeadas do Azure AD para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões. 

- [Como configurar localizações nomeadas do Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: use o Azure AD (Active Directory) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário. 

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure AD (Azure Active Directory) fornece logs para ajudar na descoberta de contas obsoletas. Além disso, use as revisões de acesso e identidade do Azure AD para gerenciar eficientemente associações de grupo, acesso aos aplicativos empresariais e atribuições de função. O acesso de usuários pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo. 

- [Entenda os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml) 

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorar as tentativas de acessar credenciais desativadas

**Diretrizes**: você tem acesso às fontes de log de atividades de entrada, de auditoria e de eventos de risco do Azure AD, que permitem a integração a qualquer ferramenta de monitoramento ou SIEM. 

Para simplificar esse processo, crie configurações de diagnóstico para contas de usuário do Azure AD e envie os logs de auditoria e os logs de entrada para um workspace do Log Analytics. Você pode configurar os alertas desejados no workspace do Log Analytics. 

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Diretrizes**: use os recursos de proteção de identidade do Azure AD para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuários. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada. 

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: os gateways de VPN têm instâncias de VM dedicadas para cada rede virtual do cliente. Implemente um isolamento usando redes virtuais, assinaturas e grupos de gerenciamento separados para domínios de segurança individuais, como o tipo de ambiente e o nível de confidencialidade de dados. Você pode restringir o nível de acesso aos recursos do Azure que seus aplicativos e ambientes empresariais exigem. Você pode controlar o acesso aos recursos do Azure com o RBAC (controle de acesso baseado em função) do Azure.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: use uma solução de terceiros do Azure Marketplace em perímetros de rede para monitorar e bloquear a transferência não autorizada de informações confidenciais, alertando os profissionais de segurança da informação. 

Quanto à plataforma subjacente, que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e protege contra a perda e a exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados. 

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: os gateways de VPN criptografam os pacotes do cliente entre os gateways de VPN do Azure e os dispositivos de VPN locais do cliente (S2S) ou clientes VPN (P2S). Os gateways de VPN também dão suporte para a criptografia de VNet para VNet.

Siga as recomendações da Central de Segurança do Azure para criptografia em repouso e em trânsito, para os recursos aplicáveis em sua rede virtual.

- [Sobre tipos de VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype)

- [Sobre dispositivos VPN e os parâmetros IPsec/IKE para conexões do Gateway de VPN Site a Site](vpn-gateway-about-vpn-devices.md#ipsec)

- [Sobre os requisitos criptográficos e os gateways de VPN do Azure](vpn-gateway-about-compliance-crypto.md)

- [Configurar a política de IPsec/IKE para conexões VPN S2S ou VNet para VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md)

- [Entenda a criptografia em trânsito com o Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: use uma ferramenta de descoberta ativa de terceiros para identificar todas as informações confidenciais armazenadas e processadas nos sistemas de tecnologia da organização ou transmitidas por eles, incluindo as informações localizadas no local ou em um provedor de serviço remoto, e atualize o inventário de informações confidenciais da organização.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usar o RBAC do Azure para controlar o acesso a recursos

**Diretrizes**: use o RBAC (controle de acesso baseado em função) do Azure para controlar o acesso a dados e recursos, caso contrário, use métodos de controle de acesso específicos de serviço. Use funções internas como proprietário, colaborador ou colaborador de rede e atribua a função ao escopo apropriado. Atribua permissões específicas a um subconjunto de recursos de rede virtual. Para isso, crie uma função personalizada e atribua a ela as permissões necessárias para redes virtuais, sub-redes, gateways de VPN, interfaces de rede, grupos de segurança de rede e tabelas de rotas.

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

- [Planejar redes virtuais](../virtual-network/virtual-network-vnet-plan-design-arm.md#permissions)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: configure alertas do Azure Monitor para disparar com base nos logs de atividades do Azure quando houver alterações em recursos críticos do Azure, como os gateways de VPN. 

- [Configurar alertas de métricas de Gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)

- [Configurar alertas para eventos de log de diagnóstico do Gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, confira [Azure Security Benchmark: gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: use um programa de pontuação de risco comum (por exemplo, o Sistema de Pontuação de Vulnerabilidade Comum) ou as classificações de risco padrão de uma ferramenta de verificação de terceiros.

- [Publicação NIST – Sistema de Pontuação de Vulnerabilidade Comum](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, confira [Azure Security Benchmark: gerenciamento de inventário e ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usar uma solução de descoberta de ativos automatizada

**Diretrizes**: use o Azure Resource Graph para consultar e descobrir todos os recursos relacionados aos gateways de VPN em suas assinaturas. Você precisa ter permissões apropriadas (leitura) em seu locatário e poder enumerar todas as assinaturas do Azure, bem como os recursos das assinaturas. Você também pode usar a CLI do Azure para enumerar os recursos do Gateway de VPN.

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

- [CLI do Azure para gateway de VPN](/cli/azure/network/vnet-gateway)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do Gateway de VPN para organizá-los logicamente de acordo com uma taxonomia definida.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos do Azure não autorizados

**Diretrizes**: use marcas, grupos de gerenciamento e assinaturas separadas, sempre que apropriado, para organizar e rastrear os recursos do Gateway de VPN. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil. 

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md) 

- [Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md) 

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário de recursos aprovados do Azure

**Diretrizes**: crie um inventário dos recursos aprovados do Azure e do software aprovado para recursos de computação, de acordo com as necessidades da sua organização.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorar recursos do Azure não aprovados

**Diretrizes**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente, usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o Azure Resource Graph para consultar e descobrir recursos nas assinaturas. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Exemplos de políticas internas do Azure Policy para rede virtual](../virtual-network/policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Diretrizes**: para impedir a criação ou o uso de recursos, os clientes podem atribuir definições do Azure Policy segundo os requisitos de segurança da organização. No entanto, você deve implementar um processo próprio para remover recursos não aprovados ou não autorizados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados nas assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

- [Exemplos de políticas internas do Azure Policy para rede virtual](../virtual-network/policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade de interação dos usuários com o Azure Resource Manager

**Diretrizes**: use o acesso condicional do Azure AD para configurar "Bloquear acesso" para o aplicativo "Gerenciamento do Microsoft Azure", limitando assim a interação dos usuários com o Azure Resource Manager. 

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, confira [Azure Security Benchmark: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: use aliases do Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de recursos de rede do Azure, incluindo o Gateway de VPN. Você também pode usar as definições internas do Azure Policy.

O Azure Resource Manager pode exportar o modelo no formato JSON (JavaScript Object Notation), que deve ser examinado para garantir que as configurações atendam ou ultrapassem os requisitos de segurança da sua organização.

Você também pode usar as recomendações da Central de Segurança do Azure como uma linha de base de configuração segura para recursos do Azure.

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Exemplos de políticas internas do Azure Policy para rede virtual](../virtual-network/policy-reference.md)

- [Exportação de um ou de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretrizes**: use modelos do Azure Resource Manager e atribuições do Azure Policy para configurar com segurança os recursos do Azure associados ao Gateway de VPN e os recursos relacionados. Os modelos do Azure Resource Manager são arquivos em JSON usados para implantar máquinas virtuais com os recursos do Azure, e é preciso realizar a manutenção de modelos personalizados. A Microsoft realiza a manutenção nos modelos de base.  Use os modos [negar] e [implantar se não existir] do Azure Policy para impor configurações seguras nos recursos do Azure.

- [Informações sobre como criar modelos do Azure Resource Manager](../virtual-machines/windows/ps-template.md)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreendendo os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

- [Exemplos de modelo do Azure Resource Manager para rede virtual](../virtual-network/template-samples.md)

- [Exemplos de políticas internas do Azure Policy para rede virtual](../virtual-network/policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Diretrizes**: use o Azure DevOps para armazenar e gerenciar código com segurança, como definições personalizadas do Azure Policy, modelos do Azure Resource Manager e scripts de configuração de estado desejado. Para acessar os recursos que você gerencia no Azure DevOps, você pode conceder ou negar permissões a usuários específicos, a grupos de segurança internos ou a grupos definidos no Azure AD [Active Directory] (se integrado ao Azure DevOps) ou ao Active Directory (se integrado ao TFS). 

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implantar ferramentas de gerenciamento de configuração para recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos do Azure com o Azure Policy. Use aliases do Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de recursos do Azure. Você também pode usar as definições de política internas relacionadas aos seus recursos. Além disso, você pode usar a Automação do Azure para implantar alterações de configuração. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como usar aliases](../governance/policy/concepts/definition-structure.md#aliases)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: atribua definições do Azure Policy para medir as configurações dos recursos do Gateway de VPN. Use os insights do Azure Policy para auditar configurações de recursos e alertar sobre alterações de configuração críticas.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exemplos de políticas internas do Azure Policy para rede virtual](../virtual-network/policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: o serviço Gateway de VPN armazena internamente e transmite chaves pré-compartilhadas do cliente e certificados em formatos criptografados. Os clientes precisam proteger as chaves pré-compartilhadas ou os certificados nos próprios sistemas.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerenciar identidades de maneira segura e automática

**Diretrizes**: a VPN P2S do Azure dá suporte para três métodos de autenticação:

- Baseado em certificados
- RAIO
- Active Directory do Azure (Azure AD)

Recomendamos usar o Azure AD porque ele permite usar as identidades gerenciadas.

- [Configurar um locatário](openvpn-azure-ad-tenant.md)

- [Configurar um locatário com vários aplicativos cliente](openvpn-azure-ad-tenant-multi-app.md)

- [Configurar autenticação multifator](openvpn-azure-ad-mfa.md)

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

*Para obter mais informações, confira [Azure Security Benchmark: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automatizados regulares

**Diretrizes**: use o Azure Resource Manager para implantar recursos do Gateway de VPN. O Azure Resource Manager permite exportar modelos, que podem ser usados como backups para restaurar recursos do Gateway de VPN. Use a Automação do Azure para chamar regularmente a API de exportação de modelos do Azure Resource Manager.

- [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Exemplos de modelo do Azure Resource Manager para rede virtual](../virtual-network/template-samples.md)

- [Grupos de recursos – Exportar modelo](/rest/api/resources/resourcegroups/exporttemplate)

- [Introdução à Automação do Azure](../automation/automation-intro.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Fazer backups completos do sistema e backups de todas as chaves gerenciadas pelo cliente

**Diretrizes**: use o Azure Resource Manager para implantar recursos do Gateway de VPN. O Azure Resource Manager permite exportar modelos, que podem ser usados como backups para restaurar recursos do Gateway de VPN e recursos relacionados. Use a Automação do Azure para chamar regularmente a API de exportação de modelos do Azure Resource Manager.

- [Exportação de um ou de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Grupos de recursos – Exportar modelo](/rest/api/resources/resourcegroups/exporttemplate)

- [Introdução à Automação do Azure](../automation/automation-intro.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, inclusive os de chaves gerenciadas pelo cliente

**Diretrizes**: realize periodicamente a implantação de modelos do Azure Resource Manager para uma assinatura isolada, se necessário.

- [Implantar recursos com modelos do ARM e portal do Azure](../azure-resource-manager/templates/deploy-portal.md)

- [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção dos backups e das chaves gerenciadas pelo cliente

**Diretrizes**: use o Azure DevOps para armazenar e gerenciar código com segurança, como definições personalizadas do Azure Policy e modelos do Azure Resource Manager. Para proteger os recursos que você gerencia no Azure DevOps, você pode conceder ou negar permissões a usuários específicos, a grupos de segurança internos ou a grupos definidos no Azure AD [Active Directory] se integrado ao Azure DevOps.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Exclusão reversível para blobs do Armazenamento do Azure ](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretrizes**: elabore um guia de resposta a incidentes para sua organização. Crie planos de resposta a incidentes por escrito, que definam todas as funções de pessoal e as fases de tratamento e gerenciamento de incidentes, desde a detecção até a revisão posterior. 

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Use o Guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a Central de Segurança do Azure atribui uma gravidade a cada alerta para ajudar a priorizar os alertas que deverão ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, aplique marcas nas assinaturas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu. 

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md) 

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretrizes**: conduza regularmente exercícios para testar a capacidade de resposta a incidentes dos sistemas e proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise o plano de resposta conforme necessário. 

- [Publicação do NIST – Guia para testar, treinar e exercitar programas para planos de TI e capacidades](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos. 

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua para identificar riscos aos recursos do Azure. A exportação contínua permite exportar alertas e recomendações de maneira manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas ao Azure Sentinel. 

- [Como configurar a exportação contínua](../security-center/continuous-export.md) 

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas a alertas de segurança e recomendações, a fim de proteger seus recursos do Azure. 

- [Como configurar a automação de fluxo de trabalho na Central de Segurança](../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, confira [Azure Security Benchmark: testes de penetração e exercícios de Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes de penetração regulares dos recursos do Azure e garantir a correção de todas as conclusões de segurança críticas

**Diretrizes**: siga as Regras de Participação do Teste de Penetração do Microsoft Cloud para verificar se os testes de penetração não estão violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft. 

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)