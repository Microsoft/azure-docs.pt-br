---
title: Linha de base de segurança do Azure para o Cache do Azure para Redis
description: A linha de base de segurança do Cache do Azure para Redis fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: cache
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f07eece54bfe456e173e664b19777cfc98b71368
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566856"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Linha de base de segurança do Azure para o Cache do Azure para Redis

Essa linha de base de segurança aplica as diretrizes do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview-v1.md) ao Cache do Azure para Redis. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo Azure Security Benchmark e pelas diretrizes relacionadas aplicáveis ao Cache do Azure para Redis. Os **controles** não aplicáveis ao Cache do Azure para Redis foram excluídos.

 
Para ver como o Cache do Azure para Redis é mapeado por completo para o Azure Security Benchmark, confira o [arquivo de mapeamento completo da linha de base de segurança do Cache do Azure para Redis](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Diretrizes**: implante a instância do Cache do Azure para Redis em uma VNet (rede virtual). Uma VNet é uma rede privada na nuvem. Quando uma instância do Cache do Azure para Redis é configurada com uma rede virtual, ela não é endereçável publicamente e somente pode ser acessada de máquinas virtuais e aplicativos dentro da rede virtual.

Também é possível especificar regras de firewall com um intervalo de endereços IP inicial e final. Quando regras de firewall são configuradas, apenas as conexões de cliente de intervalos de endereços IP especificados podem se conectar ao cache.

- [Como configurar o Suporte de Rede Virtual para um Cache do Azure para Redis Premium](cache-how-to-premium-vnet.md)

- [Como configurar regras de firewall do Cache do Azure para Redis](./cache-configure.md#firewall)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e adaptadores de rede

**Diretrizes**: quando as Máquinas Virtuais são implantadas na mesma rede virtual da instância do Cache do Azure para Redis, você pode usar NSGs (grupos de segurança de rede) para reduzir o risco de exfiltração dos dados. Habilite logs de fluxo do NSG e envie logs para uma conta do Armazenamento do Azure para a auditoria de tráfego. Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar as comunicações com endereços IP maliciosos conhecidos

**Diretrizes**: a implantação de VNet (Rede Virtual) do Azure fornece segurança e isolamento aprimorados para o Cache do Azure para Redis, bem como para sub-redes, políticas de controle de acesso e outros recursos a fim de restringir ainda mais o acesso. Quando implantado em uma VNet, o Cache do Azure para Redis não é endereçável publicamente e só pode ser acessado em máquinas virtuais e aplicativos dentro da VNet.

Habilite a Proteção contra DDoS Standard nas VNets associadas às instâncias do Cache do Azure para Redis para proteção contra ataques de DDoS (negação de serviço distribuído). Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

- [Como configurar o Suporte de Rede Virtual para um Cache do Azure para Redis Premium](cache-how-to-premium-vnet.md)

- [Gerenciar a Proteção contra DDoS do Azure Standard usando o portal do Azure](../ddos-protection/manage-ddos-protection.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="15-record-network-packets"></a>1.5: Registrar os pacotes de rede

**Diretrizes**: quando as máquinas virtuais são implantadas na mesma rede virtual da instância do Cache do Azure para Redis, você pode usar NSGs (grupos de segurança de rede) para reduzir o risco de exfiltração dos dados. Habilite logs de fluxo do NSG e envie logs para uma conta do Armazenamento do Azure para a auditoria de tráfego. Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar IDS/IPS (sistemas de detecção de intrusões/prevenção de intrusões) baseados em rede

**Diretrizes**: ao usar o Cache do Azure para Redis com seus aplicativos Web em execução no Serviço de Aplicativo do Azure ou em instâncias de computação, implante todos os recursos em uma VNet (Rede Virtual) do Azure e proteja-os com um WAF (Firewall de Aplicativo Web) do Azure no Gateway de Aplicativo Web. Configure o WAF para execução no "Modo de Prevenção". O Modo de Prevenção bloqueia intrusões e ataques detectados pelas regras. O invasor recebe uma exceção "403 acesso não autorizado" e a conexão é encerrada. O modo de Prevenção registra tais ataques nos logs do WAF.

Como alternativa, você pode escolher uma oferta do Azure Marketplace que dê suporte à funcionalidade de IDS/IPS com funcionalidades de detecção de anomalias e/ou inspeção de conteúdo.

- [Noções básicas sobre as funcionalidades do WAF do Azure](../web-application-firewall/ag/ag-overview.md)

- [Como implantar o WAF do Azure](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em NSGs (grupos de segurança de rede) e no Firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Você também pode usar ASGs (grupos de segurança de aplicativo) para ajudar a simplificar a configuração de segurança complexa. Os ASGs permitem que você configure a segurança de rede como uma extensão natural da estrutura de um aplicativo, possibilitando o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos.

- [Marcas de serviço de rede virtual](../virtual-network/service-tags-overview.md)

- [Grupos de segurança do aplicativo](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para os recursos de rede relacionados às suas instâncias do Cache do Azure para Redis com o Azure Policy. Use aliases do Azure Policy nos namespaces "Microsoft.Cache" e "Microsoft.Network" para criar políticas personalizadas a fim de auditar ou impor a configuração de rede das suas instâncias do Cache do Azure para Redis. Você também pode usar definições de política internas, como:
- Apenas conexões seguras com o Cache Redis devem ser habilitadas

- A Proteção contra DDoS Standard deve ser habilitada

Use também o Azure Blueprints para simplificar as implantações do Azure em grande escala por meio do empacotamento de artefatos de ambiente importantes, como modelos do Azure Resource Manager, o RBAC do Azure (controle de acesso baseado em função do Azure) e políticas, em uma só definição de blueprint. Aplique facilmente o blueprint a novas assinaturas e ambientes e ajuste o controle e o gerenciamento por meio do controle de versão.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar um Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Documentar regras de configuração de tráfego

**Diretrizes**: use marcas para recursos de rede associados à implantação do Cache do Azure para Redis para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretrizes**: use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados às instâncias do Cache do Azure para Redis. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, confira [Azure Security Benchmark: log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central de log de segurança

**Diretrizes**: Habilite as configurações de diagnóstico do Log de Atividades do Azure e envie os logs para um workspace do Log Analytics, um hub de eventos do Azure ou uma conta de armazenamento do Azure para arquivar. Os logs de atividades fornecem insights sobre as operações realizadas nas instâncias do Cache do Azure para Redis no nível do painel de controle. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) executada no nível do painel de controle das instâncias do Cache do Azure para Redis.

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: Habilite as configurações de diagnóstico do Log de Atividades do Azure e envie os logs para um workspace do Log Analytics, um hub de eventos do Azure ou uma conta de armazenamento do Azure para arquivar. Os logs de atividades fornecem insights sobre as operações realizadas nas instâncias do Cache do Azure para Redis no nível do painel de controle. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) executada no nível do painel de controle das instâncias do Cache do Azure para Redis.

Embora as métricas estejam disponíveis pela habilitação das Configurações de Diagnóstico, o log de auditoria no plano de dados ainda não está disponível no Cache do Azure para Redis.

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: no Azure Monitor, defina o período de retenção de log para os workspaces do Log Analytics associados às instâncias do Cache do Azure para Redis de acordo com os regulamentos de conformidade da sua organização.

Observe que o log de auditoria no plano de dados ainda não está disponível no Cache do Azure para Redis.

- [Como definir os parâmetros de retenção de log](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e examinar os logs

**Diretrizes**: habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um workspace do Log Analytics. Execute consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados do log de atividades que possam ter sido coletados para o Cache do Azure para Redis.

Observe que o log de auditoria no plano de dados ainda não está disponível no Cache do Azure para Redis.

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como coletar e analisar os logs de atividades do Azure no workspace do Log Analytics no Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Habilitar alertas sobre atividades anormais

**Diretrizes**: você pode configurar isso para receber alertas com base em métricas e logs de atividades relacionados às instâncias do Cache do Azure para Redis. O Azure Monitor permite que você configure um alerta para enviar uma notificação por email, chamar um webhook ou invocar um Aplicativo Lógico do Azure.

Embora as métricas estejam disponíveis pela habilitação das Configurações de Diagnóstico, o log de auditoria no plano de dados ainda não está disponível no Cache do Azure para Redis.

- [Como configurar alertas do Cache do Azure para Redis](./cache-how-to-monitor.md#alerts)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, confira [Azure Security Benchmark: controle de acesso e identidade](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Diretrizes**: o Azure AD (Azure Active Directory) tem funções internas que precisam ser atribuídas explicitamente e que podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc e descobrir contas que sejam membros de grupos administrativos.

- [Como obter uma função do diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Como obter membros de uma função do diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: o acesso do painel de controle ao Cache do Azure para Redis é controlado por meio do Azure AD (Azure Active Directory). O Azure AD não tem o conceito de senhas padrão.

O acesso do plano de dados ao Cache do Azure para Redis é controlado por meio de chaves de acesso. Essas chaves são usadas pelos clientes que se conectam ao seu cache e podem ser regeneradas a qualquer momento.

Não recomendamos que você crie senhas padrão no seu aplicativo. Em vez disso, armazene suas senhas no Azure Key Vault e use o Azure AD para recuperá-las.

- [Como regenerar as chaves de acesso do Cache do Azure para Redis](./cache-configure.md#settings)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas.

Além disso, para ajudá-lo a controlar contas administrativas dedicadas, você pode seguir as recomendações da Central de Segurança do Azure ou as políticas internas do Azure, como:

- Deve haver mais de um proprietário atribuído à sua assinatura

- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura

- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

Para saber mais, consulte as referências a seguir:

- [Como usar a Central de Segurança do Azure para monitorar a identidade e o acesso (versão prévia)](../security-center/security-center-identity-access.md)

- [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Usar o SSO (logon único) do Azure Active Directory

**Diretrizes**: o Cache do Azure para Redis usa chaves de acesso para autenticar usuários e não dá suporte ao SSO (logon único) no nível do plano de dados. O acesso ao painel de controle do Cache do Azure para Redis está disponível por meio da API REST e dá suporte ao SSO. Para autenticação, defina o cabeçalho de autorização para suas solicitações como um Token Web JSON obtido no Azure AD (Azure Active Directory).

- [Noções básicas sobre a API REST do Cache do Azure para Redis](/rest/api/redis/)

- [Noções básicas sobre o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: habilite a autenticação multifator do Azure AD (Azure Active Directory) e siga as recomendações do Gerenciamento de Identidades e Acesso da Central de Segurança do Azure.

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: use as PAWs (estações de trabalho com acesso privilegiado) com a autenticação multifator configurada para fazer logon nos recursos do Azure e configurá-los. 

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrar atividades suspeitas em contas administrativas e alertar sobre elas

**Diretrizes**: use o PIM (Privileged Identity Management) do Azure AD (Azure Active Directory) para geração de logs e alertas quando atividades suspeitas ou não seguras ocorrerem no ambiente.

Além disso, use as detecções de risco do Azure Active Directory para ver alertas e relatórios sobre o comportamento do usuário suspeito.

- [Como implantar o Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Entenda as detecções de risco do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: configure localizações nomeadas no acesso condicional do Azure AD (Azure Active Directory) para permitir o acesso somente em agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: use o Azure AD (Azure Active Directory) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

A autenticação do Azure AD não pode ser usada para acesso direto ao plano de dados do Cache do Azure para Redis. No entanto, as credenciais do Azure AD podem ser usadas para administração no nível do painel de controle (ou seja, o portal do Azure) a fim de controlar as chaves de acesso do Cache do Azure para Redis.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure AD (Azure Active Directory) fornece logs para ajudar você na descoberta de contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência associações a um grupo, acesso aos aplicativos empresariais e atribuições de função. O acesso de usuários pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo.

- [Noções básicas sobre os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorar as tentativas de acessar credenciais desativadas

**Diretrizes**: você tem acesso às fontes do log de eventos de risco, atividades de entrada do Azure AD (Azure Active Directory) e auditoria, que permitem a integração ao Azure Sentinel ou a um SIEM de terceiros.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando os logs de auditoria e de entrada para um workspace do Log Analytics. Você pode configurar os alertas de log desejados no Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alertar sobre desvios de comportamento de entrada na conta

**Diretrizes**: para o desvio do comportamento de logon da conta no painel de controle, use os recursos de proteção de identidade e detecção de risco do Azure AD (Azure Active Directory) para configurar respostas automatizadas e detectar ações suspeitas relacionadas às identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: Implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. As instâncias do Cache do Azure para Redis devem ser separadas por rede virtual/sub-rede e marcadas adequadamente. Opcionalmente, use o firewall do Cache do Azure para Redis para definir regras de maneira que somente as conexões de cliente em intervalos de endereços IP especificados possam se conectar ao cache.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como implantar o Cache do Azure para Redis em uma VNet](cache-how-to-premium-vnet.md)

- [Como configurar regras de firewall do Cache do Azure para Redis](./cache-configure.md#firewall)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: ainda não disponíveis. Os recursos de identificação, classificação e prevenção contra perda de dados ainda não estão disponíveis no Cache do Azure para Redis.

A Microsoft gerencia a infraestrutura subjacente do Cache do Azure para Redis e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: por padrão, o Cache do Azure para Redis exige comunicações criptografadas por TLS. No momento, há suporte para o TLS versões 1.0, 1.1 e 1.2. No entanto, o TLS 1.0 e 1.1 estão em um caminho para a substituição em todo o setor. Portanto, use o TLS 1.2, se possível. Se a biblioteca de clientes ou a ferramenta não der suporte ao TLS, a habilitação de conexões não criptografadas poderá ser feita por meio das APIs de gerenciamento ou do portal do Azure. Nos casos em que as conexões criptografadas não são possíveis, é recomendado colocar o cache e o aplicativo cliente em uma rede virtual.

- [Noções básicas sobre a criptografia em trânsito do Cache do Azure para Redis](cache-best-practices.md)

- [Noções básicas sobre as portas necessárias usadas em cenários de cache de VNet](./cache-how-to-premium-vnet.md#outbound-port-requirements)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: o [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão e é a base para as [recomendações da Central de Segurança](/azure/security-center/security-center-recommendations). As definições do Azure Policy relacionadas a esse controle são habilitadas automaticamente pela Central de Segurança. Os alertas relacionados a esse controle podem precisar de um plano do [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições internas do Azure Policy – Microsoft.Cache**:

[!INCLUDE [Resource Policy for Microsoft.Cache 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.cache-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação, classificação e prevenção contra perda de dados ainda não estão disponíveis no Cache do Azure para Redis. Marque as instâncias que contêm informações confidenciais como tais e implemente uma solução de terceiros, se necessário, para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Usar o controle de acesso baseado em função para controlar o acesso a recursos

**Diretrizes**: use o RBAC do Azure (controle de acesso baseado em função do Azure) para controlar o acesso ao painel de controle do Cache do Azure para Redis (ou seja, o portal do Azure).

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: o Cache do Azure para Redis armazena dados do cliente na memória e, embora a memória seja fortemente protegida por muitos controles implementados pela Microsoft, ela não é criptografada por padrão. Se exigido pela sua organização, criptografe o conteúdo antes de armazená-lo no Cache do Azure para Redis.

Se você estiver usando o recurso "Persistência de Dados do Redis" do Cache do Azure para Redis, os dados serão enviados para uma conta do Armazenamento do Azure da qual você seja proprietário e administrador. Configure a persistência na folha "Novo Cache do Azure para Redis" durante a criação do cache e no menu Recurso dos caches Premium existentes.

Os dados do Armazenamento do Azure são criptografados e descriptografados de maneira transparente com a criptografia AES de 256 bits, uma das codificações de bloco mais fortes disponíveis, além de estar em conformidade com o FIPS 140-2. A criptografia do Armazenamento do Azure não pode ser desabilitada. Você pode contar com as chaves gerenciadas pela Microsoft para a criptografia da sua conta de armazenamento ou gerenciar a criptografia com chaves próprias.

- [Como configurar a persistência no Cache do Azure para Redis](cache-how-to-premium-persistence.md)

- [Noções básicas sobre a criptografia de contas do Armazenamento do Azure](../storage/common/storage-service-encryption.md)

- [Noções básicas sobre a proteção de dados do cliente do Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: use o Azure Monitor com o log de atividades do Azure para criar alertas para quando ocorrerem alterações nas instâncias de produção do Cache do Azure para Redis e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, confira [Azure Security Benchmark: gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretrizes**: siga as recomendações da Central de Segurança do Azure para proteger suas instâncias do Cache do Azure para Redis e os recursos relacionados.

A Microsoft realiza o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte ao Cache do Azure para Redis.

- [Noções básicas sobre as recomendações da Central de Segurança do Azure](../security-center/recommendations-reference.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, confira [Azure Security Benchmark: gerenciamento de inventário e ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usar uma solução de descoberta de ativos automatizada

**Diretrizes**: Use o Azure Resource Graph para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos etc.) em suas assinaturas. Configure permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretrizes**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, sempre que apropriado, para organizar e acompanhar as instâncias do Cache do Azure para Redis e os recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Além disso, use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Para saber mais, consulte as referências a seguir:

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas de recurso](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorar recursos do Azure não aprovados

**Diretrizes**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Além disso, use o Azure Resource Graph para consultar por e descobrir recursos nas suas assinaturas.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Para saber mais, consulte as referências a seguir:

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade de interação dos usuários com o Azure Resource Manager

**Diretriz**: Configure o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”.

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, confira [Azure Security Benchmark: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para suas instâncias do Cache do Azure para Redis com o Azure Policy. Use aliases do Azure Policy no namespace "Microsoft.Cache" para criar políticas personalizadas a fim de auditar ou impor a configuração das instâncias do Cache do Azure para Redis. Você também usar as definições de política integradas relacionadas às suas instâncias do Cache do Azure para Redis, como:

- Apenas conexões seguras com o Cache Redis devem ser habilitadas

Para saber mais, consulte as referências a seguir:

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretriz**: use o Azure Policy [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Diretrizes**: se você estiver usando definições personalizadas do Azure Policy ou modelos do Azure Resource Manager para suas instâncias do Cache do Azure para Redis e os recursos relacionados, use o Azure Repos para armazenar e gerenciar o código com segurança.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentação do Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implantar ferramentas de gerenciamento de configuração para recursos do Azure

**Diretrizes**: use aliases do Azure Policy no namespace "Microsoft.Cache" para criar políticas personalizadas para alertar, auditar ou impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: use aliases do Azure Policy no namespace "Microsoft.Cache" para criar políticas personalizadas para alertar, auditar ou impor configurações do sistema. Use os efeitos [auditar], [negar] e [implantar se não existir] do Azure Policy para impor automaticamente as configurações para as instâncias do Cache do Azure para Redis e os recursos relacionados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: para máquinas virtuais do Azure ou aplicativos Web em execução no Serviço de Aplicativo do Azure usados para acessar suas instâncias do Cache do Azure para Redis, use a Identidade de Serviço Gerenciado com o Azure Key Vault para simplificar e proteger o gerenciamento de segredos do Cache do Azure para Redis. Verifique se a exclusão reversível do Key Vault está habilitada.

- [Como fazer a integração às Identidades Gerenciadas do Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um cofre de chaves](../key-vault/general/quick-create-portal.md)

- [Como se autenticar no Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerenciar identidades de maneira segura e automática

**Diretrizes**: para máquinas virtuais do Azure ou aplicativos Web em execução no Serviço de Aplicativo do Azure usados para acessar suas instâncias do Cache do Azure para Redis, use a Identidade de Serviço Gerenciado com o Azure Key Vault para simplificar e proteger o gerenciamento de segredos do Cache do Azure para Redis. Verifique se a exclusão temporária do cofre de chaves está habilitada.

Use Identidades Gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD (Azure Active Directory). As identidades gerenciadas permitem que você se autentique em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Azure Key Vault, sem ter credenciais no código.

- [Como configurar identidades gerenciadas](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Como fazer a integração às Identidades Gerenciadas do Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, confira [Azure Security Benchmark: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Cache do Azure para Redis), mas não é executado no conteúdo do cliente.

Verifique previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de Blobs, Banco de Dados do Azure para PostgreSQL, etc. A Microsoft não consegue acessar seus dados nessas instâncias.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, confira [Azure Security Benchmark: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automatizados regulares

**Diretrizes**: habilite a persistência do Redis. A persistência do Redis permite persistir os dados armazenados no Redis. Você também pode tirar instantâneos e fazer backup dos dados, que podem ser carregados em caso de falha de hardware. Essa é uma enorme vantagem em relação às camadas Básica ou Standard, em que todos os dados são armazenados na memória e pode haver uma possível perda de dados em caso de falha quando os nós do Cache estiverem inativos.

Você também pode usar a Exportação do Cache do Azure para Redis. A exportação permite exportar os dados armazenados no Cache do Azure para Redis para arquivos RDB compatíveis com Redis. É possível usar esse recurso para mover dados de uma instância do Cache do Azure para Redis para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na máquina virtual que hospeda a instância do servidor do Cache do Azure para Redis. Esse arquivo é carregado na conta de armazenamento designada. Após a operação de exportação ser concluída com um status de êxito ou de falha, o arquivo temporário é excluído.

- [Como habilitar a persistência do Redis](cache-how-to-premium-persistence.md)

- [Como usar a Exportação do Cache do Azure para Redis](cache-how-to-import-export-data.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Fazer backups completos do sistema e backups de todas as chaves gerenciadas pelo cliente

**Diretrizes**: habilite a persistência do Redis. A persistência do Redis permite persistir os dados armazenados no Redis. Você também pode tirar instantâneos e fazer backup dos dados, que podem ser carregados em caso de falha de hardware. Essa é uma enorme vantagem em relação às camadas Básica ou Standard, em que todos os dados são armazenados na memória e pode haver uma possível perda de dados em caso de falha quando os nós do Cache estiverem inativos.

Você também pode usar a Exportação do Cache do Azure para Redis. A exportação permite exportar os dados armazenados no Cache do Azure para Redis para arquivos RDB compatíveis com Redis. É possível usar esse recurso para mover dados de uma instância do Cache do Azure para Redis para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na máquina virtual que hospeda a instância do servidor do Cache do Azure para Redis. Esse arquivo é carregado na conta de armazenamento designada. Após a operação de exportação ser concluída com um status de êxito ou de falha, o arquivo temporário é excluído.

Se estiver usando o Azure Key Vault para armazenar credenciais para suas instâncias do Cache do Azure para Redis, garanta backups regulares automatizados das suas chaves.

- [Como habilitar a persistência do Redis](cache-how-to-premium-persistence.md)

- [Como usar a Exportação do Cache do Azure para Redis](cache-how-to-import-export-data.md)

- [Como fazer backup de chaves do Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, inclusive os de chaves gerenciadas pelo cliente

**Diretrizes**: use a Importação do Cache do Azure para Redis. A importação pode ser usada para trazer arquivos RDB compatíveis com o Redis de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, incluindo o Redis em execução no Linux e no Windows ou qualquer provedor de nuvem como a Amazon Web Services e outros. Importar os dados é uma maneira fácil de criar um cache com dados previamente populados. Durante o processo de importação, o Cache do Azure para Redis carrega os arquivos RDB do armazenamento do Azure na memória e insere as chaves no cache.

Teste periodicamente a restauração de dados dos seus segredos do Azure Key Vault.

- [Como usar a Importação do Cache do Azure para Redis](cache-how-to-import-export-data.md)

- [Como restaurar segredos do Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultsecret?preserve-view=true&view=azps-4.8.0)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança do Computador do NIST para ajudar na criação do plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade é baseada no grau de confiança da Central de Segurança nas conclusões ou na análise usada para emitir o alerta, bem como no nível de confiança em uma possível ação mal-intencionada por trás da atividade que gerou o alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomenclatura para identificar e categorizar claramente os recursos do Azure.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Veja a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal.  Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de "Aplicativos Lógicos" em alertas de segurança e recomendações.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, confira [Azure Security Benchmark: testes de penetração e exercícios de Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes de penetração regulares dos recursos do Azure e garantir a correção de todas as conclusões de segurança críticas

**Diretrizes**: siga as Regras de Participação no Teste de Penetração do Microsoft Cloud para verificar se os testes de penetração não estão violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft. 

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
