---
title: Linha de base de segurança do Azure para HDInsight
description: A linha de base de segurança do HDInsight fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bf2360bda55735aa8ef4258da5ae47f673f4d71b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738915"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Linha de base de segurança do Azure para HDInsight

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure versão 1,0](../security/benchmarks/overview-v1.md) para HDInsight. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao HDInsight. Os **controles** não aplicáveis ao HDInsight foram excluídos.

 
Para ver como o HDInsight é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança do HDInsight completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: a segurança de perímetro no Azure HDInsight é obtida por meio de redes virtuais. Um administrador corporativo pode criar um cluster dentro de uma rede virtual e usar um NSG (grupo de segurança de rede) para restringir o acesso à rede virtual. Somente os endereços IP permitidos nas regras do grupo de segurança de rede de entrada poderão se comunicar com o cluster HDInsight do Azure. Essa configuração fornece segurança de perímetro. Todos os clusters implantados em uma rede virtual também terão um ponto de extremidade privado que é resolvido para um endereço IP privado dentro da rede virtual para acesso HTTP privado aos gateways de cluster.

Para reduzir o risco de perda de dados por meio de vazamento, restrinja o tráfego de rede de saída para clusters do Azure HDInsight usando o Firewall do Azure.

- [Como implantar o Azure HDInsight em uma rede virtual e proteger com um grupo de segurança de rede](hdinsight-create-virtual-network.md)

