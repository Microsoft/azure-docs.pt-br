---
title: Linha de base de segurança do Azure para o Firewall de Aplicativo Web do Azure
description: A linha de base de segurança do Firewall de Aplicativo Web do Azure contém diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ae6b4f38772cd6c6755ece78fb5c47834a616204
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211627"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Linha de base de segurança do Azure para o Firewall de Aplicativo Web do Azure

Essa linha de base de segurança aplica as diretrizes do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview-v1.md) ao Firewall de Aplicativo Web do Azure. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo Azure Security Benchmark e pelas diretrizes relacionadas aplicáveis ao Firewall de Aplicativo Web do Azure. Os **controles** que não se aplicam ao Firewall de Aplicativo Web do Azure foram excluídos. 

Para ver como o Firewall de Aplicativo Web do Azure é mapeado completamente para o Azure Security Benchmark, confira o [arquivo de mapeamento completo da linha de base de segurança do Firewall de Aplicativo Web do Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretrizes**: use o WAF (Firewall de Aplicativo Web) do Microsoft Azure para proteção centralizada de aplicativos Web contra explorações e vulnerabilidades comuns, como injeção de SQL e cross-site scripting. 

- Modo de detecção: use este modo para conhecer o tráfego de rede, entender e examinar falsos positivos. Ele monitora e registra todos os alertas de ameaça. Selecione e ative o Diagnóstico e o log de WAF. O WAF não bloqueia as solicitações de entrada quando está operando no modo de detecção.
- Modo de prevenção: bloqueia as invasões e os ataques detectados pelas regras. O invasor recebe a exceção "403 acesso não autorizado", e a conexão é encerrada. O modo de Prevenção registra tais ataques nos logs do WAF.

Estabeleça a linha de base do tráfego de rede com o modo de detecção do WAF. Depois de determinar o tráfego necessário, configure o WAF no modo de prevenção para bloquear o tráfego indesejado.

Acompanhe as recomendações de severidade alta da Central de Segurança para os recursos habilitados para Web que não são protegidos pelo WAF.  

- [Regras e grupos de regras CRS do Firewall de Aplicativo Web](ag/application-gateway-crs-rulegroups-rules.md) 

- [Modos do WAF no Gateway de Aplicativo](ag/ag-overview.md#waf-modes)

- [Modos do WAF no Front Door](afds/afds-overview.md#waf-modes)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar as comunicações com endereços IP maliciosos conhecidos

**Diretrizes**: use regras personalizadas com o WAF (Firewall de Aplicativo Web do Azure) para permitir e bloquear o tráfego. Por exemplo, é possível bloquear todo o tráfego proveniente de um intervalo de endereços IP. Configure o WAF do Azure para execução no modo de prevenção, que bloqueia as invasões e os ataques detectados pelas regras. O invasor recebe a exceção "403 acesso não autorizado", e a conexão é encerrada. O modo de Prevenção registra tais ataques nos logs do WAF.

- [Modos do WAF no Gateway de Aplicativo](ag/ag-overview.md#waf-modes)

- [Modos do WAF no Front Door](afds/afds-overview.md#waf-modes)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretrizes**: o WAF (Firewall de Aplicativo Web) do Azure é o principal componente das proteções de aplicativos Web do Azure. Use o WAF do Azure para proteção centralizada de aplicativos Web contra explorações e vulnerabilidades comuns com conjunto de regras gerenciado pré-configurado, contra assinaturas de ataque conhecidas das 10 principais categorias do OWASP.

Personalize as regras e os grupos de regras do WAF do Azure de acordo com as necessidades do aplicativo Web e elimine falsos positivos. Associe uma política do WAF do Azure para cada site protegido por ele a fim de permitir uma configuração específica do site. O WAF do Azure dá suporte para filtragem geográfica, limitação de taxa e regras do conjunto de regras padrão gerenciadas pelo Azure. É possível criar regras personalizadas de acordo com as necessidades do aplicativo. 

Configure o WAF do Azure para execução no modo de prevenção após estabelecer a linha de base do tráfego de rede no modo de detecção por um determinado período. O WAF do Azure bloqueia as invasões e os ataques detectados pelas regras no modo de prevenção. O invasor recebe a exceção "403 acesso não autorizado", e a conexão é encerrada. O modo de Prevenção registra tais ataques nos logs do WAF.

- [Modos do WAF no Gateway de Aplicativo](ag/ag-overview.md#waf-modes)

- [Modos do WAF no Front Door](afds/afds-overview.md#waf-modes)

- [Regras e grupos de regras CRS do Firewall de Aplicativo Web](ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: crie, associe e organize logicamente os recursos de uma assinatura do Azure com marcas para detecção de erros comuns na configuração de aplicativos (por exemplo, Apache e IIS). 

Aplique regras e grupos de regras às políticas do WAF (Firewall de Aplicativo Web) do Azure com base nos metadados de marca aplicados.

- [Política do WAF no Gateway de Aplicativo](/cli/azure/network/application-gateway/waf-policy) 

- [Política do WAF no Front Door](/cli/azure/ext/front-door/network/front-door/waf-policy)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Documentar regras de configuração de tráfego

**Diretrizes**: use marcas para grupos de segurança de rede associados ao WAF (Firewall de Aplicativo Web) do Azure na sub-rede do Gateway de Aplicativo do Azure, bem como demais recursos relacionados à segurança de rede e ao fluxo de tráfego. Para regras de grupo de segurança de rede individuais, use o campo "Descrição" a fim de especificar a necessidade empresarial, a duração e outros elementos das regras que permitem o tráfego em uma rede.

Use qualquer uma das definições integradas do Azure Policy relacionadas à marca, como “Exigir marca e seu valor”, para que todos os recursos sejam criados com marcas e para gerar notificações quando há recursos não marcados.

Escolha o Azure PowerShell ou a CLI do Azure para pesquisar ou executar ações com recursos com base em suas marcas.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretrizes**: use o Log de Atividades do Azure para monitorar as configurações dos recursos de rede e detectar alterações nas configurações de rede e nos recursos relacionados às implantações do WAF (Firewall de Aplicativo Web) do Azure. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em configurações ou recursos de rede críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para obter mais informações, confira [Azure Security Benchmark: log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: crie uma regra de rede no WAF (Firewall de Aplicativo Web) do Azure para permitir o acesso a um servidor NTP com a porta e o protocolo apropriados, como a porta 123 sobre UDP.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central de log de segurança

**Diretrizes**: configure os logs do WAF (Firewall de Aplicativo Web) do Azure para serem enviados a uma solução central de gerenciamento de logs de segurança, como o Azure Sentinel ou um SIEM (Gerenciamento de Eventos e Informações de Segurança) de terceiros. Isso inclui os logs de atividade do Azure, de diagnóstico e do WAF em tempo real. É possível ver os logs em várias ferramentas, como o Azure Monitor, o Excel e o Power BI. Os logs do Firewall de Aplicativo Web do Azure fornecem insights sobre quais dados o WAF do Azure está avaliando, correspondendo e bloqueando.

O Azure Sentinel tem uma pasta de trabalho interna do WAF do Azure, que mostra uma visão geral dos eventos de segurança no WAF do Azure. A pasta de trabalho inclui eventos, regras correspondentes e bloqueadas e todo o resto que é registrado nos logs do firewall. Essa telemetria pode ser usada para iniciar a automação do guia estratégico para notificar ou realizar ações de correção com base nos eventos do WAF coletados pelo Azure Sentinel.

- [Exibir logs de atividade](../azure-resource-manager/management/view-activity-logs.md)

- [Logs de diagnóstico do Gateway de Aplicativo](../application-gateway/application-gateway-diagnostics.md)

- [Conectar dados do Firewall de Aplicativo Web da Microsoft ao Azure Sentinel](../sentinel/connect-azure-waf.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: habilite o registro em log nos recursos do WAF (Firewall de Aplicativo Web) do Azure para acesso a logs de auditoria, de segurança e de diagnóstico. O Firewall de Aplicativo Web do Azure oferece relatórios detalhados sobre cada uma das ameaças detectadas, disponibilizados nos logs de diagnóstico configurados. Logs de atividade, que estão automaticamente disponíveis, incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis.

- [Visão geral dos logs](ag/ag-overview.md#logging)

- [Visão geral da consulta de logs do Azure Monitor](../azure-monitor/logs/log-query-overview.md)

- [Visão geral dos logs da plataforma do Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: envie os logs do WAF (Firewall de Aplicativo Web) do Azure a uma conta de armazenamento personalizada e defina a política de retenção. Use o Azure Monitor para definir o período de retenção do workspace do Log Analytics de acordo com os regulamentos de conformidade da sua organização.
- [Configurar o monitoramento para uma conta de armazenamento](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e examinar os logs

**Diretrizes**: o WAF (Firewall de Aplicativo Web) do Azure gera relatórios detalhados sobre cada ameaça detectada. O registro em log é integrado aos logs de Diagnóstico do Azure, que mostram informações avançadas sobre operações e erros, que são importantes para auditoria e solução de problemas. 

As instâncias do WAF do Azure são integradas à Central de Segurança para enviar alertas e informações de integridade para relatórios. Use as recomendações da Central de Segurança para detectar aplicativos Web desprotegidos e proteger esses recursos vulneráveis. 

O Azure Sentinel tem uma pasta de trabalho interna de eventos de firewall do WAF, que mostra uma visão geral dos eventos de segurança no WAF. Isso inclui eventos, regras de correspondentes e bloqueadas e todo o resto que é registrado nos logs do firewall.

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](../azure-monitor/index.yml) 

- [Como habilitar as configurações de diagnóstico do Gateway de Aplicativo do Azure](../application-gateway/application-gateway-diagnostics.md)

- [Monitoramento de métricas e logs no Azure Front Door Service](../frontdoor/front-door-diagnostics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Habilitar alertas sobre atividades anormais

**Diretrizes**: habilite as configurações de diagnóstico do log de atividades do Azure e as configurações de diagnóstico das instâncias do WAF do Azure e envie os logs para um workspace do Log Analytics. Faça consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados coletados. Crie alertas para atividade anômala com base nas métricas do WAF. Por exemplo, se o número de solicitações bloqueadas exceder "X", faça "Y".

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como criar alertas no Azure](../azure-monitor/alerts/tutorial-response.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Diretrizes**: implante o WAF (Firewall de Aplicativo Web) do Azure na frente de aplicativos Web críticos para que haja uma inspeção adicional do tráfego de entrada. 

O Azure WAF realiza proteção centralizada de aplicativos Web contra explorações e vulnerabilidades comuns, além de inspecionar o tráfego da Web de entrada e bloquear ataques como injeção de SQL, cross-site scripting, carregamentos de malware e ataques de DDoS para proteger os aplicativos.

- [Como implantar o WAF do Azure](ag/create-waf-policy-ag.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, confira [Azure Security Benchmark: controle de acesso e identidade](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Diretrizes**: o Azure AD (Azure Active Directory) tem funções internas que podem ser consultadas e exigem atribuição explícita. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc e descobrir contas que sejam membros de grupos administrativos.

- [Como obter uma função do diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Como obter membros de uma função do diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretrizes**: não há atribuições de administrador local disponíveis no WAF. No entanto, pode haver funções Administrador do Azure AD (Azure Active Directory) no ambiente que permitam o controle de gerenciamento dos recursos do WAF do Azure.
É uma boa prática criar procedimentos operacionais padrão para o uso de contas administrativas dedicadas que têm acesso às instâncias do WAF (Firewall de Aplicativo Web) do Azure. Use os recursos de gerenciamento de identidade e acesso da Central de Segurança para monitorar a quantidade de contas administrativas.

- [Entenda a identidade e o acesso da Central de Segurança do Azure](../security-center/security-center-identity-access.md)

- [Entender como criar usuários administrativos no Banco de Dados do Azure para PostgreSQL](../postgresql/howto-create-users.md#the-server-admin-account)

- [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: habilite a MFA (Autenticação Multifator) do Azure AD (Azure Active Directory) e siga as recomendações de gerenciamento de acesso e identidade da Central de Segurança.

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: use PAWs (estações de trabalho com acesso privilegiado) com a MFA (Autenticação Multifator) configurada para fazer logon e configurar os recursos do WAF (Firewall de Aplicativo Web) do Azure e recursos relacionados. 

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrar atividades suspeitas em contas administrativas e alertar sobre elas

**Diretrizes**: use os relatórios de segurança do Azure AD (Active Directory) para gerar logs e alertas quando atividades suspeitas ou não seguras ocorrerem no ambiente. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: configure a condição de localização de uma política de acesso condicional e gerencie as localizações nomeadas. 

Crie agrupamentos lógicos de intervalos de endereços IP, países e regiões com as localizações nomeadas. Restrinja o acesso a recursos confidenciais, como os segredos do Azure Key Vault, às localizações nomeadas configuradas.

- [Quais são as condições de localização no acesso condicional do Azure Active Directory?](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: use o Azure AD (Active Directory) como o sistema de autenticação e autorização central. O Azure AD protege os dados inativos e em trânsito com criptografia forte, além incluir sal e hash nas credenciais de usuário e armazená-las em segurança.
- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure AD (Azure Active Directory) fornece logs para ajudar na descoberta de contas obsoletas. Use as revisões de acesso de identidade do Azure para gerenciar com eficiência associações a grupos, acesso a aplicativos empresariais e atribuições de função. Examine o acesso dos usuários regularmente para garantir que somente os usuários ativos tenham acesso contínuo.

- [Entenda os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorar as tentativas de acessar credenciais desativadas

**Diretrizes**: integre as fontes de log de Atividade de Entrada, Auditoria e Evento de Risco do Azure AD (Active Directory) a qualquer ferramenta SIEM ou de monitoramento, como o Azure Sentinel.

Para simplificar esse processo, crie configurações de diagnóstico para as contas de usuário do Azure AD (Active Directory) e envie os logs de auditoria e de entrada a um workspace do Log Analytics. Configure os alertas desejados no workspace do Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alertar sobre desvios de comportamento de entrada na conta

**Diretrizes**: use os recursos de proteção de identidade e proteção contra riscos do Azure AD (Active Directory) para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuários. Faça a ingestão dos dados no Azure Sentinel para uma investigação mais aprofundada.

- [Como exibir entradas suspeitas do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretrizes**: use marcas para auxiliar no acompanhamento do WAF (Firewall de Aplicativo Web) do Azure ou de recursos relacionados que armazenam ou processam informações confidenciais.
- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente um isolamento usando assinaturas e grupos de gerenciamento separados em domínios de segurança individuais, como o tipo de ambiente e o nível de confidencialidade de dados (por exemplo, ambientes de desenvolvimento, de teste e de produção). 

Controle o acesso aos recursos do Azure com o RBAC (controle de acesso baseado em função) do Azure.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: criptografe todas as informações confidenciais em trânsito. Limite a conexão às instâncias do WAF (Firewall de Aplicativo Web) do Azure e aos recursos relacionados a clientes que dão suporte para TLS 1.2 ou superior.

Siga as recomendações da Central de Segurança para criptografia em repouso e em trânsito, quando aplicável.

- [Entenda a criptografia em trânsito com o Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos

**Diretrizes**: controle o acesso aos recursos do Azure com o RBAC (controle de acesso baseado em função) do Azure.
- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: use a criptografia em repouso para todos os recursos do Azure, incluindo o WAF (Firewall de Aplicativo Web) do Azure e recursos relacionados. A Microsoft recomenda permitir que o Azure gerencie as chaves de criptografia. No entanto, há a opção de gerenciar as próprias chaves em algumas instâncias.

- [Noções básicas sobre a criptografia em repouso do Azure](../security/fundamentals/encryption-atrest.md)

- [Como configurar chaves de criptografia gerenciadas pelo cliente](../storage/common/customer-managed-keys-configure-key-vault.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: configure o WAF (Firewall de Aplicativo Web) do Azure para execução no modo de prevenção, após estabelecer a linha de base do tráfego de rede no modo de detecção por um período predeterminado. 

No modo de prevenção, o WAF do Azure bloqueia as invasões e os ataques detectados pelas regras. O invasor recebe uma exceção "403 acesso não autorizado" e a conexão é encerrada. O modo de Prevenção registra tais ataques nos logs do WAF.

- [Visão geral da integração entre o Gateway de Aplicativo e a Central de Segurança do Azure](../security-center/security-center-partner-integration.md)

- [Modos do WAF no Gateway de Aplicativo](ag/ag-overview.md#waf-modes)

- [Modos do WAF no Front Door](afds/afds-overview.md#waf-modes)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, confira [Azure Security Benchmark: gerenciamento de inventário e ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usar uma solução de descoberta de ativos automatizada

**Diretrizes**: use o Azure Resource Graph para consultar ou descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos e assim por diante) em suas assinaturas. 

Configure permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas. Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretrizes**: use marcas nas políticas do WAF (Firewall de Aplicativo Web) do Azure. É possível associar as marcas a recursos e aplicá-las logicamente para organizar o acesso aos recursos em uma assinatura de cliente. 

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos do Azure não autorizados

**Diretrizes**: use marcas, grupos de gerenciamento e assinaturas separadas, sempre que apropriado, para organizar e rastrear os recursos do WAF do Azure e relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário de recursos aprovados do Azure

**Diretrizes**: crie um inventário de recursos aprovados, incluindo a configuração com base nas necessidades organizacionais.

Use o Azure Policy para restringir os tipos de recursos criados nas suas assinaturas. Use o Azure Resource Graph para consultar e descobrir recursos dentro das assinaturas deles. Exija a aprovação de todos os recursos do Azure presentes no ambiente.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorar recursos do Azure não aprovados

**Diretrizes**: use o Azure Policy para restringir os tipos de recursos criados nas suas assinaturas.
Use o Azure Resource Graph para consultar ou descobrir os recursos do WAF (Firewall de Aplicativo Web) do Azure em suas assinaturas. Exija a aprovação de todos os recursos do WAF do Azure e relacionados presentes no ambiente.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Diretrizes**: monitore e remova recursos do WAF do Azure não aprovados com o Azure Policy, a fim de negar a implantação do WAF do Azure ou de um determinado tipo dele, como o WAF do Azure v1 em vez de v2.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: use o Azure Policy para restringir os serviços que são provisionados no ambiente.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade de interação dos usuários com o Azure Resource Manager

**Diretrizes**: use o acesso condicional do Azure para limitar a interação de um usuário com o Azure Resource Manager, por meio da configuração "Bloquear acesso" para o aplicativo "Gerenciamento do Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separar física ou logicamente os aplicativos de alto risco

**Diretrizes**: é possível associar o WAF (Firewall de Aplicativo Web) do Azure a ambientes diferentes por meio de redes, grupos de recursos ou assinaturas, a fim de separar aplicativos de alto risco.

- [Visão geral da Rede Virtual do Microsoft Azure](../virtual-network/virtual-networks-overview.md)

- [Organizar seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md)

- [Guia de decisão da assinatura](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, confira [Azure Security Benchmark: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para as configurações de rede relacionadas a implantações do WAF (Firewall de Aplicativo Web) do Azure.
Use aliases do Azure Policy no namespace "Microsoft.Network" para criar políticas personalizadas a fim de auditar ou impor a configuração de rede dos Gateways de Aplicativo do Azure, das redes virtuais e dos grupos de segurança de rede, bem como usar definições de política internas.

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretrizes**: use os efeitos [negar] e [implantar se não existir] do Azure Policy para impor configurações seguras nos recursos do WAF (Firewall de Aplicativo Web) do Azure e recursos relacionados. 

Use modelos do Azure Resource Manager para manter a configuração de segurança do WAF do Azure e dos recursos relacionados exigidos pela sua organização.

- [Entenda os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

- [Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Visão geral dos modelos do Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Diretrizes**: use o Azure DevOps para armazenar e gerenciar código com segurança, como políticas personalizadas do Azure e modelos do Azure Resource Manager. 

Conceda ou negue permissões a usuários específicos, a grupos de segurança internos ou a grupos definidos no Azure AD [Active Directory] (se integrado ao Azure DevOps) ou Active Directory (se integrado ao TFS [Team Foundation Server]).

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implantar ferramentas de gerenciamento de configuração para recursos do Azure

**Diretrizes**: use as definições internas do Azure Policy, bem como aliases do Azure Policy no namespace "Microsoft.Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Documentação do Azure Policy](../governance/policy/index.yml)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: use as definições internas do Azure Policy, bem como aliases do Azure Policy no namespace "Microsoft.Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. 

Use os efeitos [auditoria], [negar] e [implantar se não existir] do Azure Policy para impor automaticamente as configurações a recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Documentação do Azure Policy](../governance/policy/index.yml)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: use o Azure Key Vault para armazenar certificados com segurança. O Azure Key Vault é um repositório de segredos gerenciado por plataforma que você pode usar para proteger segredos, chaves e certificados SSL. 

O Gateway de Aplicativo v2 do Azure dá suporte à integração com o Key Vault para certificados de servidor que estejam anexados a ouvintes habilitados para HTTPS. 

- [Como configurar o encerramento de SSL com certificados do Key Vault usando o Azure PowerShell](../application-gateway/configure-keyvault-ps.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretrizes**: implemente o Verificador de Credenciais para identificar credenciais no código, o que também incentiva a movimentação das credenciais descobertas para locais mais seguros, como o Azure Key Vault.
- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, confira [Azure Security Benchmark: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção dos backups e das chaves gerenciadas pelo cliente

**Diretrizes**: habilite a exclusão temporária no Azure Key Vault. A exclusão temporária permite a recuperação de cofres de chaves e objetos excluídos, como chaves, segredos e certificados.

- [Como usar a exclusão temporária do Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretrizes**: elabore um guia de resposta a incidentes para sua organização. Crie planos de resposta a incidentes por escrito, que definam todas as funções de pessoal e as fases de tratamento e gerenciamento de incidentes, desde a detecção até a revisão posterior. 

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Use o Guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a Central de Segurança atribui uma severidade a cada alerta para ajudar a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.
Marque as assinaturas de modo evidente (por exemplo: produção ou não produção) e crie um sistema de nomeação para identificar e categorizar recursos do Azure com clareza.

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.
- [Veja a publicação do NIST Guia para testar, treinar e exercitar programas para planos de TI e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.
- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte os alertas e as recomendações da Central de Segurança usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Use o conector de dados da Central de Segurança do Azure para transmitir os alertas ao Azure Sentinel de acordo com os requisitos da sua organização.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: use o recurso de Automação de Fluxo de Trabalho da Central de Segurança para disparar automaticamente respostas a alertas e recomendações de segurança por meio de "Aplicativos Lógicos".
- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, confira [Azure Security Benchmark: testes de penetração e exercícios de Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes de penetração regulares dos recursos do Azure e garantir a correção de todas as conclusões de segurança críticas

**Diretrizes**: siga as Regras de Participação da Microsoft para garantir que os testes de penetração não violem as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Red Team e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft. 

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)