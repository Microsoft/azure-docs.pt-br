---
title: Linha de base de segurança do Azure para o Firewall do aplicativo Web do Azure
description: A linha de base de segurança de firewall do aplicativo Web do Azure fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ae6b4f38772cd6c6755ece78fb5c47834a616204
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211627"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Linha de base de segurança do Azure para o Firewall do aplicativo Web do Azure

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure versão 1,0](../security/benchmarks/overview-v1.md) para o Firewall do aplicativo Web do Azure. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao firewall do aplicativo Web do Azure. Os **controles** não aplicáveis ao firewall do aplicativo Web do Azure foram excluídos. 

Para ver como o Firewall do aplicativo Web do Azure é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança do firewall do aplicativo Web do Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte o [benchmark de segurança do Azure: segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Orientação**: Use Microsoft Azure WAF (firewall do aplicativo Web) para proteção centralizada de aplicativos Web contra explorações e vulnerabilidades comuns, como injeção de SQL e script entre sites. 

- Modo de detecção: Use este modo para aprender o tráfego de rede, entender e revisar falsos positivos. Ele monitora e registra todos os alertas de ameaça. Verifique se o log de diagnóstico e WAF está selecionado e ativado. Observe que o WAF não bloqueia as solicitações de entrada quando está operando no modo de detecção.
- Modo de prevenção: bloqueia invasões e ataques detectados pelas regras. Um invasor recebe uma exceção de "acesso não autorizado 403" e a conexão é fechada. O modo de Prevenção registra tais ataques nos logs do WAF.

Linha de base do tráfego de rede com o modo de detecção do WAF. Depois de determinar suas necessidades de tráfego, configure o WAF no modo de prevenção para Bock tráfego indesejado.

Acompanhe as recomendações de severidade alta da central de segurança para todos os recursos habilitados para Web que não são protegidos pelo WAF.  

- [Regras e grupos de regras CRS do Firewall de Aplicativo Web](ag/application-gateway-crs-rulegroups-rules.md) 

- [Modos de WAF no gateway de aplicativo](ag/ag-overview.md#waf-modes)

- [Modos de WAF na porta frontal](afds/afds-overview.md#waf-modes)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Diretrizes**: use regras personalizadas com o Firewall do aplicativo Web do Azure (WAF) para permitir e bloquear o tráfego. Por exemplo, todo o tráfego proveniente de um intervalo de endereços IP pode ser bloqueado. Configure o Azure WAF para ser executado no modo prevenção, que bloqueia invasões e ataques detectados pelas regras. Um invasor recebe uma exceção de "acesso não autorizado 403" e a conexão é fechada. O modo de Prevenção registra tais ataques nos logs do WAF.

- [Modos de WAF no gateway de aplicativo](ag/ag-overview.md#waf-modes)

- [Modos de WAF na porta frontal](afds/afds-overview.md#waf-modes)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretrizes**: o WAF (firewall do aplicativo Web) do Azure é o principal componente das proteções do aplicativo Web do Azure. Use o Azure WAF para fornecer proteção centralizada para aplicativos Web contra explorações e vulnerabilidades comuns com RuleSet gerenciado pré-configurado contra assinaturas de ataque conhecidas das 10 principais categorias do OWASP.

Personalize o Azure WAF com regras e grupos de regras para atender aos requisitos do aplicativo Web e eliminar falsos positivos. Associe uma política de WAF do Azure para cada site por trás de um WAF para permitir a configuração específica do site. O Azure WAF dá suporte à filtragem geográfica, à limitação de taxa, às regras de conjunto de regras padrão gerenciadas pelo Azure. e regras personalizadas podem ser criadas para atender às necessidades de um aplicativo. 

Configure o WAF do Azure para ser executado no modo de prevenção após a linha de base do tráfego de rede no modo de detecção por um determinado período de tempo. O WAF do Azure bloqueia invasões e ataques detectados pelas regras no modo de prevenção. Um invasor recebe uma exceção de "acesso não autorizado 403" e a conexão é fechada. O modo de Prevenção registra tais ataques nos logs do WAF.

- [Modos de WAF no gateway de aplicativo](ag/ag-overview.md#waf-modes)

- [Modos de WAF na porta frontal](afds/afds-overview.md#waf-modes)

- [Regras e grupos de regras CRS do firewall do aplicativo Web](ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Orientação**: criar, associar e organizar logicamente os recursos em uma assinatura do Azure com marcas para detecção de configurações incorretas de aplicativo comuns (por exemplo, Apache e IIS). 

Aplique regras e grupos de regras às políticas do WAF (firewall do aplicativo Web) do Azure com base nos metadados de marca aplicados.

- [Política de WAF no gateway de aplicativo](/cli/azure/network/application-gateway/waf-policy) 

- [Política de WAF na porta frontal](/cli/azure/ext/front-door/network/front-door/waf-policy)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: use marcas para grupos de segurança de rede associados ao firewall do aplicativo Web do Azure (WAF) em sua sub-rede aplicativo Azure gateway, bem como quaisquer outros recursos relacionados à segurança de rede e ao fluxo de tráfego. Para regras de grupo de segurança de rede individuais, use o campo "Descrição" para especificar a necessidade de negócios, a duração e assim por diante para qualquer regra que permita o tráfego de ou para uma rede.

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Escolha Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de configurações de rede e recursos relacionados às implantações do WAF (firewall do aplicativo Web) do Azure. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em configurações de rede ou recursos críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: Crie uma regra de rede para o Firewall do aplicativo Web do Azure (WAF) para permitir o acesso a um servidor NTP com a porta e o protocolo apropriados, como a porta 123 sobre UDP.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: configurar logs do WAF (firewall do aplicativo Web) do Azure para serem enviados a uma solução de gerenciamento de log de segurança central, como o Azure Sentinel ou um Siem de terceiros. Esses logs incluem atividade do Azure, diagnóstico e logs de WAF em tempo real, esses logs podem ser exibidos em diferentes ferramentas, como Azure Monitor, Excel e Power BI. Os logs de firewall do aplicativo Web do Azure fornecem informações sobre quais dados a WAF do Azure está avaliando, correspondendo e bloqueando.

O Azure Sentinel tem uma pasta de trabalho interna do Azure WAF, que fornece uma visão geral dos eventos de segurança no WAF do Azure. Esta pasta de trabalho inclui eventos, regras de correspondência e bloqueadas e todo o resto que é registrado nos logs de firewall. Essa telemetria pode ser usada para disparar a automação do guia estratégico para notificar ou realizar ações de correção com base nos eventos WAF coletados pelo Azure Sentinel.

- [Exibir logs de atividade](../azure-resource-manager/management/view-activity-logs.md)

- [Logs de diagnóstico para o gateway de aplicativo](../application-gateway/application-gateway-diagnostics.md)

- [Conectar dados do firewall do aplicativo Web da Microsoft ao Azure Sentinel](../sentinel/connect-azure-waf.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: habilite o registro em log em seus recursos de WAF (firewall do aplicativo Web) do Azure para acesso a logs de auditoria, segurança e diagnóstico. O Firewall do aplicativo Web do Azure fornece relatórios detalhados sobre cada uma de suas ameaças detectadas que são disponibilizadas nos logs de diagnóstico configurados. Logs de atividade, que estão automaticamente disponíveis, incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis.

- [Visão geral do log](ag/ag-overview.md#logging)

- [Visão geral da consulta de log de Azure Monitor](../azure-monitor/logs/log-query-overview.md)

- [Visão geral dos logs da plataforma Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: envie os logs do WAF (firewall do aplicativo Web) do Azure para uma conta de armazenamento personalizada e defina a política de retenção. Use Azure Monitor para definir seu período de retenção de espaço de trabalho de Log Analytics com base nos requisitos de conformidade de sua organização.
- [Configurar o monitoramento para uma conta de armazenamento](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: o WAF (firewall do aplicativo Web) do Azure fornece relatórios detalhados sobre cada ameaça detectada. O registro em log é integrado a logs de Diagnóstico do Azure que fornecem informações avançadas sobre operações e erros que são importantes para auditoria e solução de problemas. 

As instâncias do Azure WAF são integradas à central de segurança para enviar alertas e informações de integridade para relatórios. Use as recomendações da central de segurança para detectar aplicativos Web desprotegidos e proteger esses recursos vulneráveis. 

O Azure Sentinel tem uma pasta de trabalho interna de eventos WAF-firewall, que fornece uma visão geral dos eventos de segurança no WAF. Isso inclui eventos, regras de correspondência e bloqueadas e todo o resto que é registrado nos logs do firewall.

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](../azure-monitor/index.yml) 

- [Como habilitar as configurações de diagnóstico para Aplicativo Azure gateway](../application-gateway/application-gateway-diagnostics.md)

- [Monitoramento de métricas e logs na porta frontal do Azure](../frontdoor/front-door-diagnostics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Orientação**: habilitar as configurações de diagnóstico do log de atividades do Azure, bem como as configurações de diagnóstico para o WAF do Azure, e enviar os logs para um espaço de trabalho log Analytics. Faça consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados coletados. Crie alertas para atividade anômala com base em métricas de WAF. Por exemplo, se o número bloqueado de solicitações exceder ' X ', faça ' Y '.

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como criar alertas no Azure](../azure-monitor/alerts/tutorial-response.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Diretrizes**: implante o WAF (firewall do aplicativo Web) do Azure na frente de aplicativos Web críticos para inspeção adicional do tráfego de entrada. 

O Azure WAF fornece proteção centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns e protege seus aplicativos inspecionando o tráfego da Web de entrada para bloquear ataques como injeção de SQL, scripts entre sites, carregamentos de malware e ataques de DDoS.

- [Como implantar o Azure WAF](ag/create-waf-policy-ag.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: o Azure Active Directory (Azure AD) tem funções internas que são capazes de ser atribuídas e devem ser atribuídos explicitamente. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Orientação**: não há nenhuma atribuição de administrador local disponível no WAF. No entanto, pode haver funções de administrador do Azure Active Directory (Azure AD) no ambiente que poderiam permitir o controle de gerenciamento dos recursos de WAF do Azure.
É uma boa prática criar procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas que têm acesso às instâncias do WAF (firewall do aplicativo Web) do Azure. Use os recursos de gerenciamento de identidade e acesso da central de segurança para monitorar o número de contas administrativas.

- [Entender a identidade e o acesso da central de segurança do Azure](../security-center/security-center-identity-access.md)

- [Entenda como criar usuários administradores no banco de dados do Azure para PostgreSQL](../postgresql/howto-create-users.md#the-server-admin-account)

- [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretrizes**: habilite a autenticação multifator (MFA) do Azure Active Directory (Azure AD) e siga as recomendações de gerenciamento de identidade e acesso da central de segurança.

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Orientação**: Use a Paw (estação de trabalho com acesso privilegiado) com a MFA (autenticação multifator) configurada para fazer logon e configurar o WAF (firewall do aplicativo Web) do Azure e recursos relacionados. 

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use os relatórios de segurança do Azure Active Directory (AD do Azure) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorrer no ambiente. Use a central de segurança para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: Configure a condição de local de uma política de acesso condicional e gerencie seus locais nomeados. 

Crie agrupamentos lógicos de intervalos de endereços IP ou países e regiões com locais nomeados. Restrinja o acesso aos seus recursos confidenciais, como Azure Key Vault segredos, aos seus locais nomeados configurados.

- [Qual é a condição de local em Azure Active Directory acesso condicional](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito e também Salts, hashes e armazena com segurança as credenciais do usuário.
- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (Azure AD) fornece logs para ajudar a descobrir contas obsoletas. Use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. Examine o acesso do usuário regularmente para garantir que somente usuários ativos tenham acesso contínuo.

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: integre a atividade de entrada do Azure Active Directory (Azure AD), as fontes de log de eventos de auditoria e de risco, com qualquer ferramenta Siem ou de monitoramento, como o Azure Sentinel.

Simplifique esse processo criando configurações de diagnóstico para contas de usuário do Azure Active Directory (AD do Azure) e enviando logs de auditoria e logs de entrada para um espaço de trabalho do Log Analytics. Configure os alertas desejados no espaço de trabalho Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: Use os recursos de proteção de risco e identidade do Azure Active Directory (Azure AD) para configurar respostas automatizadas para detectar ações suspeitas relacionadas a identidades de usuário. Ingerir dados no Azure Sentinel para investigação adicional.

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: manter um inventário de informações confidenciais

**Orientação**: use marcas para ajudar a controlar o Firewall do aplicativo Web do Azure (WAF) e recursos relacionados que armazenam ou processam informações confidenciais.
- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente o isolamento usando assinaturas e grupos de gerenciamento separados para domínios de segurança individuais, como tipo de ambiente e nível de sensibilidade de dados, por exemplo, ambientes de desenvolvimento, teste e produção. 

Controle o acesso aos recursos do Azure com o Azure RBAC (controle de acesso baseado em função).

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: criptografar todas as informações confidenciais em trânsito. Certifique-se de que todos os clientes que se conectam às instâncias do WAF (firewall do aplicativo Web) do Azure e recursos relacionados sejam capazes de negociar o TLS 1,2 ou superior.

Siga as recomendações da central de segurança para criptografia em repouso e criptografia em trânsito, quando aplicável.

- [Entender a criptografia em trânsito com o Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: controlar o acesso aos recursos do Azure com o Azure RBAC (controle de acesso baseado em função).
- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: Use a criptografia em repouso para todos os recursos do Azure, incluindo o WAF (firewall do aplicativo Web) do Azure e recursos relacionados. A Microsoft recomenda permitir que o Azure gerencie suas chaves de criptografia, no entanto, há a opção de gerenciar suas próprias chaves em algumas instâncias.

- [Noções básicas sobre a criptografia em repouso do Azure](../security/fundamentals/encryption-atrest.md)

- [Como configurar chaves de criptografia gerenciadas pelo cliente](../storage/common/customer-managed-keys-configure-key-vault.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Configure o WAF (firewall do aplicativo Web) do Azure para ser executado no modo de prevenção após a linha de base do tráfego de rede no modo de detecção por um período de tempo predeterminado. 

O WAF do Azure, no modo de prevenção, bloqueia invasões e ataques detectados pelas regras. O invasor recebe uma exceção "403 acesso não autorizado" e a conexão é encerrada. O modo de Prevenção registra tais ataques nos logs do WAF.

- [Visão geral da integração entre o Gateway de Aplicativo e a Central de Segurança do Azure](../security-center/security-center-partner-integration.md)

- [Modos de WAF no gateway de aplicativo](ag/ag-overview.md#waf-modes)

- [Modos de WAF na porta frontal](afds/afds-overview.md#waf-modes)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar ou descobrir todos os recursos, como computação, armazenamento, rede, portas e protocolos, etc. em suas assinaturas. 

Verifique as permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas. Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Orientação**: use marcas nas políticas do WAF (firewall do aplicativo Web) do Azure. As marcas podem ser associadas a recursos e aplicadas logicamente para organizar o acesso a esses recursos em uma assinatura de cliente. 

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar o WAF do Azure e os recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: Crie um inventário de recursos aprovados, incluindo a configuração com base nas necessidades organizacionais.

Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas. Use o Azure Resource Graph para consultar e descobrir recursos dentro das assinaturas deles. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas.
Use o grafo de recursos do Azure para consultar ou descobrir recursos do WAF (firewall do aplicativo Web) do Azure em suas assinaturas. Verifique se todos os recursos relacionados e WAF do Azure presentes no ambiente foram aprovados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: monitore e remova recursos do Azure WAF não aprovados com Azure Policy para negar a implantação do WAF do Azure ou um determinado tipo de WAF, por exemplo, o Azure WAF v1 vs v2.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para restringir quais serviços você pode provisionar em seu ambiente.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: Use o acesso condicional do Azure para limitar a capacidade do usuário de interagir com o Gerenciador de recursos do Azure Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Gerenciador de recursos do Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separar física ou logicamente os aplicativos de alto risco

**Diretrizes**: o WAF (firewall do aplicativo Web) do Azure pode ser associado a ambientes diferentes por meio de redes, grupos de recursos ou assinaturas para separar aplicativos de alto risco.

- [Visão geral da rede virtual do Azure](../virtual-network/virtual-networks-overview.md)

- [Organizar seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md)

- [Guia de decisão da assinatura](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: definir e implementar configurações de segurança padrão para as configurações de rede relacionadas às implantações do WAF (firewall do aplicativo Web) do Azure.
Use Azure Policy aliases no namespace "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de seus gateways de Aplicativo Azure, redes virtuais, grupos de segurança de rede e usar definições de política internas.

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Orientação**: Use os efeitos de Azure Policy [Deny] e [implantar se não existir] para impor as configurações seguras em seu firewall do aplicativo Web do Azure (WAF) e recursos relacionados. 

Use modelos de Azure Resource Manager para manter a configuração de segurança de seu WAF do Azure e os recursos relacionados exigidos pela sua organização.

- [Entender Azure Policy efeitos](../governance/policy/concepts/effects.md)

- [Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Visão geral de modelos de Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Diretrizes**: Use o Azure DevOps para armazenar e gerenciar seu código com segurança, como políticas personalizadas do Azure e modelos de Azure Resource Manager. 

Conceda ou negue permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory (Azure AD), se integrado ao Azure DevOps ou Active Directory, se integrado ao Team Foundation Server (TFS).

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolver um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Documentação do Azure Policy](../governance/policy/index.yml)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. 

Use os efeitos de Azure Policy [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para os recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Documentação do Azure Policy](../governance/policy/index.yml)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: Use Azure Key Vault para armazenar certificados com segurança. Azure Key Vault é um repositório de segredos gerenciado por plataforma que você pode usar para proteger segredos, chaves e certificados SSL. 

O Gateway de Aplicativo v2 do Azure dá suporte à integração com o Key Vault para certificados de servidor que estejam anexados a ouvintes habilitados para HTTPS. 

- [Como configurar a terminação SSL com Key Vault certificados usando Azure PowerShell](../application-gateway/configure-keyvault-ps.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretrizes**: implemente o verificador de credenciais para identificar credenciais no código que também incentivam a movimentação de credenciais descobertas para locais mais seguros, como Azure Key Vault.
- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: Verifique se a exclusão reversível está habilitada para Azure Key Vault. A exclusão reversível permite a recuperação de cofres de chaves e objetos de cofre excluídos, como chaves, segredos e certificados.

- [Como usar a exclusão reversível do Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte o [benchmark de segurança do Azure: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretrizes**: desenvolver um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de manipulação e gerenciamento de incidentes, desde a detecção até a revisão após o incidente. 

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Use o guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a central de segurança atribui uma severidade a cada alerta para ajudar a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.
Marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.
- [Consulte o guia de publicação do NIST para testar, treinar e preparar programas para planos de ti e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.
- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Use o conector de dados da central de segurança do Azure para transmitir os alertas para o Azure Sentinel de acordo com os requisitos da sua organização.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações.
- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não sejam violações das políticas da Microsoft. Use a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem, serviços e aplicativos gerenciados pela Microsoft. 

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)