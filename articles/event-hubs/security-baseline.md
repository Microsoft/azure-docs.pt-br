---
title: Linha de base de segurança do Azure para hubs de eventos
description: Linha de base de segurança do Azure para hubs de eventos
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c5c4aed7a5ee1726d7afb1a671fea1519acfa67c
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806080"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Linha de base de segurança do Azure para hubs de eventos

A linha de base de segurança do Azure para hubs de eventos contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de segurança: Segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua Rede Virtual

**Orientação**: a integração dos hubs de eventos com pontos de extremidade de serviço de rede virtual permite o acesso seguro a recursos de mensagens de cargas de trabalho, como máquinas virtuais associadas a redes virtuais, com o caminho de tráfego de rede protegido em ambas as extremidades.

Uma vez associado a pelo menos um ponto de extremidade de serviço de sub-rede de rede virtual, o respectivo namespace de hubs de eventos não aceita mais tráfego de qualquer lugar, mas de sub-redes autorizadas em redes virtuais. Da perspectiva da rede virtual, a associação do namespace de seus hubs de eventos a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de mensagens. 

Você também pode criar um ponto de extremidade privado, que é uma interface de rede que conecta você de forma privada e segura ao serviço de hubs de eventos do Azure usando o serviço de link privado do Azure. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. 

Você também pode proteger seu namespace de hubs de eventos do Azure usando firewalls. Os hubs de eventos do Azure oferecem suporte a controles de acesso baseados em IP para o suporte de firewall de entrada. Você pode definir regras de firewall usando o portal do Azure, Azure Resource Manager modelos ou por meio do CLI do Azure ou Azure PowerShell.

Como usar pontos de extremidade de serviço de rede virtual com hubs de eventos do Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Para obter mais informações, consulte integrar hubs de eventos do Azure com o link privado do Azure: https://docs.microsoft.com/azure/event-hubs/private-link-service .

Habilitar a integração de redes virtuais e firewalls no namespace de hubs de eventos: https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Como configurar regras de firewall IP para namespaces de hubs de eventos do Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: monitorar e registrar a configuração e o tráfego de VNets, sub-redes e NICs

**Diretrizes**: Use a central de segurança do Azure e siga as recomendações de proteção de rede para ajudar a proteger seus recursos de hubs de eventos no Azure. Se estiver usando máquinas virtuais do Azure para acessar seus hubs de eventos, habilite os logs de fluxo do NSG (grupo de segurança de rede) e envie logs para uma conta de armazenamento para auditoria de tráfego.

Como habilitar logs de fluxo de NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Noções básicas sobre segurança de rede fornecida pela central de segurança do Azure: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Rejeitar comunicações com endereços IP maliciosos conhecidos

**Orientação**: habilite a proteção contra DDoS Standard nas redes virtuais associadas aos seus hubs de eventos para proteger contra ataques de DDoS (negação de serviço distribuído). Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

Como configurar a proteção contra DDoS: [https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../ddos-protection/manage-ddos-protection.md)

Para obter mais informações sobre a inteligência de ameaças integrada da central de segurança do Azure: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: registrar pacotes de rede e logs de fluxo

**Orientação**: se você estiver usando máquinas virtuais do Azure para acessar seus hubs de eventos, habilite os logs de fluxo do NSG (grupo de segurança de rede) e envie logs para uma conta de armazenamento para auditoria de tráfego. Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

Se necessário para investigar a atividade anômala, habilite a captura de pacotes do observador de rede.

Como habilitar logs de fluxo de NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como habilitar e usar a Análise de Tráfego: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Como habilitar o observador de rede: https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: implantar sistemas de detecção/prevenção de intrusões (IDS/IPS) baseados em rede

