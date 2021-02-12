---
title: Linha de base de segurança do Azure para armazenamento do Azure
description: Linha de base de segurança do Azure para armazenamento do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f6825fee4cfb78ab54d782cd4d942bb994bedae9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98195714"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Linha de base de segurança do Azure para armazenamento do Azure

A linha de base de segurança do Azure para o armazenamento do Azure contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de segurança: Segurança de rede](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua Rede Virtual

**Diretrizes**: Configure o firewall da sua conta de armazenamento restringindo o acesso a clientes de intervalos de endereços IP públicos específicos, selecione redes virtuais (VNets) no Azure ou para recursos específicos do Azure. Você também pode configurar pontos de extremidade privados para que o tráfego para o serviço de armazenamento de sua empresa vá exclusivamente por redes privadas.

Observação: as contas de armazenamento clássicas não dão suporte a firewalls e redes virtuais.

- [Como configurar o Firewall do armazenamento do Azure](./storage-network-security.md#change-the-default-network-access-rule)

- [Como configurar pontos de extremidade privados para o armazenamento do Azure](./storage-private-endpoints.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-vnet-subnet-and-nic-configuration-and-traffic"></a>1,2: monitorar e registrar a configuração e o tráfego da vnet, da sub-rede e da NIC

**Diretrizes**: o armazenamento do Azure fornece um modelo de segurança em camadas. Você pode limitar o acesso à sua conta de armazenamento a solicitações originadas de endereços IP especificados, intervalos de IP ou de uma lista de sub-redes em uma VNet (Rede Virtual) do Azure. Você pode usar a central de segurança do Azure e seguir as recomendações de proteção de rede para ajudar a proteger seus recursos de rede no Azure. Além disso, habilite os logs de fluxo do NSG para redes virtuais/sub-rede configuradas para as contas de armazenamento por meio do firewall da conta de armazenamento e envie os logs para uma conta de armazenamento para auditoria de tráfego. 

Observe que, se você tiver pontos de extremidade privados anexados à sua conta de armazenamento, não poderá configurar regras de NSG (grupo de segurança de rede) para sub-redes. 

- [Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure](./storage-network-security.md)

- [Como habilitar logs de fluxo de NSG](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Noções básicas sobre segurança de rede fornecida pela central de segurança do Azure](../../security-center/security-center-network-recommendations.md)

- [Noções básicas sobre pontos de extremidade privados para o armazenamento do Azure](./storage-private-endpoints.md#known-issues)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1,3: proteger aplicativos Web críticos

**Orientação**: não aplicável; a recomendação destina-se a aplicativos Web em execução em Azure App serviço ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: habilitar a proteção avançada contra ameaças para sua conta de armazenamento do Azure. A proteção avançada contra ameaças para o armazenamento do Azure fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Os alertas integrados da central de segurança do Azure são baseados em atividades para as quais a comunicação de rede foi associada a um endereço IP que foi resolvido com êxito, seja ou não um endereço IP arriscado conhecido (por exemplo, um cryptominer conhecido) ou um endereço IP que não seja reconhecido anteriormente como arriscado. Os alertas de segurança são acionados quando ocorrem anomalias na atividade. 

- [Como habilitar a proteção avançada contra ameaças](./azure-defender-storage-configure.md?tabs=azure-portal)

- [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](../../security-center/azure-defender.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: gravar pacotes de rede e logs de fluxo

**Orientação**: a captura de pacotes do observador de rede permite que você crie sessões de captura para controlar o tráfego entre a conta de armazenamento e uma máquina virtual. Os filtros são fornecidos para a sessão de captura garantir que somente o tráfego que você deseja capturar. A captura de pacote ajuda a diagnosticar problemas de rede de maneiras reativas e proativas. Outros usos incluem a coleta de estatísticas de rede, obter informações sobre as invasões de rede, depurar comunicações entre cliente e servidor e muito mais. Poder disparar remotamente a captura de pacote alivia a carga de executar uma captura de pacote manualmente em uma máquina virtual desejada, o que economiza tempo. 

- [Gerenciar as capturas de pacotes com o Observador de Rede do Azure usando o portal](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1,6: implantar sistemas de detecção de invasão/prevenção de intrusão baseados em rede

**Diretrizes**: a proteção avançada contra ameaças para o armazenamento do Azure fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Os alertas de segurança são acionados quando ocorrem anomalias na atividade. Esses alertas de segurança são integrados à Central de Segurança do Azure e também são enviados por email para administradores de assinatura, com detalhes de atividades suspeitas e recomendações sobre como investigar e corrigir ameaças. 

- [Configurar a proteção avançada contra ameaças para o armazenamento do Azure](./azure-defender-storage-configure.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-your-web-applications"></a>1,7: gerenciar o tráfego para seus aplicativos Web

**Orientação**: não aplicável; a recomendação destina-se a aplicativos Web em execução em Azure App serviço ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Orientação**: para recursos em redes virtuais que precisam de acesso à sua conta de armazenamento, use as marcas de serviço de rede virtual para a rede virtual configurada para definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, armazenamento) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços. 

Quando o acesso à rede precisa ser definido como escopo para contas de armazenamento específicas, use políticas de ponto de extremidade de serviço de rede virtual.

- [Para obter mais informações sobre como usar marcas de serviço](../../virtual-network/service-tags-overview.md)

- [Para obter mais informações sobre políticas de ponto de extremidade de serviço de rede virtual para o armazenamento do Azure](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: serviço

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede associados à sua conta de armazenamento do Azure com Azure Policy. Use aliases de Azure Policy nos namespaces "Microsoft. Storage" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos recursos da sua conta de armazenamento. 

Você também pode fazer uso de definições de política internas relacionadas à conta de armazenamento, como: as contas de armazenamento devem usar um ponto de extremidade de serviço de rede virtual 

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Exemplos de Azure Policy para armazenamento](../../governance/policy/samples/built-in-policies.md#storage)

- [Exemplos de Azure Policy para rede](../../governance/policy/samples/built-in-policies.md#network)

- [Como criar um blueprint do Azure](../../governance/blueprints/create-blueprint-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1,10: regras de configuração de tráfego do documento

**Orientação**: use marcas para NSG (grupos de segurança de rede) e outros recursos relacionados à segurança de rede e ao fluxo de tráfego. Para regras de NSG individuais, use o campo "Descrição" para especificar a necessidade empresarial e/ou a duração (etc.) para regras que permitam tráfego de/para uma rede. Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes. Você pode usar o Azure PowerShell ou a CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas. 

- [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../../virtual-network/quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](../../virtual-network/tutorial-filter-network-traffic.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretrizes**: Use Azure Policy para registrar em log as alterações de configuração para recursos de rede. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos. 

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Como criar alertas no Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de segurança: registro em log e monitoramento](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-resource"></a>2,1: usar o recurso de sincronização de tempo aprovado

**Orientação**: não aplicável; A Microsoft mantém fontes de tempo para contas de armazenamento do Azure.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurar o gerenciamento do log de segurança central

**Orientação**: ingerir logs por meio de Azure monitor para agregar dados de segurança gerados por dispositivos de pontos de extremidade, recursos de rede e outros sistemas de segurança. Em Azure Monitor, use Log Analytics espaços de trabalho para consultar e executar análises e use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento, opcionalmente com recursos de segurança como armazenamento imutável e retenção imposta.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: habilitar o log de auditoria para recursos do Azure

**Diretrizes**: análise de armazenamento do Azure fornece logs para BLOBs, filas e tabelas. Você pode usar o portal do Azure para configurar quais logs são registrados para sua conta. 

- [Como configurar o monitoramento para sua conta de armazenamento do Azure](./storage-monitor-storage-account.md#configure-monitoring-for-a-storage-account)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: coletar logs de segurança de sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurar retenção de armazenamento de log de segurança

**Orientação**: ao armazenar logs de eventos de segurança na conta de armazenamento do Azure ou log Analytics espaço de trabalho, você pode definir a política de retenção de acordo com os requisitos da sua organização. 

- [Como configurar a política de retenção para logs de conta de armazenamento do Azure](./storage-monitor-storage-account.md#configure-logging)

- [Alterar o período de retenção de dados em Log Analytics](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Orientação**: para examinar os logs de armazenamento do Azure, há as opções usuais, como consultas por meio da oferta de log Analytics, bem como uma opção exclusiva de exibir os arquivos de log diretamente. No armazenamento do Azure, os logs são armazenados em BLOBs que devem ser acessados diretamente em `http://accountname.blob.core.windows.net/$logs` (a pasta de log fica oculta por padrão, portanto, você precisará navegar diretamente. Ele não será exibido em comandos de lista) 

Além disso, habilite a proteção avançada contra ameaças para sua conta de armazenamento do Azure. A proteção avançada contra ameaças para o armazenamento do Azure fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Os alertas de segurança são acionados quando ocorrem anomalias na atividade. Esses alertas de segurança são integrados à Central de Segurança do Azure e também são enviados por email para administradores de assinatura, com detalhes de atividades suspeitas e recomendações sobre como investigar e corrigir ameaças. 

- [Registrar e examinar dados](./storage-analytics-logging.md#how-logs-are-stored)

- [Como habilitar a proteção avançada contra ameaças](./azure-defender-storage-configure.md?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: habilitar alertas para atividade anômala

**Diretrizes**: na central de segurança do Azure, habilite a proteção avançada contra ameaças para a conta de armazenamento. Habilite as configurações de diagnóstico para a conta de armazenamento e envie logs para um espaço de trabalho Log Analytics. Integre seu Workspace do Log Analytics ao Azure Sentinel, pois ele fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Assim os guias estratégicos (soluções automatizadas) podem ser criados e usados para corrigir problemas de segurança. 

- [Como integrar o Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Como gerenciar alertas na central de segurança do Azure](../../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar sobre dados de log do log Analytics](../../azure-monitor/learn/tutorial-response.md)

- [Log da análise do Armazenamento do Azure](./storage-analytics-logging.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizar o registro em log de anti-malware

**Orientação**: Use a central de segurança do Azure e habilite a proteção contra ameaças para o armazenamento do Azure para detectar carregamentos de malware no armazenamento do Azure usando a análise de reputação de hash e o acesso suspeito de um nó de saída de Tor ativo (um proxy de anonimato). 

- [Configurar a proteção avançada contra ameaças para o armazenamento do Azure](./azure-defender-storage-configure.md?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Habilitar o registro em log de consulta DNS

**Diretrizes**: a solução de análise de DNS do Azure (versão prévia) no Azure monitor coleta informações sobre a infraestrutura de DNS sobre segurança, desempenho e operações. Atualmente, isso não dá suporte a contas de armazenamento do Azure, mas você pode usar a solução de registro em log de DNS de terceiros. 

- [Reúna informações sobre sua infraestrutura de DNS com a solução de visualização Análise de DNS](../../azure-monitor/insights/dns-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2,10: habilitar o log de auditoria de linha de comando

**Orientação**: não aplicável; o parâmetro de comparação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [Controle de segurança: Identidade e controle de acesso](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3,1: manter o inventário de contas administrativas

**Diretrizes**: o Azure ad tem funções internas que devem ser explicitamente atribuídas e que são consultáveis. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos. 

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: alterar as senhas padrão quando aplicável

**Diretrizes**: as contas de armazenamento do Azure e Azure Active Directory têm o conceito de senhas padrão ou em branco. O armazenamento do Azure implementa um modelo de controle de acesso que dá suporte ao controle de acesso baseado em função do Azure (RBAC do Azure), bem como a chaves compartilhadas e SAS (assinaturas de acesso compartilhado). Uma característica de autenticação de chave compartilhada e SAS é que nenhuma identidade está associada ao chamador e, portanto, a autorização baseada em permissão de entidade de segurança não pode ser executada. 

- [Autorizando o acesso aos dados no armazenamento do Azure](./storage-auth.md)

- [Noções básicas sobre entidades de segurança e controle de acesso para a conta de armazenamento do Azure](./storage-introduction.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: usar contas administrativas dedicadas

**Diretrizes**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas que têm acesso à sua conta de armazenamento. Use a identidade da Central de Segurança do Azure e o gerenciamento de acesso para monitorar a quantidade de contas administrativas. 

Você também pode habilitar um acesso just-in-time/apenas o suficiente usando Azure AD Privileged Identity Management funções privilegiadas para serviços da Microsoft e o Azure ARM. 

- [Entender a identidade e o acesso da central de segurança do Azure](../../security-center/security-center-identity-access.md)

- [Visão geral de Privileged Identity Management](../../active-directory/privileged-identity-management/index.yml)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar Azure Active Directory único Sign-On (SSO)

**Diretrizes**: sempre que possível, use Azure Active Directory SSO em vez de configurar credenciais autônomas individuais por serviço. Use as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure. 

- [Entendendo o SSO com o Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

- [Autorizando o acesso aos dados no armazenamento do Azure](./storage-auth.md)

- [Autorizar o acesso a BLOBs e filas usando Azure Active Directory](./storage-auth-aad.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Use a autenticação multifator para todo o acesso baseado em Azure Active Directory.

**Diretrizes**: habilite Azure Active Directory autenticação multifator e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure para ajudar a proteger os recursos da sua conta de armazenamento. 

- [Como habilitar a MFA no Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usar máquinas dedicadas (estações de trabalho de acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use PAWs (estações de trabalho com acesso privilegiado) com a MFA configurada para fazer logon e configurar recursos da conta de armazenamento. 

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a MFA no Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: envie alertas de detecção de riscos da central de segurança do Azure para Azure monitor e configure alertas/notificações personalizados usando grupos de ação. Habilite a proteção avançada contra ameaças para a conta de armazenamento do Azure para gerar alertas para atividades suspeitas. Além disso, use as detecções de risco do Azure AD para exibir alertas e relatórios sobre comportamento de usuário arriscado. 

- [Como configurar a proteção avançada contra ameaças para a conta de armazenamento do Azure](./azure-defender-storage-configure.md)

- [Entenda as detecções de risco do Azure Active Directory](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar grupos de ação para alertas e notificações personalizados](../../azure-monitor/platform/action-groups.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3,8: gerenciar o recurso do Azure somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões. 

- [Como configurar locais nomeados no Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central. O Azure fornece o Azure RBAC (controle de acesso baseado em função) para um controle refinado sobre o acesso de um cliente aos recursos em uma conta de armazenamento.  Use as credenciais do Azure AD quando possível como uma prática recomendada de segurança, em vez de usar a chave de conta, que pode ser mais facilmente comprometida. Quando o design do aplicativo exigir assinaturas de acesso compartilhado para acesso ao armazenamento de BLOB, use as credenciais do Azure AD para criar uma SAS (assinatura de acesso compartilhado) de delegação de usuário, quando possível, para segurança superior.

- [Como criar e configurar uma instância do Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Usar o provedor de recursos de armazenamento do Azure para acessar recursos de gerenciamento](./authorization-resource-provider.md)

- [Como configurar o acesso ao blob do Azure e a dados da fila com o RBAC do Azure no portal do Azure](./storage-auth-aad-rbac-portal.md)

- [Autorizando o acesso aos dados no armazenamento do Azure](./storage-auth.md)

- [Conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](./storage-sas-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: examinar e reconciliar regularmente o acesso do usuário

**Diretrizes**: examine os logs de Azure Active Directory para ajudar a descobrir contas obsoletas que podem incluir aquelas com funções administrativas da conta de armazenamento. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso a aplicativos empresariais que podem ser usados para acessar recursos da conta de armazenamento e atribuições de função. O acesso do usuário deve ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo.  

Você também pode usar a SAS (assinatura de acesso compartilhado) para fornecer acesso delegado seguro aos recursos em sua conta de armazenamento sem comprometer a segurança de seus dados. Você pode controlar quais recursos o cliente pode acessar, quais permissões eles têm sobre esses recursos e por quanto tempo a SAS é válida, entre outros parâmetros.

Além disso, examine o acesso de leitura anônimo para contêineres e blobs. Por padrão, um contêiner e todos os blobs dentro dele poderão ser acessados somente por um usuário a quem as permissões apropriadas tenham sido concedidas. Você pode usar Azure Monitor para alertar sobre o acesso anônimo para contas de armazenamento usando a condição de autenticação anônima.

Uma maneira eficaz de reduzir o risco de acesso à conta de usuário não suspeita é limitar a duração do acesso concedido aos usuários. URIs SAS de tempo limitado são uma maneira eficaz de expirar automaticamente o acesso do usuário a uma conta de armazenamento. Além disso, a rotação das chaves de conta de armazenamento com frequência é uma maneira de garantir que o acesso inesperado por meio de chaves de conta de armazenamento seja de duração limitada.

- [Entender os relatórios do Azure AD](../../active-directory/reports-monitoring/index.yml)

- [Como exibir e alterar o acesso no nível da conta de armazenamento do Azure](./storage-auth-aad-rbac-portal.md)

- [Conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](./storage-sas-overview.md)

- [Gerenciar o acesso de leitura anônimo aos contêineres e blobs](../blobs/anonymous-read-access-configure.md)

- [Monitorar uma conta de armazenamento no portal do Azure](./storage-monitor-storage-account.md)

- [Gerenciar chaves de acesso da conta de armazenamento](./storage-account-keys-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorar tentativas de acessar contas desativadas

**Diretrizes**: Use análise de armazenamento para registrar informações detalhadas sobre solicitações bem-sucedidas e com falha em um serviço de armazenamento. Todos os logs são armazenados em blobs de bloco em um contêiner denominado $logs, que é criado automaticamente quando a análise de armazenamento é habilitada para uma conta de armazenamento.

Crie configurações de diagnóstico para Azure Active Directory contas de usuário, enviando os logs de auditoria e os logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas desejados no workspace do Log Analytics. Para monitorar falhas de autenticação em contas de armazenamento do Azure, você pode criar alertas para notificá-lo quando determinados limites tiverem sido atingidos para métricas de recursos de armazenamento. Além disso, use Azure Monitor para alertar sobre o acesso anônimo para contas de armazenamento usando a condição de autenticação anônima.

- [Log da análise do Armazenamento do Azure](./storage-analytics-logging.md)

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como configurar alertas de métricas para contas de armazenamento do Azure](./storage-monitor-storage-account.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de logon da conta

**Orientação**: Use os recursos de proteção de risco e identidade do Azure Active Directory para configurar respostas automatizadas para ações suspeitas detectadas relacionadas aos recursos da sua conta de armazenamento. Você deve habilitar respostas automatizadas por meio do Azure Sentinel para implementar as respostas de segurança da sua organização. 

- [Como exibir entradas suspeitas do Azure Active Directory](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: em cenários de suporte em que a Microsoft precisa acessar dados do cliente, sistema de proteção de dados do cliente (visualização para a conta de armazenamento) fornece uma interface para os clientes revisarem e aprovarem ou rejeitarem solicitações de acesso a dados do cliente. A Microsoft não exigirá, nem solicitará acesso aos segredos da sua organização armazenados na conta de armazenamento.

- [Entender Sistema de Proteção de Dados do Cliente](../../security/fundamentals/customer-lockbox-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [Controle de segurança: proteção de dados](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: manter um inventário de informações confidenciais

**Diretrizes**: use marcas para auxiliar no rastreamento de recursos de conta de armazenamento que armazenam ou processam informações confidenciais. 

- [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente o isolamento usando assinaturas separadas, grupos de gerenciamento e contas de armazenamento para domínios de segurança individuais, como a confidencialidade de dados, como ambiente.  Você pode restringir sua conta de armazenamento para controlar o nível de acesso às suas contas de armazenamento que seus aplicativos e ambientes empresariais exigem, com base no tipo e no subconjunto de redes usadas. Quando as regras de rede são configuradas, somente dados de solicitação de aplicativos do conjunto especificado de redes podem acessar uma conta de armazenamento. Você pode controlar o acesso ao armazenamento do Azure por meio do RBAC do Azure. Você também pode configurar pontos de extremidade privados para melhorar a segurança conforme o tráfego entre sua rede virtual e o serviço atravessa a rede de backbone da Microsoft, eliminando a exposição da Internet pública. 

- [Como criar assinaturas adicionais do Azure](../../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

- [Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure](./storage-network-security.md)

- [Pontos de extremidade de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitore e bloqueie a transferência não autorizada de informações confidenciais.

**Diretrizes**: para recursos de conta de armazenamento que armazenam ou processam informações confidenciais, marque os recursos como confidenciais usando marcas. Para reduzir o risco de perda de dados por meio de vazamento, restrinja o tráfego de rede de saída para contas de armazenamento do Azure usando o Firewall do Azure. 

Além disso, use políticas de ponto de extremidade de serviço de rede virtual para filtrar o tráfego de rede virtual de egresso para contas de armazenamento do Azure sobre o ponto de extremidade de serviço e permitir que os dados vazamentom apenas contas específicas do armazenamento

- [Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Políticas de ponto de extremidade de serviço de rede virtual para o Armazenamento do Azure](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Entender a proteção de dados do cliente no Azure](../../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Orientação**: você pode impor o uso de HTTPS habilitando a transferência segura necessária para a conta de armazenamento. As conexões que usam HTTP serão recusadas depois que essa opção for habilitada. Além disso, use a central de segurança do Azure e Azure Policy para impor a transferência segura para sua conta de armazenamento.

- [Como exigir transferência segura no armazenamento do Azure](./storage-require-secure-transfer.md)

- [Políticas de segurança do Azure monitoradas pela central de segurança](../../security-center/policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: os recursos de identificação de dados ainda não estão disponíveis para a conta de armazenamento do Azure e recursos relacionados. Se necessário, implemente uma solução de terceiros para fins de conformidade. 

- [Entender a proteção de dados do cliente no Azure](../../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usar o RBAC do Azure para controlar o acesso a recursos

**Diretrizes**: o Azure Active Directory (Azure AD) autoriza os direitos de acesso a recursos protegidos por meio do controle de acesso baseado em função do Azure (RBAC do Azure). O armazenamento do Azure define um conjunto de funções internas do Azure que abrangem conjuntos comuns de permissões usadas para acessar dados de BLOB ou de fila. 

- [Como atribuir funções do Azure para a conta de armazenamento do Azure](./storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal)

- [Usar o provedor de recursos de armazenamento do Azure para acessar recursos de gerenciamento](./authorization-resource-provider.md)

- [Como configurar o acesso ao blob do Azure e a dados da fila com o RBAC do Azure no portal do Azure](./storage-auth-aad-rbac-portal.md)

- [Como criar e configurar uma instância do AAD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autorizando o acesso aos dados no armazenamento do Azure](./storage-auth.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: criptografar informações confidenciais em repouso

**Diretrizes**: a criptografia de armazenamento do Azure está habilitada para todas as contas de armazenamento e não pode ser desabilitada. O armazenamento do Azure criptografa automaticamente os dados quando eles são persistidos na nuvem. Quando você faz a leitura de dados do Armazenamento do Azure, eles são descriptografados pelo armazenamento do Azure antes de serem retornados. A criptografia de armazenamento do Azure permite que você proteja seus dados em repouso sem precisar modificar código nem adicionar código a nenhum aplicativo. 

- [Noções básicas sobre a criptografia de armazenamento do Azure em repouso](./storage-service-encryption.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para os recursos da conta de armazenamento. Você também pode habilitar o log de armazenamento do Azure para controlar como cada solicitação feita no armazenamento do Azure foi autorizada. Os logs indicam se uma solicitação foi feita anonimamente, usando um token OAuth 2,0, usando a chave compartilhada ou usando uma SAS (assinatura de acesso compartilhado). Além disso, use Azure Monitor para alertar sobre o acesso anônimo para contas de armazenamento usando a condição de autenticação anônima.

- [Como criar alertas para eventos do log de atividades do Azure](../../azure-monitor/platform/alerts-activity-log.md)

- [Log da análise do Armazenamento do Azure](./storage-analytics-logging.md)

- [Como configurar alertas de métricas para contas de armazenamento do Azure](./storage-monitor-storage-account.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: gerenciamento de vulnerabilidades](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: executar ferramentas de verificação automatizada de vulnerabilidade

**Orientação**: siga as recomendações da central de segurança do Azure para auditar e monitorar continuamente a configuração de suas contas de armazenamento. 

- [Recomendações de segurança – um guia de referência](../../security-center/recommendations-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: implantar solução de gerenciamento de patches do sistema operacional automatizado

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: implantar solução de gerenciamento de patches de software de terceiros automatizada

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: comparar verificações de vulnerabilidade de back-to-back

**Orientação**: não aplicável; A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte a contas de armazenamento.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usar processo de avaliação de risco para priorizar a correção de vulnerabilidades descobertas.

**Diretrizes**: Use as classificações de risco padrão (Pontuação segura) fornecidas pela central de segurança do Azure. 

- [Entender a pontuação segura da central de segurança do Azure](../../security-center/secure-score-security-controls.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de segurança: inventário e gerenciamento de ativos](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: usar a descoberta de ativos do Azure

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (incluindo contas de armazenamento) em suas assinaturas. Verifique se você tem permissões (leitura) apropriadas em seu locatário e é capaz de enumerar todas as assinaturas do Azure, bem como os recursos nas suas assinaturas. 

- [Como criar consultas com o Azure Graph](../../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription)

- [Entender o RBAC do Azure](../../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6,2: manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos da conta de armazenamento, fornecendo metadados para organizá-los logicamente em uma taxonomia. 

- [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar contas de armazenamento e recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil. 

Além disso, use a proteção avançada contra ameaças para o armazenamento do Azure para detectar recursos não autorizados do Azure. 

- [Como criar assinaturas adicionais do Azure](../../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

- [Configurar a proteção avançada contra ameaças para o armazenamento do Azure](./azure-defender-storage-configure.md?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter inventário de recursos e títulos de software aprovados do Azure.

**Orientação**: você precisará criar um inventário de recursos aprovados do Azure de acordo com suas necessidades organizacionais. 


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorar os recursos do Azure não aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna: 

 - Tipos de recursos não permitidos 
 - Tipos de recursos permitidos 

Além disso, use o Azure Resource Graph para consultar/descobrir recursos em sua(s) assinatura(s). Isso pode ajudar em ambientes baseados em alta segurança, como aqueles com contas de armazenamento. 

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorar aplicativos de software não aprovados nos recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: remover os recursos do Azure e os aplicativos de software não aprovados

**Orientação**: o cliente pode impedir a criação ou o uso de recursos com Azure Policy conforme exigido pelas políticas da empresa do cliente. 

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Usar somente aplicativos aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6,9: usar somente os serviços do Azure aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna: 

- Tipos de recursos não permitidos 
- Tipos de recursos permitidos 

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../../governance/policy/samples/index.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: implementar lista de aplicativos aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager por meio de scripts

**Diretriz**: use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”. Isso pode impedir a criação e alterações em recursos em um ambiente de alta segurança, como aqueles com contas de armazenamento. 

- [Como configurar o acesso condicional para bloquear o acesso ao ARM](../../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: limitar a capacidade dos usuários de executar scripts em recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separar fisicamente ou logicamente os aplicativos de alto risco

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [Controle de segurança: configuração segura](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. Storage" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias de conta de armazenamento. Você também pode usar definições de Azure Policy internas para a conta de armazenamento do Azure, como: 

Auditar o acesso irrestrito à rede para contas de armazenamento  
Implantar a Proteção Avançada contra Ameaças em Contas de Armazenamento  
As contas de armazenamento devem ser migradas para os novos recursos do Azure Resource Manager  
A transferência segura para contas de armazenamento deve ser habilitada  

Use as recomendações da central de segurança do Azure como uma linha de base de configuração segura para suas contas de armazenamento. 

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7,2: estabelecer configurações seguras para seu sistema operacional

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7,3: manter configurações seguras para todos os recursos do Azure

**Orientação**: Use Azure Policy [Deny] e [implantar se não existir] para impor configurações seguras em seus recursos de conta de armazenamento. 

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Compreendendo os efeitos do Azure Policy](../../governance/policy/concepts/effects.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7,4: manter configurações seguras para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: armazenar a configuração de recursos do Azure com segurança

**Diretrizes**: Use Azure Repos para armazenar e gerenciar com segurança seu código, como políticas personalizadas do Azure, modelos de Azure Resource Manager, scripts de configuração de estado desejado etc. Para acessar os recursos que você gerencia no Azure DevOps, você pode conceder ou negar permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory (AD do Azure), se integrados ao Azure DevOps, ou Active Directory se integrado ao TFS.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: armazenar com segurança imagens personalizadas do sistema operacional

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: implantar ferramentas de gerenciamento de configuração do sistema

**Diretrizes**: Aproveite Azure Policy para alertar, auditar e impor configurações do sistema para a conta de armazenamento. Desenvolva também um processo e um pipeline para gerenciar exceções de política. 

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementar o monitoramento automatizado de configuração para os serviços do Azure

**Orientação**: Aproveite a central de segurança do Azure para executar verificações de linha de base para os recursos da sua conta de armazenamento do Azure. 

- [Como corrigir recomendações na central de segurança do Azure](../../security-center/security-center-remediate-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementar o monitoramento automatizado de configuração para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-securely-manage-azure-secrets"></a>7,11: gerenciar com segurança os segredos do Azure

**Orientação**: o armazenamento do Azure criptografa automaticamente os dados quando eles são persistidos para a nuvem. Você pode usar chaves gerenciadas pela Microsoft para a criptografia da conta de armazenamento ou pode gerenciar a criptografia com suas próprias chaves. Se você estiver usando chaves fornecidas pelo cliente, poderá aproveitar Azure Key Vault para armazenar as chaves com segurança. 

Além disso, gire as chaves da conta de armazenamento com frequência para limitar o impacto da perda ou divulgação de chaves de conta de armazenamento.

- [Criptografia do Armazenamento do Azure para dados em repouso](./storage-service-encryption.md)

- [Gerenciar chaves de acesso da conta de armazenamento](./storage-account-keys-manage.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-securely-and-automatically-manage-identities"></a>7,12: gerenciar identidades de forma segura e automática

**Orientação**: autorizar o acesso a BLOBs e filas em contas de armazenamento do Azure com Azure Active Directory e identidades gerenciadas. Os armazenamentos de blobs e de filas do Azure dão suporte à autenticação do Azure AD (Active Directory) com identidades gerenciadas para recursos do Azure. Identidades gerenciadas para recursos do Azure podem autorizar o acesso a dados de BLOB e de fila usando as credenciais do Azure AD de aplicativos em execução em VMs (máquinas virtuais) do Azure, aplicativos de funções, conjuntos de dimensionamento de máquinas virtuais e outros serviços. Usando identidades gerenciadas para recursos do Azure junto com a autenticação do Azure AD, você pode evitar o armazenamento de credenciais com seus aplicativos que são executados na nuvem. 

- [Como conceder acesso a dados de BLOB e de fila do Azure usando uma identidade gerenciada](./storage-auth-aad-rbac-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte [Controle de segurança: defesa contra malwares](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação. A Microsoft lida com o anti-malware para a plataforma subjacente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: Use a proteção contra ameaças para o armazenamento do Azure para detectar carregamentos de malware no armazenamento do Azure usando a análise de reputação de hash e o acesso suspeito de um nó de saída de Tor ativo (um proxy de anonimato). 

Você também pode examinar previamente qualquer conteúdo para malware antes de carregar para recursos não computados do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de BLOBs, etc. para atender aos requisitos de suas organizações.

- [Configurar a proteção avançada contra ameaças para o armazenamento do Azure](./azure-defender-storage-configure.md?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Verifique se o software anti-malware e as assinaturas estão atualizados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação. A Microsoft lida com o anti-malware para a plataforma subjacente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de segurança: recuperação de dados](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir backups automatizados regulares

**Orientação**: os dados em sua conta de armazenamento de Microsoft Azure são sempre replicados automaticamente para garantir durabilidade e alta disponibilidade. A redundância do Armazenamento do Azure copia os dados para que eles sejam protegidos contra eventos planejados e não planejados, incluindo falhas de hardware transitórias, interrupções de energia ou rede e desastres naturais de grandes proporções. Você pode escolher replicar os dados no mesmo datacenter, em datacenters zonais na mesma região ou entre regiões separadas geograficamente. 

Você também pode habilitar a automação do Azure para fazer instantâneos regulares dos BLOBs.

- [Noções básicas sobre contratos de Service-Level e redundância de armazenamento do Azure](./storage-redundancy.md)

- [Criar um instantâneo de um blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Visão geral da Automação do Azure](../../automation/automation-intro.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Orientação**: para fazer backup de dados dos serviços com suporte da conta de armazenamento, há vários métodos disponíveis, incluindo o uso de ferramentas azcopy ou de terceiros. O armazenem imutável para armazenamento de blobs do Azure possibilita que os usuários armazenem objetos de dados críticos baseados em negócios em um estado WORM (Gravar uma vez, reutilizar frequentemente). Esse estado torna os dados não apagáveis e não modificáveis para um intervalo especificado pelo usuário.

- [Introdução ao AzCopy](./storage-use-azcopy-v10.md)

- [Definir e gerenciar políticas de imutabilidade para o armazenamento de blobs](../blobs/storage-blob-immutability-policies-manage.md?tabs=azure-portal)

As chaves gerenciadas pelo cliente/fornecidas podem ser apoiadas em Azure Key Vault usando o CLI do Azure ou o PowerShell. 

- [Como fazer backup de chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: execute periodicamente a restauração de dados de seus Key Vault certificados, chaves, contas de armazenamento gerenciadas e segredos, com os seguintes comandos do PowerShell: 

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret 

- [Como restaurar Key Vault certificados](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

- [Como restaurar chaves de Key Vault](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

- [Como restaurar Key Vault contas de armazenamento gerenciadas](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Como restaurar segredos de Key Vault](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret)

- [AzCopy é um utilitário de linha de comando que você pode usar para copiar BLOBs, arquivos e dados de tabela de ou para uma conta de armazenamento](./storage-use-azcopy-v10.md)

Observação: se você quiser copiar dados de e para o serviço de armazenamento de tabelas do Azure, instale o AzCopy versão 7,3.


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: para habilitar chaves gerenciadas pelo cliente em uma conta de armazenamento, você deve usar um Azure Key Vault para armazenar suas chaves. Você deve habilitar a exclusão reversível e não limpar as propriedades no cofre de chaves. O recurso de exclusão reversível do Key Vault permite a recuperação de cofres excluídos e objetos de cofre, como chaves, segredos e certificados. Se estiver fazendo backup dos dados da conta de armazenamento para os blobs de armazenamento do Azure, habilite a exclusão reversível para salvar e recuperar seus dados quando BLOBs ou instantâneos de blob forem excluídos. Você deve tratar seus backups como dados confidenciais e aplicar os controles de proteção de dados e acesso relevantes como parte dessa linha de base. Além disso, para uma proteção aprimorada, você pode armazenar objetos de dados críticos para os negócios em um estado de WORM (gravar uma vez, ler muitos).

- [Como usar a exclusão reversível do Azure Key Vault](../../key-vault/general/key-vault-recovery.md)

- [Exclusão reversível para blobs do Armazenamento do Azure ](../blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Armazenar dados de blob comercialmente críticos com armazenamento imutável](../blobs/storage-blob-immutable-storage.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de segurança: resposta a incidentes](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-incident-response-guide"></a>10,1: criar guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [O cliente também pode aproveitar o guia de tratamento de incidentes de segurança do computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10,2: criar o procedimento de classificação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta. 

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) usando marcas e crie um sistema de nomeação para identificar claramente e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais. É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](../../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10,3: testar procedimentos de resposta de segurança

**Diretriz**: Conduza regularmente exercícios para testar os recursos de resposta a incidentes de seus sistemas para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Publicação do NIST - Guia para testar, treinar e exercitar programas para planos de TI e capacidades](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: fornecer detalhes de contato de incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: Exporte seus alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para os recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

- [Como configurar a exportação contínua](../../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações para proteger os recursos do Azure.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [Controle de segurança: testes de penetração e exercícios de Red Team](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources"></a>11,1: realize testes de penetração regulares de seus recursos do Azure

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não sejam violações das políticas da Microsoft. Use a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem, serviços e aplicativos gerenciados pela Microsoft.

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Consulte o [Parâmetro de segurança do Azure](../../security/benchmarks/overview.md)
- Saiba mais sobre as [Linhas de base de segurança do Azure](../../security/benchmarks/security-baselines-overview.md)