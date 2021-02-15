---
title: Linha de base de segurança do Azure para o cache do Azure para Redis
description: Linha de base de segurança do Azure para o cache do Azure para Redis
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 567652ec8da257e72aafa95e24af9f5680a2b6d3
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807947"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Linha de base de segurança do Azure para o cache do Azure para Redis

A linha de base de segurança do Azure para o cache do Azure para Redis contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de segurança: Segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua Rede Virtual

**Diretrizes**: implante seu cache do Azure para instância Redis em uma rede virtual (VNet). Uma VNet é uma rede privada na nuvem. Quando uma instância do Cache do Azure para Redis é configurada com uma rede virtual, ela não é endereçável publicamente e somente pode ser acessada de máquinas virtuais e aplicativos dentro da rede virtual.

Você também pode especificar regras de firewall com um intervalo de endereços IP inicial e final. Quando regras de firewall são configuradas, apenas as conexões de cliente de intervalos de endereços IP especificados podem se conectar ao cache.

Como configurar o suporte de rede virtual para um cache Premium do Azure para Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Como configurar o cache do Azure para regras de firewall do Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: monitorar e registrar a configuração e o tráfego de VNets, sub-redes e NICs

**Orientação**: quando as máquinas virtuais são implantadas na mesma rede virtual que o cache do Azure para a instância Redis, você pode usar grupos de segurança de rede (NSG) para reduzir o risco de dados vazamento. Habilite logs de fluxo NSG e envie logs para uma conta de armazenamento do Azure para auditoria de tráfego. Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

Como habilitar logs de fluxo de NSG: 

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como habilitar e usar a Análise de Tráfego: 

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Rejeitar comunicações com endereços IP maliciosos conhecidos

**Diretrizes**: a implantação da VNet (rede virtual) do Azure fornece segurança e isolamento aprimorados para o cache do Azure para Redis, bem como para sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso. Quando implantado em uma VNet, o cache do Azure para Redis não é publicamente endereçável e só pode ser acessado de máquinas virtuais e aplicativos na VNet.

Habilite a proteção contra DDoS Standard no VNets associado ao cache do Azure para instâncias de Redis para proteger contra ataques de DDoS (negação de serviço distribuído). Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

Como configurar o suporte de rede virtual para um cache Premium do Azure para Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Gerenciar a proteção contra DDoS do Azure Standard usando o portal do Azure:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: registrar pacotes de rede e logs de fluxo

**Orientação**: quando as máquinas virtuais são implantadas na mesma rede virtual que o cache do Azure para a instância Redis, você pode usar grupos de segurança de rede (NSG) para reduzir o risco de dados vazamento. Habilite logs de fluxo NSG e envie logs para uma conta de armazenamento do Azure para auditoria de tráfego. Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

Como habilitar logs de fluxo de NSG: 

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como habilitar e usar a Análise de Tráfego: 

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: implantar sistemas de detecção/prevenção de intrusões (IDS/IPS) baseados em rede

**Orientação**: ao usar o cache do Azure para Redis com seus aplicativos Web em execução no serviço Azure app ou instâncias de computação, implante todos os recursos em uma VNet (rede virtual) do Azure e proteja com um WAF (firewall do aplicativo Web) do Azure no gateway de aplicativo Web. Configure o WAF para executar em "modo de prevenção". O modo de prevenção bloqueia invasões e ataques que as regras detectam. O invasor recebe uma exceção "403 acesso não autorizado" e a conexão é encerrada. O modo de Prevenção registra tais ataques nos logs do WAF.

Como alternativa, você pode selecionar uma oferta do Azure Marketplace que dá suporte à funcionalidade de IDS/IPS com inspeção de carga e/ou recursos de detecção de anomalias.

Entenda os recursos de WAF do Azure:

https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview

Como implantar o Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em NSG (grupos de segurança de rede) ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Você também pode usar ASG (grupos de segurança de aplicativo) para ajudar a simplificar a configuração de segurança complexa. O ASGs permite que você configure a segurança de rede como uma extensão natural da estrutura de um aplicativo, permitindo que você agrupe máquinas virtuais e defina políticas de segurança de rede com base nesses grupos.

Marcas de serviço de rede virtual:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Grupos de segurança de aplicativo:

