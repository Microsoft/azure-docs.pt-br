---
title: Linha de base de segurança do Azure para Azure Cosmos DB
description: A linha de base de segurança Azure Cosmos DB fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5f9cf6dba0a1e670e3a27cb3546de9237466e7ce
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659419"
---
# <a name="azure-security-baseline-for-azure-cosmos-db"></a>Linha de base de segurança do Azure para Azure Cosmos DB

Essa linha de base de segurança aplica as diretrizes da [1,0 versão de avaliação de segurança do Azure](../security/benchmarks/overview-v1.md) para Azure Cosmos DB. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao Azure Cosmos DB. **Controles** não aplicáveis a Azure Cosmos DB foram excluídos.

 
Para ver como Azure Cosmos DB é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base de segurança Azure Cosmos DB](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Orientação**: usando o link privado do Azure, você pode se conectar a uma conta do Azure Cosmos por meio de um ponto de extremidade privado. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você também pode reduzir o risco de vazamento de dados Configurando um conjunto estrito de regras de saída em um grupo de segurança de rede (NSG) e associando esse NSG à sua sub-rede.

Você também pode usar pontos de extremidade de serviço para proteger sua conta do Azure Cosmos. Habilitando um ponto de extremidade de serviço, você pode configurar contas do Azure Cosmos para permitir o acesso somente de uma sub-rede específica de uma rede virtual do Azure. Depois que o ponto de extremidade de serviço do Azure Cosmos DB estiver habilitado, você poderá limitar o acesso a uma conta do Azure cosmos com conexões de uma sub-rede em uma rede virtual.

Você também pode proteger os dados armazenados em sua conta do Azure Cosmos usando firewalls de IP. O Azure Cosmos DB é compatível com controles de acesso baseados em IP para suporte de firewall de entrada. Você pode definir um firewall IP na conta do Azure Cosmos usando o portal do Azure, Azure Resource Manager modelos ou por meio do CLI do Azure ou Azure PowerShell.

- [Visão geral do Link Privado do Azure](../private-link/private-link-overview.md)

- [Como configurar um ponto de extremidade privado para Azure Cosmos DB](how-to-configure-private-endpoints.md) 

- [Como criar um grupo de segurança de rede com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como configurar o firewall IP no Cosmos DB](how-to-configure-firewall.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas – Microsoft.DocumentDB**:

[!INCLUDE [Resource Policy for Microsoft.DocumentDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.documentdb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Use a central de segurança do Azure e siga as recomendações de proteção de rede para ajudar a proteger os recursos de rede relacionados à sua conta do Azure Cosmos.

Quando as máquinas virtuais são implantadas na mesma rede virtual que sua conta do Azure Cosmos, você pode usar um NSG (grupo de segurança de rede) para reduzir o risco de dados vazamento. Habilite logs de fluxo NSG e envie logs para uma conta de armazenamento do Azure para auditorias de tráfego. Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Orientação**: Use o recurso de compartilhamento de recursos entre origens (CORS) para habilitar um aplicativo Web em execução em um domínio para acessar recursos em outro domínio. Os navegadores da Web implementam uma restrição de segurança, conhecida como política de mesma origem, que impede que uma página da Web chame APIs em um domínio diferente. No entanto, o CORS fornece uma maneira segura de permitir que o domínio de origem chame APIs em outro domínio. Após habilitar o suporte para CORS na conta do Azure Cosmos, somente as solicitações autenticadas serão avaliadas para determinar se são permitidas de acordo com as regras especificadas.

- [Configurar compartilhamento de recursos entre origens](how-to-configure-cross-origin-resource-sharing.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Diretrizes**: Use a ATP (proteção avançada contra ameaças) para Azure Cosmos DB. O ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos. Essa camada de proteção permite que você resolva as ameaças e integre-as com sistemas de monitoramento de segurança central.

Habilite a proteção contra DDoS Standard nas redes virtuais associadas às suas instâncias de Azure Cosmos DB para proteger contra ataques de DDoS. Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

- [Como configurar Azure Cosmos DB proteção avançada contra ameaças](cosmos-db-advanced-threat-protection.md)

- [Como configurar a proteção contra DDoS](/azure/virtual-network/manage-ddos-protection)

- [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](/azure/security-center/security-center-alerts-service-layer)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Diretrizes**: Habilitar logs de fluxo do NSG (grupo de segurança de rede) e enviar logs para uma conta de armazenamento para auditoria de tráfego. Você pode enviar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: Use a ATP (proteção avançada contra ameaças) para Azure Cosmos DB. O ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos. Essa camada de proteção permite que você resolva as ameaças e integre-as com sistemas de monitoramento de segurança central. 

- [Como configurar Cosmos DB proteção avançada contra ameaças](cosmos-db-advanced-threat-protection.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: para recursos que precisam de acesso à sua conta do Azure Cosmos, use marcas de serviço de rede virtual para definir controles de acesso de rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, AzureCosmosDB) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

- [Para obter mais informações sobre como usar marcas de serviço](../virtual-network/service-tags-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede com Azure Policy. Use aliases de Azure Policy nos namespaces "Microsoft.DocumentDB" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de suas instâncias de Azure Cosmos DB. Você também pode fazer uso de definições de política internas para Azure Cosmos DB, como:

- Implantar a Proteção Avançada contra Ameaças em Contas do Cosmos DB

- O Cosmos DB deve usar um ponto de extremidade de serviço de rede virtual Microsoft Azure Cosmos DB

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos de Azure Resource Manager, controle de acesso baseado em função do Azure (RBAC do Azure) e políticas em uma única definição de Blueprint. É fácil aplicar o blueprint a novas assinaturas e novos ambientes e ajustar o controle e o gerenciamento por meio do controle de versão.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Diretrizes**: use marcas para recursos de rede associados à sua implantação do Azure Cosmos DB para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados às suas instâncias de Azure Cosmos DB. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos. 

- [Como exibir e recuperar eventos do log de atividades do Azure](/azure/azure-monitor/platform/activity-log-view)

- [Como criar alertas no Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: ingerir logs por meio de Azure monitor para agregar dados de segurança gerados pelo Azure Cosmos DB. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento para armazenamento de longo prazo/arquivamento. Como alternativa, você pode integrar dados ao Azure Sentinel ou a um SIEM (gerenciamento de eventos e incidentes de segurança) de terceiros. 

- [Como habilitar os logs de diagnóstico para Azure Cosmos DB](/azure/cosmos-db/logging)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: habilite as configurações de diagnóstico para Azure Cosmos DB e envie os logs para um espaço de trabalho log Analytics ou uma conta de armazenamento do Azure. As configurações de diagnóstico no Azure Cosmos DB são usadas para coletar logs de recursos. Esses logs são capturados por solicitação e também são chamados de **logs de plano de dados**. Alguns exemplos das operações do plano de dados incluem excluir, inserir e ler. 

Você também pode habilitar as configurações de diagnóstico do log de atividades do Azure e enviar esses logs para o mesmo espaço de trabalho Log Analytics usado para logs de Azure Cosmos DB.

- [Como habilitar as configurações de diagnóstico para Azure Cosmos DB](/azure/cosmos-db/logging)

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

**Diretrizes**: em Azure monitor, defina o período de retenção de log para log Analytics espaços de trabalho associados às suas instâncias de Azure Cosmos DB de acordo com os regulamentos de conformidade da sua organização.

- [Como definir parâmetros de retenção de log](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Orientação**: você pode executar consultas em log Analytics um espaço de trabalho para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos logs de Azure Cosmos DB que você coletou.

- [Como executar consultas para Azure Cosmos DB em espaços de trabalho do Log Analytics](monitor-cosmos-db.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Orientação**: na central de segurança do Azure, habilite a proteção avançada contra ameaças para Azure Cosmos DB monitorar atividades anormais em logs de segurança e eventos. Habilite as configurações de diagnóstico no Azure Cosmos DB e envie logs para um espaço de trabalho Log Analytics.

 

Você também pode integrar seu espaço de trabalho Log Analytics ao Azure Sentinel, pois ele fornece uma solução de disparar (resposta automatizada de orquestração de segurança). Assim os guias estratégicos (soluções automatizadas) podem ser criados e usados para corrigir problemas de segurança. Além disso, você pode criar alertas de log personalizados em seu espaço de trabalho do Log Analytics usando Azure Monitor.

- [Lista de alertas de proteção contra ameaças para Azure Cosmos DB](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Criar, exibir e gerenciar alertas de log usando o Azure Monitor](/azure/azure-monitor/platform/alerts-log)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Orientação**: você pode usar o painel iam (controle de acesso e identidade) no portal do Azure para configurar o RBAC (controle de acesso baseado em função) e manter o inventário em recursos de Azure Cosmos DB. As funções são aplicadas a usuários, grupos, entidades de serviço e identidades gerenciadas no Azure Active Directory (Azure AD). Você pode usar funções internas ou funções personalizadas para indivíduos e grupos.

O Azure Cosmos DB fornece o RBAC do Azure interno para cenários comuns de gerenciamento no Azure Cosmos DB. Um indivíduo que tem um perfil no Azure AD pode atribuir essas funções do Azure a usuários, grupos, entidades de serviço ou identidades gerenciadas para conceder ou negar acesso a recursos e operações em Azure Cosmos DB recursos.

Você também pode usar o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Além disso, algumas ações no Azure Cosmos DB podem ser controladas com o Azure AD e as chaves mestras específicas da conta. Use a configuração de conta ' disableKeyBasedMetadataWriteAccess ' para controlar o acesso à chave.

- [Entender o controle de acesso baseado em função no Azure Cosmos DB](role-based-access-control.md)

- [Crie suas próprias funções personalizadas usando ações de Azure Cosmos DB (Microsoft.Docnamespace umentDB)](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb)

- [Criar uma nova função no Azure AD](../role-based-access-control/custom-roles.md)

- [Como obter uma função de diretório no Azure AD com o PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Restringir o acesso do usuário apenas às operações de dados](how-to-restrict-user-data.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Orientação**: o conceito de senhas padrão ou em branco não existe em relação ao Azure Active Directory (Azure AD) ou Azure Cosmos DB. Em vez disso, Azure Cosmos DB usa dois tipos de chaves para autenticar usuários e fornecer acesso a seus dados e recursos; chaves mestras e tokens de recurso. As chaves podem ser geradas novamente a qualquer momento.

- [Noções básicas sobre o acesso seguro aos dados no Azure Cosmos DB](secure-access-to-data.md)

- [Como regenerar chaves Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys)

- [Como acessar chaves de forma programática usando o Azure AD](certificate-based-authentication.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Orientação**: não aplicável; Azure Cosmos DB não dá suporte a contas de administrador. Todo o acesso é integrado ao Azure Active Directory (AD do Azure) e ao RBAC (controle de acesso baseado em função) do Azure.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Diretrizes**: Azure Cosmos DB usa dois tipos de chaves para autorizar usuários e não oferece suporte a Sign-On único (SSO) no nível do plano de dados. O acesso ao plano de controle para Cosmos DB está disponível por meio da API REST e dá suporte a SSO. Para autenticar, defina o cabeçalho de autorização para suas solicitações para um token Web JSON obtido do Azure Active Directory (AD do Azure).

- [Entender a API REST do banco de dados do Azure para Cosmos DB](/rest/api/cosmos-db/)

- [Entender o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretrizes**: habilite a autenticação multifator do Azure Active Directory (Azure AD) e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use Paw (estações de trabalho com acesso privilegiado) com a autenticação multifator configurada para fazer logon e configurar os recursos do Azure. 

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](/security/compass/privileged-access-devices)
 
- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use a ATP (proteção avançada contra ameaças) para Azure Cosmos DB. O ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos. Essa camada de proteção permite que você resolva as ameaças e integre-as com sistemas de monitoramento de segurança central.

Além disso, você pode usar o Azure Active Directory (Azure AD) Privileged Identity Management (PIM) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorre no ambiente.

Use as detecções de risco do Azure AD para ver alertas e relatórios sobre o comportamento do usuário suspeito.

- [Como implantar o Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Entenda as detecções de risco do Azure Active Directory](/azure/active-directory/reports-monitoring/concept-risk-events)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: Configure a condição de local de uma política de acesso condicional e gerencie seus locais nomeados. Com os locais nomeados, você pode criar agrupamentos lógicos de intervalos de endereços IP ou países e regiões. Você pode restringir o acesso a recursos confidenciais, como suas instâncias de Azure Cosmos DB, aos locais nomeados configurados.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

- [Como criar e configurar uma instância do Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Como configurar e gerenciar a autenticação do Azure AD com o Azure SQL](/azure/sql-database/sql-database-aad-authentication-configure)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (Azure AD) fornece logs para ajudar a descobrir contas obsoletas. Além disso, você pode usar as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo.

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Orientação**: você pode criar configurações de diagnóstico para contas de usuário do Azure Active Directory (AD do Azure), enviando logs de auditoria e logs de entrada para um espaço de trabalho do log Analytics em que você pode configurar os alertas desejados.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: Use a ATP (proteção avançada contra ameaças) para Azure Cosmos DB. O ATP para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos. Essa camada de proteção permite que você resolva as ameaças e integre-as com sistemas de monitoramento de segurança central.

Você também pode usar o recurso de proteção de identidade e de detecção de riscos do Azure Active Directory (Azure AD) para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Além disso, você pode ingerir os logs no Azure Sentinel para uma investigação mais aprofundada.

- [Como exibir entradas suspeitas do Azure Active Directory](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: manter um inventário de informações confidenciais

**Diretrizes**: use marcas para auxiliar no rastreamento de Azure Cosmos DB instâncias que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Azure Cosmos DB instâncias são separadas por rede virtual/sub-rede, marcadas adequadamente e protegidas em um NSG (grupo de segurança de rede) ou firewall do Azure. Azure Cosmos DB instâncias que armazenam dados confidenciais devem ser isoladas. Usando o link privado do Azure, você pode se conectar a uma conta de instância do Azure Cosmos DB por meio de um ponto de extremidade privado. O ponto de extremidade privado é um conjunto de endereços IP privados em uma sub-rede dentro da rede virtual. Você pode então limitar o acesso aos endereços IP privados selecionados. 

- [Como criar assinaturas adicionais do Azure](/azure/billing/billing-create-subscription)

- [Como criar grupos de gerenciamento](/azure/governance/management-groups/create)

- [Como criar e usar marcas](/azure/azure-resource-manager/resource-group-using-tags)

- [Como configurar um ponto de extremidade privado para Azure Cosmos DB](how-to-configure-private-endpoints.md)

- [Como criar um grupo de segurança de rede com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: você pode implantar a proteção avançada contra ameaças para Azure Cosmos DB, o que detectará atividades anormais indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. Ela pode disparar os alertas a seguir simultaneamente:

- Acesso de locais incomuns

- Extração de dados incomum

Além disso, ao usar máquinas virtuais para acessar suas instâncias de Azure Cosmos DB, use o link privado, o firewall, os grupos de segurança de rede e as marcas de serviço para atenuar a possibilidade de vazamento de dados. A Microsoft gerencia a infraestrutura subjacente para Azure Cosmos DB e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

- [Como configurar Cosmos DB proteção avançada contra ameaças](cosmos-db-advanced-threat-protection.md)

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação de dados, classificação e prevenção de perda automática ainda não estão disponíveis para Azure Cosmos DB. No entanto, você pode usar a integração de Pesquisa Cognitiva do Azure para classificação e análise de dados. Você também pode implementar uma solução de terceiros, se necessário, para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Indexar dados de Azure Cosmos DB com o Pesquisa Cognitiva do Azure](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp;bc=/azure/cosmos-db/breadcrumb/toc.json)

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usar o controle de acesso baseado em função para controlar o acesso aos recursos

**Diretrizes**: o Azure Cosmos DB fornece o Azure RBAC (controle de acesso baseado em função) interno do Azure para cenários comuns de gerenciamento no Azure Cosmos DB. Um indivíduo que tem um perfil no Azure Active Directory (Azure AD) pode atribuir essas funções do Azure a usuários, grupos, entidades de serviço ou identidades gerenciadas para conceder ou negar acesso a recursos e operações em Azure Cosmos DB recursos. As atribuições de função têm como escopo o acesso ao plano de controle, que inclui acesso a contas, bancos de dados, contêineres e ofertas (taxa de transferência) do Azure Cosmos.

- [Como implementar o RBAC do Azure no Azure Cosmos DB](role-based-access-control.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretrizes**: todos os dados de usuário armazenados no cosmos DB são criptografados em repouso por padrão. Não há controles para desativá-lo. O Azure Cosmos DB usa a criptografia AES-256 em todas as regiões onde a conta está em execução.

Por padrão, a Microsoft gerencia as chaves que são usadas para criptografar os dados em sua conta do Azure Cosmos. Opcionalmente, você pode optar por adicionar uma segunda camada de criptografia com suas próprias chaves.

- [Noções básicas sobre criptografia em repouso com Azure Cosmos DB](database-encryption-at-rest.md)

- [Entendendo o gerenciamento de chaves para criptografia em repouso com Azure Cosmos DB](/azure/cosmos-db/cosmos-db-security-controls)

- [Como configurar chaves gerenciadas pelo cliente para sua conta de Azure Cosmos DB](how-to-setup-cmk.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para as instâncias de produção do Azure Cosmos DB.

- [Como criar alertas para eventos do log de atividades do Azure](/azure/azure-monitor/platform/alerts-activity-log)

- [Como criar alertas para eventos do log de atividades do Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Orientação**: siga as recomendações da central de segurança do Azure para suas instâncias de Azure Cosmos DB. 

A Microsoft executa aplicação de patch do sistema e gerenciamento de vulnerabilidade nos hosts subjacentes que dão suporte às instâncias de Azure Cosmos DB. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Recursos com suporte disponíveis na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Diretrizes**: Use o portal do Azure ou o grafo de recursos do Azure para descobrir todos os recursos (sem limitação de Azure Cosmos DB, mas também incluindo recursos como computação, outro armazenamento, rede, portas e protocolos, etc.) em suas assinaturas. Verifique se você tem as permissões apropriadas em seu locatário e se é capaz de enumerar todas as assinaturas do Azure, bem como recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Noções básicas sobre o controle de acesso baseado em função do Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretrizes**: aplique marcas às suas instâncias de Azure Cosmos DB e aos recursos relacionados com metadados para organizá-las logicamente em uma taxonomia.

- [Como criar e usar marcas](/azure/azure-resource-manager/resource-group-using-tags)

- [Quais recursos de Azure Cosmos DB dão suporte a marcas](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar ativos, incluindo, mas não se limitando a Azure Cosmos DB recursos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gerenciamento](/azure/governance/management-groups/create)

- [Como criar e usar marcas](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o grafo de recursos do Azure para consultar e descobrir recursos dentro das assinaturas.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretriz**: use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”. Isso pode impedir a criação e alterações em recursos em um ambiente de alta segurança.

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para suas instâncias de Cosmos DB com Azure Policy. Use Azure Policy aliases no namespace **Microsoft.DocumentDB** para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias de Cosmos DB. Você também pode fazer uso de definições de política internas para Azure Cosmos DB, como:
- Implantar a Proteção Avançada contra Ameaças em Contas do Cosmos DB
- O Cosmos DB deve usar um ponto de extremidade de serviço de rede virtual Microsoft Azure Cosmos DB

- [Como exibir os aliases de Azure Policy disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Diretriz**: use o Azure Policy [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Diretrizes**: se você estiver usando definições de Azure Policy personalizadas para seu Cosmos DB ou recursos relacionados, use Azure Repos para armazenar e gerenciar suas definições de política personalizadas como código.

- [Projetar os fluxos de trabalho da Política como código](../governance/policy/concepts/policy-as-code.md)

- [Documentação do Azure Repos](/azure/devops/repos/git/gitworkflow)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft.DocumentDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft.DocumentDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use Azure Policy [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para suas instâncias de Azure Cosmos DB e recursos relacionados. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Orientação**: para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure app que está sendo usado para acessar suas instâncias de Azure Cosmos DB, use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos Azure Cosmos DB. Verifique se a exclusão temporária do cofre de chaves está habilitada.

- [Como integrar com identidades gerenciadas do Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um Key Vault](/azure/key-vault/quick-create-portal)

- [Como autenticar-se no Key Vault](../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso de Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Orientação**: para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure app que está sendo usado para acessar suas instâncias de Azure Cosmos DB, use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos Azure Cosmos DB.

Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). As identidades gerenciadas permitem que você se autentique em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.

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

**Diretrizes**: o Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, CosmosDB do Azure), no entanto, ele não é executado no conteúdo do cliente.

É sua responsabilidade verificar previamente todos os arquivos que estão sendo carregados em recursos não computados do Azure, incluindo Azure Cosmos DB. A Microsoft não pode acessar dados do cliente e, portanto, não pode realizar verificações antimalware do conteúdo do cliente em seu nome.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: Azure Cosmos DB automaticamente faz backups de seus dados em intervalos regulares. Se o banco de dados ou o contêiner for excluído, você poderá arquivar um tíquete de suporte ou chamar o suporte do Azure para restaurar os dados de backups online automáticos. O suporte do Azure está disponível para planos selecionados apenas como Standard, Developer e Plans superiores. Para restaurar um instantâneo específico do backup, o Azure Cosmos DB exige que os dados estejam disponíveis durante o ciclo de backup desse instantâneo. 

Se estiver usando Key Vault para armazenar credenciais para suas instâncias de Cosmos DB, garanta backups regulares automatizados de suas chaves.

- [Entender Azure Cosmos DB backups automatizados](online-backup-and-restore.md)

- [Como restaurar dados no Azure Cosmos DB](/azure/cosmos-db/how-to-backup-and-restore)

- [Como fazer backup de chaves de Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: se o banco de dados ou o contêiner for excluído, você poderá arquivar um tíquete de suporte ou ligar para o suporte do Azure para restaurar os dados de backups online automáticos. O suporte do Azure está disponível para planos selecionados apenas como Standard, Developer e Plans superiores. Para restaurar um instantâneo específico do backup, o Azure Cosmos DB exige que os dados estejam disponíveis durante o ciclo de backup desse instantâneo.

Teste a restauração de seus segredos armazenados no Azure Key Vault usando o PowerShell. O cmdlet Restore-AzureKeyVaultKey cria uma chave no cofre de chaves especificado. Essa chave é uma réplica da chave de backup no arquivo de entrada e tem o mesmo nome da chave original.

- [Entender Azure Cosmos DB backups automatizados](online-backup-and-restore.md)

- [Como restaurar dados no Azure Cosmos DB](/azure/cosmos-db/how-to-backup-and-restore)

- [Como restaurar segredos de Azure Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: como todos os dados de usuário armazenados no cosmos DB são criptografados em repouso e no transporte, você não precisa realizar nenhuma ação. Em outras palavras, a criptografia em repouso é "ativada" por padrão. Não existem controles para ativá-lo ou desativá-lo. O Azure Cosmos DB usa a criptografia AES-256 em todas as regiões onde a conta está em execução.

Habilite a exclusão reversível no Key Vault para proteger chaves contra exclusão acidental ou mal-intencionada.

- [Entender a criptografia de dados no Azure Cosmos DB](database-encryption-at-rest.md)

- [Como habilitar a exclusão reversível no Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Você também pode aproveitar o guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está encontrando ou a análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomenclatura para identificar e categorizar claramente os recursos do Azure.

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

**Diretrizes**: siga as regras de teste de penetração Microsoft Cloud do Engagement para garantir que seus testes de penetração não estejam violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft. 

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
