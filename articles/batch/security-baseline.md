---
title: Linha de base de segurança do Azure para lote
description: A linha de base de segurança do lote fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: batch
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d8ef3efcda55868abec188c10ef904ae40a56722
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728137"
---
# <a name="azure-security-baseline-for-batch"></a>Linha de base de segurança do Azure para lote

Essa linha de base de segurança aplica-se à orientação da [versão 1,0 do benchmark de segurança do Azure](../security/benchmarks/overview-v1.md) para o lote. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao lote. Os **controles** não aplicáveis ao lote foram excluídos.

 
Para ver como o lote é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança de lote completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: implantar pools do lote do Azure em uma rede virtual. Para permitir que os nós de computação do pool se comuniquem com segurança com outras máquinas virtuais, ou com uma rede local, você pode provisionar o pool em uma sub-rede de uma rede virtual do Azure. Além disso, a implantação do pool em uma rede virtual lhe dá controle sobre o NSG (grupo de segurança de rede) usado para proteger a NIC (interfaces de rede) dos nós individuais, bem como a sub-rede. Configure o NSG para permitir o tráfego somente de IP confiável/Locations na Internet.

Quando aplicável, desabilite o acesso à rede pública usando o link privado do Azure para se conectar à conta do lote do Azure por meio de um ponto de extremidade privado. O serviço de vínculo privado do Azure é protegido e aceita conexões somente de pontos de extremidade privados autenticados e autorizados. Além disso, você pode limitar a conectividade e a descoberta desabilitando pontos de extremidade RDP/SSH expostos publicamente para nós de computação em um pool do lote.

- [Como criar um pool do lote do Azure em uma rede virtual](batch-virtual-network.md)

- [Como criar uma conta do lote do Azure com acesso à rede desabilitado](private-connectivity.md)

- [Como criar um ponto de extremidade privado](../private-link/create-private-endpoint-portal.md)