https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede relacionados ao cache do Azure para instâncias Redis com Azure Policy. Use aliases de Azure Policy nos namespaces "Microsoft. cache" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do cache do Azure para instâncias de Redis. Você também pode fazer uso de definições de política internas, como:

Apenas conexões seguras com o Cache Redis devem ser habilitadas

A Proteção contra DDoS Standard deve ser habilitada

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos de Azure Resource Manager (ARM), controle de acesso baseado em função do Azure (RBAC do Azure) e políticas, em uma única definição de Blueprint. Aplique facilmente o blueprint a novas assinaturas e ambientes e ajuste o controle e o gerenciamento por meio do controle de versão.

Como configurar e gerenciar Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar um Azure Blueprint: 

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Diretrizes**: use marcas para recursos de rede associados ao seu cache do Azure para a implantação do Redis para organizá-los logicamente em uma taxonomia.

Como criar e usar marcas: 

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretrizes**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados ao cache do Azure para instâncias Redis. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

Como exibir e recuperar eventos do Log de Atividades do Azure: 

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

como criar alertas no Azure Monitor: 

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de segurança: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém a fonte de tempo usada para recursos do Azure, como o cache do Azure para Redis para carimbos de data/hora nos logs.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Diretrizes**: Habilite as configurações de diagnóstico do Log de Atividades do Azure e envie os logs para um workspace do Log Analytics, um hub de eventos do Azure ou uma conta de armazenamento do Azure para arquivar. Os logs de atividade fornecem informações sobre as operações que foram realizadas no cache do Azure para instâncias Redis no nível do plano de controle. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) executada no nível do plano de controle para o cache do Azure para instâncias Redis.

Como habilitar as configurações de diagnóstico para o log de atividades do Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o log de auditoria para recursos do Azure

**Diretrizes**: Habilite as configurações de diagnóstico do Log de Atividades do Azure e envie os logs para um workspace do Log Analytics, um hub de eventos do Azure ou uma conta de armazenamento do Azure para arquivar. Os logs de atividade fornecem informações sobre as operações que foram realizadas no cache do Azure para instâncias Redis no nível do plano de controle. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) executada no nível do plano de controle para o cache do Azure para instâncias Redis.

Embora as métricas estejam disponíveis habilitando as configurações de diagnóstico, o log de auditoria no plano de dados ainda não está disponível para o cache do Azure para Redis.

Como habilitar as configurações de diagnóstico para o log de atividades do Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: em Azure monitor, defina o período de retenção de log para log Analytics espaços de trabalho associados ao cache do Azure para instâncias de Redis de acordo com os regulamentos de conformidade da sua organização.

Observe que o log de auditoria no plano de dados ainda não está disponível para o cache do Azure para Redis.

Como definir parâmetros de retenção de log:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Diretrizes**: habilitar as configurações de diagnóstico do log de atividades do Azure e enviar os logs para um espaço de trabalho log Analytics. Execute consultas em Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos dados do log de atividades que podem ter sido coletados para o cache do Azure para Redis.

Observe que o log de auditoria no plano de dados ainda não está disponível para o cache do Azure para Redis.

Como habilitar as configurações de diagnóstico para o log de atividades do Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Como coletar e analisar os logs de atividades do Azure no espaço de trabalho Log Analytics no Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: habilitar alertas para atividade anormal

**Orientação**: você pode configurar o para receber alertas com base em métricas e logs de atividade relacionados ao cache do Azure para instâncias de Redis. Azure Monitor permite que você configure um alerta para enviar uma notificação por email, chamar um webhook ou invocar um aplicativo lógico do Azure.

Embora as métricas estejam disponíveis habilitando as configurações de diagnóstico, o log de auditoria no plano de dados ainda não está disponível para o cache do Azure para Redis.

Como configurar alertas para o cache do Azure para Redis: https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Orientação**: não aplicável; O cache do Azure para Redis não processa nem produz logs relacionados a anti-malware.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilitar o registro em log de consultas DNS

**Orientação**: não aplicável; O cache do Azure para Redis não processa nem produz logs relacionados ao DNS.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilitar o log de auditoria de linha de comando

