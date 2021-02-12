---
title: Linha de base de segurança do Azure para o Key Vault
description: Linha de base de segurança do Azure para o Key Vault
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad47ac7d51de6ab497d7061961c14b5324fab931
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202446"
---
# <a name="azure-security-baseline-for-key-vault"></a>Linha de base de segurança do Azure para o Key Vault

A linha de base de segurança do Azure para Key Vault contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de segurança: Segurança de rede](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua Rede Virtual

**Diretrizes**: integre Azure Key Vault com o link privado do Azure. 

O serviço de vínculo privado do Azure permite acessar os serviços do Azure (por exemplo, Azure Key Vault) e os serviços hospedados de cliente/parceiro do Azure por meio de um ponto de extremidade privado em sua rede virtual.

Um Ponto de Extremidade Privado do Azure é um adaptador de rede que conecta você de maneira privada e segura a um serviço com tecnologia do Link Privado do Azure. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.

Como integrar o Key Vault com o link privado do Azure:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: monitorar e registrar a configuração e o tráfego de VNets, sub-redes e NICs

**Orientação**: Use a central de segurança do Azure e siga as recomendações de proteção de rede para ajudar a proteger seus recursos configurados Key Vault no Azure. 

Para obter mais informações sobre a segurança de rede fornecida pela central de segurança do Azure: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Rejeitar comunicações com endereços IP maliciosos conhecidos

**Orientação**: habilitar a proteção contra DDoS do Azure Standard nas redes virtuais do Azure associadas às suas instâncias de Key Vault para proteção contra ataques de negação de serviço distribuídos. Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

 
Gerenciar a proteção contra DDoS do Azure Standard usando o portal do Azure: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Detecção de ameaças para a camada de serviço do Azure na central de segurança do Azure: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: registrar pacotes de rede e logs de fluxo

**Diretrizes**: Azure Key Vault não usa NSG (grupos de segurança de rede) e logs de fluxo para Azure Key Vault não são capturados. Em vez disso, use o link privado do Azure para proteger suas instâncias de Azure Key Vault e habilitar as configurações de diagnóstico para registrar métricas e eventos de auditoria.

Integre o Key Vault com o link privado do Azure:

https://docs.microsoft.com/azure/key-vault/private-link-service

Log de Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: implantar sistemas de detecção/prevenção de intrusões (IDS/IPS) baseados em rede

**Orientação**: esse requisito pode ser atendido Configurando a ATP (proteção avançada contra ameaças) para Azure Key Vault. ATP fornece uma camada adicional de inteligência de segurança. Essa ferramenta detecta tentativas potencialmente perigosas de acessar ou explorar contas de Azure Key Vault.

Quando a central de segurança do Azure detecta a atividade anômala, ela exibe alertas. Ele também envia emails ao administrador da assinatura com detalhes da atividade suspeita e recomendações sobre como investigar e corrigir as ameaças identificadas.

Configure a proteção avançada contra ameaças para o Azure Key Vault:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Orientação**: para obter recursos que precisam de acesso às suas instâncias de Azure Key Vault, use as marcas de serviço do Azure para a Azure Key Vault definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Visão geral das marcas de serviço do Azure: https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede associados às suas instâncias de Azure Key Vault com Azure Policy. Use aliases de Azure Policy nos namespaces "Microsoft. keyvault" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de suas instâncias de Azure Key Vault. Você também pode fazer uso de definições de política internas relacionadas a Azure Key Vault, como:

O Key Vault deve usar um ponto de extremidade de serviço de rede virtual

Tutorial: criar e gerenciar políticas para impor a conformidade:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Exemplos de Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Início rápido: definir e atribuir um plano gráfico no Portal:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: use marcas para recursos relacionados à segurança de rede e ao fluxo de tráfego para suas instâncias de Azure Key Vault para fornecer metadados e organização lógica.

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

Use marcas para organizar os recursos do Azure:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados às suas instâncias de Azure Key Vault. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

Exibir e recuperar eventos do log de atividades do Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Crie, exiba e gerencie alertas do log de atividades usando Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de segurança: registro em log e monitoramento](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Orientação**: não aplicável; A Microsoft mantém a fonte de tempo usada para recursos do Azure, como Azure Key Vault, para carimbos de data/hora nos logs.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: ingerir logs por meio de Azure monitor para agregar dados de segurança gerados pelo Azure Key Vault. Em Azure Monitor, use o espaço de trabalho do Azure Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros. 

Log de Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Início rápido: como integrar o Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: habilite as configurações de diagnóstico em suas instâncias de Azure Key Vault para acesso a logs de auditoria, segurança e diagnóstico. Logs de atividade, que estão automaticamente disponíveis, incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis.

Log de Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: em Azure monitor, para o espaço de trabalho log Analytics que está sendo usado para manter seus logs de Azure Key Vault, defina o período de retenção de acordo com os regulamentos de conformidade de sua organização. Use contas de Armazenamento do Microsoft Azure para armazenamentos de longo prazo/arquivamento.

Alterar o período de retenção de dados: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Diretrizes**: analise e monitore os logs de comportamento anormal e Examine regularmente os resultados de seus recursos protegidos por Azure Key Vault. Use o espaço de trabalho Log Analytics do Azure Monitor para examinar os logs e executar consultas nos dados de log. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros. 

Início rápido: Sentinela do Azure integrado:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Introdução ao Log Analytics no Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Introdução às consultas de log no Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: habilitar alertas para atividade anormal

**Diretrizes**: na central de segurança do Azure, habilite a ATP (proteção avançada contra ameaças) para Key Vault. Habilite as configurações de diagnóstico no Azure Key Vault e envie logs para um espaço de trabalho Log Analytics. Integre seu workspace do Log Analytics ao Azure Sentinel, pois ele fornece uma solução SOAR (resposta automatizada de orquestração de segurança). Assim os guias estratégicos (soluções automatizadas) podem ser criados e usados para corrigir problemas de segurança.

Início rápido: Sentinela do Azure integrado:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Gerenciar e responder a alertas de segurança na central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Responder a eventos com Azure Monitor alertas:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Orientação**: não aplicável; Azure Key Vault não processa nem produz logs relacionados a anti-malware.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilitar o registro em log de consultas DNS

**Orientação**: não aplicável; Azure Key Vault não processa nem produz logs relacionados ao DNS.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilitar o registro em log de auditoria de linha de comando

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [Controle de segurança: Identidade e controle de acesso](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: Mantenha um inventário de seus aplicativos registrados Azure Active Directory, bem como qualquer conta de usuário que tenha acesso a suas chaves de Azure Key Vault, segredos e certificados. Você pode usar o portal do Azure ou o PowerShell para consultar e reconciliar o acesso Key Vault. Para exibir o acesso no PowerShell, use o seguinte comando:

(Get-AzResource-ResourceId [KeyVaultResourceID]). Properties. AccessPolicies

Registrando um aplicativo com o Azure Active Directory:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Proteger o acesso a um cofre de chaves:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Orientação**: não aplicável; Azure Key Vault não tem o conceito de senhas padrão, pois a autenticação é fornecida pelo Active Directory e protegida com o controle de acesso baseado em função do Azure (RBAC do Azure).


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Diretrizes**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas que têm acesso às suas instâncias de Azure Key Vault. Use o gerenciamento de acesso e identidade da central de segurança do Azure (atualmente em versão prévia) para monitorar o número de contas administrativas ativas.

Monitorar identidade e acesso (visualização):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Orientação**: Use uma entidade de serviço do Azure em conjunto com AppID, tenantid e ClientSecret para autenticar o aplicativo diretamente e recuperar o token que será usado para acessar seus segredos de Azure Key Vault.

Autenticação serviço a serviço para Azure Key Vault usando .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: habilite Azure Active Directory autenticação multifator e siga as recomendações do gerenciamento de acesso e identidade da central de segurança do Azure (atualmente em versão prévia) para ajudar a proteger seus recursos habilitados para o Hub de eventos.

Planejando uma implantação da autenticação multifator do Azure AD baseada em nuvem:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Monitorar identidade e acesso (visualização):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use uma Paw (estação de trabalho com acesso privilegiado) com o MFA (autenticação multifator) do Azure ad configurada para fazer logon e configurar Key Vault recursos habilitados. 

Estações de trabalho com acesso privilegiado: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/ 

Planejando uma implantação da autenticação multifator do Azure AD baseada em nuvem: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: registrar e alertar sobre atividades suspeitas em contas administrativas

**Diretrizes**: Use o PIM (Azure Active Directory do Azure ad Privileged Identity Management) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorrer no ambiente. Use as detecções de risco do Azure AD para exibir alertas e relatórios sobre o comportamento do usuário arriscado. Para logs adicionais, envie alertas de detecção de riscos da central de segurança do Azure para Azure Monitor e configure alertas/notificações personalizados usando grupos de ação.

Habilite a ATP (proteção avançada contra ameaças) para Azure Key Vault gerar alertas para atividades suspeitas.

Azure AD Privileged Identity Management de implantação (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Configurar a proteção avançada contra ameaças para Azure Key Vault (versão prévia): https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Alertas para Azure Key Vault (versão prévia): https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Azure Active Directory de detecções de risco: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Crie e gerencie grupos de ações no portal do Azure: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: Configure a condição de local de uma política de acesso condicional e gerencie seus locais nomeados. Com os locais nomeados, você pode criar agrupamentos lógicos de intervalos de endereços IP ou países e regiões. Você pode restringir o acesso a recursos confidenciais, como seus segredos de Key Vault, aos seus locais nomeados configurados.

Qual é a condição de local em Azure Active Directory acesso condicional?: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central para recursos do Azure, como key Vault. Isso permite que o controle de acesso baseado em função do Azure (RBAC do Azure) administra recursos confidenciais.

 

Início rápido: criar um novo locatário no Azure Active Directory:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: examine os logs do Azure Active Directory (Azure AD) para ajudar a descobrir contas obsoletas com Azure Key Vault funções administrativas. Além disso, use as revisões de acesso do Azure AD para gerenciar com eficiência as associações de grupo, o acesso a aplicativos corporativos que podem ser usados para acessar Azure Key Vault e atribuições de função. O acesso do usuário deve ser revisado regularmente, como a cada 90 dias, para garantir que apenas os usuários certos tenham acesso contínuo.

Relatórios de Azure Active Directory e documentação de monitoramento:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

O que são as revisões de acesso do Azure AD?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: monitorar tentativas de acessar contas desativadas

**Diretrizes**: habilite as configurações de diagnóstico para Azure Key Vault e Azure Active Directory, enviando todos os logs para um espaço de trabalho log Analytics. Configure os alertas desejados (como tentativas de acessar segredos desabilitados) em Log Analytics.

Integre logs do Azure AD a logs de Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Migrando da antiga solução de Key Vault: https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Diretrizes**: Use a proteção de identidade e os recursos de detecção de risco do Azure Active Directory para configurar respostas automatizadas para ações suspeitas detectadas relacionadas aos seus Azure Key Vault recursos protegidos. Você deve habilitar respostas automatizadas por meio do Azure Sentinel para implementar as respostas de segurança da sua organização. 

Relatório de entradas arriscadas no portal de Azure Active Directory: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Como: configurar e habilitar políticas de risco: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como carregar o Azure Sentinel:  https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: não aplicável; Não há suporte para Sistema de Proteção de Dados do Cliente para Azure Key Vault.

Serviços e cenários com suporte em disponibilidade geral: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [Controle de segurança: proteção de dados](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: manter um inventário de informações confidenciais

**Diretrizes**: use marcas para auxiliar no rastreamento de recursos do Azure que armazenam ou processam informações confidenciais sobre Azure Key Vault recursos habilitados. 

Use marcas para organizar os recursos do Azure: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Orientação**: você pode proteger o acesso a Azure Key Vault fazendo uso de pontos de extremidade de serviço de rede virtual configurados para restringir o acesso a sub-redes específicas.

Depois que as regras de firewall estiverem em vigor, você só poderá executar Azure Key Vault operações do plano de dados quando sua solicitação for originada de sub-redes permitidas ou intervalos de endereços IP. Isso também se aplica a Azure Key Vault acesso no portal do Azure. Embora você possa navegar até um cofre de chaves do portal do Azure, talvez não seja possível listar chaves, segredos ou certificados se o computador cliente não estiver na lista de permissões. Isso também afeta o seletor de Azure Key Vault e outros serviços do Azure. Você poderá ver listas de cofres de chaves, mas não as chaves de lista, se as regras de firewall impedirem que o computador cliente faça isso.

Configure Azure Key Vault firewalls e redes virtuais: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Pontos de extremidade de serviço de rede virtual para Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: todos os dados armazenados em Azure Key Vault são considerados confidenciais. Use Azure Key Vault controles de acesso do plano de dados para controlar o acesso aos segredos Azure Key Vault. Você também pode usar o firewall interno do Key Vault para controlar o acesso na camada de rede. Para monitorar o acesso a Azure Key Vault, habilite Key Vault configurações de diagnóstico e envie logs para uma conta de armazenamento do Azure ou Log Analytics espaço de trabalho.

Proteger o acesso a um cofre de chaves: https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Configure Azure Key Vault firewalls e redes virtuais: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Log de Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Orientação**: todo o tráfego para Azure Key Vault para autenticação, gerenciamento e acesso ao plano de dados, é criptografado e passa por https: porta 443. (No entanto, ocasionalmente haverá tráfego HTTP [porta 80] para CRL.) Azure Key Vault continua a permitir que os dados TLS 1,1 e TLS 1,0 sejam ingeridos. Os dados podem ser restritos ao TLS 1,2 por meio da configuração no lado do cliente.



Acesse Azure Key Vault por trás de um firewall: https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: não aplicável; todos os dados dentro de Azure Key Vault (segredos, chaves e certificados) são considerados confidenciais.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: proteger o acesso ao plano de dados e gerenciamento de suas instâncias de Azure Key Vault.

Proteger o acesso a um cofre de chaves:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretrizes**: a Microsoft gerencia a infraestrutura subjacente para Azure Key Vault e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

O que é o Azure Key Vault?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Proteção de dados do cliente do Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretrizes**: todos os objetos gerenciados (chave, certificados e segredos) são criptografados em repouso no Azure Key Vault.

Documentação de suporte:

- [Modelo de criptografia e tabela de gerenciamento de chaves](../../security/fundamentals/encryption-atrest.md)


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use a solução de análise de Azure Key Vault no Azure monitor para examinar Azure Key Vault logs de eventos de auditoria.

Solução de análise de Azure Key Vault no Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: gerenciamento de vulnerabilidades](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretrizes**: a Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte ao Azure Key Vault.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretrizes**: N/A; A Microsoft executa o gerenciamento de patches nos sistemas subjacentes que dão suporte a Key Vault.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implantar uma solução automatizada de gerenciamento de patch de software de terceiros

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Diretrizes**: a Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte ao key Vault.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: Use as classificações de risco padrão (Pontuação segura) fornecidas pela central de segurança do Azure.

Melhorar sua pontuação segura na central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de segurança: inventário e gerenciamento de ativos](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: usar a descoberta de ativos do Azure

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (incluindo instâncias de Azure Key Vault) em sua assinatura. Verifique se você tem permissões (leitura) apropriadas em seu locatário e é capaz de enumerar todas as assinaturas do Azure, bem como os recursos nas suas assinaturas.

Início rápido: executar sua primeira consulta de grafo de recursos usando o Gerenciador de grafo de recursos do Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Obtenha as assinaturas que a conta atual pode acessar.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

O que é o RBAC do Azure (controle de acesso baseado em função do Azure)?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretrizes**: aplique marcas para Azure Key Vault recursos que fornecem metadados para organizá-los logicamente em uma taxonomia.

Como criar e usar marcas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar Azure Key Vault instâncias e recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Crie uma assinatura adicional do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Crie grupos de gerenciamento para organização e gerenciamento de recursos:

https://docs.microsoft.com/azure/governance/management-groups/create

Use marcas para organizar os recursos do Azure: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: manter um inventário de recursos e títulos de software aprovados do Azure

**Diretrizes**: definir a lista de recursos aprovados do Azure e o software aprovado para seus recursos de computação

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Orientação**: Use as políticas do Azure para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política internas:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o Azure Resource Graph para consultar/descobrir recursos em sua(s) assinatura(s).

Tutorial: criar e gerenciar políticas para impor a conformidade: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Início rápido: executar sua primeira consulta de grafo de recursos usando o Gerenciador de grafo de recursos do Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: não aplicável; Essa recomendação destina-se ao Azure como um todo, bem como a recursos de computação.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Usar somente aplicativos aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Orientação**: Use as políticas do Azure para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política internas:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Tutorial: criar e gerenciar políticas para impor a conformidade: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Exemplos de Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: implementar lista de aplicativos aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: limitar a capacidade dos usuários de interagir com o AzureResources Manager por meio de scripts

**Orientação**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com Azure Resource Manager (ARM) configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure". Isso pode impedir a criação e alterações em recursos em um ambiente de alta segurança, como aqueles com configuração de Key Vault.

Gerenciar o acesso ao gerenciamento do Azure com acesso condicional:

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

*Para obter mais informações, consulte [Controle de segurança: configuração segura](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. keyvault" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias de Azure Key Vault. Você também pode usar definições de Azure Policy internas para Azure Key Vault como:

Os objetos do Key Vault devem ser recuperáveis

Implantar as configurações de diagnóstico do Key Vault no workspace do Log Analytics

Os logs de diagnóstico no Key Vault deve estar habilitados

O Key Vault deve usar um ponto de extremidade de serviço de rede virtual

Aplicar as Configurações de Diagnóstico do Key Vault no Hub de Eventos

Use as recomendações da central de segurança do Azure como uma linha de base de configuração segura para suas instâncias de Azure Key Vault.

Como exibir os aliases do Azure Policy disponíveis: 

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Tutorial: criar e gerenciar políticas para impor a conformidade:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Orientação**: Use Azure Policy [Deny] e [implantar se não existir] para impor configurações seguras em seus recursos habilitados para Azure Key Vault. 

Tutorial: criar e gerenciar políticas para impor a conformidade:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Entender Azure Policy efeitos: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Diretrizes**: se você estiver usando definições de Azure Policy personalizadas para seus recursos Azure Key Vault habilitados, use Azure Repos para armazenar e gerenciar com segurança seu código.

Como armazenar código no Azure DevOps:  

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Documentação do Azure Repos:  

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração do sistema

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. keyvault" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

Como configurar e gerenciar o Azure Policy: 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar o monitoramento automatizado de configuração para serviços do Azure

**Diretrizes**: Use a central de segurança do Azure para executar verificações de linha de base para seus recursos protegidos por Azure Key Vault 

  

Como corrigir recomendações na central de segurança do Azure: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: não aplicável; Esse parâmetro de comparação destina-se a recursos de computação.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: Use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos para seus aplicativos de nuvem. Verifique se Azure Key Vault exclusão reversível está habilitada.

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

**Diretrizes**: Use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos para seus aplicativos de nuvem. 

  

* [Como integrar com identidades gerenciadas do Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Como criar um Key Vault](quick-create-portal.md)

* [Como autenticar-se no Key Vault](authentication.md)

* [Como atribuir uma política de acesso de Key Vault](assign-access-policy-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.  
  
 Como configurar o verificador de credenciais: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte [Controle de segurança: defesa contra malwares](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usar software antimalware gerenciado centralmente

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação. A Microsoft lida com o anti-malware para a plataforma subjacente.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Azure Key Vault), no entanto, ele não é executado no conteúdo do cliente.

Examine previamente qualquer conteúdo que esteja sendo carregado ou enviado para recursos não computados do Azure, como Azure Key Vault. A Microsoft não pode acessar seus dados nessas instâncias.

Entenda o Microsoft antimalware para os serviços de nuvem do Azure e máquinas virtuais: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software antimalware e as assinaturas sejam atualizados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação. A Microsoft lida com o anti-malware para a plataforma subjacente.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de segurança: recuperação de dados](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantir backups automatizados regulares

**Orientação**: garanta backups automatizados regulares de seus Key Vault certificados, chaves, contas de armazenamento gerenciadas e segredos, com os seguintes comandos do PowerShell:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Opcionalmente, você pode armazenar seus backups de Key Vault no backup do Azure.

Como fazer backup de Key Vault certificados: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Como fazer backup de chaves do cofre de chaves: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Como fazer backup Key Vault contas de armazenamento gerenciadas: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Como fazer backup de Key Vault segredos: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Como habilitar o backup do Azure: https://docs.microsoft.com/azure/backup



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Realizar backups completos do sistema e fazer backup das chaves gerenciadas pelo cliente

**Diretrizes**: execute backups de seus Key Vault certificados, chaves, contas de armazenamento gerenciadas e segredos, com os seguintes comandos do PowerShell:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Opcionalmente, você pode armazenar seus backups de Key Vault no backup do Azure.

Como fazer backup de Key Vault certificados: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Como fazer backup de chaves do cofre de chaves: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Como fazer backup Key Vault contas de armazenamento gerenciadas: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Como fazer backup de Key Vault segredos: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Como habilitar o backup do Azure: https://docs.microsoft.com/azure/backup



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: execute periodicamente a restauração de dados de seus Key Vault certificados, chaves, contas de armazenamento gerenciadas e segredos, com os seguintes comandos do PowerShell:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Como restaurar Key Vault certificados:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Como restaurar chaves de Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Como restaurar Key Vault contas de armazenamento gerenciadas: https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Como restaurar Key Vault segredos: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: Verifique se a exclusão reversível está habilitada para Azure Key Vault. A exclusão reversível permite a recuperação de cofres de chaves excluídos e objetos de cofre, como chaves, segredos e certificados. 

Como usar a exclusão reversível do Azure Key Vault: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de segurança: resposta a incidentes](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente. Esses processos devem ter um foco na proteção de sistemas confidenciais, como os que usam segredos de Key Vault.

Como configurar automações de fluxo de trabalho na Central de Segurança do Azure:  

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Orientação sobre como criar seu próprio processo de resposta a incidentes de segurança:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center:    

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

O cliente também pode aproveitar o Guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu próprio plano de resposta a incidentes:  

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta. Além disso, marque claramente as assinaturas (por exemplo, produção, não Prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos do Azure, especialmente aqueles que processam dados confidenciais, como segredos de Azure Key Vault.


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger suas instâncias de Azure Key Vault e os recursos relacionados. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e capacidades:  

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal.  Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

Como definir o contato de segurança da Central de Segurança do Azure: 

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para recursos habilitados para Azure Key Vault. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua.  Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel. 

 

Como configurar a exportação contínua: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Como transmitir alertas para o Azure Sentinel: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações para proteger seus recursos protegidos por Azure Key Vault. 

 

Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos:  

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [Controle de segurança: testes de penetração e exercícios de Red Team](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: realizar testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as constatações de segurança críticas em 60 dias

**Orientação**: você não deve executar testes de caneta diretamente no serviço de Azure Key Vault, mas é incentivado a testar os recursos do Azure que estão usando Key Vault para garantir a segurança dos segredos.

Você precisará seguir as regras de envolvimento da Microsoft para garantir que seus testes de penetração não estejam em violação às políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de Red Team e testes de penetração de sites ao vivo em infraestrutura, serviços e aplicativos de nuvem gerenciados pela Microsoft, aqui:  

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Consulte o [Parâmetro de segurança do Azure](../../security/benchmarks/overview.md)
- Saiba mais sobre as [Linhas de base de segurança do Azure](../../security/benchmarks/security-baselines-overview.md)