- [Como negar o acesso ao tráfego RDP/SSH](pool-endpoint-configuration.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Use a central de segurança do Azure e corrija as recomendações de proteção de rede relacionadas ao NSG (rede virtual/grupo de segurança de rede) associado ao pool do lote. Habilite os logs de fluxo no NSG que está sendo usado para proteger o pool do lote e envie os logs para uma conta de armazenamento do Azure para auditoria de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho do Azure Log Analytics e usar o Análise de Tráfego do Azure para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens do Azure Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar as configurações de configuração de rede.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: habilitar a proteção padrão do DDoS do Azure (negação de serviço distribuído) na rede virtual, protegendo seu pool do lote do Azure para proteção contra ataques de DDoS. Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

- [Como configurar a proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

- [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](../security-center/azure-defender.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: Habilitar logs de fluxo no NSG (grupo de segurança de rede) que está sendo usado para proteger o pool do lote do Azure e enviar logs para uma conta de armazenamento do Azure para auditoria de tráfego.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: se necessário para fins de conformidade, selecione um dispositivo de rede virtual no Azure Marketplace que ofereça suporte a sistemas de detecção de intrusão (IDs) e funcionalidade de sistemas de prevenção de intrusão (IPS) com recursos de inspeção de conteúdo.

Se a detecção de intrusão e/ou prevenção baseada na inspeção de carga não for um requisito, o Firewall do Azure com inteligência contra ameaças poderá ser usado. A filtragem baseada em inteligência de ameaças do firewall do Azure pode alertar e negar o tráfego de e para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.

Implante o Firewall do Azure com um endereço IP público na mesma rede virtual que os nós do pool do lote do Azure. Configure as regras de NAT (conversão de endereços de rede) entre locais confiáveis na Internet e os endereços IP privados de seus nós de pool individuais. No firewall do Azure, em inteligência contra ameaças, configure "alerta e negação" para bloquear o alerta e bloquear o tráfego de/para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e domínios são originados do feed do Microsoft Threat Intelligence e apenas os registros de confiança mais altos são incluídos. 

- [Como criar um pool do lote do Azure em uma rede virtual](batch-virtual-network.md)

- [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede ou firewalls do Azure associados aos pools do lote do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

- [Entender e usar marcas de serviço](../virtual-network/service-tags-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede associados aos pools do lote do Azure com Azure Policy. Use aliases de Azure Policy nos namespaces "Microsoft.Batch" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de seus pools do lote do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: use marcas para NSGs (grupos de serviço de rede) e outros recursos relacionados à segurança de rede e ao fluxo de tráfego associados aos seus pools do lote do Azure. Para regras de NSG individuais, use o campo "Descrição" para especificar a necessidade empresarial e/ou a duração (etc.) para regras que permitam tráfego de/para uma rede.

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar o Azure PowerShell ou a CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma rede virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados aos seus pools do lote do Azure. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log) 

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Diretrizes**: integrar a conta do lote do Azure ao Azure monitor para agregar dados de segurança gerados pelos dispositivos de cluster. Aproveite as consultas personalizadas para detectar e responder a ameaças no ambiente.  Para o monitoramento em nível de recursos do lote do Azure, use as APIs do lote para monitorar ou consultar o status de seus recursos, incluindo trabalhos, tarefas, nós e pools.

- [Como carregar uma conta do lote do Azure para Azure Monitor](batch-diagnostics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: para o monitoramento em nível de conta do lote do Azure, monitore cada conta do lote usando os recursos de Azure monitor. O Azure Monitor coleta métricas e, opcionalmente, logs de diagnóstico para recursos com escopo no nível de uma conta do Lote, como pools, trabalhos e tarefas. Colete e consuma esses dados manualmente ou programaticamente para monitorar atividades na conta do Lote e para diagnosticar problemas.

Para o monitoramento em nível de recurso do lote do Azure, use as APIs do lote do Azure para monitorar ou consultar o status de seus recursos, incluindo trabalhos, tarefas, nós e pools.

- [Como configurar o monitoramento e o registro em log no nível da conta do lote do Azure](monitoring-overview.md)

- [Entender o monitoramento em nível de recurso do lote](monitoring-overview.md#batch-resource-monitoring)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

#### <a name="azure-policy-built-in-definitions"></a>Azure Policy definições internas

[!INCLUDE [microsoft.batch-2-3](../../includes/policy/standards/asb/rp-controls/microsoft.batch-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Diretrizes**: o Azure monitor coleta métricas e logs de diagnóstico para recursos em sua conta do lote do Azure. Colete e consuma esses dados de várias maneiras para monitorar sua conta do lote do Azure e diagnosticar problemas. Também é possível configurar alertas de métrica para receber notificações quando uma métrica alcançar um valor especificado.

Se necessário, você pode se conectar a seus nós de pool individuais via SSH (shell protegido) ou protocolo RDP (RDP) para acessar os logs do sistema operacional local.

- [Como coletar logs de diagnóstico de sua conta do lote do Azure](batch-diagnostics.md#batch-diagnostics)

- [Como se conectar remotamente aos nós do pool do lote do Azure](./batch-service-workflow-features.md#basic-workflow)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: integrar a conta do lote do Azure ao Azure monitor. Verifique se o espaço de trabalho do Azure Log Analytics usado tem o período de retenção de log definido de acordo com os regulamentos de conformidade da sua organização

- [Como configurar o log e o monitoramento do lote do Azure](monitoring-overview.md)

- [Como configurar o período de retenção do espaço de trabalho do Azure Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Orientação**: criar alertas de métrica do lote do Azure que são disparados quando o valor de uma métrica especificada ultrapassa um determinado limite.

- [Como configurar alertas de métrica do lote do Azure](batch-diagnostics.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Orientação**: criar alertas de métrica do lote do Azure que são disparados quando o valor de uma métrica especificada ultrapassa um determinado limite.

- [Como configurar alertas de métrica do lote do Azure](batch-diagnostics.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Orientação**: Use o Windows Defender em seus nós de lote individuais no caso de sistemas operacionais Windows ou forneça sua própria solução antimalware se você estiver usando o Linux.

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS

**Orientação**: implementar solução de terceiros para registro em log DNS.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: habilitar o registro em log de auditoria de linha de comando

**Diretrizes**: configure manualmente o log do console e a transcrição do PowerShell em uma base por nó.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Orientação**: manter o registro da conta administrativa local que é criada durante o provisionamento do pool do lote do Azure, bem como qualquer outra conta que você criar. Além disso, se Azure Active Directory integração for usada, o Azure AD terá funções internas que devem ser explicitamente atribuídas e, portanto, podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Além disso, você pode usar as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Como monitorar a identidade e o acesso à central de segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Orientação**: ao provisionar um pool do lote do Azure, você terá a opção de criar contas do computador local. Não há nenhuma senha padrão a ser alterada; no entanto, você pode especificar senhas diferentes para acesso de shell seguro (SSH) e protocolo RDP (RDP). Depois que o pool do lote do Azure tiver sido configurado, você poderá gerar um usuário aleatório para nós individuais dentro do portal do Azure ou via API Azure Resource Manager.

- [Como adicionar um usuário a um nó de computação específico](/rest/api/batchservice/computenode/adduser)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Diretrizes**: integre a autenticação para aplicativos do lote do Azure com Azure Active Directory. Crie políticas e procedimentos em relação ao uso de contas administrativas dedicadas.

Além disso, você pode usar as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

- [Como autenticar aplicativos do lote com Azure Active Directory](batch-aad-auth.md)

- [Como monitorar a identidade e o acesso à central de segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretrizes**: integre a autenticação para aplicativos do lote do Azure com Azure Active Directory. Habilite a autenticação multifator do Azure AD e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

 

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usar estações de trabalho seguras e gerenciadas pelo Azure para tarefas administrativas

**Diretrizes**: Use PAWs (estações de trabalho com acesso privilegiado) com a autenticação multifator configurada para fazer logon e configurar seus recursos do lote do Azure.

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Orientação**: se você tiver a autenticação integrada para aplicativos do lote do Azure com Azure Active Directory, use Azure Active Directory relatórios de segurança para a geração de logs e alertas quando ocorrer atividade suspeita ou insegura no ambiente. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na central de segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: se você tiver a autenticação integrada para aplicativos do lote do Azure com Azure Active Directory, poderá usar os locais nomeados de acesso condicional para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory como o sistema central de autenticação e autorização e integre a autenticação para aplicativos do lote do Azure com o Azure AD. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

- [Como criar e configurar uma instância do Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Como autenticar aplicativos do lote com o Azure AD](batch-aad-auth.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Revisar e reconciliar regularmente o acesso do usuário

**Diretriz**: O Azure Active Directory fornece logs para ajudar a descobrir contas obsoletas. Além disso, você pode usar as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso dos usuários pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo.

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: Crie configurações de diagnóstico para contas de usuário Azure Active Directory, enviando logs de auditoria e logs de entrada para um espaço de trabalho do Azure log Analytics. Configure os alertas desejados no espaço de trabalho Log Analytics do Azure.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: Use Azure Active Directory recurso de detecção de riscos e de proteção de identidade para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Além disso, você pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte  

**Orientação**: não disponível; Sistema de Proteção de Dados do Cliente ainda não tem suporte para o lote do Azure.
 
- [Lista de serviços com suporte Sistema de Proteção de Dados do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Os pools do lote do Azure devem ser separados por rede virtual/sub-rede, marcados adequadamente e protegidos com um NSG (grupos de segurança de rede). Os dados do lote do Azure devem estar contidos em uma conta de armazenamento do Azure protegida.

- [Como criar um pool do lote do Azure em uma rede virtual](batch-virtual-network.md)

- [Como proteger contas de armazenamento do Azure](../storage/blobs/security-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: para contas de armazenamento do Azure associadas aos pools do lote do Azure que contêm informações confidenciais, marque-as como confidenciais usando marcas e proteja-as com as práticas recomendadas do Azure.

Os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o armazenamento do Azure ou recursos de computação. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

- [Como proteger contas de armazenamento do Azure](../storage/blobs/security-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações confidenciais em trânsito

**Diretrizes**: criptografar todas as informações confidenciais em trânsito. Microsoft Azure recursos negociará o TLS 1,2 por padrão. Verifique se todos os clientes que se conectam a seus pools de lote do Azure ou armazenamentos de dados (contas de armazenamento do Azure) são capazes de negociar o TLS 1,2 ou superior.

Verifique se o HTTPS é necessário para acessar a conta de armazenamento que contém os dados do lote do Azure.

- [Entender a criptografia da conta de armazenamento do Azure em trânsito](../storage/blobs/security-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: para contas de armazenamento do Azure associadas aos pools do lote do Azure que contêm informações confidenciais, marque-as como confidenciais usando marcas e proteja-as com as práticas recomendadas do Azure.

Os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o armazenamento do Azure ou recursos de computação. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

- [Como proteger contas de armazenamento do Azure](../storage/blobs/security-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usar o controle de acesso baseado em função para controlar o acesso aos recursos

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para controlar o acesso ao plano de gerenciamento de recursos do Azure, incluindo conta do lote, pools do lote e contas de armazenamento.

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Orientação**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o armazenamento do Azure ou para os recursos de computação. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretrizes**: para contas de armazenamento associadas à sua conta do lote do Azure, é recomendável permitir que a Microsoft gerencie chaves de criptografia, no entanto, você tem a opção de gerenciar suas próprias chaves, se necessário.

O Azure Disk Encryption pode ser usado para ajudar a proteger e proteger seus dados para atender aos compromissos de conformidade e segurança organizacional. Todos os discos gerenciados, instantâneos, imagens e dados gravados em discos existentes são automaticamente criptografados em repouso com chaves gerenciadas pela plataforma.

- [Como gerenciar chaves de criptografia para contas de armazenamento do Azure](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Como configurar chaves de criptografia gerenciadas pelo cliente](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Como criar um pool com a criptografia de disco habilitada](disk-encryption.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para recursos críticos do Azure relacionados ao ou associados às suas contas/pools do lote do Azure.

Defina as configurações de diagnóstico para contas de armazenamento associadas ao pool do lote do Azure para monitorar e registrar todas as operações CRUD em relação aos dados do pool.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Como habilitar o registro em log/auditoria adicional para uma conta de armazenamento do Azure](../storage/common/manage-storage-analytics-logs.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Orientação**: para nós do pool do lote do Azure, você é responsável por gerenciar a solução de gerenciamento de vulnerabilidades.

Opcionalmente, se você tiver um Rapid7, Qualys ou qualquer outra assinatura da plataforma de gerenciamento de vulnerabilidades, poderá instalar manualmente agentes de avaliação de vulnerabilidade em nós do pool do lote e gerenciar nós por meio do portal respectivo.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretrizes**: Microsoft para manter e atualizar as imagens de nó do pool do lote do Azure base. Verifique se o sistema operacional dos nós do pool do lote do Azure permanece corrigido durante o tempo de vida do cluster, o que pode exigir a habilitação de atualizações automáticas, o monitoramento dos nós ou a realização de reinicializações periódicas.

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: implantar solução de gerenciamento de patch automatizado para títulos de software de terceiros

**Orientação**: Verifique se os aplicativos de terceiros dos nós do pool do lote do Azure permanecem corrigidos durante o tempo de vida do cluster, o que pode exigir a habilitação de atualizações automáticas, o monitoramento dos nós ou a realização de reinicializações periódicas.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Diretrizes**: se você tiver um Rapid7, Qualys ou qualquer outra assinatura da plataforma de gerenciamento de vulnerabilidades, poderá usar o portal desse fornecedor para exibir e comparar verificações de vulnerabilidade de back-to-back.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: Use um programa de pontuação de risco comum (por exemplo, o Sistema de pontuação de vulnerabilidade comum) ou as classificações de risco padrão fornecidas pela ferramenta de verificação de terceiros.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, etc.) em suas assinaturas. Verifique se você tem permissões apropriadas (leitura) em seu locatário e se é capaz de enumerar todas as assinaturas do Azure, bem como recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Gerenciador de gráficos de recursos do Azure, é altamente recomendável criar e usar Azure Resource Manager recursos no futuro.

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar ativos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: definir a lista de recursos aprovados do Azure e o software aprovado para recursos de computação

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Use o grafo de recursos do Azure para consultar/descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Orientação**: para nós do pool do lote do Azure, implemente uma solução de terceiros para monitorar os nós de cluster para aplicativos de software não aprovados.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: para nós do pool do lote do Azure, implemente uma solução de terceiros para monitorar os nós de cluster para aplicativos de software não aprovados.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Usar somente aplicativos aprovados

**Diretrizes**: para nós do pool do lote do Azure, implemente uma solução de terceiros para impedir a execução de software não autorizado.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna: 
- Tipos de recursos não permitidos 
- Tipos de recursos permitidos 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Orientação**: para nós do pool do lote do Azure, implemente uma solução de terceiros para impedir a execução de tipos de arquivo não autorizados.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de "Gerenciamento do Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft.Batch" para criar políticas personalizadas para auditar ou impor a configuração de suas contas e pools do lote do Azure.

- [Como exibir os aliases de Azure Policy disponíveis](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Orientação**: estabelecer configurações seguras para o sistema operacional dos nós do pool do lote.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Orientação**: Use Azure Policy [Deny] e [implantar se não existir] para impor configurações seguras para os recursos do Azure relacionados à sua conta do lote e pools (como redes virtuais, sub-redes, firewalls do Azure, contas de armazenamento do Azure, etc.). Você pode usar Azure Policy aliases dos seguintes namespaces para criar políticas personalizadas:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretrizes**: imagens do sistema operacional do pool do lote do Azure gerenciadas e mantidas pela Microsoft. Você é responsável pela implementação da configuração de estado no nível do sistema operacional.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Diretrizes**: se você estiver usando definições de Azure Policy personalizadas para suas contas do lote do Azure, pools ou recursos relacionados, use Azure Repos para armazenar e gerenciar seu código com segurança.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentação do Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Diretrizes**: se você estiver usando imagens personalizadas para seus pools do lote do Azure, use o RBAC (controle de acesso baseado em função) para garantir que somente usuários autorizados possam acessar as imagens.

- [Entender o RBAC no Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Como configurar o RBAC no Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Diretrizes**: Use definições de Azure Policy internas para alertar, auditar e impor configurações de recursos relacionados ao lote do Azure.  Use Azure Policy aliases no namespace "Microsoft.Batch" para criar políticas personalizadas para suas contas e pools do lote do Azure. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

**Diretrizes**: implemente uma solução de terceiros para manter o estado desejado para os sistemas operacionais dos nós do pool do lote do Azure.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft.Batch" para criar políticas personalizadas para auditar ou impor a configuração da instância do lote do Azure. Você também pode usar quaisquer políticas internas criadas especificamente para o lote do Azure ou os recursos usados pelo lote do Azure, como:
- As sub-redes devem ser associadas a um grupo de segurança de rede-as contas de armazenamento devem usar um ponto de extremidade de serviço de rede virtual
- Os logs de diagnóstico em contas do Lote devem ser habilitados

- [Como exibir os aliases de Azure Policy disponíveis](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretrizes**: implemente uma solução de terceiros para monitorar o estado dos sistemas operacionais dos nós do pool do lote do Azure.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: Azure Key Vault podem ser usadas com implantações do lote do Azure para gerenciar chaves para armazenamento de pools em contas de armazenamento do Azure.

- [Como integrar com identidades gerenciadas do Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um Azure Key Vault](../key-vault/general/quick-create-portal.md)

- [Como autenticar-se no Key Vault](../key-vault/general/authentication.md)
- [Como atribuir uma política de acesso de Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Orientação**: Use o Windows Defender em seus nós individuais do pool do lote do Azure no caso de sistemas operacionais Windows ou forneça sua própria solução antimalware se você estiver usando o Linux.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, lote do Azure), mas não é executado no conteúdo do cliente.

Pré-examinar todos os arquivos que estão sendo carregados em recursos que não são de computação do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de BLOBs, etc. A Microsoft não pode acessar dados do cliente nessas instâncias.

- [Entender o Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais](../security/fundamentals/antimalware.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: garantir que o software antimalware e as assinaturas sejam atualizados

**Orientação**: Use o Windows Defender em seus nós de pool de lote do Azure individuais no caso de sistemas operacionais Windows e verifique se a atualização automática está habilitada. Forneça sua própria solução antimalware se você estiver usando o Linux.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares

**Orientação**: ao usar uma conta de armazenamento do Azure para o armazenamento de dados do pool do lote do Azure, escolha a opção de redundância apropriada (lRS, ZRS, grs, ra-grs). 

- [Como configurar a redundância de armazenamento para contas de armazenamento do Azure](../storage/common/storage-redundancy.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Orientação**: ao usar uma conta de armazenamento do Azure para o armazenamento de dados do pool do lote do Azure, escolha a opção de redundância apropriada (lRS, ZRS, grs, ra-grs). Se estiver usando Azure Key Vault para qualquer parte da implantação do lote do Azure, verifique se o backup das suas chaves é feito.

- [Como configurar a redundância de armazenamento para contas de armazenamento do Azure](../storage/common/storage-redundancy.md)

- [Como fazer backup de chaves do cofre de chaves no Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: se você estiver gerenciando suas próprias chaves para contas de armazenamento do Azure ou qualquer outro recurso relacionado à sua implementação do lote do Azure, teste periodicamente a restauração de chaves de backup.

- [Como fazer backup de chaves do cofre de chaves no Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

- [Como restaurar uma chave gerenciada pelo cliente com o PowerShell](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: se Azure Key Vault estiver sendo usada para manter as chaves relacionadas às contas de armazenamento do pool do lote do Azure, habilite Soft-Delete no Azure Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.

- [Como habilitar a exclusão reversível no Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Orientação**: Verifique se há planos de resposta a incidentes escritos que definem funções de pessoal, bem como fases de manipulação/gerenciamento de incidentes.

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a central de segurança atribui uma severidade aos alertas, para ajudá-lo a priorizar a ordem em que você participa de cada alerta, para que, quando um recurso for comprometido, você possa acessá-lo imediatamente. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Veja a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretrizes**: as informações de contato de incidente de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que os dados foram acessados por uma parte ilegal ou não autorizada.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de "Aplicativos Lógicos" em alertas de segurança e recomendações.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: [siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não estejam em violação às políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

Você pode encontrar mais informações sobre a estratégia e a execução da Microsoft de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem gerenciada da Microsoft, serviços e aplicativos, abaixo: 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)