**Diretrizes**: se estiver usando máquinas virtuais do Azure para acessar seus hubs de eventos, selecione uma oferta do Azure Marketplace que dá suporte à funcionalidade de IDS/IPS com recursos de inspeção de conteúdo. Se a detecção de intrusão e/ou prevenção baseada na inspeção de carga não for necessária para sua organização, você poderá usar o recurso de firewall interno dos hubs de eventos do Azure. Você pode limitar o acesso ao namespace de seus hubs de eventos para um intervalo limitado de endereços IP ou um endereço IP específico usando regras de firewall.

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Como adicionar uma regra de firewall em hubs de eventos para um endereço IP especificado:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitoramento da central de segurança do Azure**: ainda não disponível

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Orientação**: não aplicável, essa recomendação destina-se a aplicativos Web em execução em Azure app serviço ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede associados aos seus namespaces de hubs de eventos do Azure com o Azure Policy. Use aliases de Azure Policy nos namespaces "Microsoft. EventHub" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de seus namespaces de hubs de eventos. Você também pode fazer uso de definições de política internas relacionadas aos hubs de eventos do Azure, como:

- O Hub de eventos deve usar um ponto de extremidade de serviço de rede virtual.

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Política interna do Azure para namespace de hubs de eventos:  https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Amostras do Azure Policy para rede: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Como criar um Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: use marcas para redes virtuais e outros recursos relacionados à segurança de rede e ao fluxo de tráfego associados aos seus hubs de eventos.

Como criar e usar marcas: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados aos hubs de eventos do Azure. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

Como exibir e recuperar eventos do Log de Atividades do Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

como criar alertas no Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de segurança: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Orientação**: não aplicável; A Microsoft mantém a fonte de tempo usada para recursos do Azure, como hubs de eventos do Azure, para carimbos de data/hora nos logs.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Diretrizes**: em Azure monitor, configure logs relacionados aos hubs de eventos no log de atividades e nas configurações de diagnóstico do hub de eventos para enviar logs em um espaço de trabalho do log Analytics para serem consultados ou em uma conta de armazenamento para armazenamento de arquivamento de longo prazo.

Como definir configurações de diagnóstico para hubs de eventos do Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Noções básicas sobre o log de atividades do Azure: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: Habilitar configurações de diagnóstico para seu namespace de hubs de eventos do Azure. Há três categorias de configurações de diagnóstico para hubs de eventos do Azure: logs de arquivamento, logs operacionais e logs de dimensionamento automático. Habilite logs operacionais para capturar informações sobre o que está acontecendo durante operações de hubs de eventos, especificamente, o tipo de operação, incluindo a criação do hub de eventos, os recursos usados e o status da operação.

Além disso, você pode habilitar as configurações de diagnóstico do log de atividades do Azure e enviá-las para uma conta de armazenamento do Azure, um hub de eventos ou um espaço de trabalho Log Analytics. Os logs de atividade fornecem informações sobre as operações que foram realizadas em seus hubs de eventos do Azure e outros recursos. Usando os logs de atividade, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) realizada em seus namespaces de hubs de eventos do Azure.

Como habilitar configurações de diagnóstico para hubs de eventos do Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Como habilitar as configurações de diagnóstico para o log de atividades do Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: em Azure monitor, defina seu período de retenção de espaço de trabalho de log Analytics de acordo com os regulamentos de conformidade da sua organização para capturar e examinar incidentes relacionados ao Hub de eventos.

Como definir parâmetros de retenção de log para Log Analytics espaços de trabalho: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Diretrizes**: analise e monitore os logs de comportamento anormal e Examine regularmente os resultados relacionados aos seus hubs de eventos. Use o Log Analytics do Azure Monitor para revisar os logs e realizar consultas nos dados de log. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.
 

Para obter mais informações sobre o espaço de trabalho Log Analytics: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como realizar consultas personalizadas no Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Como integrar o Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: habilitar alertas para atividade anormal

**Diretrizes**: em Azure monitor, configure logs relacionados aos hubs de eventos do Azure no log de atividades e configurações de diagnóstico de hubs de eventos para enviar logs para um espaço de trabalho do log Analytics para serem consultados ou em uma conta de armazenamento para armazenamento de arquivamento de longo prazo. Use Log Analytics espaço de trabalho para criar alertas para atividade anômala encontrada em eventos e logs de segurança.

Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel. 

Entenda o log de atividades do Azure: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Como definir configurações de diagnóstico para hubs de eventos do Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Como alertar sobre Log Analytics dados de log do espaço de trabalho: https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Como integrar o Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da central de segurança do Azure**: ainda não disponível

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Orientação**: não aplicável; O Hub de eventos não processa o log antimalware.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilitar o registro em log de consultas DNS

