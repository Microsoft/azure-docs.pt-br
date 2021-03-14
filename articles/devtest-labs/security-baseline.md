---
title: Linha de base de segurança do Azure para Azure DevTest Labs
description: A linha de base de segurança Azure DevTest Labs fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: devtest-lab
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 13585173b54c79158161da54f968ba66eb018f6a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705731"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Linha de base de segurança do Azure para Azure DevTest Labs

Essa linha de base de segurança aplica as diretrizes da [1,0 versão de avaliação de segurança do Azure](../security/benchmarks/overview-v1.md) para Azure DevTest Labs. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao Azure DevTest Labs. **Controles** não aplicáveis a Azure DevTest Labs foram excluídos.

Para ver como Azure DevTest Labs é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base de segurança Azure DevTest Labs](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Orientação**: ao implantar Azure DevTest Labs recursos, você deve criar ou usar uma rede virtual existente. Certifique-se de que a rede virtual escolhida tenha um grupo de segurança de rede aplicado a suas sub-redes e controles de acesso à rede configurados específicos para as portas e fontes confiáveis do seu aplicativo. Quando os recursos de um laboratório são configurados com uma rede virtual, eles não são publicamente endereçáveis e só podem ser acessados de dentro da rede virtual. Você também pode optar por criar um laboratório isolado de rede, no qual ao lado dos ambientes de laboratório, os recursos de laboratório como a conta de armazenamento e os cofres de chaves também serão completamente isolados e acessíveis somente por meio de pontos de extremidade especificados. 

Dependendo das suas necessidades organizacionais, use o serviço de firewall do Azure para criar, impor e registrar de forma centralizada as políticas de conectividade de rede e de aplicativo em assinaturas e redes virtuais.

- [Como configurar uma rede virtual para Azure DevTest Labs](devtest-lab-configure-vnet.md)

- [Como criar uma instância do DevTest Labs de rede isolada](network-isolation.md)

- [Como criar um grupo de segurança de rede com regras de segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como implantar e configurar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Diretrizes**: implante um grupo de segurança de rede na rede em que seus recursos de Azure DevTest Labs são implantados. Habilite os logs de fluxo do grupo de segurança de rede em seus grupos de segurança de rede para a auditoria de tráfego.

Você também pode enviar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretrizes**: implante o WAF (firewall do aplicativo Web) do Azure na frente de aplicativos Web críticos implantados usando modelos de Azure Resource Manager para inspeção adicional do tráfego de entrada. Habilite a configuração de diagnóstico para WAF e ingerir logs em uma conta de armazenamento, Hub de eventos ou espaço de trabalho de Log Analytics.

- [Como implantar o Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: rejeitar comunicações com endereços IP maliciosos conhecidos

**Orientação**: a implantação de uma VNet (rede virtual) do Azure para um laboratório aprimora a segurança e o isolamento do seu laboratório. As sub-redes, as políticas de controle de acesso e outros recursos também ajudam a restringir o acesso. Quando implantado em uma VNet, Azure DevTest Labs não é publicamente endereçável e só pode ser acessado de máquinas virtuais e aplicativos na VNet.

Habilite a proteção padrão de DDoS em suas redes virtuais do Azure para proteger contra ataques de DDoS. Use a inteligência de ameaças integrada da central de segurança do Azure para negar comunicações com endereços IP mal-intencionados conhecidos.

Implante o Firewall do Azure em cada um dos limites de rede da organização com inteligência contra ameaças habilitada e configurada para "alertar e negar" para tráfego de rede mal-intencionado. Use o acesso à rede just in time da central de segurança do Azure para configurar o NSGs para limitar a exposição de pontos de extremidade a endereços IP aprovados por um período limitado. Use a proteção de rede adaptável da central de segurança do Azure para recomendar configurações de NSG que limitam portas e IPs de origem com base no tráfego real e na inteligência contra ameaças.

- [Como configurar uma rede virtual para Azure DevTest Labs](devtest-lab-configure-vnet.md)

- [Como configurar a proteção contra DDoS](/azure/virtual-network/manage-ddos-protection)

- [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](/azure/security-center/threat-protection)

- [Entender a proteção de rede adaptável da central de segurança do Azure](../security-center/security-center-adaptive-network-hardening.md)

- [Entender o controle de acesso à rede just in time da central de segurança do Azure](../security-center/security-center-just-in-time.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: habilitar a captura de pacotes do observador de rede em sua rede virtual do laboratório para investigar atividades anormais. 

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: Use um firewall do Azure implantado em sua rede virtual com inteligência contra ameaças habilitada. A filtragem baseada em inteligência contra ameaças do Firewall do Azure pode alertar e rejeitar o tráfego de e para endereços IP e domínios conhecidos mal-intencionados. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. Se sua organização exigir uma funcionalidade adicional sobre o que o Firewall do Azure pode fornecer, selecione uma oferta apropriada do Azure Marketplace que dá suporte à funcionalidade de IDS/IPS com recursos de inspeção de conteúdo.

Implante a solução de firewall de sua escolha em cada um dos limites de rede da sua organização para detectar e/ou negar tráfego mal-intencionado.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)
 

- [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)
 

- [Como configurar alertas com o Firewall do Azure](../firewall/threat-intel.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Orientação**: ao trabalhar com ambientes baseados no DevTest Labs Azure Resource Manager que incluem aplicativos Web, implante um Gateway de aplicativo Azure com HTTPS/TLS habilitado para certificados confiáveis.

- [Como implantar o gateway de aplicativo](../application-gateway/quick-create-portal.md)

- [Como configurar o gateway de aplicativo para usar HTTPS](../application-gateway/create-ssl-portal.md)

- [Entender o balanceamento de carga de camada 7 com gateways de aplicativo Web do Azure](../application-gateway/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure configurados para seus recursos do DevTest Labs. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Você também pode usar grupos de segurança de aplicativo para ajudar a simplificar a configuração de segurança complexa. Os grupos de segurança de aplicativo permitem a você configurar a segurança de rede como uma extensão natural da estrutura de um aplicativo, permitindo o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos.

- [Entender e usar marcas de serviço](../virtual-network/service-tags-overview.md)

- [Entender e usar grupos de segurança de aplicativos](/azure/virtual-network/security-overview#application-security-groups)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede com Azure Policy.

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos do Azure Resource Manager, controles RBAC e políticas, em uma única definição de Blueprint. Você pode aplicar o plano gráfico a novas assinaturas e ajustar o controle e o gerenciamento por meio da versão.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exemplos de Azure Policy para rede](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Diretrizes**: use marcas para recursos de rede associados à sua implantação do Azure DevTest Labs para organizá-los logicamente em uma taxonomia. Para regras de NSG individuais, use o campo "Descrição" para especificar a necessidade empresarial e/ou a duração (etc.) para regras que permitam tráfego de/para uma rede.

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos e detectar alterações nos recursos do Azure. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Como criar alertas no Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém fontes de tempo para recursos do Azure. No entanto, você pode gerenciar as configurações de sincronização de horário para seus recursos de computação.

- [Sincronização de Data/Hora para VMs do Windows no Azure](../virtual-machines/windows/time-sync.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Diretrizes**: Habilite as configurações de diagnóstico do Log de Atividades do Azure e envie os logs para um workspace do Log Analytics, um hub de eventos do Azure ou uma conta de armazenamento do Azure para arquivar. Os logs de atividade fornecem informações sobre as operações que foram feitas em suas instâncias de Azure DevTest Labs no nível de plano de gerenciamento. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) feita no nível de plano de gerenciamento para suas instâncias do DevTest Labs.

- [Criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes](/azure/azure-monitor/platform/diagnostic-settings)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o log de auditoria para recursos do Azure

**Diretrizes**: Habilite as configurações de diagnóstico do Log de Atividades do Azure e envie os logs para um workspace do Log Analytics, um hub de eventos do Azure ou uma conta de armazenamento do Azure para arquivar. Os logs de atividade fornecem informações sobre as operações que foram feitas em suas instâncias de Azure DevTest Labs no nível de plano de gerenciamento. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) feita no nível de plano de gerenciamento para suas instâncias do DevTest Labs.

- [Criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes](/azure/azure-monitor/platform/diagnostic-settings)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Diretrizes**: Azure DevTest Labs VMS (máquinas virtuais) são criadas e pertencentes ao cliente. Portanto, é responsabilidade da organização monitorá-la. Você pode usar a central de segurança do Azure para monitorar o sistema operacional de computação. Os dados coletados pela central de segurança do sistema operacional incluem o tipo e a versão do so, sistema operacional (logs de eventos do Windows), processos em execução, nome da máquina, endereços IP e usuário conectado. O agente de Log Analytics também coleta arquivos de despejo de memória.

Para obter mais informações, consulte os seguintes artigos:

- [Como coletar logs de host interno da máquina virtual do Azure com Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Entender a coleta de dados da central de segurança do Azure](../security-center/security-center-enable-data-collection.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

**Diretrizes**: em Azure monitor, defina o período de retenção de log para log Analytics espaços de trabalho associados às suas instâncias de Azure DevTest Labs de acordo com os regulamentos de conformidade da sua organização.

- [Para obter mais informações, consulte o seguinte artigo](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Diretrizes**: habilitar as configurações de diagnóstico do log de atividades do Azure e enviar os logs para um espaço de trabalho log Analytics. Execute consultas em Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos dados do log de atividades que podem ter sido coletados para Azure DevTest Labs.

Para obter mais informações, consulte os seguintes artigos:

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](/azure/azure-monitor/platform/diagnostic-settings)

- [Como coletar e analisar os logs de atividades do Azure no espaço de trabalho Log Analytics no Azure Monitor](/azure/azure-monitor/platform/activity-log)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: Use o espaço de trabalho do Azure log Analytics para monitoramento e alertas em atividades anormais em logs de segurança e eventos relacionados à sua Azure DevTest Labs.

- [Como alertar sobre dados de log do log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: Azure Active Directory (Azure AD) tem funções internas que devem ser explicitamente atribuídas e que podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure DevTest Labs funções](devtest-lab-add-devtest-user.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: Azure Active Directory (Azure AD) não tem o conceito de senhas padrão. Outros recursos do Azure que exigem uma senha forçam a criação de uma senha com requisitos de complexidade e um comprimento mínimo de senha, que difere dependendo do serviço. Você é responsável por aplicativos de terceiros e serviços do Marketplace que podem usar senhas padrão.

O DevTest Labs não tem o conceito de senhas padrão.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas.

Além disso, para ajudá-lo a controlar contas administrativas dedicadas, você pode seguir as recomendações da Central de Segurança do Azure ou as políticas internas do Azure, como:

- Deve haver mais de um proprietário atribuído à sua assinatura

- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

- [Como usar a Central de Segurança do Azure para monitorar a identidade e o acesso (versão prévia)](../security-center/security-center-identity-access.md)

- [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure DevTest Labs funções](devtest-lab-add-devtest-user.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Diretrizes**: o DevTest Labs usa o serviço do Azure Active Directory (AD do Azure) para gerenciamento de identidades. Considere esses dois aspectos principais ao conceder aos usuários acesso a um ambiente baseado em DevTest Labs:
- Gerenciamento de recursos: fornece acesso ao portal do Azure para gerenciar recursos (criar VMs, criar ambientes, iniciar, parar, reiniciar, excluir e aplicar artefatos e assim por diante). O gerenciamento de recursos é feito no Azure usando o controle de acesso baseado em função do Azure (RBAC do Azure). Você atribui funções a usuários e define permissões de nível de acesso e recursos.
- Máquinas virtuais (nível de rede): na configuração padrão, as VMs usam uma conta de administrador local. Se houver um domínio disponível (Azure Active Directory Domain Services (AD DS do Azure), um domínio local ou um domínio baseado em nuvem), as máquinas poderão ser unidas ao domínio. Os usuários podem usar suas identidades baseadas em domínio usando o artefato de ingresso no domínio para se conectarem aos computadores.

- [Arquitetura de referência para o DevTest Labs](https://docs.microsoft.com/azure/devtest-labs/devtest-lab-reference-architecture#architecture)

- [Entender o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: habilite a autenticação multifator do Azure Active Directory (Azure AD) e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use PAWs (estações de trabalho com acesso privilegiado) com a autenticação multifator configurada para fazer logon e configurar os recursos do Azure. 

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 
- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use os relatórios de segurança do Azure Active Directory (AD do Azure) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorrer no ambiente. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: gerenciar recursos do Azure somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar locais nomeados no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (Azure AD) fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso de usuários pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo.

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/overview-reports.md)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Orientação**: você tem acesso à atividade de entrada do Azure Active Directory (Azure AD), auditoria e fontes de log de eventos de risco, que permitem a integração com qualquer ferramenta de/Monitoring de Siem (gerenciamento de informações e eventos de segurança).

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar alertas no espaço de trabalho Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Diretrizes**: Use os recursos de proteção de risco e identidade do Azure Active Directory (Azure AD) para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário.

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usar o RBAC do Azure para gerenciar o acesso aos recursos

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para controlar o acesso a laboratórios no Azure DevTest Labs.

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

- [Entender as funções no DevTest Labs](devtest-lab-add-devtest-user.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem em instâncias do DevTest Labs e outros recursos importantes ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](/azure/azure-monitor/platform/alerts-activity-log)

- [Como criar alertas para eventos do log de atividades do DevTest Labs](create-alerts.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (incluindo os recursos do DevTest Labs) em suas assinaturas. Verifique se você tem permissões apropriadas (leitura) em seu locatário e pode enumerar todas as assinaturas e recursos do Azure em suas assinaturas.

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente de acordo com uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Configurar marcas para o DevTest Labs](devtest-lab-add-tag.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas e separe os laboratórios, quando apropriado, para organizar e controlar laboratórios e recursos relacionados ao laboratório. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura rapidamente.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar um laboratório usando o DevTest Labs](devtest-lab-create-lab.md)

- [Como criar e usar marcas](devtest-lab-add-tag.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definir e manter um inventário de recursos aprovados do Azure

**Diretrizes**: Crie um inventário dos recursos aprovados do Azure e do software aprovado para recursos de computação de acordo com as necessidades organizacionais. Como administrador de assinatura, você também pode usar controles de aplicativo adaptáveis, um recurso da central de segurança do Azure para ajudá-lo a definir um conjunto de aplicativos que podem ser executados em grupos de computadores de laboratório configurados. Esse recurso está disponível para Windows Azure e não Azure (todas as versões, clássicas ou Azure Resource Manager) e computadores Linux.

- [Como habilitar o controle de aplicativo adaptável](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use a política do Azure para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o grafo de recursos do Azure para consultar/descobrir recursos dentro das assinaturas. Ele pode ajudar em ambientes com alta segurança, como aqueles com contas de armazenamento.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Orientação**: a automação do Azure fornece controle completo durante a implantação, operações e encerramento de cargas de trabalho e recursos. Como administrador de assinatura, você pode usar o inventário de máquina virtual do Azure para automatizar a coleta de informações sobre todos os softwares em VMs do DevTest Labs em sua assinatura. As propriedades nome do software, versão, editor e hora de atualização estão disponíveis na portal do Azure. Para obter acesso à data de instalação e outras informações, o cliente precisou habilitar o diagnóstico em nível de convidado e colocar os logs de eventos do Windows em um espaço de trabalho Log Analytics.

Além de usar Controle de Alterações para o monitoramento de aplicativos de software, os controles de aplicativo adaptáveis na central de segurança do Azure usam o aprendizado de máquina para analisar os aplicativos em execução em seus computadores e criar uma lista de permissões dessa inteligência. Esse recurso simplifica muito o processo de configuração e manutenção de políticas de lista de permissões de aplicativo, permitindo que você evite o uso de software indesejado em seu ambiente. Você pode configurar o modo de auditoria ou o modo impor. O modo de auditoria só audita a atividade nas VMs protegidas. O modo impor impõe as regras e garante que os aplicativos que não têm permissão para serem executadas sejam bloqueados. 

- [Uma introdução à Automação do Azure](../automation/automation-intro.md)

- [Como habilitar o inventário de VM do Azure](../automation/automation-tutorial-installed-software.md)

- [Entender os controles de aplicativo adaptáveis](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: a automação do Azure fornece controle completo durante a implantação, operações e encerramento de cargas de trabalho e recursos. Como administrador de assinatura, você pode usar Controle de Alterações para identificar todos os softwares instalados em VMs hospedadas no DevTest Labs. Você pode implementar seu próprio processo ou usar a configuração de estado da automação do Azure para remover software não autorizado.

- [Uma introdução à Automação do Azure](../automation/automation-intro.md)

- [Controlar alterações no ambiente com a solução Controle de Alterações](../automation/change-tracking/overview.md)

- [Visão geral da configuração do estado de automação do Azure](../automation/automation-dsc-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="68-use-only-approved-applications"></a>6.8: usar somente aplicativos aprovados

**Orientação**: como um administrador de assinatura, você pode usar os controles de aplicativo adaptáveis da central de segurança do Azure para garantir que apenas o software autorizado seja executado e todos os softwares não autorizados sejam impedidos de serem executados em VMs do Azure hospedadas no DevTest Labs.

- [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretrizes**: Use a política do Azure para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Material de referência:

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Orientação**: o controle de aplicativo adaptável é uma solução inteligente, automatizada e de ponta a ponta da central de segurança do Azure, que ajuda você a controlar quais aplicativos podem ser executados em suas máquinas do Azure e não Azure (Windows e Linux), hospedados no DevTest Labs. Observe que você precisa ser um administrador de assinatura para definir essa configuração para os recursos de computação subjacentes hospedados no DevTest Labs. Implemente uma solução de terceiros se essa configuração não atender ao requisito da sua organização.

- [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com Azure Resource Manager Configurando o **acesso de bloco** para o aplicativo de **Gerenciamento de Microsoft Azure** .

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: limitar a capacidade dos usuários de executar scripts em recursos de computação

**Orientação**: dependendo do tipo de scripts, você pode usar configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts nas VMs hospedadas no DevTest Labs. Você também pode usar os controles de aplicativo adaptáveis da central de segurança do Azure para garantir que apenas o software autorizado seja executado, e que todos os softwares não autorizados sejam impedidos de serem executados nas VMs do Azure subjacentes.

- [Como controlar a execução de script do PowerShell em ambientes Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

- [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separar física ou logicamente os aplicativos de alto risco

**Diretrizes**: aplicativos de alto risco implantados em seu ambiente do Azure podem ser isolados usando rede virtual, sub-rede, assinaturas, grupos de gerenciamento e assim por diante. e é suficientemente protegido com um firewall do Azure, o WAF (firewall do aplicativo Web) ou o NSG (grupo de segurança de rede).

- [Configurar a rede virtual para o DevTest Labs](devtest-lab-configure-vnet.md)

- [Visão geral do Firewall do Azure](../web-application-firewall/overview.md)

- [Visão geral de Firewall de Aplicativo Web](../virtual-network/network-security-groups-overview.md)

- [Visão geral da segurança da rede](security-baseline.md)

- [Organizar seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md)

- [Guia de decisão da assinatura](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor a configuração dos recursos do Azure criados como parte do DevTest Labs. Você também pode usar definições de Azure Policy internas.

Além disso, Azure Resource Manager tem a capacidade de exportar o modelo no JavaScript Object Notation (JSON), que deve ser revisado para garantir que as configurações atendam/excedam os requisitos de segurança para sua organização.

Você também pode usar recomendações da central de segurança do Azure como uma linha de base de configuração segura para seus recursos do Azure.

- [Como exibir os aliases de Azure Policy disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Tutorial: Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Orientação**: Use as recomendações da central de segurança do Azure para manter as configurações de segurança em todos os recursos de computação subjacentes criados como parte do DevTest Labs. Além disso, você pode usar imagens personalizadas do sistema operacional ou os artefatos da configuração de estado da automação do Azure ou do DevTest Labs para estabelecer a configuração de segurança do sistema operacional exigido pela sua organização.

- [Como monitorar as recomendações da central de segurança do Azure](../security-center/security-center-recommendations.md)

- [Recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md)

- [Visão geral do State Configuration da Automação do Azure](../automation/automation-dsc-overview.md)

- [Carregar um VHD e usá-lo para criar novas VMs do Windows no Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Criar uma VM Linux usando um disco personalizado com a CLI do Azure](../virtual-machines/linux/upload-vhd.md)

- [Criar e distribuir imagens personalizadas para vários DevTest Labs](image-factory-save-distribute-custom-images.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Diretrizes**: Use Azure Policy **negar** e **implantar se não houver** regras para impor configurações seguras em seus recursos do Azure criados como parte do DevTest Labs. Além disso, você pode usar modelos de Azure Resource Manager para manter a configuração de segurança dos recursos do Azure exigidos por sua organização.

- [Entender Azure Policy efeitos](../governance/policy/concepts/effects.md)

- [Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Visão geral de modelos de Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Orientação**: siga as recomendações da central de segurança do Azure sobre como executar avaliações de vulnerabilidade em seus recursos de computação do Azure subjacentes criados como parte de um laboratório. Além disso, você pode usar modelos de Azure Resource Manager, imagens do sistema operacional personalizado ou configuração de estado da automação do Azure para manter a configuração de segurança do sistema operacional exigido pela sua organização. Você também pode usar a solução de fábrica de imagem, que é uma solução de configuração como código que cria e distribui imagens automaticamente de forma regular com todas as configurações desejadas.

Além disso, as imagens de máquina virtual do Azure Marketplace publicadas pela Microsoft são gerenciadas e mantidas pela Microsoft.

- [Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure](../security-center/deploy-vulnerability-assessment-vm.md)

- [Visão geral do State Configuration da Automação do Azure](../automation/automation-dsc-overview.md)

- [Script de exemplo para carregar um VHD para o Azure e criar uma nova VM](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

- [Como criar uma fábrica de imagens no DevTest Labs](image-factory-create.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Diretrizes**: Use o Azure DevOps para armazenar e gerenciar com segurança seu código, como políticas personalizadas do Azure, modelos de Azure Resource Manager e scripts de configuração de estado desejado. Para acessar os recursos que você gerencia no Azure DevOps, você pode conceder ou negar permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory (AD do Azure), se integrados ao Azure DevOps.

- [Azure Repos o tutorial do git](/azure/devops/repos/git/gitworkflow)

- [Sobre permissões e grupos](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions?view=azure-devops&amp;tabs=preview-page&amp;preserve-view=true)

- [Integração entre Azure DevTest Labs e o fluxo de trabalho DevOps do Azure](devtest-lab-dev-ops.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Orientação**: se estiver usando imagens personalizadas, use o Azure RBAC (controle de acesso baseado em função) para garantir que somente usuários autorizados possam acessar as imagens. Usando uma galeria de imagens compartilhadas, você pode compartilhar suas imagens para laboratórios específicos que precisam dela. Para imagens de contêiner, armazene-as no registro de contêiner do Azure e use o RBAC do Azure para garantir que somente usuários autorizados possam acessar as imagens.

- [Entender o RBAC do Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Como configurar o RBAC do Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Configurar uma galeria de imagens compartilhada para um DevTest Labs](configure-shared-image-gallery.md)

- [Entender o RBAC do Azure para registro de contêiner](../container-registry/container-registry-roles.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: definir e implementar configurações de segurança padrão para recursos do Azure usando Azure Policy. Use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos recursos do Azure criados em DevTest Labs. Você também pode fazer uso de definições de política internas relacionadas aos seus recursos específicos. Além disso, você pode usar a automação do Azure para implantar alterações de configuração.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como usar aliases](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

**Diretrizes**: a configuração de estado da automação do Azure é um serviço de gerenciamento de configuração para nós de DSC (configuração de estado desejado) em qualquer datacenter local ou na nuvem. Você pode, facilmente, integrar máquinas, atribuir a elas configurações declarativas e exibir relatórios que mostram a conformidade de cada computador com o estado desejado especificado. Você também pode escrever um artefato personalizado que pode ser instalado em cada computador de laboratório para garantir que eles estejam seguindo as políticas organizacionais. 

- [Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure](../automation/automation-dsc-onboarding.md)

- [Criando artefatos personalizados para máquinas virtuais do DevTest Labs](devtest-lab-artifact-author.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: Use a central de segurança do Azure para executar verificações de linha de base para os recursos do Azure criados em DevTest Labs. Além disso, use Azure Policy para alertar e auditar configurações de recursos do Azure.

- [Como corrigir recomendações na central de segurança do Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: Use a central de segurança do Azure para executar verificações de linha de base para configurações do sistema operacional e do Docker para contêineres que são executados em seu laboratório.

- [Entenda as recomendações do contêiner da Central de Segurança do Azure](../security-center/container-security.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Diretrizes**: Use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos para seus aplicativos de nuvem.

- [Configurar a identidade gerenciada para implantar ambientes de Azure Resource Manager no DevTest Labs](use-managed-identities-environments.md)

- [Configurar identidade gerenciada para implantar máquinas virtuais no DevTest Labs](enable-managed-identities-lab-vms.md)

- [Como criar um cofre de chaves](../key-vault/general/quick-create-portal.md)

- [Como fornecer Key Vault autenticação com uma identidade gerenciada](../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso de Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretrizes**: Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). As identidades gerenciadas permitem que você se autentique em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.

- [Configurar a identidade gerenciada para implantar ambientes de Azure Resource Manager no DevTest Labs](use-managed-identities-environments.md)

- [Configurar identidade gerenciada para implantar máquinas virtuais no DevTest Labs](enable-managed-identities-lab-vms.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Diretrizes**: Use o Microsoft antimalware para Azure para monitorar e defender continuamente seus recursos. Para o Linux, use a solução antimalware de terceiros.  Além disso, use a detecção de ameaças da central de segurança do Azure para serviços de dados para detectar malwares carregados em contas de armazenamento. 

- [Como configurar o Microsoft antimalware para o Azure](../security/fundamentals/antimalware.md) 

- [Proteção contra ameaças na Central de Segurança do Azure](/azure/security-center/threat-protection)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Azure app serviço hospedado em um laboratório), no entanto, ele não é executado no seu conteúdo. 

Pré-examinar arquivos carregados em recursos não computados do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de BLOBs e assim por diante. 

Use a detecção de ameaças da central de segurança do Azure para serviços de dados para detectar malwares carregados em contas de armazenamento. 

- [Entender o Microsoft antimalware para o Azure](../security/fundamentals/antimalware.md) 

- [Entender a detecção de ameaças da central de segurança do Azure para serviços de dados](/azure/security-center/security-center-alerts-data-services)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: garantir que o software antimalware e as assinaturas sejam atualizados

**Orientação**: quando implantado, o Microsoft antimalware para Azure instalará automaticamente as atualizações de assinatura, plataforma e mecanismo mais recentes por padrão. Siga as recomendações na central de segurança do Azure: " &amp; aplicativos de computação" para garantir que todos os pontos de extremidade para DevTest Labs subjacentes aos recursos de computação estejam atualizados com as assinaturas mais recentes. O sistema operacional Windows pode estar mais protegido com segurança adicional para limitar o risco de ataques baseados em vírus ou malware com o serviço de proteção avançada contra ameaças do Microsoft defender, que se integra à central de segurança do Azure.

- [Como implantar o Microsoft antimalware para o Azure](../security/fundamentals/antimalware.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantir backups automatizados regulares

**Diretrizes**: atualmente, Azure DevTest Labs não dá suporte a backups e instantâneos de VM. No entanto, você pode habilitar e configurar o backup do Azure nas VMs do Azure subjacentes hospedadas no DevTest Labs. Além disso, você também pode configurar a frequência desejada e o período de retenção para backups automáticos, contanto que você tenha acesso apropriado aos recursos de computação subjacentes. 

- [Uma visão geral do backup de VM do Azure](../backup/backup-azure-vms-introduction.md)

- [Fazer backup de uma VM do Azure usando as configurações da VM](../backup/backup-azure-vms-first-look-arm.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: atualmente, Azure DevTest Labs não dá suporte a backups e instantâneos de VM. No entanto, você pode criar instantâneos de suas VMs do Azure subjacentes hospedadas no DevTest Labs ou os discos gerenciados anexados a essas instâncias usando o PowerShell ou APIs REST, contanto que você tenha acesso apropriado aos recursos de computação subjacentes. Você também pode fazer backup de qualquer chave gerenciada pelo cliente dentro de Azure Key Vault.

Habilite o backup do Azure em VMs do Azure de destino e os períodos de frequência e retenção desejados. Ele inclui o backup completo do estado do sistema. Se você estiver usando o Azure Disk Encryption, o backup de VM do Azure manipulará automaticamente o backup de chaves gerenciadas pelo cliente.

- [Fazer backup em VMs do Azure que usam criptografia](../backup/backup-azure-vms-encryption.md)

- [Visão geral do backup de VM do Azure](../backup/backup-azure-vms-introduction.md)

- [Uma visão geral do backup de VM do Azure](../backup/backup-azure-vms-introduction.md)

- [Como fazer backup de chaves Key Vault no Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: garanta a capacidade de executar periodicamente a restauração de dados de conteúdo no backup do Azure. Se necessário, teste a restauração de conteúdo para uma rede virtual isolada ou assinatura. Além disso, teste a restauração de chaves de backup gerenciadas pelo cliente.

Se você estiver usando o Azure Disk Encryption, poderá restaurar a VM do Azure com as chaves de criptografia de disco. Ao usar a criptografia de disco, você pode restaurar a VM do Azure com as chaves de criptografia de disco.

- [Fazer backup em VMs do Azure que usam criptografia](../backup/backup-azure-vms-encryption.md)

- [Como recuperar arquivos do backup de VM do Azure](../backup/backup-azure-restore-files-from-vm.md)

- [Como restaurar chaves do cofre de chaves no Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Como fazer backup e restaurar uma VM criptografada](../backup/backup-azure-vms-encryption.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: quando você faz backup de discos gerenciados com o backup do Azure, as VMs são criptografadas em repouso com o criptografia do serviço de armazenamento (SSE). O backup do Azure também pode fazer backup de VMs do Azure que são criptografadas usando Azure Disk Encryption. O Azure Disk Encryption se integra com as chaves de criptografia do BitLocker (BEKs), que são protegidas em um cofre de chaves como segredos. O Azure Disk Encryption também se integra com Azure Key Vault chaves de criptografia de chave (KEKs). Habilite a exclusão reversível no Key Vault para proteger chaves contra exclusão acidental ou mal-intencionada.

- [Exclusão reversível para VMs](../backup/soft-delete-virtual-machines.md)

- [Visão geral de exclusão reversível do Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretrizes**: desenvolver um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de manipulação e gerenciamento de incidentes, desde a detecção até a revisão após o incidente. 

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Use o guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a central de segurança do Azure atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque as assinaturas usando marcas e crie um sistema de nomeação para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na importância dos recursos do Azure e do ambiente em que o incidente ocorreu. 

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md) 

- [Usar marcas para organizar seus recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise seu plano de resposta conforme necessário. 

- [Publicação do NIST - Guia para testar, treinar e exercitar programas para planos de TI e capacidades](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos. 

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua, continuamente. Você pode usar o conector de dados da central de segurança do Azure para transmitir os alertas para o Azure Sentinel. 

- [Como configurar a exportação contínua](../security-center/continuous-export.md) 

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Orientação**: usar o recurso de automação de fluxo de trabalho central de segurança do Azure para disparar automaticamente respostas a alertas de segurança e recomendações para proteger os recursos do Azure. 

- [Como configurar a automação do fluxo de trabalho na central de segurança](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de teste de penetração Microsoft Cloud do Engagement para garantir que seus testes de penetração não estejam violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft. 

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
