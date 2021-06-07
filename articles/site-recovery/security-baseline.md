---
title: Linha de base de segurança do Azure para Site Recovery
description: A linha de base de segurança Site Recovery fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bf91c0a6e533acb326fe815c3e3c1088c959a603
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576717"
---
# <a name="azure-security-baseline-for-site-recovery"></a>Linha de base de segurança do Azure para Site Recovery

Essa linha de base de segurança aplica as diretrizes da [1,0 versão de avaliação de segurança do Azure](../security/benchmarks/overview-v1.md) para site Recovery. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao site Recovery. **Controles** não aplicáveis a site Recovery foram excluídos. 

Para ver como Site Recovery é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base de segurança site Recovery](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: Microsoft Azure site Recovery não oferece suporte à implantação em uma rede virtual do Azure. Configure Site Recovery serviço com um ponto de extremidade privado do Azure para impor comunicações seguras em sua rede.

- [Suporte ao link privado Azure Site Recovery](azure-to-azure-how-to-enable-replication-private-endpoints.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Orientação**: Site Recovery serviço dá suporte a marcas de serviço, que permitem que os clientes Abram o tráfego somente para serviços e portas específicos. Os clientes precisam permitir a marca de serviço "AzureSiteRecovery" em seu firewall ou grupo de segurança de rede para permitir o acesso de saída ao serviço de Site Recovery.

- [Conectividade de saída usando marcas de serviço](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Diretrizes**: use marcas de recurso para grupos de segurança de rede e outros recursos relacionados à segurança de rede e ao fluxo de tráfego. Para regras de grupo de segurança de rede individuais, use o campo "Descrição" para documentar as regras que permitem o tráfego de e para uma rede. 

Incorpore qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes. 

Você pode usar Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas. 

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md) 

- [Como criar uma rede virtual do Azure](../virtual-network/quick-create-portal.md) 

- [Como filtrar o tráfego de rede com regras de grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: monitorar as alterações nas configurações de recurso de rede relacionadas ao serviço de site Recovery usando os logs de atividades do Azure. Crie alertas no Azure Monitor para notificá-lo quando críticos Site Recovery recursos de rede forem alterados.

- [Exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Criar, exibir e gerenciar alertas do log de atividades usando o Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: habilitar as configurações de diagnóstico do log de atividades do Azure para log de auditoria e enviar os logs para um espaço de trabalho log Analytics, conta de armazenamento do Azure ou um hub de eventos do Azure para arquivamento.

Use os dados do log de atividades do Azure para determinar o "o que, quem e quando" para quaisquer operações de gravação (PUT, POST e DELETE) executadas em seus recursos do Azure.

Ingerir Site Recovery logs em Azure Monitor para agregar dados de segurança gerados. Em Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento para armazenamento de longo prazo ou arquivamento. Além disso, você pode habilitar o e os dados integrados ao Azure Sentinel ou uma solução SIEM (gerenciamento de incidentes e eventos de segurança) de terceiros.

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Monitorar Site Recovery com os logs do Azure Monitor](monitor-log-analytics.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: habilitar as configurações de diagnóstico do log de atividades do Azure para log de auditoria e enviar os logs para um espaço de trabalho log Analytics, uma conta de armazenamento do Azure ou um hub de eventos do Azure para arquivamento. 

Use os dados do log de atividades do Azure para determinar o "o que, quem e quando" para quaisquer operações de gravação (PUT, POST e DELETE) executadas em seus recursos do Azure.

Ingerir Site Recovery logs com Azure Monitor para agregar dados de segurança gerados. Em Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento para armazenamento de longo prazo/arquivamento. Habilite e integre dados ao Azure Sentinel ou uma solução de SIEM (gerenciamento de incidentes e eventos de segurança) de terceiros.

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Monitorar Site Recovery com os logs do Azure Monitor](monitor-log-analytics.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: defina o período de retenção de log para log Analytics espaços de trabalho associados aos seus cofres dos serviços de recuperação do Azure usando Azure monitor de acordo com os regulamentos de conformidade da sua organização. 

- [Como definir parâmetros de retenção de log](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Diretrizes**: habilitar as configurações de diagnóstico do log de atividades do Azure e enviar os logs para um espaço de trabalho log Analytics. 

Execute consultas em Log Analytics para pesquisar termos, identificar tendências, analisar padrões e informações sobre os dados do log de atividades coletados dos cofres dos serviços de recuperação.

- [Monitorar Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como coletar e analisar os logs de atividades do Azure no espaço de trabalho Log Analytics no Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: monitorar máquinas replicadas por Azure Site Recovery usando Azure monitor logs e log Analytics. Use Log Analytics em Azure Monitor para gravar e testar consultas de log e para analisar interativamente os dados de log. Azure Monitor coleta logs de atividade e de recursos, juntamente com outros dados de monitoramento. 

Visualize e consulte os resultados do log e configure alertas para executar ações com base em dados monitorados. Configure alertas em um espaço de trabalho Log Analytics para o Azure Sentinel, pois ele fornece uma solução de disparar (resposta automatizada de orquestração de segurança). Isso permite que soluções automatizadas, como guias estratégicos, sejam criadas e usadas para corrigir problemas de segurança. Crie alertas de log personalizados em seu espaço de trabalho do Log Analytics usando Azure Monitor. 

- [Monitorar Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Criar, exibir e gerenciar alertas de log usando o Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: nenhuma função é atribuída por padrão. Eles precisam ser atribuídos explicitamente com base na necessidade dos negócios. Qualquer atribuição de função pode ser verificada com a CLI do PowerShell ou Azure Active Directory (Azure AD) para descobrir contas que são membros de grupos administrativos.

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use os recursos de gerenciamento de identidade e acesso da central de segurança para monitorar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar as contas administrativas dedicadas, use as recomendações da central de segurança ou de políticas internas do Azure, como: 

- Deve haver mais de um proprietário atribuído à sua assinatura 
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura 

- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

Crie um processo para controlar a identidade e o controle de acesso para contas administrativas e examiná-las periodicamente.

- [Como usar a central de segurança do Azure para monitorar a identidade e o acesso](../security-center/security-center-identity-access.md)

- [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Orientação**: Use o registro de aplicativo do Azure com uma entidade de serviço para recuperar um token a ser usado para interagir com seus cofres dos serviços de recuperação por meio de chamadas à API.

- [Como chamar as APIs REST do Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Como registrar seu aplicativo cliente (entidade de serviço) com o Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informações da API dos serviços de recuperação do Azure](/rest/api/recoveryservices)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Orientação**: habilitar o Azure AD, autenticação multifator e seguir as recomendações de acesso e identidade da central de segurança. 
- [Planejar uma implantação da autenticação multifator do Azure AD](../active-directory/authentication/howto-mfa-getstarted.md)

- [Monitorar a identidade e acesso](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Orientação**: Use uma estação de trabalho segura e gerenciada pelo Azure (também conhecida como Paw (estação de trabalho de acesso privilegiado) com a autenticação multifator do Azure para tarefas administrativas e para executar ações privilegiadas em site Recovery recursos.

- [Estações de trabalho com acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Planejar uma implantação da Autenticação Multifator do Azure AD baseada em nuvem](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use o recurso PIM (Privileged Identity Management do Azure ad para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorrer no ambiente.
Exibir alertas e relatórios sobre o comportamento do usuário arriscado com o recurso de detecção de riscos do Azure AD.

- [Como implantar o Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Entenda as detecções de risco do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: gerenciar recursos do Azure somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso ao portal do Azure apenas de agrupamentos lógicos específicos de intervalos de endereços IP, regiões ou países.
- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use o Azure ad como o sistema de autenticação e autorização central para site Recovery. O Azure AD protege os dados usando criptografia forte para dados em repouso, em trânsito e também em Salts, em hashes e armazena com segurança as credenciais do usuário. 

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Orientação**: Use os logs do Azure ad para ajudar a descobrir contas obsoletas. 

Gerencie com eficiência associações de grupo, acesso a aplicativos empresariais e atribuições de função com a identidade e as revisões de acesso do Azure AD. 

Crie um processo para revisar o acesso do usuário regularmente para garantir que somente os usuários com revisões de acesso concluídas tenham acesso contínuo. 

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Orientação**: Use o Azure ad como o sistema de autenticação e autorização central para site Recovery recursos. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito e também Salts, hashes e armazena com segurança as credenciais do usuário.

Você tem acesso à atividade de entrada do Azure AD, auditoria e fontes de log de eventos de risco, que permitem integrá-los com o Azure Sentinel ou qualquer ferramenta de monitoramento ou SIEM disponível no Azure Marketplace.

Simplifique ainda mais esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando os logs de auditoria e de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas desejados em um espaço de trabalho Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Orientação**: Use o Azure ad como o sistema de autenticação e autorização central para seus cofres dos serviços de recuperação. 

Empregue recursos de proteção de identidade do Azure AD para detecção de comportamento de logon de conta e para configurar respostas automatizadas para ações suspeitas detectadas, conforme relacionado a identidades de usuário. Também ingerir dados no Azure Sentinel para investigação adicional.

- [Como exibir o logon arriscado do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente assinaturas ou grupos de gerenciamento separados para desenvolvimento, teste e cofres de serviços de recuperação de produção. Separe os recursos com uma rede virtual ou sub-rede, marcados adequadamente e protegidos por um grupo de segurança de rede ou firewall do Azure. 

Desligue as máquinas virtuais, que armazenam ou processam dados confidenciais, quando não estão em uso. Implemente políticas e procedimentos para tornar esse processo recorrente. 

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Visão geral do Site Recovery](site-recovery-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: Use o link privado ou ponto de extremidade privado, grupos de segurança de rede e marcas de serviço para atenuar todas as oportunidades de vazamento de dados das máquinas virtuais habilitadas site Recovery.

A Microsoft gerencia a plataforma subjacente usada pelo Site Recovery e trata todo o conteúdo do cliente como confidencial e protege contra exposição e perda de dados do cliente. A Microsoft implementou e mantém um conjunto de recursos e controles de proteção de dados robustos para garantir que os dados do cliente no Azure permaneçam seguros. 

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

- [Replicar máquinas virtuais com pontos de extremidade privados do Azure](azure-to-azure-how-to-enable-replication-private-endpoints.md)

- [Replicar máquinas virtuais com marcas de serviço Azure Site Recovery](azure-to-azure-about-networking.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações confidenciais em trânsito

**Diretrizes**: o site Recovery usa um canal https seguro, criptografado usando criptografia AES (AES 256), de servidores de carga de trabalho do Azure para site Recovery serviços hospedados atrás de um cofre dos serviços de recuperação.

As versões atuais do TLS com suporte para Site Recovery são TLS 1,0, TLS 1,1, TLS 1,2 em regiões, que estavam ativas até o final de 2019. O TLS 1.2 é a única versão de TLS com suporte para novas regiões.

- [Noções básicas sobre criptografia em trânsito para Azure Site Recovery](physical-azure-set-up-source.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para site Recovery. 

Implemente uma solução de terceiros, conforme necessário, para fins de conformidade.

A Microsoft gerencia a plataforma subjacente usada pelo Site Recovery e trata todo o conteúdo do cliente como confidencial e protege contra perda e exposição de dados do cliente. Ele implementou e mantém um conjunto de recursos e controles robustos de proteção de dados para garantir que os dados do cliente no Azure permaneçam seguros. 

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usar o RBAC do Azure para gerenciar o acesso aos recursos

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para gerenciar o acesso a dados e recursos relacionados a recursos de site Recovery. 

Separe as tarefas de trabalho com o RBAC do Azure e conceda o acesso apropriado necessário para elas. Use as funções de Site Recovery internas para controlar as operações de gerenciamento de Site Recovery.

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

- [Usar o controle de acesso Role-Based para gerenciar Azure Site Recovery](site-recovery-role-based-linked-access-control.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: habilite a criptografia dupla com as chaves de plataforma e gerenciadas pelo cliente. Essa funcionalidade está disponível em Site Recovery. 

O Site Recovery dá suporte à criptografia em repouso para dados. Para cargas de trabalho de IaaS do Azure, os dados são criptografados em repouso usando Criptografia do Serviço de Armazenamento (SSE). 

Somente o cliente tem acesso à chave de criptografia ao usar um cofre de serviços de recuperação criptografado com uma chave gerenciada pelo cliente. A Microsoft nunca mantém uma cópia, não tem acesso à chave e não descriptografa os dados transferidos do local principal para a recuperação de desastres em qualquer ponto. 

- [Suporte a chaves gerenciadas pelo cliente para Azure Site Recovery](azure-to-azure-how-to-enable-replication-cmk-disks.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com os logs de atividades do Azure para criar alertas quando as alterações ocorrerem em recursos críticos,. Esses recursos podem incluir instâncias de produção de cofres dos serviços de recuperação, recursos do serviço Site Recovery e recursos relacionados.
- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar ou descobrir todos os recursos, incluindo cofres de serviços de recuperação, dentro de suas assinaturas. Certifique-se de que as permissões de leitura apropriadas em seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretrizes**: aplique marcas a cofres dos serviços de recuperação e outros recursos relacionados, usados por site Recovery com metadados, para organizá-los logicamente em uma taxonomia.
- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e controlar site Recovery (cofres dos serviços de recuperação) e outros recursos relacionados. 

Além disso, use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna: 

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definir e manter um inventário de recursos aprovados do Azure

**Diretrizes**: Crie um inventário dos recursos aprovados do Azure e do software aprovado para recursos de computação com base nos requisitos organizacionais do cliente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna: 

- Tipos de recursos não permitidos 
- Tipos de recursos permitidos

Use o grafo de recursos do Azure para consultar e descobrir recursos dentro das assinaturas.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos 
- Tipos de recursos permitidos

Compreender como criar e gerenciar políticas no Azure é importante para manter a conformidade com padrões corporativos e com contratos de nível de serviço.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de "Gerenciamento do Microsoft Azure". Isso pode impedir a criação e alterações em recursos em um ambiente de alta segurança.

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para seu cofre de serviços de recuperação com Azure Policy. 

Use Azure Policy aliases no namespace "Microsoft. Recoveryservices" para criar políticas personalizadas para auditoria ou impor a configuração dos recursos do cofre dos serviços de recuperação do serviço Site Recovery.
- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Orientação**: Use os efeitos de Azure Policy [Deny] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.
- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Orientação**: escolha Azure Repos para armazenar e gerenciar seu código com segurança se você estiver usando definições de Azure Policy personalizadas para seus cofres de serviços de recuperação e recursos relacionados.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentação do Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. recoveryservices" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. 

Desenvolva também um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. recoveryservices" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. 

Use os efeitos de Azure Policy [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para os recursos do Azure.
- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: o cliente deve gerenciar site Recovery segredos integrados ao Azure Key Vault, ao mesmo tempo em que permite a recuperação de desastre para máquinas virtuais habilitadas para Azure Disk Encryption. 

- [Como criar um cofre de chaves](../key-vault/general/quick-create-portal.md)

- [Como autenticar no Key Vault](../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso do cofre de chaves](../key-vault/general/assign-access-policy-portal.md)

- [Como habilitar a DR para máquinas virtuais habilitadas para Azure Disk Encryption usando Site Recovery](azure-to-azure-how-to-enable-replication-ade-vms.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretrizes**: Site Recovery dá suporte somente à identidade gerenciada pelo sistema, na qual um cliente pode habilitar a identidade gerenciada pelo sistema no cofre dos serviços de recuperação. A mesma metodologia se aplica aos recursos usados na oferta de recuperação de desastre para definir o limite de acesso. 

Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. 

Identidades gerenciadas permitem que você se autentique em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Key Vault, sem nenhuma credencial em seu código.

- [Como integrar com identidades gerenciadas do Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md?tabs=core2x)

- [Como habilitar a identidade gerenciada do sistema no cofre dos serviços de recuperação](azure-to-azure-how-to-enable-replication-private-endpoints.md#enable-the-managed-identity-for-the-vault)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, site Recovery), no entanto, ele não é executado no seu conteúdo. Examine previamente todos os arquivos que estão sendo carregados em recursos não computados do Azure, como serviço de aplicativo, Data Lake Storage e armazenamento de BLOBs.

Use a detecção de ameaças da central de segurança para serviços de dados para detectar malwares carregados em contas de armazenamento.

- [Entender o Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais](../security/fundamentals/antimalware.md)

- [Entender a detecção de ameaças da central de segurança do Azure para serviços de dados](../security-center/azure-defender.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: o site Recovery usa internamente uma conta de armazenamento do Azure para manter o estado da solução de recuperação de desastre, conforme configurado pelos clientes em suas cargas de trabalho.

Todos os recursos de armazenamento usados por Site Recovery serviços de metadados com configuração do tipo: armazenamento com redundância geográfica com acesso de leitura (RA-GRS). As contas de armazenamento do tipo acima de GRS (como RAGRS, pano-ZRS) replicam seus dados para uma região secundária (centenas de quilômetros de distância do local principal dos dados de origem) para continuar a atender à recuperação de desastres para clientes durante interrupções.

Isso está fora do escopo do cliente e a equipe de Site Recovery cuida internamente. O cliente pode fazer backup Key Vault chaves no Azure.

- [Como fazer backup de chaves do cofre de chaves no Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: testar periodicamente restaurações de chaves gerenciadas pelo cliente de backup.

- [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: os dados são criptografados em repouso usando criptografia do serviço de armazenamento (SSE) com máquinas virtuais baseadas em IaaS (infraestrutura como serviço) do Azure. Habilite a exclusão reversível no Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.

- [Como habilitar a exclusão reversível no Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. 

Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como fases de manipulação ou gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [O cliente também pode aproveitar o guia de tratamento de incidentes de segurança do computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Orientação**: priorize quais alertas devem ser investigados primeiro com base na severidade de alerta atribuída à central de segurança. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Marque as assinaturas claramente (por exemplo, produção, não produção) e crie um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md) 

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identificar pontos fracos e lacunas e revisar o plano conforme necessário

- [Consulte o guia de publicação do NIST para testar, treinar e exercitar programas para planos de ti e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal. 

Crie um processo para examinar incidentes, postar ocorrência, para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. 

Use o conector de dados da central de segurança para transmitir os alertas para o Azure Sentinel, conforme necessário.
- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações.
- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretriz**: siga as regras de participação da Microsoft para garantir que os testes de penetração não violem as políticas da Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de Red Team e testes de penetração de sites ao vivo em infraestrutura, serviços e aplicativos de nuvem gerenciados pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)