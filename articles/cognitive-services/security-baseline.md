---
title: Linha de base de segurança do Azure para serviços cognitivas
description: A linha de base de segurança de serviços cognitivas fornece orientações de procedimentos e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 218810183f547d4e90043364a318615a204df9d8
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044848"
---
# <a name="azure-security-baseline-for-cognitive-services"></a>Linha de base de segurança do Azure para serviços cognitivas

Essa linha de base de segurança aplica diretrizes da [versão 1,0 do benchmark de segurança do Azure](../security/benchmarks/overview-v1.md) para serviços cognitivas. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis aos serviços cognitivas. Os **controles** não aplicáveis aos serviços cognitivas foram excluídos.

 
Para ver como os serviços cognitivas são totalmente mapeados para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base de serviços cognitivas](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: Microsoft Azure serviços cognitivas fornecem um modelo de segurança em camadas. Esse modelo permite que você assegure suas contas de Serviços Cognitivos a um subconjunto específico de redes. Quando as regras de rede são configuradas, somente dados de solicitação de aplicativos do conjunto especificado de redes podem acessar a conta. Você pode limitar o acesso a seus recursos com filtragem de solicitações, permitindo apenas solicitações originadas de endereços IP especificados, intervalos de IP ou de uma lista de sub-redes em redes virtuais do Azure.

O suporte a rede virtual e ponto de extremidade de serviço para serviços cognitivas é limitado a um conjunto específico de regiões.

- [Como configurar redes virtuais de serviços cognitivas do Azure](./cognitive-services-virtual-networks.md?tabs=portal)

- [Visão geral das redes virtuais do Azure](../virtual-network/virtual-networks-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: quando as máquinas virtuais são implantadas na mesma rede virtual que o seu contêiner de serviços cognitivas, você pode usar grupos de segurança de rede para reduzir o risco de vazamento de dados. Habilite logs de fluxo de grupos de segurança de rede e envie logs para uma conta de armazenamento do Azure para auditoria de tráfego. Você também pode enviar logs de fluxo de grupos de segurança de rede para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Orientação**: se você estiver usando serviços cognitivas em um contêiner, poderá aumentar sua implantação de contêiner com uma solução de firewall de aplicativo Web de front-face que filtra o tráfego mal-intencionado e dá suporte à criptografia TLS de ponta a ponta, mantendo o ponto de extremidade do contêiner privado e seguro. 

Tenha em mente que os contêineres de serviços cognitivas são necessários para enviar informações de medição para fins de cobrança. A única exceção é os contêineres offline, pois eles seguem uma metodologia de cobrança diferente. Falha ao permitir listar vários canais de rede dos quais os contêineres de serviços cognitivas dependem impedirão que o contêiner funcione. O host deve permitir a porta de lista 443 e os seguintes domínios:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Observe também que você deve desabilitar a inspeção profunda de pacotes para sua solução de firewall nos canais seguros que os contêineres de serviços cognitivas criam para servidores Microsoft. Se não for feito, isso impedirá que o contêiner funcione corretamente.

- [Entender a segurança do contêiner de serviços cognitivas do Azure](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: quando as máquinas virtuais são implantadas na mesma rede virtual que o seu contêiner de serviços cognitivas, defina e implemente configurações de segurança padrão para recursos de rede relacionados com o Azure Policy. Use aliases de Azure Policy nos namespaces "Microsoft. Cognitivaservices" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do cache do Azure para instâncias Redis. Você também pode fazer uso de definições de política internas, como:

- A Proteção contra DDoS Standard deve ser habilitada

Use plantas do Azure para simplificar implantações de grande escala do Azure empacotando artefatos de ambiente-chave, como modelos de Azure Resource Manager, controle de acesso baseado em função do Azure (RBAC do Azure) e políticas, em uma única definição de Blueprint. Aplique facilmente o blueprint a novas assinaturas e ambientes e ajuste o controle e o gerenciamento por meio do controle de versão.

Se você estiver usando serviços cognitivas em um contêiner, poderá aumentar sua implantação de contêiner com uma solução de firewall de aplicativo Web de front-face que filtra o tráfego mal-intencionado e dá suporte à criptografia TLS de ponta a ponta, mantendo o ponto de extremidade do contêiner privado e seguro. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

- [Entender a segurança do contêiner de serviços cognitivas do Azure](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: quando as máquinas virtuais são implantadas na mesma rede virtual que o seu contêiner de serviços cognitivas, você pode usar grupos de segurança de rede para reduzir o risco de vazamento de dados. Habilite logs de fluxo de grupos de segurança de rede e envie logs para uma conta de armazenamento do Azure para auditoria de tráfego. Você também pode enviar logs de fluxo de grupos de segurança de rede para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Orientação**: se estiver usando serviços cognitivas em um contêiner, você pode aumentar sua implantação de contêiner com uma solução de firewall de aplicativo Web de front-face que filtra o tráfego mal-intencionado e dá suporte à criptografia TLS de ponta a ponta, mantendo o ponto de extremidade do contêiner privado e seguro.  Você pode selecionar uma oferta do Azure Marketplace que dá suporte à funcionalidade de IDS/IPS com a capacidade de desabilitar a inspeção de carga.

Tenha em mente que os contêineres de serviços cognitivas são necessários para enviar informações de medição para fins de cobrança. A única exceção são os contêineres offline, pois eles seguem uma metodologia de cobrança diferente. Falha ao permitir listar vários canais de rede dos quais os contêineres de serviços cognitivas dependem impedirão que o contêiner funcione. O host deve permitir a porta de lista 443 e os seguintes domínios:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Observe também que você deve desabilitar a inspeção profunda de pacotes para sua solução de firewall nos canais seguros que os contêineres de serviços cognitivas criam para servidores Microsoft. Se não for feito, isso impedirá que o contêiner funcione corretamente.

- [Entender a segurança do contêiner de serviços cognitivas do Azure](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Orientação**: se estiver usando serviços cognitivas em um contêiner, você pode aumentar sua implantação de contêiner com uma solução de firewall de aplicativo Web de front-face que filtra o tráfego mal-intencionado e dá suporte à criptografia TLS de ponta a ponta, mantendo o ponto de extremidade do contêiner privado e seguro. 

Tenha em mente que os contêineres de serviços cognitivas são necessários para enviar informações de medição para fins de cobrança. A única exceção é os contêineres offline, pois eles seguem uma metodologia de cobrança diferente. Falha ao permitir listar vários canais de rede dos quais os contêineres de serviços cognitivas dependem impedirão que o contêiner funcione. O host deve permitir a porta de lista 443 e os seguintes domínios:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Observe também que você deve desabilitar a inspeção profunda de pacotes para sua solução de firewall nos canais seguros que os contêineres de serviços cognitivas criam para servidores Microsoft. Se não for feito, isso impedirá que o contêiner funcione corretamente.

- [Entender a segurança do contêiner de serviços cognitivas do Azure](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Você também pode usar grupos de segurança de aplicativo para ajudar a simplificar a configuração de segurança complexa. Os grupos de segurança de aplicativo permitem a você configurar a segurança de rede como uma extensão natural da estrutura de um aplicativo, permitindo o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos.

- [Marcas de serviço de rede virtual](../virtual-network/service-tags-overview.md)

- [Grupos de segurança de aplicativo](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede relacionados ao seu contêiner de serviços cognitivas com Azure Policy. Use aliases de Azure Policy nos namespaces "Microsoft. Cognitivaservices" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do cache do Azure para instâncias Redis.

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos de Azure Resource Manager, controle de acesso baseado em função (RBAC do Azure) e políticas, em uma única definição de Blueprint. Aplique facilmente o blueprint a novas assinaturas e ambientes e ajuste o controle e o gerenciamento por meio do controle de versão.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Diretrizes**: use marcas para recursos de rede associados ao seu contêiner de serviços cognitivas para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretrizes**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados ao seu contêiner de serviços cognitivas. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Diretrizes**: Habilite as configurações de diagnóstico do Log de Atividades do Azure e envie os logs para um workspace do Log Analytics, um hub de eventos do Azure ou uma conta de armazenamento do Azure para arquivar. Os logs de atividade fornecem informações sobre as operações que foram executadas em seu contêiner de serviços cognitivas no nível do plano de controle. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) executada no nível do plano de controle para o cache do Azure para instâncias Redis.

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: para log de auditoria do plano de controle, habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um espaço de trabalho log Analytics, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento Usando os dados do Log de Atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) executada no nível do plano de controle para seus recursos do Azure.

Além disso, os serviços cognitivas enviam eventos de diagnóstico que podem ser coletados e usados para fins de análise, alertas e relatórios. Você pode definir configurações de diagnóstico para um contêiner de serviços cognitivas por meio do portal do Azure. Você pode enviar um ou mais eventos de diagnóstico para uma conta de armazenamento, Hub de eventos ou um espaço de trabalho Log Analytics.

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Usando configurações de diagnóstico para os serviços cognitivas do Azure](diagnostic-logging.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretriz**: No Azure Monitor, defina o período de retenção do workspace do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Use contas do Armazenamento do Microsoft Azure para armazenamento de longo prazo/arquivamento.

- [Como definir parâmetros de retenção de log para workspaces do Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: habilitar as configurações de diagnóstico do log de atividades do Azure e enviar os logs para um espaço de trabalho log Analytics. Esses logs fornecem dados avançados e frequentes sobre a operação de um recurso que são usados para identificação e depuração de problemas. Execute consultas em Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos dados do log de atividades que podem ter sido coletados para os serviços cognitivas do Azure.

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como coletar e analisar os logs de atividades do Azure no espaço de trabalho Log Analytics no Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Orientação**: você pode gerar alertas sobre as métricas com suporte em serviços cognitivas acessando a seção alertas e métricas em Azure monitor.

Defina as configurações de diagnóstico para seu contêiner de serviços cognitivas e envie logs para um espaço de trabalho Log Analytics. No workspace do Log Analytics, configure alertas para quando um conjunto predefinido de condições ocorrer. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Criar, exibir e gerenciar alertas de log usando o Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: Azure Active Directory (Azure AD) tem funções internas que devem ser explicitamente atribuídas e que podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?amp;preserve-view=true&view=azureadps-2.0)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?amp;preserve-view=true&view=azureadps-2.0)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: o acesso do plano de controle aos serviços cognitivas é controlado por meio do Azure Active Directory (AD do Azure). O Azure AD não tem o conceito de senhas padrão.

O acesso do plano de dados a serviços cognitivas é controlado por meio de chaves de acesso. Essas chaves são usadas pelos clientes que se conectam ao seu cache e podem ser regeneradas a qualquer momento.

Não é recomendável que você crie senhas padrão em seu aplicativo. Em vez disso, você pode armazenar suas senhas em Azure Key Vault e, em seguida, usar o Azure AD para recuperá-las.

- [Como regenerar o cache do Azure para chaves de acesso Redis](../azure-cache-for-redis/cache-configure.md#settings)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use o gerenciamento de acesso e identidade da central de segurança para monitorar o número de contas administrativas.

Além disso, para ajudá-lo a manter o controle de contas administrativas dedicadas, você pode usar recomendações da central de segurança ou de políticas internas do Azure, como:

- Deve haver mais de um proprietário atribuído à sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

- [Como usar a Central de Segurança do Azure para monitorar a identidade e o acesso (versão prévia)](../security-center/security-center-identity-access.md)

- [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Orientação**: os serviços cognitivas usam chaves de acesso para autenticar usuários e não oferecem suporte ao SSO (logon único) no nível do plano de dados. O acesso ao plano de controle para serviços cognitivas está disponível por meio da API REST e dá suporte a SSO. Para autenticar, defina o cabeçalho de autorização para suas solicitações para um token da Web JSON (JavaScript Object Notation) que você obtém do Azure Active Directory (AD do Azure).

- [Entender a API REST dos serviços cognitivas do Azure](/rest/api/cognitiveservices/)

- [Entender o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretrizes**: habilite a autenticação multifator do Azure Active Directory (Azure AD) e siga as recomendações de gerenciamento de acesso e identidade da central de segurança.

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usar estações de trabalho seguras e gerenciadas pelo Azure para tarefas administrativas

**Diretrizes**: Use Paw (estações de trabalho com acesso privilegiado) com a autenticação multifator configurada para fazer logon e configurar os recursos do Azure. 

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use o PIM (Azure Active Directory do Azure ad Privileged Identity Management) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorrer no ambiente.

Além disso, use as detecções de risco do Azure Active Directory para ver alertas e relatórios sobre o comportamento do usuário suspeito.

- [Como implantar o Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Entenda as detecções de risco do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Orientação**: Configure locais nomeados no acesso condicional do Azure Active Directory (Azure AD) para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países e regiões.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito e também Salts, hashes e armazena com segurança as credenciais do usuário. Se o caso de uso oferecer suporte à autenticação do AD, use o Azure AD para autenticar solicitações para sua API de serviços cognitivas. 

Atualmente, somente o API da Pesquisa Visual Computacional, API de Detecção Facial, API de Análise de Texto, leitor de imersão, reconhecedor de formulário, detector de anomalias e todos os serviços do Bing, exceto Pesquisa Personalizada do Bing dar suporte à autenticação usando o Azure AD.

- [Como autenticar solicitações para serviços cognitivas](./authentication.md#authenticate-with-azure-active-directory)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (Azure AD) fornece logs para ajudar a descobrir contas obsoletas. Além disso, o cliente deve utilizar as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas usuários ativos tenham acesso contínuo.

Cliente para manter o inventário das contas de usuário do gerenciamento de API, reconcilie o acesso conforme necessário. No Gerenciamento de API, os desenvolvedores são os usuários das APIs que você expõe utilizando o Gerenciamento d API. Por padrão, as contas de desenvolvedor criadas recentemente têm o estado “Ativa” e são associadas ao grupo Desenvolvedores. As contas de desenvolvedor que estão com estado “Ativa” podem ser utilizadas para acessar todas as APIs nas quais estão inscritas.

- [Como gerenciar contas de usuário no Gerenciamento de API do Azure](../api-management/api-management-howto-create-or-invite-developers.md)

- [Como obter a lista de usuários do Gerenciamento de API](/powershell/module/az.apimanagement/get-azapimanagementuser?amp;preserve-view=true&view=azps-4.8.0)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Orientação**: você tem acesso a atividades de entrada do Azure Active Directory (Azure AD), auditoria e risco de registro de eventos, que permitem a integração com o Azure Sentinel ou um Siem de terceiros.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas de log desejados no Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: para o desvio do comportamento de logon da conta no plano de controle, use os recursos de proteção de identidade e de detecção de riscos do Azure Active Directory (Azure AD) para configurar respostas automatizadas para detectar ações suspeitas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: não disponível para serviços cognitivas. O Sistema de Proteção de Dados do Cliente ainda não tem suporte para serviços cognitivas.

- [Lista de serviços suportados do Sistema de Proteção de Dados do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente assinaturas ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Os recursos devem ser separados por redes virtuais ou sub-redes, marcados adequadamente e protegidos por um grupo de segurança de rede ou firewall do Azure. Os recursos que armazenam ou processam dados confidenciais devem ser suficientemente isolados. Para máquinas virtuais que armazenam ou processam dados confidenciais, implemente políticas e procedimentos para desativá-los quando não estiverem em uso.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alerta ou alerta e negar com o Firewall do Azure](../firewall/threat-intel.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: ainda não disponível. Os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para serviços cognitivas.

A Microsoft gerencia a infraestrutura subjacente para serviços cognitivas e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: todos os pontos de extremidade de serviços cognitivas são expostos por http impõem o TLS 1,2. Com um protocolo de segurança imposto, os consumidores que tentam chamar um ponto de extremidade de serviços cognitivas devem aderir a estas diretrizes:

- O sistema operacional do cliente (SO) precisa oferecer suporte a TLS 1,2.
- A linguagem (e plataforma) usada para fazer a chamada HTTP precisa especificar o TLS 1,2 como parte da solicitação. Dependendo do idioma e da plataforma, a especificação de TLS é feita implicitamente ou explicitamente.

- [Entender a segurança da camada de transporte para serviços cognitivas do Azure](cognitive-services-security.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para serviços cognitivas. Instâncias de marca que contêm informações confidenciais como tal e implementam solução de terceiros, se necessário, para fins de conformidade.

A Microsoft gerencia a plataforma subjacente e trata todo o conteúdo do cliente como confidencial e leva a um grande comprimento para se proteger contra a perda e a exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos 

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para controlar o acesso ao plano de controle de serviços cognitivas (ou seja, portal do Azure). 

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretrizes**: a criptografia em repouso para serviços cognitivas depende do serviço específico que está sendo usado. Na maioria dos casos, os dados são criptografados e descriptografados usando a criptografia AES de 256 bits compatível com FIPS 140-2. A criptografia e a descriptografia são transparentes, o que significa que a criptografia e o acesso são gerenciados para os clientes da Microsoft. Os dados do cliente são seguros por padrão e não precisam modificar seu código ou aplicativos para tirar proveito da criptografia.

Você também pode usar Azure Key Vault para armazenar as chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves.

- [Lista de serviços que criptografam informações em repouso](./encryption/cognitive-services-encryption-keys-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem em instâncias de produção de serviços cognitivas e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar ou descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos, etc.) em suas assinaturas. Configure permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?amp;preserve-view=true&view=azps-4.8.0)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear o cache do Azure para instâncias Redis e recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Além disso, use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Além disso, use o grafo de recursos do Azure para consultar ou descobrir recursos dentro das assinaturas.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

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

**Diretrizes**: defina e implemente configurações de segurança padrão para seu contêiner de serviços cognitivas com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. Cognitivaservices" para criar políticas personalizadas para auditar ou impor a configuração do cache do Azure para instâncias Redis.

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?amp;preserve-view=true&view=azps-4.8.0)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Orientação**: Use os efeitos de Azure Policy [Deny] e [implantar se não existir], para impor configurações seguras em seus recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Diretrizes**: se você estiver usando definições de Azure Policy personalizadas ou modelos de Azure Resource Manager para seus contêineres de serviços cognitivas e recursos relacionados, use Azure Repos para armazenar e gerenciar seu código com segurança.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?amp;preserve-view=true&view=azure-devops)

- [Documentação do Azure Repos](/azure/devops/repos/?amp;preserve-view=true&view=azure-devops)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. cache" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. cognitivaservices" para criar definições de Azure Policy personalizadas para alertar, auditar e impor configurações do sistema. Use os efeitos de Azure Policy [auditoria], [negar] e [implantar se não houver], para impor automaticamente as configurações para o cache do Azure para instâncias Redis e recursos relacionados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Diretrizes**: para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure app que está sendo usado para acessar sua API de serviços cognitivas, use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de chaves de serviços cognitivas. Certifique-se de que Key Vault exclusão reversível esteja habilitada.

- [Como integrar com identidades gerenciadas do Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Como autenticar-se no Key Vault](../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso de Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretrizes**: para máquinas virtuais do Azure ou aplicativos Web em execução no serviço Azure app que está sendo usado para acessar sua API de serviços cognitivas, use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de chaves de serviços cognitivas. Verifique se a exclusão temporária do cofre de chaves está habilitada.

Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Identidades gerenciadas permitem que você se autentique em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Azure Key Vault, sem nenhuma credencial em seu código.

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

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, cache do Azure para Redis), no entanto, ele não é executado no conteúdo do cliente.

Examine previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de BLOBs, banco de dados do Azure para PostgreSQL e assim por diante. A Microsoft não pode acessar seus dados nessas instâncias.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares

**Orientação**: os dados em sua conta de armazenamento de Microsoft Azure são sempre replicados automaticamente para garantir durabilidade e alta disponibilidade. A redundância do Armazenamento do Azure copia os dados para que eles sejam protegidos contra eventos planejados e não planejados, incluindo falhas de hardware transitórias, interrupções de energia ou rede e desastres naturais de grandes proporções. Você pode escolher replicar os dados no mesmo datacenter, em datacenters zonais na mesma região ou entre regiões separadas geograficamente. 

Você também pode usar o recurso gerenciamento de ciclo de vida para fazer backup de dados na camada de arquivo morto. Além disso, habilite a exclusão reversível para seus backups armazenados na conta de armazenamento.

- [Noções básicas sobre contratos de Service-Level e redundância de armazenamento do Azure](../storage/common/storage-redundancy.md)

- [Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Exclusão reversível para blobs do Armazenamento do Azure ](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: Use Azure Resource Manager para implantar serviços cognitivas e recursos relacionados. Azure Resource Manager fornece a capacidade de exportar modelos, o que permite que você reimplante sua solução em todo o ciclo de vida de desenvolvimento e tenha confiança de que seus recursos são implantados em um estado consistente. Use a automação do Azure para chamar a API de exportação do modelo Azure Resource Manager regularmente. Fazer backup de chaves pré-compartilhadas dentro do Azure Key Vault.

- [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Como criar um recurso de serviços cognitivas usando um modelo de Azure Resource Manager](./create-account-resource-manager-template.md?tabs=portal)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Grupos de recursos-exportar modelo](/rest/api/resources/resourcegroups/exporttemplate)

- [Introdução à automação do Azure](../automation/automation-intro.md)

- [Como fazer backup de chaves do cofre de chaves no Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?amp;preserve-view=true&view=azps-4.8.0)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: garanta a capacidade de executar periodicamente a implantação de modelos de Azure Resource Manager regularmente para uma assinatura isolada, se necessário. Teste a restauração das chaves pré-compartilhadas de backup.

- [Implantar recursos com modelos ARM e portal do Azure](../azure-resource-manager/templates/deploy-portal.md)

- [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-4.8.0)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: Use o Azure DevOps para armazenar e gerenciar com segurança seus modelos de Azure Resource Manager. Para proteger os recursos que você gerencia no Azure DevOps, você pode conceder ou negar permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory (AD do Azure), se integrados ao Azure DevOps, ou Active Directory se integrados ao Team Foundation Server.

Use o controle de acesso baseado em função do Azure para proteger chaves gerenciadas pelo cliente. Habilite a proteção de Soft-Delete e limpeza no Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada. 

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?amp;preserve-view=true&view=azure-devops)

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Exclusão reversível para blobs do Armazenamento do Azure ](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Você também pode aproveitar o guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou na análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta. 

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomenclatura para identificar e categorizar claramente os recursos do Azure.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Veja a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal.  Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da central de segurança para transmitir os alertas para o Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações.

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

- Confira a [Visão geral do Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)