**Orientação**: não aplicável; Os hubs de eventos não processam nem produzem logs relacionados ao DNS.

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

Como obter uma função de diretório no Azure AD com o PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole

Como obter membros de uma função de diretório no Azure AD com o PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Orientação**: controle o acesso do plano aos hubs de eventos é controlado por meio do Azure Active Directory (AD). O Azure AD não tem o conceito de senhas padrão.

O acesso do plano de dados aos hubs de eventos é controlado por meio do Azure AD com identidades gerenciadas ou Registros de aplicativo, bem como assinaturas de acesso compartilhado. As assinaturas de acesso compartilhado são usadas pelos clientes que se conectam aos seus hubs de eventos e podem ser regeneradas a qualquer momento.

Entenda as assinaturas de acesso compartilhado para os hubs de eventos: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

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

**Diretrizes**: Microsoft Azure fornece gerenciamento de controle de acesso integrado para recursos e aplicativos com base em Azure Active Directory (AD). Uma vantagem importante de usar o Azure AD com os hubs de eventos do Azure é que você não precisa mais armazenar suas credenciais no código. Em vez disso, você pode solicitar um token de acesso OAuth 2,0 da plataforma de identidade da Microsoft. O nome do recurso para solicitar um token é https: \/ /Eventhubs.Azure.net/. O Azure AD autentica a entidade de segurança (um usuário, grupo ou entidade de serviço) que executa o aplicativo. Se a autenticação for bem sucedido, o Azure AD retornará um token de acesso para o aplicativo e o aplicativo poderá usar o token de acesso para autorizar a solicitação aos recursos dos hubs de eventos do Azure.

Como autenticar um aplicativo com o Azure AD para acessar recursos de hubs de eventos: https://docs.microsoft.com/azure/event-hubs/authenticate-application

Entendendo o SSO com o Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: habilite o Azure Active Directory MFA (autenticação multifator) e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure para ajudar a proteger seus recursos habilitados para o Hub de eventos.

Como habilitar a MFA no Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorar identidade e acesso na Central de Segurança do Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use Paw (estações de trabalho com acesso privilegiado) com a MFA (autenticação multifator) configurada para fazer logon e configurar recursos habilitados para o Hub de eventos.

Saiba mais sobre estações de trabalho com acesso privilegiado: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Como habilitar a MFA no Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registrar e alertar sobre atividades suspeitas em contas administrativas

**Diretriz**: Use o Privileged Identity Management (PIM) do Azure Active Directory (AD) para geração de logs e alertas quando atividades suspeitas ou inseguras ocorrem no ambiente. Use as detecções de risco do Azure AD para exibir alertas e relatórios sobre o comportamento do usuário arriscado. Para logs adicionais, envie alertas de detecção de riscos da central de segurança do Azure para Azure Monitor e configure alertas/notificações personalizados usando grupos de ação.

Como implantar o PIM: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Entenda as detecções de risco do Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Como configurar grupos de ações para alertas e notificações personalizados: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: Use localizações nomeadas de acesso condicional para permitir o acesso somente de agrupamentos lógicos de intervalos de endereços IP ou de países/regiões específicos.



Como configurar localizações nomeadas no Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use o Azure Active Directory (AD) como o sistema de autenticação e autorização central para recursos do Azure, como hubs de eventos. Isso permite o controle de acesso baseado em função do Azure (RBAC do Azure) para recursos confidenciais administrativos.

 Como criar e configurar uma instância do Azure AD: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Para saber mais sobre como os hubs de eventos do Azure se integram ao Azure Active Directory (AAD), consulte autorizar o acesso aos recursos dos hubs de eventos usando Azure Active Directory: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (AD) fornece logs para ajudá-lo a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso de usuários pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo.

Em outras, gire regularmente as assinaturas de acesso compartilhado dos seus hubs de eventos.

Entender os relatórios do Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como usar as revisões de acesso de identidade do Azure: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Noções básicas sobre assinaturas de acesso compartilhado para hubs de eventos: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: monitorar tentativas de acessar contas desativadas

