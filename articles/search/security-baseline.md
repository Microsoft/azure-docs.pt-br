---
title: Linha de base de segurança do Azure para Azure Pesquisa Cognitiva
description: A linha de base de segurança de Pesquisa Cognitiva do Azure fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ebf948c76196224806afda21bd2f266b1b797f74
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604366"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Linha de base de segurança do Azure para Azure Pesquisa Cognitiva

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure versão 1,0](../security/benchmarks/overview-v1.md) para o Azure pesquisa cognitiva. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao Azure pesquisa cognitiva. Os **controles** não se aplicam ao Azure pesquisa cognitiva, ou o cliente foram excluídos.

Para ver como o Azure Pesquisa Cognitiva é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base do azure pesquisa cognitiva Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Orientação**: Verifique se todas as implantações de sub-rede rede virtual do Microsoft Azure têm um grupo de segurança de rede aplicado com regras para implementar um esquema de acesso "menos privilegiado". Permitir acesso somente às portas confiáveis e aos intervalos de endereços IP do seu aplicativo. Implante o Azure Pesquisa Cognitiva com um ponto de extremidade privado do Azure, onde viável, para habilitar o acesso privado aos serviços de sua rede virtual.

O Pesquisa Cognitiva também dá suporte à funcionalidade de segurança de rede adicional para gerenciar listas de controle de acesso à rede. Configure seu serviço de pesquisa para permitir somente a comunicação com fontes confiáveis restringindo o acesso de intervalos de endereços IP públicos específicos usando seu recurso de firewall.

- [Como configurar pontos de extremidade privados para o Azure Pesquisa Cognitiva](service-create-private-endpoint.md)

