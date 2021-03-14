---
title: Linha de base de segurança do Azure para o banco de dados do Azure para PostgreSQL-servidor único
description: O banco de dados do Azure para PostgreSQL-a linha de base de segurança de servidor único fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d1ff047c44833a94ae9db0a410f501a1f23a369f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576238"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql---single-server"></a>Linha de base de segurança do Azure para o banco de dados do Azure para PostgreSQL-servidor único

A linha de base de segurança do Azure para o banco de dados do Azure para PostgreSQL-um único servidor contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte o [benchmark de segurança do Azure: segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretriz**: Configure o Link Privado para o Banco de Dados do Azure para PostgreSQL com pontos de extremidade privados. O Link Privado permite que você se conecte a vários serviços de PaaS no Azure por meio de um ponto de extremidade privado. O Link Privado do Azure essencialmente traz os serviços do Azure dentro de sua VNet (Rede Virtual privada). O tráfego entre sua rede virtual e a instância do PostgreSQL viaja pela rede de backbone da Microsoft.

Uma alternativa é o uso dos pontos de extremidade de serviço de rede virtual para proteger e limitar o acesso da rede às implementações do seu Banco de Dados do Azure para PostgreSQL. As regras da rede virtual são um recurso de segurança de firewall que controla se o servidor do Banco de Dados do Azure para PostgreSQL aceita comunicações que sejam enviadas de sub-redes particulares em redes virtuais.

Você também pode proteger seu servidor de Banco de Dados do Azure para PostgreSQL com regras de firewall. O firewall do servidor impede todo acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. Para configurar seu firewall, você deve criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível de servidor.

- [Como configurar o link privado para o banco de dados do Azure para PostgreSQL](howto-configure-privatelink-portal.md)

- [Como criar e gerenciar pontos de extremidade de serviço VNet e regras de VNet no banco de dados do Azure para PostgreSQL](howto-manage-vnet-using-portal.md)

- [Como configurar as regras de firewall do banco de dados do Azure para PostgreSQL](howto-manage-firewall-using-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Diretriz**: Quando a instância do Banco de Dados do Azure para PostgreSQL é protegida para um ponto de extremidade privado, você pode implantar máquinas virtuais na mesma rede virtual. Use um NSG (grupo de segurança de rede) para reduzir o risco de vazamento de dados. Habilite logs de fluxo de NSG e envie logs para uma conta de armazenamento para auditoria de tráfego. Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

- [Como configurar o link privado para o banco de dados do Azure para PostgreSQL](howto-configure-privatelink-portal.md)

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretriz**: não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Diretriz**: Use a Proteção Avançada contra Ameaças para o Banco de Dados do Azure para PostgreSQL. A Proteção Avançada contra Ameaças detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acesso ou exploração dos bancos de dados.

Habilite a Proteção contra DDoS Padrão nas redes virtuais do Azure associadas às suas instâncias do Banco de Dados do Azure para PostgreSQL para proteção contra ataques de negação de serviço distribuído. Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

- [Como configurar a proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL](howto-database-threat-protection-portal.md)

- [Como configurar a proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Diretriz**: Quando a instância do Banco de Dados do Azure para PostgreSQL é protegida para um ponto de extremidade privado, você pode implantar máquinas virtuais na mesma rede virtual. Em seguida, você pode configurar um NSG (grupo de segurança de rede) para reduzir o risco de vazamento de dados. Habilite logs de fluxo de NSG e envie logs para uma conta de armazenamento para auditoria de tráfego. Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretriz**: Use a Proteção Avançada contra Ameaças para o Banco de Dados do Azure para PostgreSQL. A Proteção Avançada contra Ameaças detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acesso ou exploração dos bancos de dados.

- [Como configurar a proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL](howto-database-threat-protection-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretriz**: não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretriz**: Para recursos que precisam de acesso às instâncias do Banco de Dados do Azure para PostgreSQL, use as marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede ou no Firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, SQL.WestUs) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Observação: O Banco de Dados do Azure para PostgreSQL usa a marca de serviço “Microsoft.Sql”.

- [Para obter mais informações sobre como usar marcas de serviço](../virtual-network/service-tags-overview.md)

- [Entender o uso da marca de serviço para o banco de dados do Azure para PostgreSQL](concepts-data-access-and-security-vnet.md#terminology-and-description)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretriz**: Defina e implemente configurações de segurança padrão para configurações de rede e recursos de rede associados às instâncias do Banco de Dados do Azure para PostgreSQL com o Azure Policy. Use aliases do Azure Policy nos namespaces “Microsoft.DBforPostgreSQL” e “Microsoft.Network” para criar políticas personalizadas para auditar ou impor a configuração de rede de suas instâncias do Banco de Dados do Azure para PostgreSQL. Você também usar as definições de política integradas relacionadas à rede ou às instâncias do Banco de Dados do Azure para PostgreSQL, como:

- A Proteção contra DDoS Standard deve ser habilitada

- Impor conexão TLS deve ser habilitada para servidores de banco de dados PostgreSQL

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exemplos de Azure Policy para rede](../governance/policy/samples/index.md)

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Diretriz**: Use marcas para recursos relacionados à segurança de rede e ao fluxo de tráfego para as instâncias do Banco de Dados do Azure para PostgreSQL para fornecer metadados e organização lógica.

Use qualquer uma das definições do Azure Policy integrado relacionadas à marcação, como “Exigir marca e seu valor” para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar o Azure PowerShell ou a CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretriz**: Use o Log de Atividades do Azure para monitorar configurações de recursos de rede e detectar alterações de recursos de rede relacionadas às suas instâncias do Banco de Dados do Azure para PostgreSQL. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Diretriz**: A Microsoft mantém a fonte de tempo usada para recursos do Azure, como o Banco de Dados do Azure para PostgreSQL para carimbos de data/hora nos logs.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Diretriz**: Habilite as configurações de diagnóstico e os logs de servidor e os logs de ingestão para agregar dados de segurança gerados pelas suas instâncias do Banco de Dados do Azure para PostgreSQL. No Azure Monitor, use o Workspace do Log Analytics para consultar e realizar análises, e use contas de Armazenamento do Microsoft Azure para armazenamento de longo prazo/arquivamento. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

- [Como configurar e acessar logs de servidor para o banco de dados do Azure para PostgreSQL](howto-configure-server-logs-in-portal.md)

- [Como configurar e acessar os logs de auditoria do banco de dados do Azure para PostgreSQL](concepts-audit.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o registro em log de auditoria para recursos do Azure

**Diretriz**: Habilite as configurações de diagnóstico em suas instâncias do Banco de Dados do Azure para PostgreSQL para acesso a logs de auditoria, segurança e recursos. Verifique se você habilitou especificamente o log de auditoria do PostgreSQL. Logs de atividade, que estão automaticamente disponíveis, incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis. Você também pode habilitar as configurações de diagnóstico do log de atividades do Azure e enviar os logs para o mesmo workspace do Log Analytics ou conta de armazenamento.

- [Como configurar e acessar logs de servidor para o banco de dados do Azure para PostgreSQL](howto-configure-server-logs-in-portal.md)

- [Como configurar e acessar os logs de auditoria do banco de dados do Azure para PostgreSQL](concepts-audit.md)

- [Como definir configurações de diagnóstico para o log de atividades do Azure](../azure-monitor/essentials/activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

**Diretriz**: No Azure Monitor, para o workspace do Log Analytics que está sendo usado para armazenar os logs do Banco de Dados do Azure para PostgreSQL, defina o período de retenção de acordo com os regulamentos de conformidade da sua organização. Use contas de Armazenamento do Microsoft Azure para armazenamentos de longo prazo/arquivamento.

- [Como definir parâmetros de retenção de log para workspaces do Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Armazenando logs de recursos em uma conta de armazenamento do Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretriz**: Analise e monitore logs de suas instâncias do Banco de Dados do Azure para PostgreSQL para comportamento anormal. Use o Log Analytics do Azure Monitor para revisar os logs e realizar consultas nos dados de log. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Para obter mais informações sobre o Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretriz**: Habilite a Proteção Avançada contra Ameaças para o Banco de Dados do Azure para PostgreSQL. A Proteção Avançada contra Ameaças detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acesso ou exploração dos bancos de dados.

Além disso, você pode habilitar os logs do servidor e as configurações de diagnóstico do PostgreSQL e enviar os logs para um Workspace do Log Analytics. Integre seu Workspace do Log Analytics ao Azure Sentinel, pois ele fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Assim os guias estratégicos (soluções automatizadas) podem ser criados e usados para corrigir problemas de segurança.

- [Como habilitar a proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL](howto-database-threat-protection-portal.md)

- [Como configurar e acessar logs de servidor para o banco de dados do Azure para PostgreSQL](howto-configure-server-logs-in-portal.md)

- [Como configurar e acessar os logs de auditoria do banco de dados do Azure para PostgreSQL](concepts-audit.md)

- [Como definir configurações de diagnóstico para o log de atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar o registro em log de antimalware

**Diretriz**: Não aplicável; o Banco de Dados do Azure para PostgreSQL não processa nem produz logs relacionados a antimalware.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilitar o registro em log de consultas DNS

**Diretriz**: Não aplicável; o Banco de Dados do Azure para PostgreSQL não processa nem produz logs relacionados ao DNS.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilitar o registro em log de auditoria de linha de comando

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Diretriz**: Mantenha um inventário das contas de usuário que têm acesso administrativo ao plano de controle (por exemplo, portal do Azure) de suas instâncias do Banco de Dados do Azure para PostgreSQL. Além disso, mantenha um inventário das contas administrativas que têm acesso ao plano de dados (no próprio banco de dados) das suas instâncias do Banco de Dados do Azure para PostgreSQL. (Você deve fornecer credenciais para um usuário administrador ao criar o servidor PostgreSQL. Tal administrador pode ser usado para criar usuários adicionais do PostgreSQL.)

O Banco de Dados do Azure para PostgreSQL não dá suporte ao controle de acesso baseado em função interna, mas você pode criar funções personalizadas com base em operações do provedor de recursos específico.

- [Entender as funções personalizadas da assinatura do Azure](../role-based-access-control/custom-roles.md) 

- [Entender as operações do provedor de recursos do banco de dados do Azure para PostgreSQL](../role-based-access-control/resource-provider-operations.md#microsoftdbforpostgresql) 

- [Entender o gerenciamento de acesso do banco de dados do Azure para PostgreSQL](concepts-security.md#access-management)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar senhas padrão quando aplicável

**Diretriz**: O Azure Active Directory e o Banco de Dados do Azure para PostgreSQL não têm o conceito de senhas padrão.

Após a criação do Banco de Dados do Azure para PostgreSQL em si, o Azure força a criação de um usuário administrativo com uma senha forte. No entanto, depois que a instância do PostgreSQL for criada, você poderá usar a primeira conta de administrador do servidor que você criou para criar usuários adicionais e conceder acesso administrativo a eles. Configure uma senha forte diferente para cada conta ao criá-las.

- [Como criar contas adicionais para o banco de dados do Azure para PostgreSQL](howto-create-users.md)

- [Como atualizar a senha do administrador](howto-create-manage-server-portal.md#update-admin-password)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas que têm acesso às suas instâncias do Banco de Dados do Azure para PostgreSQL. Use a identidade da Central de Segurança do Azure e o gerenciamento de acesso para monitorar a quantidade de contas administrativas. 

- [Entender a identidade e o acesso da central de segurança do Azure](../security-center/security-center-identity-access.md) 

- [Entenda como criar usuários administradores no banco de dados do Azure para PostgreSQL](howto-create-users.md#the-server-admin-account)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Diretriz**: Entrar no Banco de Dados do Azure para PostgreSQL é possível usando o nome de usuário/senha configurados diretamente no banco de dados, bem como usando uma identidade de Azure Active Directory (AD) e um token do Azure Active Directory para se conectar. Métodos diferentes são compatíveis ao usar um token do Azure AD, como um usuário do Azure AD, um grupo do Azure AD ou um aplicativo do Azure AD conectando-se ao banco de dados.

Separadamente, o acesso ao plano de controle para PostgreSQL está disponível por meio da API REST e dá suporte a SSO. Para autenticar, defina o cabeçalho de autorização para suas solicitações como um Token Web JSON que você obtém no Azure Active Directory.

- [Usar Azure Active Directory para autenticação com o banco de dados do Azure para PostgreSQL](howto-configure-sign-in-aad-authentication.md)

- [Entender a API REST do banco de dados do Azure para PostgreSQL](/rest/api/postgresql/)

- [Entender o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretriz**: habilite a autenticação multifator (MFA) do Azure Active Directory e siga as recomendações de gerenciamento de acesso e identidade da Central de Segurança do Azure. Ao utilizar tokens do Azure AD para entrar no banco de dados, isso permite que você exija a autenticação multifator para entradas de banco de dados.

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Usar Azure Active Directory para autenticação com o banco de dados do Azure para PostgreSQL](howto-configure-sign-in-aad-authentication.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usar estações de trabalho seguras e gerenciadas pelo Azure para tarefas administrativas

**Diretriz**: Use PAWs (estações de trabalho com acesso privilegiado) com a MFA (autenticação multifator) configurada para fazer logon e configurar os recursos do Azure.

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretriz**: Habilitar a Proteção Avançada contra Ameaças para o Banco de Dados do Azure para PostgreSQL para gerar alertas de atividades suspeitas.

Além disso, você pode usar o Privileged Identity Management (PIM) do Azure Active Directory (AD) para geração de logs e alertas quando atividades suspeitas ou inseguras ocorrem no ambiente.

Use as detecções de risco do Azure AD para ver alertas e relatórios sobre o comportamento do usuário suspeito.

- [Como configurar a proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL](howto-database-threat-protection-portal.md)

- [Como implantar o Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Entenda as detecções de risco do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretriz**: use localizações nomeadas de acesso condicional para permitir o acesso ao portal e ao Azure Resource Manager somente para agrupamentos lógicos de intervalos de endereços IP ou de países/regiões específicos.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretriz**: use o Azure Active Directory (AD) como o sistema central de autenticação e autorização. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure AD também inclui sais, hashes e armazena com segurança as credenciais do usuário.

Para entrar no Banco de Dados do Azure para PostgreSQL, é recomendável usar o Azure AD e usar um token do Azure AD para se conectar. Métodos diferentes são compatíveis ao usar um token do Azure AD, como um usuário do Azure AD, um grupo do Azure AD ou um aplicativo do Azure AD conectando-se ao banco de dados.

Para controlar as contas de administrador do PostgreSQ, as credenciais do Azure AD também podem ser usadas para administração no nível do plano de gerenciamento (por exemplo, o portal do Azure).

- [Usar Azure Active Directory para autenticação com o banco de dados do Azure para PostgreSQL](howto-configure-sign-in-aad-authentication.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Revisar e reconciliar regularmente o acesso do usuário

**Diretriz**: Revise os logs de Azure Active Directory para ajudar a descobrir contas obsoletas que podem incluir aquelas com funções administrativas do Banco de Dados do Azure para PostgreSQL. Além disso, use as revisões de acesso de identidade do Azure para gerenciar de forma eficiente associações de grupo, o acesso aos aplicativos empresariais que podem ser usados para acessar o Banco de Dados do Azure para PostgreSQL e as atribuições de função. O acesso de usuários deve ser revisado regularmente, a cada 90 dias, para garantir que somente os usuários corretos tenham acesso contínuo.

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

- [Examine os usuários do PostgreSQL e as funções atribuídas](https://www.postgresql.org/docs/current/database-roles.html)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretriz**: Habilite as configurações de diagnóstico do Banco de Dados do Azure para PostgreSQL e do Azure Active Directory, enviando todos os logs para um workspace do Log Analytics. Configure os alertas desejados (como tentativas de autenticação com falha) no Log Analytics.

- [Como configurar e acessar logs de servidor para o banco de dados do Azure para PostgreSQL](howto-configure-server-logs-in-portal.md)

- [Como configurar e acessar os logs de auditoria do banco de dados do Azure para PostgreSQL](concepts-audit.md)

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretriz**: Habilitar a Proteção Avançada contra Ameaças para o Banco de Dados do Azure para PostgreSQL para gerar alertas de atividades suspeitas.

Use os recursos da Azure Active Directory Identity Protection e de detecção de riscos do Azure Active Directory para configurar respostas automatizadas para ações suspeitas detectadas. Você pode habilitar respostas automatizadas por meio do Azure Sentinel para implementar as respostas de segurança da sua organização.

Você também pode ingerir logs no Azure Sentinel para uma investigação mais aprofundada.

- [Como configurar a proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL](howto-database-threat-protection-portal.md)

- [Visão geral do Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Diretriz**: Não disponível no momento; o Sistema de Proteção de Dados do Cliente ainda não é suportado no Banco de Dados do Azure para PostgreSQL.

- [Lista de serviços com suporte Sistema de Proteção de Dados do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: manter um inventário de informações confidenciais

**Diretriz**: Use marcas para auxiliar no rastreamento de instâncias do Banco de Dados do Azure para PostgreSQL ou recursos relacionados que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Use uma combinação de link privado, pontos de extremidade de serviço e/ou regras de firewall para isolar e limitar o acesso à rede para suas instâncias do Banco de Dados do Azure para PostgreSQL.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como configurar o link privado para o banco de dados do Azure para PostgreSQL](howto-configure-privatelink-portal.md)

- [Como criar e gerenciar pontos de extremidade de serviço VNet e regras de VNet no banco de dados do Azure para PostgreSQL](howto-manage-vnet-using-portal.md)

- [Como configurar as regras de firewall do banco de dados do Azure para PostgreSQL](concepts-firewall-rules.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretriz**: Ao usar as máquinas virtuais do Azure para acessar instâncias do Banco de Dados do Azure para PostgreSQL, use o link privado, as configurações de rede do PostgreSQL, os grupos de segurança de rede e as marcas de serviço para atenuar a possibilidade de vazamento de dados.

A Microsoft gerencia a infraestrutura subjacente para o Banco de Dados do Azure para PostgreSQL e implementou controles rígidos para evitar a perda ou a exposição dos dados do cliente.

- [Como mitigar o data vazamento para o banco de dados do Azure para PostgreSQL](concepts-data-access-and-security-private-link.md)

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretriz**: O Banco de Dados do Azure para PostgreSQL dá suporte à conexão do seu servidor do PostgreSQL para aplicativos clientes que usam o protocolo TLS, anteriormente conhecido como protocolo SSL. Impor conexões TLS entre seu servidor de banco de dados e os aplicativos clientes ajuda a proteger contra ataques de “intermediários” ao criptografar o fluxo de dados entre o servidor e seu aplicativo. No portal do Azure, certifique-se de que “Impor conexão SSL” esteja habilitado para todas as instâncias do Banco de Dados do Azure para PostgreSQL por padrão.

Atualmente, a versão do TLS com suporte para o Banco de Dados do Azure para PostgreSQL é TLS 1.0, TLS 1.1, TLS 1.2.

- [Como configurar a criptografia em trânsito para o banco de dados do Azure para PostgreSQL](concepts-ssl-connection-security.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretriz**: Os recursos de identificação de dados, classificação e prevenção de perdas ainda não estão disponíveis para o Banco de Dados do Azure para PostgreSQL. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para controlar o acesso ao banco de dados do Azure para o plano de controle PostgreSQL (por exemplo, portal do Azure). Para o acesso do plano de dados (dentro do próprio banco de dados), use consultas SQL para criar usuários e configurar permissões de usuário. O RBAC do Azure não afeta as permissões de usuário no banco de dados.

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

- [Como configurar o acesso do usuário com o SQL para o banco de dados do Azure para PostgreSQL](howto-create-users.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

A Microsoft gerencia a infraestrutura subjacente para o Banco de Dados do Azure para PostgreSQL e implementou controles rígidos para evitar a perda ou a exposição dos dados do cliente.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretriz**: O serviço Banco de Dados do Azure para PostgreSQL usa o módulo de criptografia validado por FIPS 140-2 para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, são criptografados no disco, com exceção dos arquivos temporários criados durante a execução de consultas. O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A criptografia de armazenamento está sempre ativada e não pode ser desabilitada.

A criptografia de dados com chaves gerenciadas pelo cliente (CMK) para o servidor único do Banco de Dados do Azure para PostgreSQL permite que você traga sua própria chave (BYOK) para proteção de dados em repouso. No momento, é necessário solicitar acesso para usar esta capacidade. Para fazer isso, entre em contato com:

AskAzureDBforPostgreSQL@service.microsoft.com.

- [Entender a criptografia em repouso para o banco de dados do Azure para PostgreSQL](concepts-security.md)

- [Entender a criptografia em repouso para o banco de dados do Azure para PostgreSQL usando chaves gerenciadas pelo cliente](concepts-data-encryption-postgresql.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretriz**: Use o Azure Monitor com o log de atividades do Azure para criar alertas para quando alterações ocorrerem nas instâncias de produção do Banco de Dados do Azure para PostgreSQL e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Orientação**: siga as recomendações da central de segurança do Azure para proteger seu banco de dados do Azure para PostgreSQL e recursos relacionados.

A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte ao banco de dados do Azure para PostgreSQL.

- [Entender as recomendações da central de segurança do Azure](../security-center/recommendations-reference.md)

- [Cobertura de recursos para os serviços de PaaS do Azure na central de segurança do Azure](../security-center/features-paas.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: implantar solução de gerenciamento de patch automatizado para títulos de software de terceiros

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretriz**: A Microsoft realiza o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte ao Banco de Dados do Azure para PostgreSQL.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (incluindo instâncias do banco de dados do Azure para PostgreSQL) em suas assinaturas. Verifique se você tem permissões (leitura) apropriadas em seu locatário e é capaz de enumerar todas as assinaturas do Azure, bem como os recursos nas suas assinaturas.

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas às instâncias do Banco de Dados do Azure para PostgreSQL e outros recursos relacionados fornecendo metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos do Azure não autorizados

**Diretriz**: Use marcação, grupos de gerenciamento e assinaturas separadas, sempre que apropriado, para organizar e rastrear instâncias do Banco de Dados do Azure para PostgreSQL e recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação e ao Azure como um todo.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o Azure Resource Graph para consultar/descobrir recursos em sua(s) assinatura(s).

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação e ao Azure como um todo.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Usar somente aplicativos aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretriz**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”. Isso pode impedir a criação e as alterações em recursos em um ambiente de alta segurança, como instâncias do Banco de Dados do Azure para PostgreSQL que contêm informações confidenciais.

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts nos recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separar física ou logicamente os aplicativos de alto risco

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Diretriz**: Defina e implemente configurações de segurança padrão para suas instâncias do Banco de Dados do Azure para PostgreSQL com o Azure Policy. Use aliases do Azure Policy no namespace “Microsoft.DBforPostgreSQL” para criar políticas personalizadas para auditar ou impor a configuração de rede de suas instâncias do Banco de Dados do Azure para PostgreSQL. Você também usar as definições de política integradas relacionadas às suas instâncias do Banco de Dados do Azure para PostgreSQL, como:

- Impor conexão TLS deve ser habilitada para servidores de banco de dados PostgreSQL

- As conexões de log devem ser habilitadas para os servidores de banco de dados PostgreSQL

- [Como exibir os aliases de Azure Policy disponíveis](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretriz**: use o Azure Policy [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Diretriz**: Se você estiver usando definições personalizadas do Azure Policy para suas instâncias do Banco de Dados do Azure para PostgreSQL e recursos relacionados, use o Azure Repos para armazenar e gerenciar seu código com segurança.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

- [Documentação do Azure Repos](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Diretriz**: Use aliases do Azure Policy no namespace “Microsoft.DBforPostgreSQL” para criar políticas personalizadas para alertar, auditar ou impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretriz**: Use aliases do Azure Policy no namespace “Microsoft.DBforPostgreSQL” para criar políticas personalizadas para alertar, auditar ou impor configurações do sistema. Use o Azure Policy [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para as instâncias do Banco de Dados do Azure para PostgreSQL e recursos relacionados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretriz**: Para máquinas virtuais do Azure ou aplicativos Web em execução no Serviço de Aplicativo do Azure que está sendo usado para acessar suas instâncias do Banco de Dados do Azure para PostgreSQL, use a Identidade de Serviço Gerenciado junto com o Azure Key Vault para simplificar e proteger o gerenciamento de segredos do Banco de Dados do Azure para PostgreSQL. Verifique se a exclusão temporária do cofre de chaves está habilitada.

- [Como integrar com identidades gerenciadas do Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um Key Vault](../key-vault/general/quick-create-portal.md)

- [Como fornecer Key Vault autenticação com uma identidade gerenciada](../key-vault/general/assign-access-policy-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerenciar identidades de maneira segura e automática

**Diretriz**: O servidor de Banco de Dados do Azure para PostgreSQL oferece suporte à autenticação do Azure Active Directory para acessar bancos de dados.  Ao criar o servidor do Banco de Dados do Azure para PostgreSQL, você fornece credenciais para um usuário administrador. Esse administrador pode ser usado para criar usuários adicionais do banco de dados.  

Para máquinas virtuais do Azure ou aplicativos Web em execução no Serviço de Aplicativo do Azure que está sendo usado para acessar o servidor do Banco de Dados do Azure para PostgreSQL, use a Identidade de Serviço Gerenciado junto com o Azure Key Vault para armazenar e recuperar as credenciais do servidor do Banco de Dados do Azure para PostgreSQL. Verifique se a exclusão temporária do cofre de chaves está habilitada.

Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (AD). As identidades gerenciadas permitem que você se autentique em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.

- [Como configurar identidades gerenciadas](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Como integrar com identidades gerenciadas do Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Diretriz**: Não aplicável; esta recomendação destina-se a recursos de computação.

O antimalware da Microsoft está habilitado no host subjacente que oferece suporte aos serviços do Azure (por exemplo, Serviço de Aplicativo do Azure), mas não é executado no conteúdo do cliente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretriz**: O antimalware da Microsoft está habilitado no host subjacente que oferece suporte aos serviços do Azure (por exemplo, Banco de Dados do Azure para PostgreSQL), mas não é executado no conteúdo do cliente.

Verifique previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de Blobs, Banco de Dados do Azure para PostgreSQL, etc. A Microsoft não consegue acessar seus dados nessas instâncias.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: garantir que o software antimalware e as assinaturas sejam atualizados

**Diretriz**: Não aplicável; esta recomendação destina-se a recursos de computação.

O antimalware da Microsoft está habilitado no host subjacente que oferece suporte aos serviços do Azure (por exemplo, Banco de Dados do Azure para PostgreSQL), mas não é executado no conteúdo do cliente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares

**Diretriz**: O Banco de Dados do Azure para PostgreSQL faz backups dos arquivos de dados e do log de transações. Dependendo do tamanho máximo de armazenamento com suporte, fazemos backups totais e diferenciais (servidores de armazenamento máximo de 4 TB) ou backups de instantâneo (servidores de armazenamento máximo de até 16 TB). Esses backups permitem que você restaure um servidor pontualmente dentro de seu período de retenção de backup configurado. O período de retenção de backup padrão é de sete dias. Você pode, opcionalmente, configurá-lo para até 35 dias. Todos os backups são criptografados usando a criptografia AES de 256 bits.

- [Como fazer backup de um servidor no banco de dados do Azure para PostgreSQL](howto-restore-server-portal.md)

- [Entender a configuração inicial do banco de dados do Azure para PostgreSQL](tutorial-design-database-using-azure-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretriz**: O Banco de Dados do Azure para PostgreSQL cria automaticamente backups do servidor e os armazena em armazenamento redundante localmente ou redundante geograficamente, de acordo com a escolha do usuário. Os backups podem ser usados para restaurar o servidor pontualmente. Os recursos de backup e restauração são uma parte essencial de qualquer estratégia de continuidade dos negócios, pois eles protegem seus dados contra exclusão ou corrupção acidentais.

Se estiver usando o Azure Key Vault para armazenar credenciais para suas instâncias do Banco de Dados do Azure para PostgreSQL, garanta backups regulares automatizados de suas chaves.

- [Como fazer backup de um servidor no banco de dados do Azure para PostgreSQL](howto-restore-server-portal.md)

- [Como fazer backup de chaves de Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretriz**: No Banco de Dados do Azure para PostgreSQL, a execução de uma restauração cria um novo servidor de backup do servidor original. Há dois tipos de restauração disponíveis: Restauração pontual e restauração geográfica. A restauração pontual está disponível em qualquer opção de redundância de backup e cria um novo servidor na mesma região do servidor original. A restauração geográfica está disponível somente se você configurou seu servidor para armazenamento com redundância geográfica; ele permite que você restaure o servidor em uma região diferente.

O tempo estimado de recuperação dependerá de vários fatores, incluindo os tamanhos dos bancos de dados, o tamanho do log de transações, a largura de banda de rede e o número total de bancos de dados de recuperação na mesma região e ao mesmo tempo. Normalmente, o tempo de recuperação é menor do que 12 horas.

Teste periodicamente a restauração das suas instâncias do Banco de Dados do Azure para PostgreSQL.

- [Como fazer backup de um servidor no banco de dados do Azure para PostgreSQL](howto-restore-server-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretriz**: O Banco de Dados do Azure para PostgreSQL usa backups completos, diferenciais e de log de transações. Esses backups permitem que você restaure um servidor pontualmente dentro de seu período de retenção de backup configurado. O período de retenção de backup padrão é de sete dias. Você pode, opcionalmente, configurá-lo para até 35 dias. Todos os backups são criptografados usando a criptografia AES de 256 bits.

- [Entender o backup e a restauração no banco de dados do Azure para PostgreSQL](concepts-backup.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte o [benchmark de segurança do Azure: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [O cliente também pode aproveitar o guia de tratamento de incidentes de segurança do computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomenclatura para identificar e categorizar claramente os recursos do Azure.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Veja a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal.  Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de "Aplicativos Lógicos" em alertas de segurança e recomendações.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretriz**: siga as regras de participação da Microsoft para garantir que os testes de penetração não violem as políticas da Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura, serviços e aplicativos de nuvem gerenciados pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)