**Orientação**: você tem acesso a atividades de entrada do Azure Active Directory (AD), auditoria e risco de registro de eventos, que permitem a integração com qualquer ferramenta Siem/Monitoring.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas de log desejados no Log Analytics.

Como integrar os logs de atividades do Azure ao Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Autorizar o acesso aos recursos dos hubs de eventos usando Azure Active Directory: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Diretrizes**: Use a proteção de identidade e os recursos de detecção de risco do Azure Active Directory para configurar respostas automatizadas para detectar ações suspeitas relacionadas aos recursos habilitados para os hubs de eventos. Você deve habilitar respostas automatizadas por meio do Azure Sentinel para implementar as respostas de segurança da sua organização.

Como exibir entradas suspeitas do Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e habilitar políticas de risco de proteção de identidade: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como integrar o Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Diretrizes**: atualmente não disponível; O Sistema de Proteção de Dados do Cliente ainda não tem suporte para hubs de eventos.

Lista de serviços com suporte Sistema de Proteção de Dados do Cliente: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Não disponível no momento

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [Controle de segurança: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: manter um inventário de informações confidenciais

**Orientação**: use marcas em recursos relacionados aos seus hubs de eventos para auxiliar no rastreamento de recursos do Azure que armazenam ou processam informações confidenciais.

Como criar e usar marcas: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Os namespaces dos hubs de eventos devem ser separados por uma rede virtual com pontos de extremidade de serviço habilitados e marcados adequadamente.

Você também pode proteger o namespace de hubs de eventos do Azure usando firewalls. Os hubs de eventos do Azure oferecem suporte a controles de acesso baseados em IP para o suporte de firewall de entrada. Você pode definir regras de firewall usando o portal do Azure, Azure Resource Manager modelos ou por meio do CLI do Azure ou Azure PowerShell.

Como criar assinaturas adicionais do Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gerenciamento: https://docs.microsoft.com/azure/governance/management-groups/create

Configurar regras de firewall IP para namespaces de hubs de eventos do Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Como criar e utilizar marcas: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como criar uma rede virtual: https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: ao usar máquinas virtuais para acessar seus hubs de eventos, use redes virtuais, pontos de extremidade de serviço, firewall de hubs de eventos, grupos de segurança de rede e marcas de serviço para atenuar a possibilidade de vazamento de dados.

A Microsoft gerencia a infraestrutura subjacente para os hubs de eventos do Azure e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

Configurar regras de firewall IP para namespaces de hubs de eventos do Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Entender os pontos de extremidade de serviço de rede virtual com os hubs de eventos do Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Integre hubs de eventos do Azure com o link privado do Azure: https://docs.microsoft.com/azure/event-hubs/private-link-service

Entenda os grupos de segurança de rede e as marcas de serviço: https://docs.microsoft.com/azure/virtual-network/security-overview

Entenda a proteção de dados do cliente no Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: os hubs de eventos do Azure impõe comunicações criptografadas por TLS por padrão. No momento, há suporte para as versões 1,0, 1,1 e 1,2 do TLS. No entanto, o TLS 1,0 e o 1,1 estão em um caminho para a substituição em todo o setor, portanto, use o TLS 1,2, se possível.

Para entender os recursos de segurança dos hubs de eventos, consulte segurança de rede:  https://docs.microsoft.com/azure/event-hubs/network-security

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para os hubs de eventos do Azure. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

Entender a proteção de dados do cliente no Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos

**Diretrizes**: os hubs de eventos do Azure dão suporte ao uso de Azure Active Directory (AD) para autorizar solicitações para recursos de hubs de eventos. Com o Azure AD, você pode usar o Azure RBAC (controle de acesso baseado em função) para conceder permissões a uma entidade de segurança, que pode ser um usuário ou uma entidade de serviço de aplicativo.

Entenda as funções do RBAC do Azure e disponíveis para os hubs de eventos do Azure: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

A Microsoft gerencia a infraestrutura subjacente para os hubs de eventos e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

Entenda a proteção de dados do cliente no Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: os hubs de eventos do Azure dão suporte à opção de criptografar dados em repouso com chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente. Esse recurso permite que você crie, gire, desabilite e revogue o acesso às chaves gerenciadas pelo cliente que são usadas para criptografar dados de hubs de eventos do Azure em repouso.

Como configurar chaves gerenciadas pelo cliente para criptografar hubs de eventos do Azure: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para as instâncias de produção dos hubs de eventos do Azure e outros recursos críticos ou relacionados.

Como criar alertas para eventos do log de atividades do Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Orientação**: não aplicável; A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte a hubs de eventos.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Orientação**: não aplicável; A Microsoft executa o gerenciamento de patches nos sistemas subjacentes que dão suporte aos hubs de eventos.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: implantar uma solução automatizada de gerenciamento de patch de software de terceiros

**Orientação**: não aplicável; o parâmetro de comparação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: comparar verificações de vulnerabilidade consecutivas

**Orientação**: não aplicável; A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte a hubs de eventos.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Orientação**: não aplicável; A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte a hubs de eventos.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de segurança: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: usar a descoberta de ativos do Azure

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (incluindo namespaces de hubs de eventos do Azure) em suas assinaturas. Verifique se você tem permissões (leitura) apropriadas em seu locatário e é capaz de enumerar todas as assinaturas do Azure, bem como os recursos nas suas assinaturas.

Como criar consultas com o Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como exibir suas assinaturas do Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Entenda o RBAC do Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

Como criar e usar marcas: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear namespaces dos hubs de eventos do Azure e recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

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

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o grafo de recursos do Azure para consultar/descobrir recursos dentro das assinaturas.

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo de recurso específico com o Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: implementar lista de aplicativos aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limitar a capacidade dos usuários de interagir com o Azure Resource Manager por meio de scripts</div>

**Diretriz**: Configure o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”.

Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

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

**Orientação**: definir e implementar configurações de segurança padrão para suas implantações de hubs de eventos do Azure. Use Azure Policy aliases no namespace "Microsoft. EventHub" para criar políticas personalizadas para auditar ou impor configurações. Você também pode fazer uso de definições de política internas para hubs de eventos do Azure, como:

- Os logs de diagnóstico no Hub de eventos devem ser habilitados

- O Hub de Eventos deve usar um ponto de extremidade de serviço de rede virtual

Política interna do Azure para namespace de hubs de eventos: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Como exibir os aliases do Azure Policy disponíveis: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Orientação**: Use Azure Policy [Deny] e [implantar se não existir] para impor configurações seguras em seus recursos habilitados para hubs de eventos. 

Como configurar e gerenciar Azure Policy:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Para obter mais informações sobre os efeitos de Azure Policy:  https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Orientação**: se você estiver usando definições de Azure Policy personalizadas para seus hubs de eventos ou recursos relacionados, use Azure Repos para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow

Documentação do Azure Repos: https://docs.microsoft.com/azure/devops/repos/index

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração do sistema

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. EventHub" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar o monitoramento automatizado de configuração para serviços do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. EventHub" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use Azure Policy [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para as implantações de hubs de eventos do Azure e recursos relacionados.

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Orientação**: para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure app que está sendo usado para acessar seus hubs de eventos, use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de assinatura de acesso compartilhado para suas implantações de hubs de eventos do Azure. Certifique-se de que Key Vault exclusão reversível esteja habilitada.

Autentique uma identidade gerenciada com Azure Active Directory para acessar os recursos dos hubs de eventos: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Configurar chaves gerenciadas pelo cliente para hubs de eventos: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Como integrar-se às Identidades Gerenciadas do Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um cofre de chaves: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Como autenticar para o Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Como atribuir uma política de acesso de Key Vault: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Orientação**: para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure app que está sendo usado para acessar seus hubs de eventos, use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger os hubs de eventos do Azure. Certifique-se de que Key Vault exclusão reversível esteja habilitada.

Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (AD). Identidades gerenciadas permitem que você se autentique em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Azure Key Vault, sem nenhuma credencial em seu código.

Autentique uma identidade gerenciada com Azure Active Directory para acessar os recursos dos hubs de eventos: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Configurar chaves gerenciadas pelo cliente para hubs de eventos: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Como configurar identidades gerenciadas: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Como integrar-se às Identidades Gerenciadas do Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

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

**Diretrizes**: examinar previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure, como hubs de eventos do Azure, serviço de aplicativo, data Lake Storage, armazenamento de BLOBs, banco de dados do Azure para PostgreSQL etc. A Microsoft não pode acessar seus dados nessas instâncias.

O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, cache do Azure para Redis), no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software antimalware e as assinaturas sejam atualizados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de segurança: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantir backups automatizados regulares

**Orientação**: configurar a recuperação de desastre geográfico para os hubs de eventos do Azure. Quando datacenters ou regiões inteiras do Azure (se nenhuma zona de disponibilidade for usada) enfrentam tempo de inatividade, é essencial para o processamento de dados continuar a operar em uma região ou datacenter diferente. Como tal, a recuperação de desastre em área geográfica e a replicação geográfica são recursos importantes para qualquer empresa. Os Hubs de Eventos do Azure dão suporte à recuperação de desastre de área geográfica e à replicação geográfica no nível do namespace. 

Entenda a recuperação de desastres geograficamente para os hubs de eventos do Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Realizar backups completos do sistema e fazer backup das chaves gerenciadas pelo cliente

**Diretrizes**: os hubs de eventos do Azure fornecem a criptografia de dados em repouso com Azure criptografia do serviço de armazenamento (Azure SSE). Os hubs de eventos dependem do armazenamento do Azure para armazenar os dados e, por padrão, todos os dados armazenados com o armazenamento do Azure são criptografados usando chaves gerenciadas pela Microsoft. Se você usar Azure Key Vault para armazenar chaves gerenciadas pelo cliente, garanta backups automatizados regulares de suas chaves.

Garanta backups automatizados regulares de seus segredos de Key Vault com o seguinte comando do PowerShell: Backup-AzKeyVaultSecret

Como configurar chaves gerenciadas pelo cliente para criptografar dados de hubs de eventos do Azure em repouso: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Como fazer backup de Key Vault segredos: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: testar a restauração de chaves de backup gerenciadas pelo cliente.

 

Como restaurar chaves do Key Vault no Azure: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: habilite a exclusão reversível em Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada. Os hubs de eventos do Azure exigem que as chaves gerenciadas pelo cliente tenham exclusão reversível e não sejam limpas configuradas.

Configure a exclusão reversível para a conta de armazenamento do Azure que é usada para capturar dados de hubs de eventos. Observe que esse recurso não tem suporte para o Azure Data Lake Storage Gen 2 ainda.

Como habilitar a exclusão reversível no Key Vault: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Configure um cofre de chaves com chaves: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Exclusão reversível para BLOBs de armazenamento do Azure: https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de segurança: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Orientação**: Verifique se há planos de resposta a incidentes escritos que definem funções de pessoal, bem como fases de manipulação/gerenciamento de incidentes.

Como configurar automações de fluxo de trabalho na Central de Segurança do Azure: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a central de segurança atribui uma severidade aos alertas, para ajudá-lo a priorizar a ordem em que você participa de cada alerta, para que, quando um recurso for comprometido, você possa acessá-lo imediatamente. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10,3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e capacidades: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal.  Examine os incidentes após o fato para garantir que os problemas sejam resolvidos. 

Como definir o contato de segurança da Central de Segurança do Azure: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

Como configurar a exportação contínua: https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para o Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretriz**: use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de “Aplicativos Lógicos” em alertas de segurança e recomendações.

Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos: https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [Controle de segurança: testes de penetração e exercícios de Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: realizar testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as constatações de segurança críticas em 60 dias

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não estejam em violação às políticas da Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .
Você pode encontrar mais informações sobre a estratégia e a execução da Microsoft de equipes vermelhas e testes de penetração de sites ativos em infraestrutura, serviços e aplicativos da nuvem gerenciada da Microsoft, aqui: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Próximas etapas

- Consulte o [Parâmetro de segurança do Azure](../security/benchmarks/overview.md)
- Saiba mais sobre as [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)