- [Como configurar o firewall de Pesquisa Cognitiva do Azure](service-configure-firewall.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Orientação**: pesquisa cognitiva não pode ser implantada diretamente em uma rede virtual. No entanto, se o aplicativo cliente ou as fontes de dados estiverem em uma rede virtual, você poderá monitorar e registrar o tráfego para esses componentes na rede, incluindo solicitações enviadas a um serviço de pesquisa na nuvem. As recomendações padrão incluem habilitar um log de fluxo do grupo de segurança de rede e enviar logs para o armazenamento do Azure ou um espaço de trabalho Log Analytics. Opcionalmente, você pode usar Análise de Tráfego para obter informações sobre os padrões de tráfego.

- [Como habilitar logs de fluxo do grupo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: rejeitar comunicações com endereços IP maliciosos conhecidos

**Orientação**: a pesquisa cognitiva não fornece um recurso específico para combater um ataque de negação de serviço distribuído, mas você pode habilitar a proteção contra DDoS Standard nas redes virtuais associadas ao serviço de pesquisa cognitiva para proteção geral.

- [Como configurar a proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Diretrizes**: habilitar os logs de fluxo do grupo de segurança de rede para os grupos de segurança de rede protegendo as máquinas virtuais (VM) do Azure que serão conectadas ao seu serviço de pesquisa cognitiva. Envie logs para uma conta de armazenamento do Azure para auditoria de tráfego. 

Habilite a captura de pacotes do observador de rede, se necessário, para investigar a atividade anômala.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar o Observador de Rede](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: pesquisa cognitiva não dá suporte à detecção de intrusão de rede, mas como uma mitigação de intrusão, você pode configurar regras de firewall para especificar os endereços IP aceitos pelo serviço de pesquisa cognitiva. Configure um ponto de extremidade privado para manter o tráfego de pesquisa fora da Internet pública.

- [Como configurar chaves gerenciadas pelo cliente para criptografia de dados](search-security-manage-encryption-keys.md)

- [Como obter informações de chave gerenciadas pelo cliente de índices e mapas de sinônimos](search-security-get-encryption-keys.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Orientação**: use marcas de serviço, se você estiver aproveitando indexadores e habilidades no pesquisa cognitiva, para representar um intervalo de endereços IP que têm permissão para se conectar a recursos externos. 

Permita ou negue o tráfego para recursos especificando o nome da marca de serviço (por exemplo, AzureCognitiveSearch) no campo de origem ou de destino apropriado de uma regra. 

- [Marcas de serviço de rede virtual](../virtual-network/service-tags-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: você pode configurar pesquisa cognitiva com um ponto de extremidade privado do Azure para integrar seu serviço de pesquisa a uma rede virtual.  Use marcas de recurso para grupos de segurança de rede e outros recursos relacionados à segurança de rede e ao fluxo de tráfego. Para regras de grupo de segurança de rede individuais, use o campo "Descrição" para documentar as regras que permitem o tráfego de/para uma rede. 
 

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como os efeitos "exigir marca e seu valor", para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes. 

Você pode usar Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.  

 
- [Como criar um ponto de extremidade privado para Pesquisa Cognitiva](service-create-private-endpoint.md) 

 
 
- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma rede virtual do Azure](../virtual-network/quick-create-portal.md) 

 
- [Como filtrar o tráfego de rede com regras de grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: ingerir logs relacionados a pesquisa cognitiva por meio de Azure monitor para agregar dados de segurança gerados por dispositivos de ponto de extremidade, recursos de rede e outros sistemas de segurança. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento. Como alternativa, você pode habilitar e integrar esses dados ao Azure Sentinel ou a um SIEM de terceiros.
 

 
- [Introdução à integração do Azure Monitor e ao SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)
 

 
- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 
 

 
- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: diagnóstico e logs operacionais fornecem informações sobre as operações detalhadas do pesquisa cognitiva e são úteis para monitorar o serviço e para cargas de trabalho que acessam seu serviço.  Para capturar dados de diagnóstico, habilite o log especificando onde as informações de log são armazenadas.
 

 
- [Como coletar e analisar dados de log para o Azure Pesquisa Cognitiva](search-monitor-logs.md) 

 
- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas – Microsoft. Search**:

[!INCLUDE [Resource Policy for Microsoft.Search 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.search-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

**Orientação**: os dados históricos que alimentam as métricas de diagnóstico são preservados por pesquisa cognitiva por 30 dias por padrão. Para maior retenção, certifique-se de habilitar a configuração que especifica uma opção de armazenamento para manter métricas e eventos registrados em log.
 

 
Em Azure Monitor, defina seu período de retenção de espaço de trabalho de Log Analytics de acordo com os regulamentos de conformidade de sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento. 
 

 
- [Alterar o período de retenção de dados em Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period) 

 
- [Como configurar a política de retenção para logs de conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#enable-logs)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Diretrizes**: analise e monitore logs de seu serviço de pesquisa cognitiva para um comportamento anormal. Use o Log Analytics do Azure Monitor para revisar os logs e realizar consultas nos dados de log. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros. 

 
 
- [Como coletar e analisar dados de log para Pesquisa Cognitiva](search-monitor-logs.md)
 
- [Como Visualizar dados de log de pesquisa no Power BI](search-monitor-logs-powerbi.md)
 

 
- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)
 

 
- [Saiba mais sobre o Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)
 

 
- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: Use a central de segurança com log Analytics espaço de trabalho para monitoramento e alertas em atividade anômala encontrada em eventos e logs de segurança. Como alternativa, você pode habilitar o e os dados integrados para o Azure Sentinel.
 

 
- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)
 

 
- [Como gerenciar alertas na central de segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md)
 

 
- [Como alertar sobre dados de log do log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: o Azure RBAC (controle de acesso baseado em função) do Azure permite que você gerencie o acesso aos recursos do Azure por meio de atribuições de função. Você pode atribuir essas funções a usuários, grupos de entidades de serviço e identidades gerenciadas. Há funções internas predefinidas para determinados recursos, e essas funções podem ser inventariadas ou consultadas por meio de ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure.

Pesquisa Cognitiva funções estão associadas a permissões que dão suporte a tarefas de gerenciamento de nível de serviço. Essas funções não concedem acesso ao ponto de extremidade de serviço. Acesso a operações no ponto de extremidade, (como gerenciamento de índice, preenchimento de índice e consultas em dados de pesquisa), use chaves de API para autenticar a solicitação.

- [Definir funções para acesso administrativo ao Azure Pesquisa Cognitiva](search-security-rbac.md)

- [Criar e gerenciar chaves de API para um serviço de Pesquisa Cognitiva do Azure](search-security-api-keys.md)

- [Como obter uma função de diretório no Azure Active Directory (Azure AD) com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Diretrizes**: a pesquisa cognitiva não tem o conceito de contas de administrador de nível local ou Azure Active Directory (AD do Azure) que podem ser usadas para gerenciar índices e operações. 

Use as funções internas do Azure AD que devem ser explicitamente atribuídas para operações de gerenciamento. Invoque o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como usar funções para acesso administrativo no Pesquisa Cognitiva](search-security-rbac.md)

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Diretrizes**: Use a autenticação de SSO com o Azure Active Directory (Azure AD) para acessar informações de serviço de pesquisa para operações de gerenciamento com suporte por meio de Azure Resource Manager. 

Estabeleça um processo para reduzir o número de identidades e credenciais habilitando o SSO para o serviço com as identidades preexistentes de sua organização.

- [Entender o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: habilitar o recurso de autenticação multifator do Azure Active Directory (Azure AD) e seguir as recomendações de acesso e identidade da central de segurança.

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use uma Paw (estação de trabalho com acesso privilegiado) com autenticação multifator configurada para fazer logon e acessar os recursos do Azure.
 

 
- [Entenda as estações de trabalho seguras e gerenciadas pelo Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

 
- [Como habilitar a autenticação multifator do Azure Active Directory (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use os relatórios de segurança do Azure Active Directory (AD do Azure) e o monitoramento para detectar quando atividades suspeitas ou inseguras ocorrem no ambiente. Use a central de segurança para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central para tarefas de gerenciamento de nível de serviço no Azure pesquisa cognitiva. As identidades do Azure AD não concedem acesso ao ponto de extremidade do serviço de pesquisa.  O acesso a operações como gerenciamento de índice, preenchimento de índice e consultas em dados de pesquisa estão disponíveis por meio de chaves de API.

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Criar e gerenciar chaves de API para um serviço de Pesquisa Cognitiva do Azure](search-security-api-keys.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (Azure AD) fornece logs para ajudar a descobrir contas obsoletas. Use as revisões de identidade e acesso do Azure AD para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo. 

Examine os logs de diagnóstico de Pesquisa Cognitiva para atividade no ponto de extremidade do serviço de pesquisa, como gerenciamento de índice, preenchimento de índice e consultas.

- [Entender os relatórios do Azure AD](/azure/active-directory/reports-monitoring/)

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Monitorar operações e atividades do Azure Pesquisa Cognitiva](search-monitor-usage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: a atividade de entrada, a auditoria e as fontes de log de eventos de risco do acesso ao Azure Active Directory (Azure AD) permitem a integração com qualquer ferramenta Siem ou de monitoramento.

Simplifique esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Configure os alertas desejados no espaço de trabalho Log Analytics.

- [Como integrar os logs de atividades do Azure com o Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Diretrizes**: Use os recursos de proteção de identidade do Azure Active Directory (Azure AD) para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Ingerir dados no Azure Sentinel para investigação adicional, conforme necessário.

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Os recursos devem ser separados por rede virtual/sub-rede, marcados adequadamente e protegidos em um grupo de segurança de rede ou firewall do Azure. Os recursos que armazenam ou processam dados confidenciais devem ser isolados. Use o link privado para configurar um ponto de extremidade privado para Pesquisa Cognitiva.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como criar um ponto de extremidade privado para Pesquisa Cognitiva](service-create-private-endpoint.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: Use uma solução de terceiros do Azure Marketplace em perímetros de rede para monitorar a transferência não autorizada de informações confidenciais e bloquear essas transferências ao alertar os profissionais de segurança de informações.

A Microsoft gerencia a plataforma subjacente e trata todo o conteúdo do cliente como confidencial e protege contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para pesquisa cognitiva. Implemente uma solução de terceiros, se necessário, para fins de conformidade. 

A Microsoft gerencia a plataforma subjacente e trata todo o conteúdo do cliente como confidencial e protege contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usar o RBAC do Azure para gerenciar o acesso aos recursos

**Diretrizes**: para administração de serviços, use o Azure RBAC (controle de acesso baseado em função) para gerenciar o acesso a chaves e configurações. Para operações de conteúdo, como indexação e consultas, Pesquisa Cognitiva usa chaves em vez de um modelo de controle de acesso baseado em identidade. Use o RBAC do Azure para controlar o acesso às chaves. 

 
- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)  
 
 
- [Como usar funções para acesso administrativo a Pesquisa Cognitiva](search-security-rbac.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretrizes**: o pesquisa cognitiva criptografa automaticamente o conteúdo indexado em repouso com chaves gerenciadas pela Microsoft. Se for necessária mais proteção, você poderá complementar a criptografia padrão com uma segunda camada de criptografia usando as chaves que você cria e gerencia no Azure Key Vault.

- [Configurar chaves gerenciadas pelo cliente para criptografia de dados no Azure Pesquisa Cognitiva](search-security-manage-encryption-keys.md)

- [Noções básicas sobre a criptografia em repouso do Azure](../security/fundamentals/encryption-atrest.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem em instâncias de produção do pesquisa cognitiva e outros recursos críticos ou relacionados. 

 
- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Como criar alertas para atividades de Pesquisa Cognitiva](search-monitor-logs.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos e assim por diante) em suas assinaturas.

Verifique as permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do Azure com metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar ativos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definir e manter um inventário de recursos aprovados do Azure

**Orientação**: defina uma lista de recursos aprovados do Azure relacionados à indexação e ao processamento do continhante no pesquisa cognitiva.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Orientação**: é recomendável que você defina um inventário dos recursos do Azure que foram aprovados para uso de acordo com suas políticas organizacionais e padrões anteriores e, em seguida, monitore os recursos do Azure não aprovados com o Azure Policy ou o grafo de recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para posicionar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Use o grafo de recursos do Azure para consultar ou descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](/azure/governance/policy/samples/)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de "Gerenciamento do Microsoft Azure".  

 
Controle o acesso às chaves usadas para autenticar solicitações para todas as outras operações, especialmente aquelas relacionadas ao conteúdo com Pesquisa Cognitiva.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. Search" para criar políticas personalizadas para auditar ou impor a configuração de seus recursos de pesquisa cognitiva do Azure. Você também pode usar definições de Azure Policy internas para serviços de Pesquisa Cognitiva, como:

habilitar o registro em log de auditoria para recursos do Azure

Azure Resource Manager tem a capacidade de exportar o modelo no JavaScript Object Notation (JSON), que deve ser revisado para garantir que as configurações atendam aos requisitos de segurança da sua organização.

Você também pode usar as recomendações da central de segurança do Azure como uma linha de base de configuração segura para os recursos do Azure.

- [Controles de Conformidade Regulatória do Azure Policy para o Azure Cognitive Search](security-controls-policy.md)

- [Como exibir os aliases de Azure Policy disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Diretrizes**: Use os efeitos de Azure Policy [negar] e [implantar se não existir], para impor configurações seguras em seus recursos de serviço do pesquisa cognitiva. 

Azure Resource Manager modelos podem ser usados para manter a configuração de segurança dos recursos do Azure exigidos por sua organização. 

- [Entender Azure Policy efeitos](../governance/policy/concepts/effects.md)

- [Controles de Conformidade Regulatória do Azure Policy para o Azure Cognitive Search](security-controls-policy.md)

- [Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Visão geral de modelos de Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Orientação**: se estiver usando definições de Azure Policy personalizadas, use o Azure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentação do Azure Repos](/azure/devops/repos/index)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para seus recursos de serviço Pesquisa Cognitiva usando Azure Policy. 

Use aliases para criar políticas personalizadas para auditar ou impor as configurações de rede. Você também pode fazer uso de definições de política internas relacionadas aos seus recursos específicos. 

Além disso, você pode usar a automação do Azure para implantar alterações de configuração e gerenciar exceções de política. 

- [Controles de Conformidade Regulatória do Azure Policy para o Azure Cognitive Search](security-controls-policy.md)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: Use a central de segurança para executar verificações de linha de base de seus recursos de serviço pesquisa cognitiva.  Além disso, use Azure Policy para alertar e auditar suas configurações de recurso. 

- [Como corrigir recomendações na central de segurança do Azure](../security-center/security-center-remediate-recommendations.md)

- [Controles de Conformidade Regulatória do Azure Policy para o Azure Cognitive Search](security-controls-policy.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Orientação**: Use identidades gerenciadas do Azure em conjunto com Azure Key Vault para simplificar o gerenciamento de segredos para seus aplicativos de nuvem.

- [Como usar identidades gerenciadas para recursos do Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Como criar um Key Vault](../key-vault/general/quick-create-portal.md)

- [Como fornecer Key Vault autenticação com uma identidade gerenciada](../key-vault/general/assign-access-policy-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Orientação**: Use uma identidade gerenciada do Azure para fornecer pesquisa cognitiva acesso a outros serviços do Azure, como key Vault e fontes de dados do indexador, usando uma identidade gerenciada automaticamente no Azure Active Directory (AD do Azure). Identidades gerenciadas permitem que você se autentique em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Azure Key Vault, sem nenhuma credencial em seu código. 

- [Configurar uma conexão do indexador com uma fonte de dados usando uma identidade gerenciada](search-howto-managed-identities-data-sources.md)

- [Configurar chaves gerenciadas pelo cliente para criptografia de dados usando uma identidade gerenciada](search-security-manage-encryption-keys.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: examinar previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure, como pesquisa cognitiva, armazenamento de BLOBs, banco de dados SQL do Azure e assim por diante. 

É sua responsabilidade verificar previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure. A Microsoft não pode acessar dados do cliente e, portanto, não pode realizar verificações antimalware do conteúdo do cliente em seu nome.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: garantir que o software antimalware e as assinaturas sejam atualizados

**Orientação**: não aplicável a pesquisa cognitiva. Ele não permite que soluções Antimalware sejam instaladas em seus recursos. Para a plataforma subjacente, a Microsoft lida com a atualização de qualquer software e assinaturas antimalware.  

 
Para todos os recursos de computação que pertencem à sua organização e usados em sua solução de pesquisa, siga as recomendações na central de segurança, &amp; aplicativos de computação para garantir que todos os pontos de extremidade estejam atualizados com as assinaturas mais recentes. Para o Linux, use uma solução antimalware de terceiros.

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantir backups automatizados regulares

**Orientação**: não é possível fazer backup do conteúdo armazenado em um serviço de pesquisa por meio do backup do Azure ou de qualquer outro mecanismo interno, mas você pode recriar um índice do código-fonte do aplicativo e das fontes de dados primárias ou criar uma ferramenta personalizada para recuperar e armazenar conteúdo indexado. 

 
- [Índice do GitHub – exemplo de restauração de backup](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Orientação**: pesquisa cognitiva atualmente não dá suporte a backup automatizado de dados em um serviço de pesquisa e deve ser feito backup por meio de um processo manual. Você também pode fazer backup de chaves gerenciadas pelo cliente no Azure Key Vault.
 

- [Fazer backup e restaurar um índice de Pesquisa Cognitiva do Azure](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Como fazer backup de chaves de Key Vault no Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: pesquisa cognitiva atualmente não dá suporte a backup automatizado de dados em um serviço de pesquisa e deve ser feito backup e ser restaurado por meio de um processo manual. Execute periodicamente a restauração de dados de conteúdo que você fez backup manualmente para garantir a integridade de ponta a ponta do seu processo de backup.

- [Fazer backup e restaurar um índice de Pesquisa Cognitiva do Azure](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Como restaurar chaves de Key Vault no Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: pesquisa cognitiva atualmente não dá suporte a backup automatizado de dados em um serviço de pesquisa e deve ser feito backup por meio de um processo manual.  Você também pode fazer backup de chaves gerenciadas pelo cliente no Azure Key Vault.  

 
Habilite a exclusão reversível e a proteção de limpeza no Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada. Se o armazenamento do Azure for usado para armazenar backups manuais, habilite a exclusão reversível para salvar e recuperar seus dados quando BLOBs ou instantâneos de blob forem excluídos. 
 

 
- [Fazer backup e restaurar um índice de Pesquisa Cognitiva do Azure](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)
 

 
- [Como habilitar a exclusão temporária e a proteção de limpeza no Key Vault](../storage/blobs/soft-delete-blob-overview.md) 

- [Exclusão reversível para o armazenamento de BLOBs do Azure](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretrizes**: desenvolver um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de manipulação e gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [O cliente também pode aproveitar o guia de tratamento de incidentes de segurança do computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A gravidade é baseada em quão confiante a central de segurança está na localização ou na forma analítica usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque as assinaturas usando marcas e crie um sistema de nomeação para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais. É sua responsabilidade priorizar a correção de alertas com base na importância dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Consulte a publicação do NIST, "guia para testar, treinar e exercitar programas para recursos e planos de ti"](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações manualmente ou continuamente. Você pode usar o conector de dados da central de segurança para transmitir os alertas para o Azure Sentinel.

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
