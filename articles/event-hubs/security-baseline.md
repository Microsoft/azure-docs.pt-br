---
title: Linha de base de segurança do Azure para hubs de eventos
description: A linha de base de segurança dos hubs de eventos fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: event-hubs
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 57862a450d313f5d8850b14047ecc3d25d6ba5c1
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563677"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Linha de base de segurança do Azure para hubs de eventos

Essa linha de base de segurança aplica diretrizes da [versão 1,0 do benchmark de segurança do Azure](../security/benchmarks/overview-v1.md) para os hubs de eventos. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis aos hubs de eventos. Os **controles** não aplicáveis aos hubs de eventos foram excluídos.

 
Para ver como os hubs de eventos são completamente mapeados para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança dos hubs de eventos](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: a integração dos hubs de eventos do Azure com pontos de extremidade de serviço de rede virtual permite o acesso seguro a recursos de mensagens de cargas de trabalho, como máquinas virtuais associadas a redes virtuais, com o caminho de tráfego de rede protegido em ambas as extremidades.

Uma vez associado a pelo menos um ponto de extremidade de serviço de sub-rede de rede virtual, o respectivo namespace de hubs de eventos não aceita mais tráfego de qualquer lugar, mas de sub-redes autorizadas em redes virtuais. Da perspectiva da rede virtual, a associação do namespace de seus hubs de eventos a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de mensagens. 

Você também pode criar um ponto de extremidade privado, que é uma interface de rede que conecta você de forma privada e segura ao serviço de hubs de eventos usando o serviço de link privado do Azure. O ponto de extremidade privado usa um endereço IP privado de sua rede virtual, efetivamente colocando o serviço em sua rede virtual. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. 

Você também pode proteger seu namespace de hubs de eventos do Azure usando firewalls. Os hubs de eventos oferecem suporte a controles de acesso baseados em IP para o suporte de firewall de entrada. Você pode definir regras de firewall usando o portal do Azure, Azure Resource Manager modelos ou por meio do CLI do Azure ou Azure PowerShell.

- [Como usar pontos de extremidade de serviço de rede virtual com hubs de eventos do Azure](event-hubs-service-endpoints.md)

- [Integração dos Hubs de Eventos do Azure com o Link Privado do Azure](private-link-service.md)

- [Como configurar regras de firewall IP para namespaces de hubs de eventos do Azure](event-hubs-ip-filtering.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/azure/security-center/security-center-recommendations). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Diretrizes**: Use a central de segurança do Azure e siga as recomendações de proteção de rede para ajudar a proteger seus recursos de hubs de eventos no Azure. Se estiver usando máquinas virtuais do Azure para acessar seus hubs de eventos, habilite logs de fluxo do grupo de segurança de rede e envie logs para uma conta de armazenamento para auditoria de tráfego.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Noções básicas sobre segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: habilite a proteção contra DDoS Standard nas redes virtuais associadas aos seus hubs de eventos para proteger contra ataques de DDoS (negação de serviço distribuído). Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

- [Como configurar a proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

- [Para obter mais informações sobre a inteligência de ameaças integrada da central de segurança do Azure](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: se estiver usando máquinas virtuais do Azure para acessar seus hubs de eventos, habilite logs de fluxo do grupo de segurança de rede e envie logs para uma conta de armazenamento para auditoria de tráfego. Você também pode enviar logs de fluxo do grupo de segurança de rede para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

Se necessário para investigar a atividade anômala, habilite a captura de pacotes do observador de rede.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Como habilitar o Observador de Rede](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: se estiver usando máquinas virtuais do Azure para acessar seus hubs de eventos, selecione uma oferta do Azure Marketplace que dá suporte à funcionalidade de IDS/IPS com recursos de inspeção de conteúdo. Se a detecção de intrusão e/ou prevenção baseada na inspeção de carga não for necessária para sua organização, você poderá usar o recurso de firewall interno dos hubs de eventos do Azure. Você pode limitar o acesso ao namespace de seus hubs de eventos para um intervalo limitado de endereços IP ou um endereço IP específico usando regras de firewall.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Como adicionar uma regra de firewall em hubs de eventos para um endereço IP especificado](event-hubs-ip-filtering.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede associados aos seus namespaces de hubs de eventos do Azure com o Azure Policy. Use aliases de Azure Policy nos namespaces **Microsoft. EventHub** e **Microsoft. Network** para criar definições de política personalizadas para auditar ou impor a configuração de rede de seus namespaces de hubs de eventos. Você também pode fazer uso de definições de política internas relacionadas aos hubs de eventos do Azure, como:

- O Hub de eventos deve usar um ponto de extremidade de serviço de rede virtual.

Informações adicionais estão disponíveis nos links referenciados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Política interna do Azure para namespace de hubs de eventos](../governance/policy/samples/built-in-policies.md#event-hub)

- [Exemplos de Azure Policy para rede](../governance/policy/samples/built-in-policies.md#network)

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: use marcas para redes virtuais e outros recursos relacionados à segurança de rede e ao fluxo de tráfego associados aos seus hubs de eventos.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados aos hubs de eventos do Azure. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Diretrizes**: em Azure monitor, configure logs relacionados aos hubs de eventos no log de atividades e nas configurações de diagnóstico do hub de eventos para enviar logs em um espaço de trabalho do log Analytics para serem consultados ou em uma conta de armazenamento para armazenamento de arquivamento de longo prazo.

- [Como definir configurações de diagnóstico para hubs de eventos do Azure](event-hubs-diagnostic-logs.md)

- [Noções básicas sobre o log de atividades do Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: Habilitar configurações de diagnóstico para seu namespace de hubs de eventos do Azure. Há três categorias de configurações de diagnóstico para hubs de eventos do Azure: logs de arquivamento, logs operacionais e logs de dimensionamento automático. Habilite logs operacionais para capturar informações sobre o que está acontecendo durante operações de hubs de eventos, especificamente, o tipo de operação, incluindo a criação do hub de eventos, os recursos usados e o status da operação.

Além disso, você pode habilitar as configurações de diagnóstico do log de atividades do Azure e enviá-las para uma conta de armazenamento do Azure, um hub de eventos ou um espaço de trabalho Log Analytics. Os logs de atividade fornecem informações sobre as operações que foram realizadas em seus hubs de eventos do Azure e outros recursos. Usando os logs de atividade, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) realizada em seus namespaces de hubs de eventos do Azure.

- [Como habilitar configurações de diagnóstico para hubs de eventos do Azure](event-hubs-diagnostic-logs.md)

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/azure/security-center/security-center-recommendations). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

**Diretrizes**: em Azure monitor, defina seu período de retenção de espaço de trabalho de log Analytics de acordo com os regulamentos de conformidade da sua organização para capturar e examinar incidentes relacionados ao Hub de eventos.

- [Como definir parâmetros de retenção de log para espaços de trabalho do Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: analise e monitore os logs de comportamento anormal e Examine regularmente os resultados relacionados aos seus hubs de eventos. Use o Log Analytics do Azure Monitor para revisar os logs e realizar consultas nos dados de log. Como alternativa, você pode habilitar o e os dados integrados ao Azure Sentinel ou a uma solução de gerenciamento de eventos e informações do sistema de terceiros.

- [Para obter mais informações sobre o espaço de trabalho Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: em Azure monitor, configure logs relacionados aos hubs de eventos do Azure no log de atividades e configurações de diagnóstico de hubs de eventos para enviar logs para um espaço de trabalho do log Analytics para serem consultados ou em uma conta de armazenamento para armazenamento de arquivamento de longo prazo. Use Log Analytics espaço de trabalho para criar alertas para atividade anômala encontrada em eventos e logs de segurança.

Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel. 

- [Entender o log de atividades do Azure](../azure-monitor/essentials/platform-logs-overview.md)

- [Como definir configurações de diagnóstico para hubs de eventos do Azure](event-hubs-diagnostic-logs.md)

- [Como alertar sobre Log Analytics dados de log do espaço de trabalho](../azure-monitor/alerts/tutorial-response.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: Azure Active Directory (Azure AD) tem funções internas que devem ser explicitamente atribuídas e que podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Orientação**: controle o acesso do plano aos hubs de eventos é controlado por meio do Azure Active Directory (AD do Azure). O Azure AD não tem o conceito de senhas padrão.

O acesso do plano de dados aos hubs de eventos é controlado por meio do Azure AD com identidades gerenciadas ou Registros de aplicativo, bem como assinaturas de acesso compartilhado. As assinaturas de acesso compartilhado são usadas pelos clientes que se conectam aos seus hubs de eventos e podem ser regeneradas a qualquer momento.

- [Entender as assinaturas de acesso compartilhado para hubs de eventos](authenticate-shared-access-signature.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas.

Além disso, para ajudá-lo a controlar contas administrativas dedicadas, você pode seguir as recomendações da Central de Segurança do Azure ou as políticas internas do Azure, como:

- Deve haver mais de um proprietário atribuído à sua assinatura

- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura

- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

Informações adicionais estão disponíveis nos links referenciados.

- [Como usar a Central de Segurança do Azure para monitorar a identidade e o acesso (versão prévia)](../security-center/security-center-identity-access.md)

- [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Diretrizes**: Microsoft Azure fornece gerenciamento de controle de acesso integrado para recursos e aplicativos com base no Azure Active Directory (AD do Azure). Uma vantagem importante de usar o Azure AD com os hubs de eventos do Azure é que você não precisa mais armazenar suas credenciais no código. Em vez disso, você pode solicitar um token de acesso OAuth 2,0 da plataforma de identidade da Microsoft. O nome do recurso para solicitar um token é https: \/ /Eventhubs.Azure.net/. O Azure AD autentica a entidade de segurança (um usuário, grupo ou entidade de serviço) que executa o aplicativo. Se a autenticação for bem sucedido, o Azure AD retornará um token de acesso para o aplicativo e o aplicativo poderá usar o token de acesso para autorizar a solicitação aos recursos dos hubs de eventos do Azure.

- [Como autenticar um aplicativo com o Azure AD para acessar recursos de hubs de eventos](authenticate-application.md)

- [Entendendo o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretrizes**: habilite a autenticação multifator do Azure Active Directory (Azure AD) e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure para ajudar a proteger seus recursos habilitados para o Hub de eventos.

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use Paw (estações de trabalho com acesso privilegiado) com a autenticação multifator configurada para fazer logon e configurar recursos habilitados para o Hub de eventos.

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](/security/compass/privileged-access-devices)

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use Azure Active Directory (AD do Azure) Privileged Identity Management para geração de logs e alertas quando atividades suspeitas ou inseguras ocorrerem no ambiente. Use as detecções de risco do Azure AD para exibir alertas e relatórios sobre o comportamento do usuário arriscado. Para logs adicionais, envie alertas de detecção de riscos da central de segurança do Azure para Azure Monitor e configure alertas/notificações personalizados usando grupos de ação.

- [Entenda as detecções de risco do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar grupos de ação para alertas e notificações personalizados](../azure-monitor/alerts/action-groups.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: Use localizações nomeadas de acesso condicional para permitir o acesso somente de agrupamentos lógicos de intervalos de endereços IP ou de países/regiões específicos.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central para recursos do Azure, como hubs de eventos. Isso permite o controle de acesso baseado em função do Azure (RBAC do Azure) para recursos confidenciais administrativos.

- [ Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autorizar o acesso a recursos de hubs de eventos usando o Azure AD](authorize-access-azure-active-directory.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (Azure AD) fornece logs para ajudá-lo a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso de usuários pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo.

Além disso, gire regularmente as assinaturas de acesso compartilhado dos seus hubs de eventos.

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

- [Noções básicas sobre assinaturas de acesso compartilhado para hubs de eventos](authenticate-shared-access-signature.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Orientação**: você tem acesso a atividades de entrada do Azure Active Directory (Azure AD), auditoria e risco de registro de eventos, que permitem a integração com qualquer solução de gerenciamento de eventos e informações do sistema de terceiros ou uma ferramenta de monitoramento.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas de log desejados no Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Autorizar o acesso a recursos de hubs de eventos usando o Azure AD](authorize-access-azure-active-directory.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: Use a proteção de identidade e os recursos de detecção de risco no Azure Active Directory (Azure AD) para configurar respostas automatizadas para detectar ações suspeitas relacionadas aos recursos habilitados para os hubs de eventos. Você deve habilitar respostas automatizadas por meio do Azure Sentinel para implementar as respostas de segurança da sua organização.

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Diretrizes**: atualmente não disponível; O Sistema de Proteção de Dados do Cliente ainda não tem suporte para hubs de eventos.

- [Lista de serviços suportados do Sistema de Proteção de Dados do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: manter um inventário de informações confidenciais

**Orientação**: use marcas em recursos relacionados aos seus hubs de eventos para auxiliar no rastreamento de recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente assinaturas separadas, opcionalmente com grupos de gerenciamento para desenvolvimento, teste e produção. Os namespaces dos hubs de eventos devem ser separados por uma rede virtual com pontos de extremidade de serviço habilitados e marcados adequadamente.

Você também pode proteger o namespace de hubs de eventos do Azure usando firewalls. Os hubs de eventos do Azure oferecem suporte a controles de acesso baseados em IP para o suporte de firewall de entrada. Você pode definir regras de firewall usando o portal do Azure, Azure Resource Manager modelos ou por meio do CLI do Azure ou Azure PowerShell.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Configurar regras de firewall IP para namespaces de hubs de eventos do Azure](event-hubs-ip-filtering.md)

- [Como criar e utilizar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: ao usar máquinas virtuais para acessar seus hubs de eventos, use redes virtuais, pontos de extremidade de serviço, firewall de hubs de eventos, grupos de segurança de rede e marcas de serviço para atenuar a possibilidade de vazamento de dados.

A Microsoft gerencia a infraestrutura subjacente para os hubs de eventos do Azure e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

- [Configurar regras de firewall IP para namespaces de hubs de eventos do Azure](event-hubs-ip-filtering.md)

- [Entender os pontos de extremidade de serviço de rede virtual com os hubs de eventos do Azure](event-hubs-service-endpoints.md)

- [Integração dos Hubs de Eventos do Azure com o Link Privado do Azure](private-link-service.md)

- [Entender os grupos de segurança de rede e as marcas de serviço](../virtual-network/network-security-groups-overview.md)

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para os hubs de eventos do Azure. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usar o controle de acesso baseado em função para controlar o acesso aos recursos

**Diretrizes**: os hubs de eventos do Azure dão suporte ao uso do Azure Active Directory (AD do Azure) para autorizar solicitações para recursos de hubs de eventos. Com o Azure AD, você pode usar o Azure RBAC (controle de acesso baseado em função) para conceder permissões a uma entidade de segurança, que pode ser um usuário ou uma entidade de serviço de aplicativo.

- [Entender o RBAC do Azure e as funções disponíveis para os hubs de eventos do Azure](authorize-access-azure-active-directory.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretrizes**: os hubs de eventos do Azure dão suporte à opção de criptografar dados em repouso com chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente. Esse recurso permite que você crie, gire, desabilite e revogue o acesso às chaves gerenciadas pelo cliente que são usadas para criptografar dados de hubs de eventos do Azure em repouso.

- [Como configurar chaves gerenciadas pelo cliente para criptografar hubs de eventos do Azure](configure-customer-managed-key.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para as instâncias de produção dos hubs de eventos do Azure e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (incluindo namespaces de hubs de eventos do Azure) em suas assinaturas. Verifique se você tem permissões (leitura) apropriadas em seu locatário e é capaz de enumerar todas as assinaturas do Azure, bem como os recursos nas suas assinaturas.

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear namespaces dos hubs de eventos do Azure e recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o grafo de recursos do Azure para consultar/descobrir recursos dentro das assinaturas.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Informações adicionais estão disponíveis nos links referenciados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretriz**: Configure o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”.

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: definir e implementar configurações de segurança padrão para suas implantações de hubs de eventos do Azure. Use Azure Policy aliases no namespace **Microsoft. EventHub** para criar políticas personalizadas para auditar ou impor configurações. Você também pode fazer uso de definições de política internas para hubs de eventos do Azure, como:

- Os logs de diagnóstico no Hub de eventos devem ser habilitados

- O Hub de Eventos deve usar um ponto de extremidade de serviço de rede virtual

Informações adicionais estão disponíveis nos links referenciados.

- [Política interna do Azure para namespace de hubs de eventos](../governance/policy/samples/built-in-policies.md#event-hub)

- [Como exibir os aliases de Azure Policy disponíveis](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Orientação**: Use os efeitos de Azure Policy [Deny] e [implantar se não existir] para impor as configurações seguras nos recursos habilitados para os hubs de eventos. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

 
- [Para obter mais informações sobre os efeitos de Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. EventHub" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. EventHub" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use os efeitos de Azure Policy [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para as implantações de hubs de eventos do Azure e recursos relacionados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Orientação**: para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure app que está sendo usado para acessar seus hubs de eventos, use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de assinatura de acesso compartilhado para suas implantações de hubs de eventos do Azure. Verifique se Key Vault está configurado com a exclusão reversível habilitada.

- [Autenticar uma identidade gerenciada com o Azure Active Directory (Azure AD) para acessar recursos de hubs de eventos](./authenticate-managed-identity.md?tabs=latest)

- [Configurar chaves gerenciadas pelo cliente para hubs de eventos](configure-customer-managed-key.md)

- [Como integrar com identidades gerenciadas do Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Como fornecer Key Vault autenticação com uma identidade gerenciada](../key-vault/general/assign-access-policy-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Orientação**: para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure app que está sendo usado para acessar seus hubs de eventos, use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger os hubs de eventos do Azure. Verifique se Key Vault está configurado com a exclusão reversível habilitada.

Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Identidades gerenciadas permitem que você se autentique em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Azure Key Vault, sem nenhuma credencial em seu código.

- [Autenticar uma identidade gerenciada com o Azure AD para acessar recursos de hubs de eventos](./authenticate-managed-identity.md?tabs=latest)

- [Configurar chaves gerenciadas pelo cliente para hubs de eventos](configure-customer-managed-key.md)

- [Como configurar identidades gerenciadas](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Como integrar com identidades gerenciadas do Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: examinar previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure, como hubs de eventos do Azure, serviço de aplicativo, data Lake Storage, armazenamento de BLOBs, banco de dados do Azure para PostgreSQL etc. A Microsoft não pode acessar seus dados nessas instâncias.

O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, cache do Azure para Redis), no entanto, ele não é executado no conteúdo do cliente.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares

**Orientação**: configurar a recuperação de desastre geográfico para os hubs de eventos do Azure. Quando datacenters ou regiões inteiras do Azure (se nenhuma zona de disponibilidade for usada) enfrentam tempo de inatividade, é essencial para o processamento de dados continuar a operar em uma região ou datacenter diferente. Como tal, a recuperação de desastre em área geográfica e a replicação geográfica são recursos importantes para qualquer empresa. Os Hubs de Eventos do Azure dão suporte à recuperação de desastre de área geográfica e à replicação geográfica no nível do namespace. 

- [Entender a recuperação de desastres geograficamente para os hubs de eventos do Azure](./event-hubs-geo-dr.md#availability-zones)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: os hubs de eventos do Azure fornecem a criptografia de dados em repouso com Azure criptografia do serviço de armazenamento (Azure SSE). Os hubs de eventos dependem do armazenamento do Azure para armazenar os dados e, por padrão, todos os dados armazenados com o armazenamento do Azure são criptografados usando chaves gerenciadas pela Microsoft. Se você usar Azure Key Vault para armazenar chaves gerenciadas pelo cliente, garanta backups automatizados regulares de suas chaves.

Garanta backups automatizados regulares de seus segredos de Key Vault com o seguinte comando do PowerShell: Backup-AzKeyVaultSecret

- [Como configurar chaves gerenciadas pelo cliente para criptografar dados de hubs de eventos do Azure em repouso](configure-customer-managed-key.md)

- [Como fazer backup de Key Vault segredos](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: testar a restauração de chaves de backup gerenciadas pelo cliente.

- [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: habilite a exclusão reversível em Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada. Os hubs de eventos do Azure exigem que as chaves gerenciadas pelo cliente tenham exclusão reversível e não sejam limpas configuradas.

Configure a exclusão reversível para a conta de armazenamento do Azure que é usada para capturar dados de hubs de eventos. Observe que esse recurso não tem suporte para o Azure Data Lake Storage Gen 2 ainda.

- [Como habilitar a exclusão reversível no Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Configurar um cofre de chaves com chaves](configure-customer-managed-key.md)

- [Exclusão reversível para blobs do Armazenamento do Azure ](//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Orientação**: Verifique se há planos de resposta a incidentes escritos que definem funções de pessoal, bem como fases de manipulação/gerenciamento de incidentes.

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a central de segurança atribui uma severidade aos alertas, para ajudá-lo a priorizar a ordem em que você participa de cada alerta, para que, quando um recurso for comprometido, você possa acessá-lo imediatamente. A gravidade se baseia em quão confiante a central de segurança está na localização ou na análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Veja a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal.  Examine os incidentes após o fato para garantir que os problemas sejam resolvidos. 

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de "Aplicativos Lógicos" em alertas de segurança e recomendações.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de teste de penetração Microsoft Cloud do Engagement para garantir que seus testes de penetração não estejam violando as políticas da Microsoft. Use a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem, serviços e aplicativos gerenciados pela Microsoft. 

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
