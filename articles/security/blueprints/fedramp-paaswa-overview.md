---
title: Blueprint de segurança e conformidade do Azure - aplicativo Web de PaaS para FedRAMP
description: Blueprint de segurança e conformidade do Azure - aplicativo Web de PaaS para FedRAMP
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: 46c72191ee17f63311b041d798cccec279e4b000
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585943"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Blueprint de Segurança e Conformidade do Azure: Aplicativo Web de PaaS para FedRAMP

## <a name="overview"></a>Visão geral

O [FedRAMP (Federal Risk and Authorization Management Program)](https://www.fedramp.gov/) é um programa do governo dos Estados Unidos que fornece uma abordagem padronizada para a avaliação, a autorização e o monitoramento contínuo de segurança para produtos e serviços de nuvem. Este blueprint de conformidade e segurança do Azure fornece diretrizes sobre como fornecer uma arquitetura de PaaS (plataforma como serviço) do Microsoft Azure que ajuda a implementar um subconjunto de controles FedRAMP High. Essa solução fornece diretrizes sobre a implantação e a configuração de recursos do Azure para uma arquitetura de referência comum, demonstrando maneiras pelas quais os clientes podem atender a requisitos específicos de segurança e conformidade, e serve como uma base para os clientes compilarem e configurarem suas próprias soluções no Azure.

Essa arquitetura de referência, os guias de implementação de controle associados e os modelos de risco são destinados a servir como uma base que os clientes devem ajustar a seus requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção. Implantar um aplicativo nesse ambiente sem modificações é insuficiente para atender completamente aos requisitos da linha de base FedRAMP High. Observe o seguinte:
- A arquitetura fornece uma linha de base para ajudar os clientes a implantarem cargas de trabalho no Azure de forma compatível com o FedRAMP.
- Os clientes são responsáveis por realizar as devidas avaliações de segurança e conformidade de qualquer solução criada usando essa arquitetura, uma vez que os requisitos podem variar com base nas particularidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
Essa solução fornece uma arquitetura de referência para um aplicativo Web PaaS com um back-end do Banco de Dados SQL do Azure. O aplicativo Web é hospedado em um Ambiente do Serviço de Aplicativo do Azure, que um ambiente privado dedicado em um datacenter do Azure. O ambiente realiza o balanceamento de carga do tráfego para o aplicativo Web entre as VMs gerenciadas pelo Azure. Essa arquitetura também inclui os grupos de segurança de rede, um Gateway de Aplicativo, o DNS do Azure e o balanceador de carga. Além disso, o Azure Monitor fornece análises em tempo real da integridade do sistema. **O Azure recomenda configurar uma conexão VPN ou do ExpressRoute para gerenciamento e importação de dados para a sub-rede na arquitetura de referência.**

![Diagrama de arquitetura de referência do aplicativo Web de PaaS para FedRAMP](images/fedramp-paaswa-architecture.png?raw=true "Diagrama de arquitetura de referência do aplicativo Web de PaaS para FedRAMP")

A solução usa os serviços do Azure a seguir. Os detalhes da arquitetura de implantação estão localizados na seção [Arquitetura de implantação](#deployment-architecture).

- Azure Active Directory
- Cofre da Chave do Azure
- Banco de Dados SQL do Azure
- Gateway de Aplicativo
    - (1) Firewall do Aplicativo Web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP 3.0
        - Ouvinte: porta 443
- rede virtual do Azure
- Grupos de segurança de rede
- DNS do Azure
- Armazenamento do Azure
- Azure Monitor
- Ambiente do Serviço de Aplicativo v2
- Azure Load Balancer
- Aplicativo Web do Azure
- Azure Resource Manager

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir fornece detalhes sobre os elementos de implantação e implementação.

**Azure Resource Manager**: O [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) permite que os clientes trabalhem com os recursos da solução como um grupo. Os clientes podem implantar, atualizar ou excluir todos os recursos da solução em uma única operação coordenada. Os clientes usam um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. O Resource Manager fornece recursos de segurança, auditoria e marcação para ajudar os clientes a gerenciar seus recursos após a implantação.

**Ambiente do Serviço de Aplicativo v2**: O [Ambiente do Serviço de Aplicativo do Azure (ASE)](https://docs.microsoft.com/azure/app-service/environment/intro) é um recurso do Serviço de Aplicativo que fornece um ambiente totalmente isolado e dedicado para a execução segura de aplicativos de Serviço de Aplicativo em grande escala.

Os ASEs são isolados para executar somente aplicativos de um único cliente e sempre são implantados em uma rede virtual. Os clientes têm um controle refinado sobre o tráfego de entrada e saída da rede de aplicativos, e os aplicativos podem estabelecer conexões seguras de alta velocidade por meio de redes virtuais com recursos corporativos locais.

O uso de ASEs para essa arquitetura é permitido para os seguintes controles/configurações:

- Host dentro de uma Rede Virtual do Azure protegida e regras de segurança de rede
- ASE configurado com o certificado ILB autoassinado para comunicação HTTPS
- [Modo Balanceamento de Carga Interno](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Desabilitar o [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Alterar [Codificação TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Controlar [portas N/W de tráfego de entrada](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Firewall do aplicativo Web – Restringir Dados](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Permitir o [Tráfego de Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

As [Diretrizes e recomendações](#guidance-and-recommendations) contêm informações adicionais sobre ASEs.

**Aplicativo Web do Azure**: O [Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/) permite que os clientes criem e hospedem aplicativos Web na linguagem de programação de sua escolha sem gerenciar a infraestrutura. Eles oferecem o dimensionamento automático e alta disponibilidade, compatível com Windows e Linux e permite implantações automatizadas do GitHub, Azure DevOps ou qualquer repositório Git.

### <a name="virtual-network"></a>Rede Virtual
A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: Os [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Grupos de Segurança de Rede) contêm listas de controle de acesso que permitem ou negam tráfego dentro de uma rede virtual. Os NSGs podem ser usados para proteger o tráfego em nível individual de VM ou sub-rede. Os seguintes NSGs existem:
- 1 NSG para o Gateway de Aplicativo
- 1 NSG para o Ambiente de Serviço de Aplicativo
- 1 NSG para o Banco de Dados SQL do Azure

Cada NSG tem portas e protocolos específicos abertos para que a solução possa funcionar segura e corretamente. Além disso, as seguintes configurações estão habilitadas para cada NSG:
  - [Eventos e logs de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
  - Os logs do Azure Monitor está conectado ao [diagnóstico do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**sub-redes**: Cada sub-rede está associada ao seu NSG correspondente.

**DNS do Azure**: O sistema de nomes de domínio, ou DNS, é responsável por converter (ou seja, resolver) um nome do site ou serviço para seu endereço IP. O [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de hospedagem para domínios DNS que fornece a resolução de nomes usando a infraestrutura do Azure. Ao hospedar domínios no Azure, os usuários podem gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. O DNS do Azure também dá suporte a domínios DNS privados.

**Azure Load Balancer**: O [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) permite que os clientes dimensionem os aplicativos e criem alta disponibilidade para serviços. O Azure Load Balancer é compatível tanto com cenários de entrada como de saída e fornece latência baixa, taxa de transferência alta e escala verticalmente a milhões de fluxos para todos os aplicativos TCP e UDP.

### <a name="data-in-transit"></a>Dados em trânsito
O Azure criptografa todas as comunicações entre datacenters do Azure por padrão. Todas as transações para o Armazenamento do Azure por meio do portal do Azure ocorrem por HTTPS.

### <a name="data-at-rest"></a>Dados em repouso
A arquitetura protege dados em repouso usando criptografia, auditoria de banco de dados e outras medidas.

**Armazenamento do Azure**: Para atender aos requisitos de criptografia de dados em repouso, o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption**
[O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução é integrada ao Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de Dados SQL do Azure**: A instância do Banco de Dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:
-   A [autenticação e a autorização do AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades dos usuários de banco de dados e de outros serviços da Microsoft em uma única localização central.
-   A [auditoria do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) controla os eventos de banco de dados e grava-os em um log de auditoria em uma conta de armazenamento do Azure.
-   O Banco de Dados SQL do Azure está configurado para usar [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia do banco de dados, dos backups associados e dos arquivos de log de transações para proteger as informações em repouso.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem o acesso aos servidores do banco de dados até que as permissões apropriadas sejam concedidas. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.
-   A [Detecção de Ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite detectar e responder a possíveis ameaças conforme elas ocorrem, fornecendo alertas de segurança sobre atividades suspeitas no banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões anormais de acesso ao banco de dados.
-   As [Colunas Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que dados confidenciais nunca sejam exibidos como texto não criptografado dentro do sistema do banco de dados. Após a habilitação da criptografia de dados, somente aplicativos cliente ou servidores de aplicativo com acesso às chaves poderão acessar dados de texto não criptografado.
- A [Segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite aos usuários definir políticas para restringir o acesso aos dados para interromper o processamento.
- A [Máscara de Dados Dinâmicos do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) pode ser usada após a implantação da arquitetura de referência. Os clientes precisarão ajustar as configurações da Máscara de Dados Dinâmicos para seguir o esquema de banco de dados que usam.

### <a name="identity-management"></a>Gerenciamento de identidades
As tecnologias a seguir fornecem funcionalidades de gerenciamento de identidades no ambiente do Azure:
-   O [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidades e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários para esta solução são criados no AAD, incluindo os usuários que acessam o Banco de Dados SQL do Azure.
-   A autenticação para o aplicativo é executada usando o AAD. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a criptografia da coluna do banco de dados utiliza o Azure Active Directory para autenticar o aplicativo no Banco de Dados SQL do Azure. Para obter mais informações, veja como [proteger dados confidenciais no Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   O [Controle de acesso baseado em função do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite o gerenciamento de acesso detalhado para o Azure. O acesso à assinatura é limitado ao administrador da assinatura, e o acesso aos recursos pode ser limitado com base na função de usuário.
- O [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permite que os clientes minimizem o número de usuários que tenham acesso a determinadas informações.  Os administradores podem usar o Privileged Identity Management do AAD para descobrir, restringir e monitorar identidades privilegiadas e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor o acesso administrativo sob demanda Just-In-Time quando necessário.
- O [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as identidades da organização, configura as respostas automatizadas para detectar ações suspeitas relacionadas a essas identidades e investiga incidentes suspeitos e toma a medida apropriada para resolvê-los.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: a solução usa o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para gerenciar chaves e segredos. O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Os recursos do Azure Key Vault a seguir ajudam os clientes a proteger os dados e o acesso a esses dados:
- Políticas de acesso avançadas são configuradas com base na necessidade.
- As políticas de acesso do Key Vault são definidas com o mínimo de permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault têm datas de validade.
- Todas as chaves no Key Vault são protegidas pelos HSMs (módulos de segurança de hardware) especializados. O tipo de chave é uma Chave RSA de 2048 bits protegida por HSM.
- Todos os usuários e identidades recebem permissões mínimas necessárias usando o controle de acesso baseado em função.
- Os Logs de diagnóstico para Key Vault são habilitados com um período de retenção de pelo menos 365 dias.
- As operações criptográficas permitidas para chaves são restritas às necessárias.

**Gateway de Aplicativo**: a arquitetura reduz o risco de vulnerabilidades de segurança usando o Gateway de Aplicativo com o Firewall do Aplicativo Web e o conjunto de regras OWASP habilitados. Dentre outros recursos estão:
- [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Habilitar [Descarregamento SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Desabilitar [TLS versões 1.0 e 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall do Aplicativo Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3.0
- [Habilitar o registro em log de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Investigações de integridade personalizadas](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center) e o [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecer proteção e notificações adicionais. A Central de Segurança do Azure também fornece um sistema de reputação.

### <a name="logging-and-auditing"></a>Registro em log e auditoria
O Azure Monitor fornece registro extensivo de atividade do sistema e do usuário, bem como integridade do sistema. Ele coleta e analisa dados gerados por recursos no Azure e em ambientes locais.
- **Logs de atividades**: Os [logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem insights sobre as operações executadas em recursos em uma assinatura. Os logs de atividade podem ajudar a determinar o iniciador, o horário da ocorrência e o status de uma operação.
- **Logs de diagnóstico**: Os [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registros emitidos por todos os recursos. Esses logs são logs do sistema de eventos do Windows, logs de Armazenamento do Azure, logs de auditoria do Key Vault e logs de acesso e firewall do Gateway de Aplicativo.
- **Arquivamento em log**: Todos os logs de diagnóstico são gravados em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, de até 730 dias, para atender aos requisitos de retenção específicos da organização. Esses logs conectem-se aos logs do Azure Monitor para processar, armazenar e relatórios de painel.

Além disso, as seguintes soluções de monitoramento são incluídas como parte dessa arquitetura:
-   [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de Verificação de Integridade do Active Directory avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Avaliação de Antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A solução de Antimalware fornece informações sobre malware, ameaças e status de proteção.
-   [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): A solução de Automação do Azure armazena, executa e gerencia runbooks. Nessa solução, runbooks ajudam a coletar logs do Application Insights e do Banco de Dados SQL do Azure.
-   [Segurança e Auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): O painel de Segurança e Auditoria fornece insights de alto nível sobre o estado de segurança dos recursos, fornecendo métricas sobre domínios de segurança, problemas importantes, detecções, inteligência contra ameaças e consultas de segurança comuns.
-   [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de Verificação de Integridade do SQL avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Gerenciamento de Atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): A solução de Gerenciamento de Atualizações permite que os clientes gerenciem as atualizações de segurança do sistema operacional, incluindo o status das atualizações disponíveis e o processo de instalação das atualizações necessárias.
-   [Integridade do Agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de Integridade do Agente informa quantos agentes são implantados e a distribuição geográfica deles, além de quantos agentes estão sem resposta e o número de agentes que estão enviando dados operacionais.
-   [Logs de Atividades do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução de Análise do Log de Atividades auxilia na análise dos logs de atividades do Azure em todas as assinaturas do Azure para um cliente.
-   [Controle de Alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking): A solução de Controle de Alterações permite que os clientes facilmente identifiquem alterações no ambiente.

**Azure Monitor**
[O Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a rastrear o desempenho, manter a segurança e identificar tendências, permitindo que as organizações auditem, criem alertas e arquivem dados, incluindo o rastreamento de chamadas à API nos recursos do Azure dos clientes.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados dessa arquitetura de referência está disponível para [download](https://aka.ms/fedrampPaaSWebAppDFD) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entenderem os pontos de risco em potencial na infraestrutura do sistema ao fazer modificações.

![Aplicativo Web de PaaS para o modelo contra ameaças do FedRAMP](images/fedramp-paaswa-threat-model.png?raw=true "Aplicativo Web de PaaS para o modelo de risco do FedRAMP")

## <a name="compliance-documentation"></a>Documentação de conformidade
O [Blueprint de Segurança e Conformidade do Azure – Matriz de Responsabilidades do Cliente do FedRAMP High](https://aka.ms/blueprinthighcrm) lista todos os controles de segurança necessários para a linha de base do FedRAMP High. A matriz indica se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou é compartilhada pelos dois.

O [Blueprint de Segurança e Conformidade do Azure – Matriz de Implantação de Controle de Aplicativo Web de PaaS do FedRAMP High](https://aka.ms/fedrampPaaSWebAppCIM) lista todos os controles de segurança necessários para a linha de base do FedRAMP High. A matriz fornece informações sobre quais controles estão cobertos pela arquitetura do aplicativo Web de PaaS, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada controle coberto.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações
### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel de VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa ser configurado para estabelecer uma conexão segura com os recursos implantados como parte dessa arquitetura de referência de aplicativo Web de PaaS. Configurando adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, uma conexão VPN entre uma rede local e uma rede virtual do Azure pode ser criada. Essa conexão ocorre pela Internet e permite que os clientes enviem informações por "túnel” com segurança usando um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. O [modo de túnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego do túnel de VPN passa pela Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link de WAN dedicado entre o Azure e a instalação local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não ocorrem pela Internet, elas oferecem mais confiabilidade, mais velocidade, latências mais baixas e maior segurança que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicações do cliente, os dados não passam pela Internet e, portanto, não estão expostos a ela.

Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) práticas recomendadas para a implementação de uma rede híbrida segura que estende uma rede local para o Azure.

## <a name="disclaimer"></a>Isenção de responsabilidade

 - Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de consulta interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.
 - Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
 - Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