**Diretrizes**: Não aplicável; esta diretriz destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [Controle de segurança: Identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: o Azure Active Directory (AD) tem funções internas que devem ser explicitamente atribuídas e que podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Como obter uma função de diretório no Azure AD com o PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de uma função de diretório no Azure AD com o PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Orientação**: controle o acesso do plano ao cache do Azure para Redis é controlado por meio de Azure Active Directory (AD). O Azure AD não tem o conceito de senhas padrão. 

O acesso do plano de dados ao cache do Azure para Redis é controlado por meio de chaves de acesso. Essas chaves são usadas pelos clientes que se conectam ao seu cache e podem ser regeneradas a qualquer momento.

Não é recomendável que você crie senhas padrão em seu aplicativo. Em vez disso, você pode armazenar suas senhas em Azure Key Vault e, em seguida, usar Azure Active Directory para recuperá-las.

Como regenerar o cache do Azure para chaves de acesso Redis: https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas.

Além disso, para ajudá-lo a controlar contas administrativas dedicadas, você pode seguir as recomendações da Central de Segurança do Azure ou as políticas internas do Azure, como:

- Deve haver mais de um proprietário atribuído à sua assinatura

- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura

- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

Como usar a central de segurança do Azure para monitorar a identidade e o acesso (visualização): https://docs.microsoft.com/azure/security-center/security-center-identity-access

Como usar Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Diretrizes**: o cache do Azure para Redis usa chaves de acesso para autenticar usuários e não oferece suporte ao SSO (logon único) no nível do plano de dados. O acesso ao plano de controle para o cache do Azure para Redis está disponível por meio da API REST e dá suporte a SSO. Para autenticar, defina o cabeçalho de autorização para suas solicitações como um Token Web JSON que você obtém no Azure Active Directory.

Entender o cache do Azure para a API REST do Redis: https://docs.microsoft.com/rest/api/redis/

Entenda SSO com o Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretriz**: Habilite a autenticação multifator (MFA) do Azure Active Directory (AD)e siga as recomendações de gerenciamento de acesso e identidade da Central de Segurança do Azure.

Como habilitar a MFA no Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorar identidade e acesso na Central de Segurança do Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretriz**: Use PAWs (estações de trabalho com acesso privilegiado) com a MFA (autenticação multifator) configurada para fazer logon e configurar os recursos do Azure.

Saiba mais sobre estações de trabalho com acesso privilegiado:

https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Como habilitar a MFA no Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registrar e alertar sobre atividades suspeitas em contas administrativas

**Diretriz**: Use o Privileged Identity Management (PIM) do Azure Active Directory (AD) para geração de logs e alertas quando atividades suspeitas ou inseguras ocorrem no ambiente.

Além disso, use as detecções de risco do Azure Active Directory para ver alertas e relatórios sobre o comportamento do usuário suspeito.

Como implantar o PIM: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Entenda as detecções de risco do Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Orientação**: configurar locais nomeados no acesso condicional do Azure Active Directory (AD) para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

Como configurar localizações nomeadas no Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretriz**: use o Azure Active Directory (AD) como o sistema central de autenticação e autorização. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

A autenticação do Azure AD não pode ser usada para acesso direto ao cache do Azure para o plano de dados do Redis, no entanto, as credenciais do Azure AD podem ser usadas para administração no nível do plano de controle (ou seja, o portal do Azure) para controlar o cache do Azure para chaves de acesso Redis.


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (AD) fornece logs para ajudá-lo a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso de usuários pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo. 

Entender os relatórios do Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como usar as revisões de acesso de identidade do Azure: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: monitorar tentativas de acessar contas desativadas

**Orientação**: você tem acesso a atividades de entrada do Azure Active Directory (AD), auditoria e risco de registro de eventos, que permitem a integração com o Azure Sentinel ou um Siem de terceiros.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas de log desejados no Log Analytics.

Como integrar os logs de atividades do Azure ao Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Como integrar o Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Diretrizes**: para o desvio do comportamento de logon da conta no plano de controle, use os recursos de proteção de identidade e de detecção de riscos do Azure Active Directory (AD) para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

Como exibir entradas suspeitas do Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e habilitar políticas de risco de proteção de identidade: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como integrar o Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: ainda não disponível; Sistema de Proteção de Dados do Cliente ainda não tem suporte para o cache do Azure para Redis.

Lista de serviços com suporte Sistema de Proteção de Dados do Cliente:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [Controle de segurança: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

Como criar e usar marcas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. O cache do Azure para instâncias Redis deve ser separado por rede virtual/sub-rede e marcado adequadamente. Opcionalmente, use o cache do Azure para Redis firewall para definir regras de forma que somente as conexões de cliente de intervalos de endereços IP especificados possam se conectar ao cache.

Como criar assinaturas adicionais do Azure: 

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gerenciamento: 

https://docs.microsoft.com/azure/governance/management-groups/create

Como implantar o cache do Azure para Redis em uma vnet:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Como configurar o cache do Azure para regras de firewall do Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

Como criar e usar marcas: 

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: ainda não disponível; os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o cache do Azure para Redis.

A Microsoft gerencia a infraestrutura subjacente para o cache do Azure para Redis e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

Entender a proteção de dados do cliente no Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações confidenciais em trânsito

**Diretrizes**: o cache do Azure para Redis requer comunicações criptografadas por TLS por padrão. No momento, há suporte para as versões 1,0, 1,1 e 1,2 do TLS. No entanto, o TLS 1,0 e o 1,1 estão em um caminho para a substituição em todo o setor, portanto, use o TLS 1,2, se possível. Se a sua biblioteca ou ferramenta de cliente não oferecer suporte a TLS, a habilitação de conexões não criptografadas poderá ser feita por meio das APIs de gerenciamento ou portal do Azure. Nesses casos em que as conexões criptografadas não são possíveis, colocar o cache e o aplicativo cliente em uma rede virtual seria recomendado.

Entenda a criptografia em trânsito para o cache do Azure para Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-best-practices

Entenda as portas necessárias usadas em cenários de cache de vnet:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o cache do Azure para Redis. Instâncias de marca que contêm informações confidenciais como tal e implementam solução de terceiros, se necessário, para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

Entender a proteção de dados do cliente no Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para controlar o acesso ao cache do Azure para o plano de controle Redis (ou seja, portal do Azure). 

Como configurar o RBAC do Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

A Microsoft gerencia a infraestrutura subjacente para o cache do Azure para Redis e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

Entender a proteção de dados do cliente no Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: o cache do Azure para Redis armazena dados do cliente na memória e, embora seja fortemente protegido por vários controles implementados pela Microsoft, a memória não é criptografada por padrão. Se exigido pela sua organização, criptografe o conteúdo antes de armazenar no cache do Azure para Redis.

Se estiver usando o cache do Azure para o recurso Redis "persistência de dados do Redis", os dados serão enviados para uma conta de armazenamento do Azure que você possui e gerencia. Você pode configurar a persistência na folha "novo cache do Azure para Redis" durante a criação do cache e no menu de recursos para os caches Premium existentes.

Os dados no armazenamento do Azure são criptografados e descriptografados de forma transparente usando a criptografia AES de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2. A criptografia de armazenamento do Azure não pode ser desabilitada. Você pode contar com chaves gerenciadas pela Microsoft para a criptografia da sua conta de armazenamento ou pode gerenciar a criptografia com suas próprias chaves.

Como configurar a persistência no cache do Azure para Redis: https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Entender a criptografia para contas de armazenamento do Azure: https://docs.microsoft.com/azure/storage/common/storage-service-encryption

Entender a proteção de dados do cliente do Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para as instâncias de produção do cache do Azure para Redis e outros recursos críticos ou relacionados.

Como criar alertas para eventos do log de atividades do Azure: 

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Orientação**: siga as recomendações da central de segurança do Azure para proteger seu cache do Azure para instâncias Redis e recursos relacionados.

A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte ao cache do Azure para Redis.

Entenda as recomendações da central de segurança do Azure: https://docs.microsoft.com/azure/security-center/recommendations-reference

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implantar uma solução automatizada de gerenciamento de patch de software de terceiros

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: a Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte ao cache do Azure para Redis.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de segurança: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Usar a descoberta de ativos do Azure

**Diretriz**: Use o Azure Resource Graph para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos etc.) em suas assinaturas.  Configure permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

Como criar consultas com o Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como exibir suas assinaturas do Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Entenda o RBAC do Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

Como criar e usar marcas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear o cache do Azure para instâncias Redis e recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Além disso, use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Como criar assinaturas adicionais do Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gerenciamento: https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar marcas: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: manter um inventário de recursos e títulos de software aprovados do Azure

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação e ao Azure como um todo.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

Tipos de recursos não permitidos

Tipos de recursos permitidos

Além disso, use o grafo de recursos do Azure para consultar/descobrir recursos dentro das assinaturas.

Como configurar e gerenciar Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph: 

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação e ao Azure como um todo.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Usar somente aplicativos aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

Tipos de recursos não permitidos

Tipos de recursos permitidos

Como configurar e gerenciar Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo de recurso específico com o Azure Policy: 

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: implementar lista de aplicativos aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitar a capacidade dos usuários de interagir com o Azure Resource Manager por meio de scripts

**Orientação**: Configure o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com Azure Resource Manager (ARM) configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

Como configurar o acesso condicional para bloquear o acesso ao ARM:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts nos recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separar física ou logicamente os aplicativos de alto risco

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [Controle de segurança: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para o cache do Azure para instâncias Redis com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. cache" para criar políticas personalizadas para auditar ou impor a configuração do cache do Azure para instâncias Redis. Você também pode fazer uso de definições de política internas relacionadas ao cache do Azure para instâncias Redis, como:

Apenas conexões seguras com o Cache Redis devem ser habilitadas

Como exibir os aliases do Azure Policy disponíveis: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretriz**: use o Azure Policy [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Compreender os efeitos do Azure Policy: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Orientação**: se estiver usando definições de Azure Policy personalizadas ou modelos de Azure Resource Manager para o cache do Azure para instâncias Redis e recursos relacionados, use Azure Repos para armazenar e gerenciar com segurança seu código.

Como armazenar código no Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação do Azure Repos: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração do sistema

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. cache" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar o monitoramento automatizado de configuração para serviços do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. cache" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use Azure Policy [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para o cache do Azure para instâncias Redis e recursos relacionados.

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Orientação**: para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure app que está sendo usado para acessar o cache do Azure para instâncias Redis, use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o cache do Azure para gerenciamento de segredos Redis. Certifique-se de que Key Vault exclusão reversível esteja habilitada.

Como integrar-se às Identidades Gerenciadas do Azure: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um cofre de chaves: 

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Como autenticar para o Key Vault:

https://docs.microsoft.com/azure/key-vault/general/authentication

Como atribuir uma política de acesso de Key Vault:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Orientação**: para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure app que está sendo usado para acessar o cache do Azure para instâncias Redis, use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o cache do Azure para gerenciamento de segredos Redis. Verifique se a exclusão temporária do cofre de chaves está habilitada.

Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Identidades gerenciadas permitem que você se autentique em qualquer serviço que dê suporte à autenticação do AAD, incluindo Azure Key Vault, sem nenhuma credencial em seu código.

Como configurar identidades gerenciadas: 

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Como integrar-se às Identidades Gerenciadas do Azure: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Como configurar o verificador de credenciais: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte [Controle de segurança: defesa contra malwares](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usar software antimalware gerenciado centralmente

**Diretriz**: Não aplicável; esta recomendação destina-se a recursos de computação.

O antimalware da Microsoft está habilitado no host subjacente que oferece suporte aos serviços do Azure (por exemplo, Serviço de Aplicativo do Azure), mas não é executado no conteúdo do cliente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, cache do Azure para Redis), no entanto, ele não é executado no conteúdo do cliente.

Verifique previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de Blobs, Banco de Dados do Azure para PostgreSQL, etc. A Microsoft não consegue acessar seus dados nessas instâncias.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software antimalware e as assinaturas sejam atualizados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, cache do Azure para Redis), no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de segurança: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantir backups automatizados regulares

**Orientação**: habilitar persistência Redis. A persistência do Redis permite persistir os dados armazenados no Redis. Você também pode tirar instantâneos e fazer backup dos dados, que podem ser carregados em caso de falha de hardware. Essa é uma enorme vantagem em relação às camadas Básica ou Standard, em que todos os dados são armazenados na memória e pode haver uma possível perda de dados em caso de falha quando os nós do Cache estiverem inativos.

Você também pode usar o cache do Azure para exportar Redis. A exportação permite exportar os dados armazenados no Cache do Azure para Redis para arquivos RDB compatíveis com Redis. É possível usar esse recurso para mover dados de uma instância do Cache do Azure para Redis para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na máquina virtual que hospeda o cache do Azure para a instância do servidor Redis e o arquivo é carregado para a conta de armazenamento designada. Após a operação de exportação ser concluída com um status de êxito ou de falha, o arquivo temporário é excluído.

Como habilitar a persistência de Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Como usar o cache do Azure para a exportação do Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Realizar backups completos do sistema e fazer backup das chaves gerenciadas pelo cliente

**Orientação**: habilitar persistência Redis. A persistência do Redis permite persistir os dados armazenados no Redis. Você também pode tirar instantâneos e fazer backup dos dados, que podem ser carregados em caso de falha de hardware. Essa é uma enorme vantagem em relação às camadas Básica ou Standard, em que todos os dados são armazenados na memória e pode haver uma possível perda de dados em caso de falha quando os nós do Cache estiverem inativos.

Você também pode usar o cache do Azure para exportar Redis. A exportação permite exportar os dados armazenados no Cache do Azure para Redis para arquivos RDB compatíveis com Redis. É possível usar esse recurso para mover dados de uma instância do Cache do Azure para Redis para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na máquina virtual que hospeda o cache do Azure para a instância do servidor Redis e o arquivo é carregado para a conta de armazenamento designada. Após a operação de exportação ser concluída com um status de êxito ou de falha, o arquivo temporário é excluído.

Se estiver usando Azure Key Vault para armazenar credenciais para o cache do Azure para instâncias Redis, garanta backups regulares automatizados de suas chaves.

Como habilitar a persistência de Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Como usar o cache do Azure para a exportação do Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Como fazer backup de chaves do cofre de chaves: 

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: Use o cache do Azure para importação de Redis. A importação pode ser usada para trazer arquivos RDB compatíveis com Redis de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, incluindo Redis em execução no Linux, Windows ou qualquer provedor de nuvem, como Amazon Web Services e outros. Importar os dados é uma maneira fácil de criar um cache com dados previamente populados. Durante o processo de importação, o Cache do Azure para Redis carrega os arquivos RDB do armazenamento do Azure na memória e insere as chaves no cache.

Teste periodicamente a restauração de dados de seus segredos de Azure Key Vault.

Como usar o cache do Azure para importação de Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Como restaurar Key Vault segredos:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: o cache do Azure para backups Redis de Redis exportação e persistência de Redis são armazenados em sua conta de armazenamento do Azure selecionada. Os dados no armazenamento do Azure são criptografados e descriptografados de forma transparente usando a criptografia AES de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2. A criptografia de armazenamento do Azure não pode ser desabilitada. Você pode contar com chaves gerenciadas pela Microsoft para a criptografia da sua conta de armazenamento ou pode gerenciar a criptografia com suas próprias chaves.

Entender a criptografia para contas de armazenamento do Azure: https://docs.microsoft.com/azure/storage/common/storage-service-encryption

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Microsoft

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de segurança: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

Como configurar automações de fluxo de trabalho na Central de Segurança do Azure: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Orientação sobre como criar seu próprio processo de resposta a incidentes de segurança: 

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center: 

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

O cliente também pode aproveitar o Guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu próprio plano de resposta a incidentes: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomenclatura para identificar e categorizar claramente os recursos do Azure.

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e capacidades: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal.  Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

Como definir o contato de segurança da Central de Segurança do Azure: 

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

Como configurar a exportação contínua:

https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para o Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretriz**: use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de “Aplicativos Lógicos” em alertas de segurança e recomendações.

Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [Controle de segurança: testes de penetração e exercícios de Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: realizar testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as constatações de segurança críticas em 60 dias

**Diretriz**: siga as regras de participação da Microsoft para garantir que os testes de penetração não violem as políticas da Microsoft: 

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de Red Team e testes de penetração de sites ao vivo em infraestrutura, serviços e aplicativos de nuvem gerenciados pela Microsoft, aqui:  

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Consulte o [Parâmetro de segurança do Azure](../security/benchmarks/overview.md)
- Saiba mais sobre as [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)