- [Como restringir o tráfego de saída para clusters do Azure HDInsight com o Firewall do Azure](hdinsight-restrict-outbound-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Use a central de segurança do Azure e corrija as recomendações de proteção de rede para a rede virtual, a sub-rede e o grupo de segurança de rede que estão sendo usados para proteger o cluster HDInsight do Azure. Habilite logs de fluxo do NSG (grupo de segurança de rede) e envie logs para uma conta de armazenamento do Azure para auditoria de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho do Azure Log Analytics e usar o Análise de Tráfego do Azure para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens do Azure Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar as configurações de configuração de rede.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar o Azure Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: para proteger contra ataques de DDoS, habilite a proteção padrão do DDoS do Azure na rede virtual em que o Azure HDInsight está implantado. Use a inteligência de ameaças integrada da central de segurança do Azure para negar comunicações com endereços IP de Internet mal-intencionados ou não utilizados conhecidos.

- [Como configurar a proteção contra DDoS](/azure/virtual-network/manage-ddos-protection)

- [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](/azure/security-center/security-center-alerts-service-layer)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Diretrizes**: habilitar os logs de fluxo do NSG (grupo de segurança de rede) para o NSG anexado à sub-rede que está sendo usada para proteger o cluster do Azure HDInsight. Registre os logs de fluxo do NSG em uma conta de armazenamento do Azure para gerar registros de fluxo. Se necessário para investigar a atividade anômala, habilite a captura de pacotes do observador de rede do Azure.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar o Observador de Rede](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Orientação**: o requisito pode ser atendido na ID de controle de segurança do Azure 1,1; Implante o cluster HDInsight do Azure em uma rede virtual e proteja-o com um NSG (grupo de segurança de rede).

Há várias dependências para o Azure HDInsight que exigem tráfego de entrada. O tráfego de gerenciamento de entrada não pode ser enviado por meio de um dispositivo de firewall. Os endereços de origem para o tráfego de gerenciamento necessário são conhecidos e publicados. Crie regras de grupo de segurança de rede com essas informações para permitir o tráfego somente de locais confiáveis, protegendo o tráfego de entrada para os clusters.

Para reduzir o risco de perda de dados por meio de vazamento, restrinja o tráfego de rede de saída para clusters do Azure HDInsight usando o Firewall do Azure.

- [Como implantar o HDInsight em uma rede virtual e proteger com um grupo de segurança de rede](hdinsight-create-virtual-network.md)

- [Entender as dependências do HDInsight e o uso do firewall](hdinsight-restrict-outbound-traffic.md)

- [Endereços IP de gerenciamento do HDInsight](hdinsight-management-ip-addresses.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em NSG (grupos de segurança de rede) que são anexados à sub-rede em que o cluster do Azure HDInsight está implantado. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

- [Entender e usar marcas de serviço para o Azure HDInsight](/azure/virtual-network/security-overview#service-tags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Orientação**: definir e implementar configurações de segurança padrão para recursos de rede relacionados ao cluster do Azure HDInsight. Use aliases de Azure Policy nos namespaces "Microsoft. HDInsight" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do cluster do Azure HDInsight.

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos de Azure Resource Manager, controles RBAC do Azure e políticas, em uma única definição de Blueprint. Aplique facilmente o blueprint a novas assinaturas e ambientes e ajuste o controle e o gerenciamento por meio do controle de versão.

- [Como exibir os aliases de Azure Policy disponíveis](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Diretrizes**: use marcas para NSGs (grupo de segurança de rede) e outros recursos relacionados à segurança de rede e ao fluxo de tráfego associados ao cluster HDInsight do Azure. Para regras de NSG individuais, use o campo "Descrição" para especificar a necessidade empresarial e/ou a duração (etc.) para regras que permitam tráfego de/para uma rede.

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar Azure PowerShell ou CLI (interface de linha de comando) do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

- [Como criar e usar marcas](/azure/azure-resource-manager/resource-group-using-tags)

- [Como criar uma rede virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados às implantações do Azure HDInsight. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](/azure/azure-monitor/platform/activity-log-view)

- [Como criar alertas no Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: você pode integrar seu cluster do Azure HDInsight para Azure monitor para agregar dados de segurança gerados pelo cluster. Aproveite as consultas personalizadas para detectar e responder a ameaças no ambiente. 

- [Como carregar um cluster do Azure HDInsight para Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Como criar consultas personalizadas para um cluster do Azure HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: habilite Azure monitor para o cluster HDInsight, direcione-o para um espaço de trabalho log Analytics. Isso registrará em log as informações relevantes do cluster e as métricas do sistema operacional para todos os nós do cluster do Azure HDInsight.

- [Como habilitar o registro em log para o cluster HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Como consultar logs do HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Diretrizes**: integrar o cluster HDInsight do Azure ao Azure monitor. Verifique se o espaço de trabalho Log Analytics usado tem o período de retenção de log definido de acordo com os regulamentos de conformidade da sua organização.

- [Como carregar um cluster do Azure HDInsight para Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Como configurar Log Analytics período de retenção do espaço de trabalho](/azure/azure-monitor/platform/manage-cost-storage)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

**Diretrizes**: integrar o cluster HDInsight do Azure ao Azure monitor. Verifique se o espaço de trabalho do Azure Log Analytics usado tem o período de retenção de log definido de acordo com os regulamentos de conformidade da sua organização.

- [Como carregar um cluster do Azure HDInsight para Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Como configurar Log Analytics período de retenção do espaço de trabalho](/azure/azure-monitor/platform/manage-cost-storage)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: usar consultas de espaço de trabalho do Azure log Analytics para consultar logs do Azure HDInsight:

- [Como criar consultas personalizadas para clusters do Azure HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: Use o espaço de trabalho do Azure log Analytics para monitoramento e alertas em atividades anormais em logs de segurança e eventos relacionados ao cluster HDInsight do Azure.

- [Como gerenciar alertas na central de segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar sobre dados de log do log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Diretrizes**: o Azure HDInsight vem com o clamscan pré-instalado e habilitado para as imagens de nó de cluster, no entanto, você deve gerenciar o software e agregar/monitorar manualmente todos os logs que o clamscan produz.

- [Entender o clamscan](https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS

**Orientação**: implementar solução de terceiros para registro em log DNS.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="210-enable-command-line-audit-logging"></a>2.10: habilitar o registro em log de auditoria de linha de comando

**Orientação**: configurar manualmente o registro em log do console em uma base por nó.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Orientação**: manter o registro da conta administrativa local que é criada durante o provisionamento de cluster do cluster HDInsight do Azure, bem como quaisquer outras contas que você criar. Além disso, se a integração do Azure Active Directory (AD do Azure) for usada, o Azure AD terá funções internas que devem ser explicitamente atribuídas e, portanto, podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Além disso, você pode usar as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Como monitorar a identidade e o acesso à central de segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Orientação**: ao provisionar um cluster, o Azure exige que você crie novas senhas para o portal da Web e o acesso de Secure Shell (SSH). Não há nenhuma senha padrão a ser alterada; no entanto, você pode especificar senhas diferentes para o SSH e o acesso ao portal da Web.

- [Como definir senhas ao provisionar um cluster do Azure HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Diretrizes**: integre a autenticação para o cluster HDInsight do Azure com o Azure Active Directory (Azure AD). Crie políticas e procedimentos em relação ao uso de contas administrativas dedicadas.

Além disso, você pode usar as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

- [Como integrar a autenticação do Azure HDInsight ao Azure AD](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Como monitorar a identidade e o acesso à central de segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Orientação**: Use o agente de ID do Azure HDInsight para entrar em clusters Enterprise Security Package (ESP) usando a autenticação multifator, sem fornecer nenhuma senha. Se você já tiver entrado em outros serviços do Azure, como o portal do Azure, poderá entrar no cluster do Azure HDInsight com uma experiência de logon único (SSO).

- [Como habilitar o agente de ID do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretrizes**: habilite a autenticação multifator do Azure Active Directory (Azure AD) e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure. Os clusters do Azure HDInsight com o Enterprise Security Package configurado podem ser conectados a um domínio para que os usuários de domínio possam usar suas credenciais de domínio para autenticar com os clusters e executar trabalhos de Big Data. Ao autenticar com a autenticação multifator habilitada, os usuários serão desafiados a fornecer um segundo fator de autenticação.

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use PAWs (estações de trabalho com acesso privilegiado) com a autenticação multifator configurada para fazer logon e configurar seus clusters do Azure HDInsight e recursos relacionados.

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: os clusters do Azure HDInsight com o Enterprise Security Package configurado podem ser conectados a um domínio para que os usuários do domínio possam usar suas credenciais de domínio para autenticar. Você pode usar os relatórios de segurança do Azure Active Directory (AD do Azure) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorre no ambiente do Azure AD. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Como monitorar a atividade de identidade e acesso dos usuários na central de segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: os clusters do Azure HDInsight com o Enterprise Security Package configurado podem ser conectados a um domínio para que os usuários do domínio possam usar suas credenciais de domínio para autenticar. Use locais nomeados de acesso condicional para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

Os clusters do Azure HDInsight com o Enterprise Security Package (ESP) configurado podem ser conectados a um domínio para que os usuários do domínio possam usar suas credenciais de domínio para autenticar com os clusters.

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Como configurar Enterprise Security Package com Azure AD Domain Services no Azure HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: Use a autenticação do Azure Active Directory (Azure AD) com o cluster HDInsight do Azure. O Azure AD fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo.

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: Use os logs de entrada e de auditoria do Azure Active Directory (Azure AD) para monitorar as tentativas de acesso às contas desativadas; esses logs podem ser integrados a qualquer ferramenta de monitoramento/SIEM de terceiros.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD, enviando os logs de auditoria e os logs de entrada para um espaço de trabalho do Azure Log Analytics. Configure os alertas desejados no espaço de trabalho Log Analytics do Azure.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: os clusters do Azure HDInsight com o Enterprise Security Package (ESP) configurado podem ser conectados a um domínio para que os usuários do domínio possam usar suas credenciais de domínio para autenticar com os clusters. Use o recurso de proteção de risco do Azure Active Directory (Azure AD) para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Além disso, você pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

- [Como exibir entradas suspeitas do Azure Active Directory](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: não disponível; Sistema de Proteção de Dados do Cliente ainda não tem suporte para o Azure HDInsight.

- [Lista de serviços com suporte Sistema de Proteção de Dados do Cliente](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: manter um inventário de informações confidenciais

**Orientação**: use marcas em recursos relacionados às implantações do Azure HDInsight para auxiliar no rastreamento de recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Os clusters do Azure HDInsight e as contas de armazenamento associadas devem ser separados por rede virtual/sub-rede, marcados adequadamente e protegidos em um NSG (grupo de segurança de rede) ou firewall do Azure. Os dados do cluster devem estar contidos em uma conta de armazenamento do Azure protegida ou Azure Data Lake Storage (Gen1 ou Gen2).

- [Escolha as opções de armazenamento para o cluster HDInsight do Azure](hdinsight-hadoop-compare-storage-options.md)

- [Como proteger Azure Data Lake Storage](../data-lake-store/data-lake-store-security-overview.md)

- [Como proteger contas de armazenamento do Azure](/azure/storage/common/storage-security-guide)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: para clusters HDInsight do Azure que armazenam ou processam informações confidenciais, marque o cluster e os recursos relacionados como confidenciais usando marcas. Para reduzir o risco de perda de dados por meio de vazamento, restrinja o tráfego de rede de saída para clusters do Azure HDInsight usando o Firewall do Azure.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Como restringir o tráfego de saída para clusters do Azure HDInsight com o Firewall do Azure](hdinsight-restrict-outbound-traffic.md)

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: criptografar todas as informações confidenciais em trânsito. Verifique se todos os clientes que se conectam ao cluster do Azure HDInsight ou armazenamentos de dados de cluster (contas de armazenamento do Azure ou Azure Data Lake Storage Gen1/Gen2) são capazes de negociar o TLS 1,2 ou superior. Microsoft Azure recursos negociará o TLS 1,2 por padrão. 

- [Entender Azure Data Lake Storage criptografia em trânsito](../data-lake-store/data-lake-store-security-overview.md)

- [Entender a criptografia da conta de armazenamento do Azure em trânsito](../storage/blobs/security-recommendations.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o armazenamento do Azure ou para os recursos de computação. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos 

**Orientação**: com o Azure HDInsight Enterprise Security Package (ESP), você pode usar o Apache Ranger para criar e gerenciar políticas de ofuscamento de dados e controle de acesso refinado para seus dados armazenados em arquivos, pastas, bancos de dados, tabelas e linhas/colunas. O administrador do Hadoop pode configurar o RBAC (controle de acesso baseado em função) para proteger Apache Hive, HBase, Kafka e Spark usando esses plug-ins no Apache Ranger.

A configuração de políticas RBAC com o Apache Ranger permite que você associe permissões a uma função na organização. Essa camada de abstração facilita garantir que as pessoas tenham apenas as permissões necessárias para realizar suas responsabilidades de trabalho.

- [Enterprise Security Package configurações com os serviços de domínio do Azure Active Directory (AD do Azure) no HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Visão geral da segurança empresarial no Azure HDInsight](domain-joined/hdinsight-security-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretrizes**: para clusters HDInsight do Azure que armazenam ou processam informações confidenciais, marque o cluster e os recursos relacionados como confidenciais usando marcas. Os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o armazenamento do Azure ou recursos de computação. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Orientação**: se estiver usando o banco de dados SQL do Azure para armazenar os metadados Apache Hive e Apache Oozie, verifique se os dados do SQL permanecem criptografados em todos os momentos. Para contas de armazenamento do Azure e Data Lake Storage (Gen1 ou Gen2), é recomendável permitir que a Microsoft gerencie suas chaves de criptografia, no entanto, você tem a opção de gerenciar suas próprias chaves.

- [Como gerenciar chaves de criptografia para contas de armazenamento do Azure](/azure/storage/common/storage-encryption-keys-portal)

- [Como criar Azure Data Lake Storage usando chaves de criptografia gerenciadas pelo cliente](../data-lake-store/data-lake-store-get-started-portal.md)

- [Entender a criptografia do banco de dados SQL do Azure](/azure/sql-database/sql-database-technical-overview#data-encryption)

- [Como configurar Transparent Data Encryption para o banco de dados SQL usando chaves gerenciadas pelo cliente](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Orientação**: definir configurações de diagnóstico para contas de armazenamento do Azure associadas aos clusters do Azure HDInsight para monitorar e registrar todas as operações CRUD em relação aos dados do cluster. Habilite a auditoria para qualquer conta de armazenamento ou armazenamentos Data Lake associados ao cluster HDInsight do Azure.

- [Como habilitar o registro em log/auditoria adicional para uma conta de armazenamento do Azure](/azure/storage/common/storage-monitor-storage-account)

- [Como habilitar o registro em log/auditoria adicional para Azure Data Lake Storage](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretrizes**: Implemente uma solução de gerenciamento de vulnerabilidades de terceiros.

Opcionalmente, se você tiver um Rapid7, Qualys ou qualquer outra assinatura da plataforma de gerenciamento de vulnerabilidades, poderá usar ações de script para instalar agentes de avaliação de vulnerabilidade em seus nós de cluster do Azure HDInsight e gerenciar os nós por meio do respectivo Portal.

- [Como instalar o agente do Rapid7 manualmente](https://insightvm.help.rapid7.com/docs/install)

- [Como instalar o agente do Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

- [Como usar ações de script](hdinsight-hadoop-customize-cluster-linux.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretrizes**: as atualizações automáticas do sistema foram habilitadas para imagens de nó de cluster, no entanto, você deve reinicializar os nós de cluster periodicamente para garantir que as atualizações

Microsoft para manter e atualizar as imagens de nó base do Azure HDInsight.

- [Como configurar a agenda de aplicação de patch do sistema operacional para clusters HDInsight](hdinsight-os-patching.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: implantar solução de gerenciamento de patch automatizado para títulos de software de terceiros

**Orientação**: Use as ações de script ou outros mecanismos para corrigir seus clusters do Azure HDInsight. Clusters recém-criados sempre terão as atualizações mais recentes disponíveis, incluindo os mais recentes patches de segurança.

- [Como configurar o agendamento de aplicação de patch do sistema operacional para clusters do Azure HDInsight baseados em Linux](hdinsight-os-patching.md)

- [Como usar ações de script](hdinsight-hadoop-customize-cluster-linux.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: comparar verificações de vulnerabilidade consecutivas

**Diretrizes**: implemente uma solução de gerenciamento de vulnerabilidades de terceiros que tenha a capacidade de comparar verificações de vulnerabilidade ao longo do tempo. Se você tiver uma assinatura do Rapid7 ou do Qualys, poderá usar o portal desse fornecedor para exibir e comparar as verificações de vulnerabilidade de back-to-back.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: Use um programa de pontuação de risco comum (por exemplo, o Sistema de pontuação de vulnerabilidade comum) ou as classificações de risco padrão fornecidas pela ferramenta de verificação de terceiros.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos, etc.), incluindo clusters HDInsight do Azure, em suas assinaturas. Verifique se você tem permissões (leitura) apropriadas em seu locatário e é capaz de enumerar todas as assinaturas do Azure, bem como os recursos nas suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do grafo de recursos do Azure, é altamente recomendável criar e usar Azure Resource Manager recursos no futuro.

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar ativos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gerenciamento](/azure/governance/management-groups/create)

- [Como criar e usar marcas](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: definir a lista de recursos aprovados do Azure e o software aprovado para seus recursos de computação

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Use o grafo de recursos do Azure para consultar e descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Diretrizes**: implemente uma solução de terceiros para monitorar nós de cluster para aplicativos de software não aprovados.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos, etc.), incluindo clusters HDInsight do Azure, em suas assinaturas.  Remova os recursos do Azure não aprovados que você descobrir. Para nós de cluster do Azure HDInsight, implemente uma solução de terceiros para remover ou alertar sobre software não aprovado.

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="68-use-only-approved-applications"></a>6.8: usar somente aplicativos aprovados

**Diretrizes**: para nós de cluster do Azure HDInsight, implemente uma solução de terceiros para impedir a execução de software não autorizado.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Para saber mais, consulte as referências a seguir:

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Diretrizes**: para nós de cluster do Azure HDInsight, implemente uma solução de terceiros para impedir a execução de tipos de arquivo não autorizados.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de "Gerenciamento do Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. HDInsight" para criar políticas personalizadas para auditar ou impor a configuração de rede do cluster HDInsight.

- [Como exibir os aliases de Azure Policy disponíveis](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Diretrizes**: imagens do sistema operacional Azure HDInsight gerenciadas e mantidas pela Microsoft. Cliente responsável pela implementação de configurações seguras para o sistema operacional de nós de cluster.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Diretrizes**: Use Azure Policy [Deny] e [implantar se não existir] para impor configurações seguras para seus clusters do Azure HDInsight e recursos relacionados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretrizes**: imagens do sistema operacional Azure HDInsight gerenciadas e mantidas pela Microsoft. Cliente responsável pela implementação da configuração de estado no nível do sistema operacional.

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Orientação**: se estiver usando definições de Azure Policy personalizadas, use o Azure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

- [Azure Repos o tutorial do git](/azure/devops/repos/git/gitworkflow)

- [Documentação do Azure Repos](/azure/devops/repos/index)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. HDInsight" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

**Diretrizes**: implemente uma solução de terceiros para manter o estado desejado para os sistemas operacionais do nó de cluster.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. HDInsight" para criar políticas personalizadas para auditar ou impor a configuração do cluster HDInsight.

- [Como exibir os aliases de Azure Policy disponíveis](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretrizes**: implemente uma solução de terceiros para monitorar o estado dos sistemas operacionais do nó de cluster.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Diretrizes**: o Azure HDInsight inclui suporte a Bring your own Key (BYOK) para Apache Kafka. Essa funcionalidade permite que você tenha e gerencie as chaves usadas para criptografar dados em repouso.

Todos os discos gerenciados no Azure HDInsight são protegidos com o Azure Criptografia do Serviço de Armazenamento (SSE). Por padrão, os dados nesses discos são criptografados usando chaves gerenciadas pela Microsoft. Se você habilitar o BYOK, você fornecerá a chave de criptografia do Azure HDInsight para usá-la e gerenciá-la usando Azure Key Vault.

Key Vault também pode ser usado com implantações do Azure HDInsight para gerenciar chaves para armazenamento de cluster (contas de armazenamento do Azure e Azure Data Lake Storage)

- [Como trazer sua própria chave para Apache Kafka no Azure HDInsight](/azure/hdinsight/kafka/apache-kafka-byok)

- [Como gerenciar chaves de criptografia para contas de armazenamento do Azure](/azure/storage/common/storage-encryption-keys-portal)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Orientação**: identidades gerenciadas podem ser usadas no Azure HDInsight para permitir que seus clusters acessem os serviços de domínio Azure Active Directory (Azure AD), acessar Azure Key Vault ou acessar arquivos no Azure data Lake Storage Gen2.

- [Entenda as identidades gerenciadas com o Azure HDInsight](hdinsight-managed-identities.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Orientação**: se estiver usando qualquer código relacionado à sua implantação do Azure HDInsight, você poderá implementar o verificador de credenciais para identificar as credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Diretrizes**: o Azure HDInsight vem com o clamscan pré-instalado e habilitado para as imagens de nó de cluster, no entanto, você deve gerenciar o software e agregar/monitorar manualmente todos os logs que o clamscan produz.

- [Entender o clamscan para o Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure, no entanto, ele não é executado no conteúdo do cliente.

Examine previamente todos os arquivos que estão sendo carregados em todos os recursos do Azure relacionados à implantação do cluster HDInsight do Azure, como Data Lake Storage, armazenamento de BLOBs etc. A Microsoft não pode acessar dados do cliente nessas instâncias.

- [Entender o Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais](../security/fundamentals/antimalware.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: garantir que o software antimalware e as assinaturas sejam atualizados

**Diretrizes**: o Azure HDInsight vem com o clamscan pré-instalado e habilitado para as imagens de nó de cluster. O clamscan executará atualizações de mecanismos e definições automaticamente, no entanto, a agregação e o gerenciamento de logs precisarão ser executados manualmente.

- [Entender o clamscan para o Azure Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares

**Orientação**: ao usar uma conta de armazenamento do Azure para o armazenamento de dados do cluster HDInsight, escolha a opção de redundância apropriada (lRS, ZRS, grs, ra-grs).  Ao usar um banco de dados SQL do Azure para o armazenamento de dados do cluster HDInsight do Azure, configure a replicação geográfica ativa.

- [Como configurar a redundância de armazenamento para contas de armazenamento do Azure](../storage/common/storage-redundancy.md)

- [Como configurar a redundância para o banco de dados SQL do Azure](/azure/sql-database/sql-database-active-geo-replication)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Orientação**: ao usar uma conta de armazenamento do Azure para o armazenamento de dados do cluster HDInsight do Azure, escolha a opção de redundância apropriada (lRS, ZRS, grs, ra-grs). Se estiver usando Azure Key Vault para qualquer parte da sua implantação do Azure HDInsight, verifique se o backup das suas chaves é feito.

- [Escolha as opções de armazenamento para o cluster HDInsight do Azure](hdinsight-hadoop-compare-storage-options.md)

- [Como configurar a redundância de armazenamento para contas de armazenamento do Azure](../storage/common/storage-redundancy.md)

- [Como fazer backup de chaves de Key Vault no Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: se Azure Key Vault estiver sendo usada com sua implantação do Azure HDInsight, teste a restauração de chaves de backup gerenciadas pelo cliente.

- [Como trazer sua própria chave para Apache Kafka no Azure HDInsight](/azure/hdinsight/kafka/apache-kafka-byok)

- [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: se Azure Key Vault estiver sendo usada com sua implantação do Azure HDInsight, habilite a exclusão reversível no Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.

- [Como habilitar a exclusão reversível Azure Key Vault](/azure/key-vault/key-vault-ovw-soft-delete)

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

**Diretrizes**: a central de segurança atribui uma severidade aos alertas, para ajudá-lo a priorizar a ordem em que você participa de cada alerta, para que, quando um recurso for comprometido, você possa acessá-lo imediatamente. A gravidade se baseia em quão confiante a central de segurança está na localização ou na análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Veja a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal.

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

**Diretrizes**: siga as regras de teste de penetração Microsoft Cloud do Engagement para garantir que seus testes de penetração não estejam violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft. 

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
