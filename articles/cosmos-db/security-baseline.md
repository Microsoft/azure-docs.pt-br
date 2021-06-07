---
title: Linha de base de segurança do Azure para o Azure Cosmos DB
description: A linha de base de segurança do Azure Cosmos DB fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: dead43f2e9f2e8913bcebde43d543b8df8d33ced
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565666"
---
# <a name="azure-security-baseline-for-azure-cosmos-db"></a>Linha de base de segurança do Azure para o Azure Cosmos DB

Essa linha de base de segurança aplica as diretrizes do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview-v1.md) ao Azure Cosmos DB. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo Azure Security Benchmark e pelas diretrizes relacionadas aplicáveis ao Azure Cosmos DB. Os **controles** não aplicáveis ao Azure Cosmos DB foram excluídos.

 
Para ver como o Azure Cosmos DB é mapeado por completo para o Azure Security Benchmark, confira o [arquivo de mapeamento completo da linha de base de segurança do Azure Cosmos DB](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Diretrizes**: usando o Link Privado do Azure, você pode se conectar a uma conta do Azure Cosmos por meio de um ponto de extremidade privado. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você também pode reduzir o risco de exfiltração dos dados configurando um conjunto estrito de regras de saída em um NSG (grupo de segurança de rede) e associando esse NSG à sua sub-rede.

Use também pontos de extremidade de serviço para proteger sua conta do Azure Cosmos. Habilitando um ponto de extremidade de serviço, você pode configurar contas do Azure Cosmos para permitir o acesso somente em uma sub-rede específica de uma rede virtual do Azure. Depois que o ponto de extremidade de serviço do Azure Cosmos DB estiver habilitado, você poderá limitar o acesso a uma conta do Azure Cosmos com conexões de uma sub-rede em uma rede virtual.

Você também pode proteger os dados armazenados na sua conta do Azure Cosmos usando firewalls de IP. O Azure Cosmos DB é compatível com controles de acesso baseados em IP para suporte de firewall de entrada. Você pode definir um firewall de IP na conta do Azure Cosmos usando o portal do Azure, os modelos do Azure Resource Manager, a CLI do Azure ou o Azure PowerShell.

- [Visão geral do Link Privado do Azure](../private-link/private-link-overview.md)

- [Como configurar um ponto de extremidade privado para o Azure Cosmos DB](how-to-configure-private-endpoints.md) 

- [Como criar um grupo de segurança de rede com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como configurar o firewall de IP no Cosmos DB](how-to-configure-firewall.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: o [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão e é a base para as [recomendações da Central de Segurança](/azure/security-center/security-center-recommendations). As definições do Azure Policy relacionadas a esse controle são habilitadas automaticamente pela Central de Segurança. Os alertas relacionados a esse controle podem precisar de um plano do [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições internas do Azure Policy – Microsoft.DocumentDB**:

[!INCLUDE [Resource Policy for Microsoft.DocumentDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.documentdb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e adaptadores de rede

**Diretrizes**: use a Central de Segurança do Azure e siga as recomendações de proteção de rede para ajudar a proteger os recursos de rede relacionados à sua conta do Azure Cosmos.

Quando as máquinas virtuais são implantadas na mesma rede virtual da sua conta do Azure Cosmos, você pode usar um NSG (grupo de segurança de rede) para reduzir o risco de exfiltração dos dados. Habilite logs de fluxo de NSG e envie os logs para uma conta do Armazenamento do Azure para auditorias de tráfego. Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

- [Noções básicas sobre a segurança de rede fornecida pela Central de Segurança do Azure](../security-center/security-center-network-recommendations.md)

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretrizes**: o CORS (Compartilhamento de Recursos entre Origens) é um recurso usado para permitir que um aplicativo Web em execução em um domínio acesse recursos em outro domínio. Os navegadores da Web implementam uma restrição de segurança, conhecida como política de mesma origem, que impede que uma página da Web chame APIs em um domínio diferente. No entanto, o CORS fornece uma forma segura para permitir que um domínio de origem chame APIs em outro domínio. Após habilitar o suporte para CORS na conta do Azure Cosmos, somente as solicitações autenticadas serão avaliadas para determinar se são permitidas de acordo com as regras especificadas.

- [Configurar o Compartilhamento de Recursos entre Origens](how-to-configure-cross-origin-resource-sharing.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar as comunicações com endereços IP maliciosos conhecidos

**Diretrizes**: use a ATP (Proteção Avançada contra Ameaças) do Azure Cosmos DB. A ATP do Azure Cosmos DB oferece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar as contas do Azure Cosmos DB. Essa camada de proteção permite que você resolva as ameaças e integre-as aos sistemas centrais de monitoramento de segurança.

Habilite a Proteção contra DDoS Standard nas Redes Virtuais associadas às suas instâncias do Azure Cosmos DB para proteção contra DDoS. Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

- [Como configurar a Proteção Avançada contra Ameaças do Azure Cosmos DB](cosmos-db-advanced-threat-protection.md)

- [Como configurar a Proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

- [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="15-record-network-packets"></a>1.5: Registrar os pacotes de rede

**Diretrizes**: habilite logs de fluxo de NSG e envie os logs para uma conta de armazenamento para auditoria de tráfego. Envie os logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego na sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar configurações de rede incorretas.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar IDS/IPS (sistemas de detecção de intrusões/prevenção de intrusões) baseados em rede

**Diretrizes**: use a ATP (Proteção Avançada contra Ameaças) do Azure Cosmos DB. A ATP do Azure Cosmos DB oferece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar as contas do Azure Cosmos DB. Essa camada de proteção permite que você resolva as ameaças e integre-as aos sistemas centrais de monitoramento de segurança. 

- [Como configurar a Proteção Avançada contra Ameaças do Cosmos DB](cosmos-db-advanced-threat-protection.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: para os recursos que precisam ter acesso à sua conta do Azure Cosmos, use marcas de serviço de Rede Virtual para definir os controles de acesso à rede em grupos de segurança de rede ou no Firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Especificando o nome da marca de serviço (por exemplo, AzureCosmosDB) no campo apropriado de origem ou destino de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

- [Para obter mais informações sobre como usar as marcas de serviço](../virtual-network/service-tags-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede com o Azure Policy. Use aliases do Azure Policy nos namespaces "Microsoft.DocumentDB" e "Microsoft.Network" para criar políticas personalizadas a fim de auditar ou impor a configuração de rede das suas instâncias do Azure Cosmos DB. Você também pode usar definições de política internas para o Azure Cosmos DB, como:

- Implantar a Proteção Avançada contra Ameaças em Contas do Cosmos DB

- O Cosmos DB deve usar um ponto de extremidade de serviço de rede virtual Microsoft Azure Cosmos DB

Use também o Azure Blueprints para simplificar as implantações do Azure em grande escala por meio do empacotamento de artefatos de ambiente importantes, como modelos do Azure Resource Manager, o RBAC do Azure (controle de acesso baseado em função do Azure) e políticas, em uma só definição de blueprint. É fácil aplicar o blueprint a novas assinaturas e novos ambientes e ajustar o controle e o gerenciamento por meio do controle de versão.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar um Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Documentar regras de configuração de tráfego

**Diretrizes**: use marcas para recursos de rede associados à implantação do Azure Cosmos DB para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretrizes**: use o Log de Atividades do Azure para monitorar configurações de recursos de rede e detectar alterações de recursos de rede relacionados às suas instâncias do Azure Cosmos DB. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos. 

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, confira [Azure Security Benchmark: log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central de log de segurança

**Diretrizes**: ingira logs por meio do Azure Monitor para agregar dados de segurança gerados pelo Azure Cosmos DB. No Azure Monitor, use workspaces do Log Analytics para consultar e realizar análises e use contas de armazenamento para o armazenamento de longo prazo/arquivamento. Como alternativa, você pode integrar os dados ao Azure Sentinel ou a um SIEM (gerenciamento de eventos e informações de segurança) de terceiros. 

- [Como habilitar os logs de diagnóstico do Azure Cosmos DB](./monitor-cosmos-db.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: habilite as configurações de diagnóstico no Azure Cosmos DB e envie os logs para um workspace do Log Analytics ou uma conta de armazenamento do Azure. As configurações de diagnóstico do Azure Cosmos DB são usadas para coletar logs de recursos. Esses logs são capturados por solicitação e são chamados de **logs do plano de dados**. Entre alguns exemplos de operações do plano de dados estão exclusão, inserção e leitura. 

Você também pode habilitar as configurações de diagnóstico do log de atividades do Azure e enviar esses logs para o mesmo workspace do Log Analytics usado para os logs do Azure Cosmos DB.

- [Como habilitar as configurações de diagnóstico do Azure Cosmos DB](./monitor-cosmos-db.md)

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](../azure-monitor/essentials/activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: no Azure Monitor, defina o período de retenção de log para os workspaces do Log Analytics associados às suas instâncias do Azure Cosmos DB de acordo com os regulamentos de conformidade da sua organização.

- [Como definir os parâmetros de retenção de log](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e examinar os logs

**Diretrizes**: você pode executar consultas em um workspace do Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos logs do Azure Cosmos DB que você coletou.

- [Como executar consultas do Azure Cosmos DB em workspaces do Log Analytics](monitor-cosmos-db.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Habilitar alertas sobre atividades anormais

**Diretrizes**: na Central de Segurança do Azure, habilite a Proteção Avançada contra Ameaças do Azure Cosmos DB para monitorar atividades anormais em logs de segurança e eventos. Habilite as configurações de diagnóstico do Azure Cosmos DB e envie os logs para um workspace do Log Analytics.

 

Integre também seu workspace do Log Analytics ao Azure Sentinel, pois ele fornece uma solução SOAR (resposta automatizada de orquestração de segurança). Assim os guias estratégicos (soluções automatizadas) podem ser criados e usados para corrigir problemas de segurança. Além disso, você pode criar alertas de log personalizados no seu workspace do Log Analytics usando o Azure Monitor.

- [Lista de alertas de proteção contra ameaças do Azure Cosmos DB](../security-center/alerts-reference.md#alerts-azurecosmos)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Criar, exibir e gerenciar alertas de log usando o Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, confira [Azure Security Benchmark: controle de acesso e identidade](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Diretrizes**: você pode usar o painel do Controle de Acesso e Identidade (IAM) no portal do Azure para configurar o RBAC (controle de acesso baseado em função) e manter o inventário dos recursos do Azure Cosmos DB. As funções são aplicadas a usuários, grupos, entidades de serviço e identidades gerenciadas no Azure AD (Azure Active Directory). Você pode usar funções internas ou personalizadas para indivíduos e grupos.

O Azure Cosmos DB fornece o RBAC do Azure interno para cenários comuns de gerenciamento no Azure Cosmos DB. Um indivíduo que tenha um perfil no Azure AD pode atribuir essas funções do Azure a usuários, grupos, entidades de serviço ou identidades gerenciadas para permitir ou negar acesso a recursos e operações nos recursos do Azure Cosmos DB.

Use também o módulo do PowerShell do Azure AD para executar consultas ad hoc e descobrir contas que sejam membros de grupos administrativos.

Além disso, algumas ações no Azure Cosmos DB podem ser controladas com o Azure AD e as chaves mestras específicas da conta. Use a configuração de conta 'disableKeyBasedMetadataWriteAccess' para controlar o acesso à chave.

- [Noções básicas sobre o controle de acesso baseado em função do Azure Cosmos DB](role-based-access-control.md)

- [Criar funções personalizadas próprias usando ações do Azure Cosmos DB (namespace Microsoft.DocumentDB)](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

- [Criar uma função no Azure AD](../role-based-access-control/custom-roles.md)

- [Como obter uma função do diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Como obter membros de uma função do diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Restringir o acesso do usuário apenas às operações de dados](how-to-restrict-user-data.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: o conceito de senhas padrão ou em branco não existe em relação ao Azure AD (Azure Active Directory) ou ao Azure Cosmos DB. Em vez disso, o Azure Cosmos DB usa dois tipos de chaves para autenticar usuários e fornecer acesso a dados e recursos: chaves mestras e tokens de recurso. As chaves podem ser regeneradas a qualquer momento.

- [Noções básicas sobre o acesso seguro a dados no Azure Cosmos DB](secure-access-to-data.md)

- [Como regenerar chaves do Azure Cosmos DB](./manage-with-powershell.md#regenerate-keys)

- [Como acessar chaves de modo programático usando o Azure AD](certificate-based-authentication.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretrizes**: não aplicável. O Azure Cosmos DB não dá suporte a contas de administrador. Todo o acesso é integrado ao Azure AD (Azure Active Directory) e ao RBAC do Azure (controle de acesso baseado em função do Azure).

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Usar o SSO (logon único) do Azure Active Directory

**Diretrizes**: o Azure Cosmos DB usa dois tipos de chaves para autorizar usuários e não dá suporte ao SSO (logon único) no nível do plano de dados. O acesso ao painel de controle do Cosmos DB está disponível por meio da API REST e dá suporte ao SSO. Para autenticação, defina o cabeçalho de autorização para suas solicitações como um Token Web JSON obtido no Azure AD (Azure Active Directory).

- [Noções básicas sobre a API REST do Banco de Dados do Azure para Cosmos DB](/rest/api/cosmos-db/)

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

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](/security/compass/privileged-access-devices)
 
- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrar atividades suspeitas em contas administrativas e alertar sobre elas

**Diretrizes**: use a ATP (Proteção Avançada contra Ameaças) do Azure Cosmos DB. A ATP do Azure Cosmos DB oferece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar as contas do Azure Cosmos DB. Essa camada de proteção permite que você resolva as ameaças e integre-as aos sistemas centrais de monitoramento de segurança.

Além disso, use o PIM (Privileged Identity Management) do Azure AD (Azure Active Directory) para geração de logs e alertas quando atividades suspeitas ou não seguras ocorrerem no ambiente.

Use as detecções de risco do Azure AD para ver alertas e relatórios sobre o comportamento do usuário suspeito.

- [Como implantar o Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Entenda as detecções de risco do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: configure a condição de localização de uma política de acesso condicional e gerencie as localizações nomeadas. Com as localizações nomeadas, você pode criar agrupamentos lógicos de intervalos de endereços IP, países e regiões. Você pode restringir o acesso a recursos confidenciais, como suas instâncias do Azure Cosmos DB, às localizações nomeadas configuradas.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: use o Azure AD (Azure Active Directory) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

- [Como criar e configurar uma instância do Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Como configurar e gerenciar a autenticação do Azure AD com o SQL do Azure](../azure-sql/database/authentication-aad-configure.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure AD (Azure Active Directory) fornece logs para ajudar na descoberta de contas obsoletas. Além disso, você pode usar as revisões de acesso de identidade do Azure para gerenciar com eficiência associações a um grupo, acesso aos aplicativos empresariais e atribuições de função. O acesso do usuário pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo.

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorar as tentativas de acessar credenciais desativadas

**Diretrizes**: crie configurações de diagnóstico para contas de usuário do Azure AD (Azure Active Directory) enviando logs de auditoria e de entrada para um workspace do Log Analytics em que poderá configurar os alertas desejados.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alertar sobre desvios de comportamento de entrada na conta

**Diretrizes**: use a ATP (Proteção Avançada contra Ameaças) do Azure Cosmos DB. A ATP do Azure Cosmos DB oferece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar as contas do Azure Cosmos DB. Essa camada de proteção permite que você resolva as ameaças e integre-as aos sistemas centrais de monitoramento de segurança.

Use também o recurso de proteção de identidade e de detecção de riscos do Azure AD (Azure Active Directory) para configurar respostas automatizadas para ações suspeitas detectadas relacionadas às identidades de usuário. Além disso, você pode ingerir logs no Azure Sentinel para uma investigação mais aprofundada.

- [Como exibir entradas suspeitas do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretrizes**: use marcas para ajudar a acompanhar as instâncias do Azure Cosmos DB que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: Implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. As instâncias do Azure Cosmos DB são separadas por rede virtual/sub-rede, marcadas adequadamente e protegidas em um NSG (grupo de segurança de rede) ou no Firewall do Azure. As instâncias do Azure Cosmos DB que armazenam dados confidenciais devem ser isoladas. Usando o Link Privado do Azure, você pode se conectar a uma conta da instância do Azure Cosmos DB por meio de um ponto de extremidade privado. O ponto de extremidade privado é um conjunto de endereços IP privados em uma sub-rede dentro da rede virtual. Em seguida, você poderá limitar o acesso aos endereços IP privados selecionados. 

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como configurar um ponto de extremidade privado para o Azure Cosmos DB](how-to-configure-private-endpoints.md)

- [Como criar um grupo de segurança de rede com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: implante a Proteção Avançada contra Ameaças do Azure Cosmos DB, que detectará atividades anormais indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. Ela pode disparar os alertas a seguir simultaneamente:

- Acesso de locais incomuns

- Extração de dados incomum

Além disso, ao usar as máquinas virtuais para acessar suas instâncias do Azure Cosmos DB, use o Link Privado, o Firewall, os grupos de segurança de rede e as marcas de serviço para atenuar a possibilidade de exfiltração dos dados. A Microsoft gerencia a infraestrutura subjacente do Azure Cosmos DB e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

- [Como configurar a Proteção Avançada contra Ameaças do Cosmos DB](cosmos-db-advanced-threat-protection.md)

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação, classificação e prevenção automáticas contra perda de dados ainda não estão disponíveis no Azure Cosmos DB. No entanto, você pode usar a integração do Azure Cognitive Search para classificação e análise de dados. Você também pode implementar uma solução de terceiros, se necessário, para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Indexar dados do Azure Cosmos DB com o Azure Cognitive Search](../search/search-howto-index-cosmosdb.md?bc=%2fazure%2fcosmos-db%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcosmos-db%2ftoc.json)

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Usar o controle de acesso baseado em função para controlar o acesso a recursos

**Diretrizes**: o Azure Cosmos DB fornece o RBAC do Azure (controle de acesso baseado em função) interno do Azure para cenários comuns de gerenciamento no Azure Cosmos DB. Um indivíduo que tenha um perfil no Azure AD (Azure Active Directory) pode atribuir essas funções do Azure a usuários, grupos, entidades de serviço ou identidades gerenciadas para permitir ou negar acesso a recursos e operações nos recursos do Azure Cosmos DB. As atribuições de função têm como escopo somente o acesso ao painel de controle, que inclui acesso a contas, bancos de dados, contêineres e ofertas (taxa de transferência) do Azure Cosmos.

- [Como implementar o RBAC do Azure no Azure Cosmos DB](role-based-access-control.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: por padrão, todos os dados de usuário armazenados no Cosmos DB são criptografados em repouso. Não existem controles para desativar isso. O Azure Cosmos DB usa a criptografia AES-256 em todas as regiões onde a conta está em execução.

Por padrão, a Microsoft gerencia as chaves usadas para criptografar os dados da sua conta do Azure Cosmos. Opcionalmente, você pode adicionar uma segunda camada de criptografia com chaves próprias.

- [Noções básicas sobre a criptografia em repouso com o Azure Cosmos DB](database-encryption-at-rest.md)

- [Noções básicas sobre o gerenciamento de chaves para criptografia em repouso com o Azure Cosmos DB]()

- [Como configurar chaves gerenciadas pelo cliente para a sua conta do Azure Cosmos DB](how-to-setup-cmk.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: use o Azure Monitor com o log de atividades do Azure para criar alertas para quando ocorrerem alterações nas instâncias de produção do Azure Cosmos DB.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, confira [Azure Security Benchmark: gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretrizes**: siga as recomendações da Central de Segurança do Azure para suas instâncias do Azure Cosmos DB. 

A Microsoft executa a aplicação de patch do sistema e o gerenciamento de vulnerabilidade nos hosts subjacentes que dão suporte às instâncias do Azure Cosmos DB. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Recursos com suporte disponíveis na Central de Segurança do Azure](../security-center/security-center-services.md?tabs=features-windows)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, confira [Azure Security Benchmark: gerenciamento de inventário e ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usar uma solução de descoberta de ativos automatizada

**Diretrizes**: use o portal do Azure ou o Azure Resource Graph para descobrir todos os recursos (não só do Azure Cosmos DB, mas também recursos como computação, outros tipos de armazenamento, rede, portas e protocolos etc.) nas suas assinaturas. Verifique se você tem permissões apropriadas no seu locatário e pode enumerar todas as assinaturas do Azure, bem como os recursos nas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Noções básicas sobre o controle de acesso baseado em função do Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretrizes**: aplique marcas às instâncias do Azure Cosmos DB e a outros recursos relacionados com metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Quais recursos do Azure Cosmos DB dão suporte a marcas](../azure-resource-manager/management/tag-support.md#microsoftdocumentdb)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar ativos, incluindo, entre outros, recursos do Azure Cosmos DB. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorar recursos do Azure não aprovados

**Diretrizes**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o Azure Resource Graph para consultar e descobrir recursos nas suas assinaturas.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade de interação dos usuários com o Azure Resource Manager

**Diretriz**: use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”. Isso pode impedir a criação e as alterações em recursos em um ambiente de alta segurança.

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, confira [Azure Security Benchmark: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para suas instâncias do Cosmos DB com o Azure Policy. Use aliases do Azure Policy no namespace **Microsoft.DocumentDB** para criar políticas personalizadas a fim de auditar ou impor a configuração das instâncias do Cosmos DB. Você também pode usar definições de política internas para o Azure Cosmos DB, como:
- Implantar a Proteção Avançada contra Ameaças em Contas do Cosmos DB
- O Cosmos DB deve usar um ponto de extremidade de serviço de rede virtual Microsoft Azure Cosmos DB

- [Como ver os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

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

**Diretrizes**: se você estiver usando definições personalizadas do Azure Policy para os recursos do Cosmos DB ou recursos relacionados, use o Azure Repos para armazenar e gerenciar suas definições de política personalizadas como um código.

- [Projetar os fluxos de trabalho da Política como código](../governance/policy/concepts/policy-as-code.md)

- [Documentação do Azure Repos](/azure/devops/repos/git/gitworkflow)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implantar ferramentas de gerenciamento de configuração para recursos do Azure

**Diretrizes**: use aliases do Azure Policy no namespace "Microsoft.DocumentDB" para criar políticas personalizadas a fim de auditar e impor configurações do sistema, além de fornecer alertas sobre elas. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: use aliases do Azure Policy no namespace "Microsoft.DocumentDB" para criar políticas personalizadas a fim de auditar e impor configurações do sistema, além de fornecer alertas sobre elas. Use os efeitos [auditar], [negar] e [implantar se não existir] do Azure Policy para impor automaticamente as configurações para as instâncias do Azure Cosmos DB e os recursos relacionados. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: para máquinas virtuais do Azure ou aplicativos Web em execução no Serviço de Aplicativo do Azure usados para acessar suas instâncias do Azure Cosmos DB, use a Identidade de Serviço Gerenciado com o Azure Key Vault para simplificar e proteger o gerenciamento de segredos do Azure Cosmos DB. Verifique se a exclusão temporária do cofre de chaves está habilitada.

- [Como fazer a integração às Identidades Gerenciadas do Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um cofre de chaves](../key-vault/secrets/quick-create-portal.md)

- [Como se autenticar no Key Vault](../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso do Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerenciar identidades de maneira segura e automática

**Diretrizes**: para máquinas virtuais do Azure ou aplicativos Web em execução no Serviço de Aplicativo do Azure usados para acessar suas instâncias do Azure Cosmos DB, use a Identidade de Serviço Gerenciado com o Azure Key Vault para simplificar e proteger o gerenciamento de segredos do Azure Cosmos DB.

Use Identidades Gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD (Azure Active Directory). As identidades gerenciadas permitem que você se autentique em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.

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

**Diretrizes**: o Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, o Azure Cosmos DB), mas não é executado no conteúdo do cliente.

É sua responsabilidade verificar previamente todos os arquivos que estão sendo carregados em recursos de não computação do Azure, incluindo o Azure Cosmos DB. A Microsoft não pode acessar dados do cliente e, portanto, não pode realizar verificações antimalware do conteúdo do cliente em seu nome.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, confira [Azure Security Benchmark: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Fazer backups completos do sistema e backups de todas as chaves gerenciadas pelo cliente

**Diretrizes**: o Azure Cosmos DB faz backups automáticos dos seus dados em intervalos regulares. Se um banco de dados ou um contêiner for excluído, envie um tíquete de suporte ou ligue para o Suporte do Azure para restaurar os dados de backups online automáticos. O Suporte do Azure está disponível apenas para planos selecionados, como Standard, Desenvolvedor e planos superiores a eles. Para restaurar um instantâneo específico do backup, o Azure Cosmos DB exige que os dados estejam disponíveis durante o ciclo de backup desse instantâneo. 

Se estiver usando o Key Vault para armazenar credenciais para suas instâncias do Cosmos DB, garanta backups regulares automatizados das suas chaves.

- [Noções básicas sobre os backups automatizados do Azure Cosmos DB](online-backup-and-restore.md)

- [Como restaurar dados no Azure Cosmos DB](./online-backup-and-restore.md)

- [Como fazer backup de chaves do Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, inclusive os de chaves gerenciadas pelo cliente

**Diretrizes**: se um banco de dados ou um contêiner for excluído, envie um tíquete de suporte ou ligue para o Suporte do Azure para restaurar os dados de backups online automáticos. O Suporte do Azure está disponível apenas para planos selecionados, como Standard, Desenvolvedor e planos superiores a eles. Para restaurar um instantâneo específico do backup, o Azure Cosmos DB exige que os dados estejam disponíveis durante o ciclo de backup desse instantâneo.

Teste a restauração dos seus segredos armazenados no Azure Key Vault usando o PowerShell. O cmdlet Restore-AzureKeyVaultKey cria uma chave no cofre de chaves especificado. Essa chave é uma réplica da chave de backup no arquivo de entrada e tem o mesmo nome da chave original.

- [Noções básicas sobre os backups automatizados do Azure Cosmos DB](online-backup-and-restore.md)

- [Como restaurar dados no Azure Cosmos DB](./online-backup-and-restore.md)

- [Como restaurar segredos do Azure Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção dos backups e das chaves gerenciadas pelo cliente

**Diretrizes**: como todos os dados de usuário armazenados no Cosmos DB são criptografados em repouso e em transporte, você não precisa realizar nenhuma ação. Em outras palavras, a criptografia em repouso é "ativada" por padrão. Não existem controles para ativá-lo ou desativá-lo. O Azure Cosmos DB usa a criptografia AES-256 em todas as regiões onde a conta está em execução.

Habilite a exclusão reversível no Key Vault para proteger chaves contra exclusão acidental ou mal-intencionada.

- [Noções básicas sobre a criptografia de dados do Azure Cosmos DB](database-encryption-at-rest.md)

- [Como habilitar a exclusão reversível no Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Você também pode aproveitar o Guia de Tratamento de Incidentes de Segurança do Computador do NIST para ajudar na criação do seu plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade é baseada no nível de confiança da Central de Segurança na conclusão ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que resultou no alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomenclatura para identificar e categorizar claramente os recursos do Azure.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretrizes**: conduza exercícios para testar as funcionalidades de resposta a incidentes dos sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

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
