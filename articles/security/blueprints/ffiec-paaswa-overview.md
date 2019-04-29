---
title: Blueprint de segurança e conformidade do Azure – aplicativo Web de PaaS para o FFIEC
description: Blueprint de segurança e conformidade do Azure – aplicativo Web de PaaS para o FFIEC
services: security
author: meladie
ms.assetid: a552e313-2b46-4001-b6e2-bed0b7757ac5
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: meladie
ms.openlocfilehash: 63ad692e1050f900310f8195b79f26dd99704b93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609635"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-ffiec-financial-services"></a>Blueprint de Segurança e Conformidade do Azure: Aplicativo Web de PaaS para Serviços Financeiros FFIEC

## <a name="overview"></a>Visão geral

Esta automação do Blueprint de segurança e conformidade do Azure fornece as diretrizes para a implantação de um ambiente de PaaS (plataforma como serviço) adequado para a coleta, o armazenamento e a recuperação de dados financeiros, regulamentado pelo FFIEC (Federal Financial Institution Examination Council). Essa solução automatiza a implantação e a configuração de recursos do Azure para uma arquitetura de referência comum, demonstrando maneiras pelas quais os clientes podem atender aos requisitos específicos de segurança e conformidade, e serve como base para os clientes criarem e Configurarem suas próprias soluções no Azure. A solução implementa um subconjunto dos requisitos dos manuais do FFIEC. Para obter mais informações sobre os requisitos do FFIEC e esta solução, confira a seção [documentação de conformidade](#compliance-documentation).

Esta automação do Blueprint de segurança e conformidade do Azure implanta uma arquitetura de referência de aplicativo Web de PaaS com controles de segurança pré-configurados para ajudar os clientes a alcançarem a conformidade com os requisitos do FFIEC. A solução consiste em modelos do Azure Resource Manager e scripts do PowerShell que guiam a implantação e a configuração dos recursos.

Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção. A implantação de um aplicativo nesse ambiente sem modificações não é suficiente para atender completamente aos requisitos dos objetivos do FFIEC. Observe o seguinte:
- Essa arquitetura fornece uma linha de base para ajudar os clientes a usar o Azure em conformidade com o FFIEC.
- Os clientes são responsáveis por realizar as devidas avaliações de segurança e conformidade de qualquer solução criada usando essa arquitetura, já que os requisitos podem variar com base nas particularidades da implementação de cada cliente.

Para alcançar a conformidade com o FFIEC é necessário que auditores qualificados certifiquem a solução de produção do cliente. As auditorias são supervisionadas por examinadores de agências membros do FFIEC, incluindo o Comitê de Administradores dos órgãos FRB (Federal Reserve System), FDIC (Federal Deposit Insurance Corporation), NCUA (National Credit Union Administration), OCC (Office of the Comptroller of the Currency) e CFPB (Consumer Financial Protection Bureau). Esses examinadores certificam que as auditorias sejam realizadas por avaliadores independentes da instituição auditada. Os clientes são responsáveis por realizar as devidas avaliações de segurança e conformidade de qualquer solução criada usando essa arquitetura, uma vez que os requisitos podem variar com base nas particularidades da implementação de cada cliente.

Clique [aqui](https://aka.ms/ffiec-paaswa-repo) para obter instruções de implantação.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura

Esta automação do Blueprint de segurança e conformidade do Azure implanta uma arquitetura de referência para um aplicativo Web de PaaS com um back-end do Banco de Dados SQL do Azure. O aplicativo Web é hospedado em um Ambiente do Serviço de Aplicativo do Azure, que um ambiente privado dedicado em um datacenter do Azure. O ambiente balanceia a carga de tráfego do aplicativo Web entre as máquinas virtuais gerenciadas pelo Azure. Essa arquitetura também inclui os grupos de segurança de rede, um Gateway de Aplicativo, o DNS do Azure e o balanceador de carga.

Para análises e relatórios aprimorados, os bancos de dados SQL do Azure podem ser configurados com índices columnstore. Os bancos de dados SQL do Azure podem ser ampliados ou reduzidos ou desligados completamente em resposta ao uso do cliente. Todo o tráfego SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como melhor prática, o Azure recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.

A solução usa contas de Armazenamento do Azure, que os clientes podem configurar para usar a Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O Azure armazena três cópias dos dados dentro de um datacenter selecionado do cliente para garantir a resiliência. O armazenamento com redundância geográfica garante que os dados sejam replicados para um datacenter secundário centenas de milhas de distância e armazenados novamente como três cópias nesse datacenter, impedindo que um evento prejudicial resulte em perda de dados no data center principal do cliente.

Para maior segurança, todos os recursos nessa solução são gerenciados como um grupo de recursos por meio do Azure Resource Manager. O controle de acesso baseado em função do Azure Active Directory é usado para controlar o acesso aos recursos implantados, incluindo as chaves no Azure Key Vault. A integridade do sistema é monitorada por meio do Azure Monitor. Os clientes configuram os dois serviços de monitoramento para capturar logs e exibir a integridade do sistema em um único painel facilmente navegável.

O Banco de Dados SQL do Azure normalmente é gerenciado por meio do SQL Server Management Studio, que é executado de um computador local configurado para acessar o Banco de Dados SQL do Azure usando uma conexão segura VPN ou do ExpressRoute.

Além disso, o Application Insights fornece gerenciamento de desempenho de aplicativos em tempo real e análise por meio de logs do Azure Monitor. **A Microsoft recomenda configurar uma conexão VPN ou do ExpressRoute para gerenciamento e importação de dados para a sub-rede na arquitetura de referência.**

![Diagrama da arquitetura de referência de aplicativo Web de PaaS para o FFIEC](images/ffiec-paaswa-architecture.png "Diagrama da arquitetura de referência de aplicativo Web de PaaS para o FFIEC")

A solução usa os serviços do Azure a seguir. Há detalhes da arquitetura de implantação na seção [arquitetura de implantação](#deployment-architecture).

- Gateway de Aplicativo
    - Firewall do aplicativo Web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP
        - Porta do ouvinte: 443
- Application Insights
- Azure Active Directory
- Ambiente do Serviço de Aplicativo Azure v2
- Automação do Azure
- DNS do Azure
- Cofre da Chave do Azure
- Azure Load Balancer
- O Azure Monitor (logs)
- Azure Resource Manager
- Central de Segurança do Azure
- Banco de Dados SQL do Azure
- Armazenamento do Azure
- Rede Virtual do Azure
    - (1) /16 rede
    - (4) /24 redes
    - Grupos de segurança de rede
- Serviço de aplicativo do Azure

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir fornece detalhes sobre os elementos de implantação e implementação.

**Azure Resource Manager**: O [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) permite que os clientes trabalhem com os recursos da solução como um grupo. Os clientes podem implantar, atualizar ou excluir todos os recursos da solução em uma única operação coordenada. Os clientes usam um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. O Resource Manager fornece recursos de segurança, auditoria e marcação para ajudar os clientes a gerenciar seus recursos após a implantação.

**Host bastião**: O host bastião é o único ponto de entrada que permite que os usuários acessem os recursos implantados nesse ambiente. O host bastião fornece uma conexão segura com os recursos implantados, permitindo apenas tráfego remoto de endereços IP públicos em uma lista de segurança. Para permitir o tráfego RDP (área de trabalho remota), a origem do tráfego precisa ser definida no grupo de segurança de rede.

Essa solução cria uma máquina virtual como um host bastião ingressado em domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Extensão de Diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usando o Azure Key Vault
-   Uma [política de desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos de máquina virtual quando eles não estiverem em uso
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) habilitado, para que as credenciais e outras informações secretas sejam executadas em um ambiente protegido isolado do sistema operacional em execução

**Ambiente do Serviço de Aplicativo v2**: O Ambiente do Serviço de Aplicativo do Azure é um recurso do Serviço de Aplicativo que fornece um ambiente totalmente isolado e dedicado para a execução segura de aplicativos de Serviço de Aplicativo em grande escala. Esse recurso de isolamento é necessário para atender aos requisitos de conformidade do FFIEC.

Os ambientes do Serviço de Aplicativo são isolados para executar somente aplicativos de um único cliente e sempre são implantados em uma rede virtual. Esse recurso de isolamento permite o isolamento completo do locatário na arquitetura de referência, removendo-o do ambiente multilocatário do Azure e proibindo que esses vários locatários enumerem os recursos implantados do Ambiente do Serviço de Aplicativo. Os clientes têm um controle refinado sobre o tráfego de entrada e saída da rede de aplicativos, e os aplicativos podem estabelecer conexões seguras de alta velocidade por meio de redes virtuais com recursos corporativos locais. Os clientes podem realizar o "dimensionamento automático" com o Ambiente do Serviço de Aplicativo com base em métricas de carga, no orçamento disponível ou em um agendamento definido.

O uso do Ambiente do Serviço de Aplicativo para essa arquitetura permite as seguintes configurações:

- Hospedagem dentro de uma rede virtual do Azure protegida e regras de segurança de rede
- Certificado do balanceador de carga interno autoassinado para comunicação HTTPS. Como melhor prática, a Microsoft recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.
- [Modo Balanceamento de Carga Interno](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Desabilitar o [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Alterar a [criptografia TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Controlar [portas N/W de tráfego de entrada](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Firewall do aplicativo Web – restringir dados](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Permitir o [Tráfego de Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Serviço de Aplicativo do Azure**: O [Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/) permite que os clientes criem e hospedem aplicativos Web na linguagem de programação de sua escolha sem gerenciar a infraestrutura. Eles oferecem o dimensionamento automático e alta disponibilidade, compatível com Windows e Linux e permite implantações automatizadas do GitHub, Azure DevOps ou qualquer repositório Git.

### <a name="virtual-network"></a>Rede Virtual

A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: Os [Grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm listas de controle de acesso que permitem ou negam tráfego dentro de uma rede virtual. Os grupos de segurança de rede podem ser usados para proteger o tráfego no nível da sub-rede ou da máquina virtual individual. Existem os seguintes grupos de segurança de rede:

- 1 Grupo de segurança de rede do Gateway de Aplicativo
- 1 Grupo de segurança de rede do Ambiente do Serviço de Aplicativo
- 1 Grupo de segurança de rede do Banco de Dados SQL do Azure
- 1 Grupo de segurança de rede do host bastião

Cada um dos grupos de segurança de rede têm portas e protocolos específicos abertos para que a solução possa funcionar corretamente e com segurança. Além disso, as configurações a seguir são habilitadas para cada grupo de segurança de rede:

- [Eventos e logs de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
- Os logs do Azure Monitor está conectado às [grupo de segurança de rede&#39;s os logs de diagnóstico](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**sub-redes**: Cada sub-rede está associada ao seu grupo de segurança de rede correspondente.

**DNS do Azure**: O sistema de nomes de domínio, ou DNS, é responsável por converter (ou seja, resolver) um nome do site ou serviço para seu endereço IP. O [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de hospedagem para domínios DNS que fornece a resolução de nomes usando a infraestrutura do Azure. Ao hospedar domínios no Azure, os usuários podem gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. O DNS do Azure também dá suporte a domínios DNS privados.

**Azure Load Balancer**: O [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) permite que os clientes dimensionem os aplicativos e criem alta disponibilidade para serviços. O Azure Load Balancer é compatível tanto com cenários de entrada como de saída e fornece latência baixa, taxa de transferência alta e escala verticalmente a milhões de fluxos para todos os aplicativos TCP e UDP.

### <a name="data-in-transit"></a>Dados em trânsito

O Azure criptografa todas as comunicações entre datacenters do Azure por padrão. Todas as transações para o Armazenamento do Azure por meio do portal do Azure ocorrem por HTTPS.

### <a name="data-at-rest"></a>Dados em repouso

A arquitetura protege dados em repouso usando criptografia, auditoria de banco de dados e outras medidas.

**Armazenamento do Azure**: Para satisfazer os requisitos de dados em repouso criptografado, todo o [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/) usa [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption). Isso ajuda a proteger os dados pessoais em apoio aos compromissos de segurança organizacional e aos requisitos de conformidade definidos pelo FFIEC.

**Azure Disk Encryption**: O [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utiliza o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução é integrada ao Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de Dados SQL do Azure**: A instância do Banco de Dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:

- A [autenticação e a autorização do Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidade dos usuários de banco de dados e de outros serviços da Microsoft em uma única localização central.
- A [auditoria do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) controla os eventos de banco de dados e grava-os em um log de auditoria em uma conta de armazenamento do Azure.
- O Banco de Dados SQL do Azure está configurado para usar a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia em tempo real do banco de dados, dos backups associados e dos arquivos de log de transações para proteger as informações em repouso. A Transparent Data Encryption oferece a garantia de que os dados armazenados não fiquem sujeitos a acesso não autorizado.
- [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem o acesso aos servidores do banco de dados até que as permissões apropriadas sejam concedidas. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.
- A [Detecção de Ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite detectar e responder a possíveis ameaças conforme elas ocorrem, fornecendo alertas de segurança sobre atividades suspeitas no banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões anormais de acesso ao banco de dados.
- As [Colunas Criptografadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca sejam exibidos como texto não criptografado no sistema do banco de dados. Após a habilitação da criptografia de dados, somente aplicativos cliente ou servidores de aplicativo com acesso às chaves poderão acessar dados de texto não criptografado.
- A [Máscara de Dados Dinâmicos do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos não privilegiados. A Máscara de Dados Dinâmicos pode descobrir automaticamente dados potencialmente confidenciais e sugerir as máscaras apropriadas a serem aplicadas. Isso ajuda a identificar e reduzir o acesso aos dados, para que eles não saiam do banco de dados por meio de acesso não autorizado. Os clientes são responsáveis por ajustar as configurações da máscara de dados dinâmicos para seguir seu esquema de banco de dados.

### <a name="identity-management"></a>Gerenciamento de identidades

As seguintes tecnologias oferecem funcionalidades para gerenciar o acesso a dados no ambiente do Azure:

- O [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários dessa solução são criados no Azure Active Directory, incluindo os usuários que acessam o Banco de Dados SQL do Azure.
- A autenticação no aplicativo é executada usando o Azure Active Directory. Para obter mais informações, confira [Integrando aplicativos com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a criptografia da coluna do banco de dados utiliza o Azure Active Directory para autenticar o aplicativo no Banco de Dados SQL do Azure. Para obter mais informações, veja como [proteger dados confidenciais no Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- O [controle de acesso baseado em função do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite que os administradores definam permissões de acesso refinadas para conceder apenas a quantidade de acesso de que os usuários precisam para realizar seus trabalhos. Em vez de conceder permissão irrestrita aos recursos do Azure a todos os usuários, os administradores podem permitir apenas determinadas ações para acessar os dados. O acesso à assinatura é limitado ao administrador da assinatura.
- O [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permite que os clientes minimizem o número de usuários que tenham acesso a determinadas informações. Os administradores podem usar o Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar identidades privilegiadas e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor o acesso administrativo sob demanda Just-In-Time quando necessário.
- O [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as identidades de uma organização, configura respostas automatizadas para detectar ações suspeitas relacionadas a essas identidades e investiga incidentes suspeitos para tomar medidas apropriadas para resolvê-los.

### <a name="security"></a>Segurança

**Gerenciamento de segredos**: A solução usa o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. As seguintes funcionalidades do Azure Key Vault ajudam os clientes a proteger os dados e o acesso a eles:

- Políticas de acesso avançadas são configuradas com base na necessidade.
- As políticas de acesso do Key Vault são definidas com o mínimo de permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault têm datas de validade.
- Todas as chaves no Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma Chave RSA de 2048 bits protegida por HSM.
- Todos os usuários e identidades recebem permissões mínimas necessárias usando o controle de acesso baseado em função.
- Os Logs de diagnóstico para Key Vault são habilitados com um período de retenção de pelo menos 365 dias.
- As operações criptográficas permitidas para chaves são restritas às necessárias.

**Central de Segurança do Azure**: Com a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem aplicar e gerenciar políticas de segurança em cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques de forma centralizada. Além disso, a Central de Segurança do Azure acessa as configurações existentes de serviços do Azure para fornecer recomendações de serviço e configuração, a fim de ajudar a melhorar a postura de segurança e proteger os dados.

A Central de Segurança do Azure usa uma variedade de funcionalidades de detecção para alertar os clientes de ataques potenciais direcionados a seus ambientes. Esses alertas contêm informações valiosas sobre o que disparou o alerta, os recursos de destino e a origem do ataque. A Central de Segurança do Azure tem um conjunto de [alertas de segurança predefinidos](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), que são disparados em caso de ameaça ou atividade suspeita. As [regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) na Central de Segurança do Azure permitem que os clientes definam novos alertas de segurança com base nos dados já coletados do ambiente.

A Central de Segurança do Azure fornece alertas de segurança e incidentes priorizados, simplificando a descoberta e a resolução por parte dos clientes de possíveis problemas de segurança. Um [relatório de inteligência contra ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada, a fim de ajudar as equipes de resposta a incidentes a investigar e corrigir as ameaças.

**Gateway de Aplicativo do Azure**: A arquitetura reduz o risco de vulnerabilidades de segurança usando um Gateway de Aplicativo do Azure com um firewall de aplicativo Web configurado e o conjunto de regras OWASP habilitado. Dentre outros recursos estão:

- [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Habilitar [Descarregamento SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Desabilitar [TLS versões 1.0 e 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall do aplicativo Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo de prevenção)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3.0
- [Habilitar o registro em log de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Investigações de integridade personalizadas](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center) e o [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecer proteção e notificações adicionais. A Central de Segurança do Azure também fornece um sistema de reputação.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

Os serviços do Azure registram em log de forma extensiva as atividades do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividades**: Os [logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem insights sobre as operações executadas em recursos em uma assinatura. Os logs de atividade podem ajudar a determinar o iniciador, o horário da ocorrência e o status de uma operação.
- **Logs de diagnóstico**: Os [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registros emitidos por todos os recursos. Esses logs são logs do sistema de eventos do Windows, logs de Armazenamento do Azure, logs de auditoria do Key Vault e logs de acesso e firewall do Gateway de Aplicativo. Todos os logs de diagnóstico são gravados em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, de até 730 dias, para atender aos requisitos de retenção específicos da organização.

**Logs do Azure Monitor**: Esses logs são consolidados [registra em log do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e emissão de relatórios do painel. Depois de coletados, os dados são organizados em tabelas separadas para cada tipo de dados nos espaços de trabalho do Log Analytics, o que permite que todos os dados sejam analisados juntos, independentemente de sua origem original. Além disso, a Central de segurança do Azure integra-se com os logs do Azure Monitor permitindo que os clientes usem Kusto consultas para acessar seus dados de evento de segurança e combiná-lo com dados de outros serviços.

O Azure a seguir [soluções de monitoramento](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) são incluídos como parte dessa arquitetura:
-   [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de Verificação de Integridade do Active Directory avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de Verificação de Integridade do SQL avalia o risco e a integridade dos ambientes do servidor em intervalos regulares e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do Agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de Integridade do Agente informa quantos agentes são implantados e sua distribuição geográfica, além de quantos agentes sem resposta e o número de agentes que estão enviando dados operacionais.
-   [Análise do Log de Atividades](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução de Análise do Log de Atividades auxilia na análise dos logs de atividades do Azure em todas as assinaturas do Azure para um cliente.

**Automação do Azure**: A [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nessa solução, os runbooks ajudam a coletar logs do Banco de Dados SQL do Azure. A solução [Controle de Alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking) da Automação permite que os clientes identifiquem com facilidade as alterações no ambiente.

**Azure Monitor**: O [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações façam auditoria, criem alertas e arquivem dados, incluindo o rastreamento de chamadas à API nos recursos do Azure.

**Application Insights**: O [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço de Gerenciamento de Desempenho de Aplicativos extensível para desenvolvedores da Web em várias plataformas. O Application Insights detecta anomalias de desempenho e os clientes podem usá-lo para monitorar o aplicativo Web online. Ele inclui ferramentas de análise avançadas para ajudar os clientes a diagnosticarem problemas e entenderem o que os usuários realmente fazem com seu aplicativo. Ele foi projetado para ajudar os clientes a aprimorar continuamente o desempenho e a usabilidade.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados dessa arquitetura de referência está disponível para [download](https://aka.ms/ffiec-paaswa-tm) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entenderem os pontos de risco em potencial na infraestrutura do sistema ao fazer modificações.

![Modelo de ameaça de aplicativo Web de PaaS para o FFIEC](images/ffiec-paaswa-threat-model.png "Modelo de ameaça de aplicativo Web de PaaS para o FFIEC")

## <a name="compliance-documentation"></a>Documentação de conformidade

O [Blueprint de Segurança e Conformidade do Azure – matriz de responsabilidades do cliente para FFIEC](https://aka.ms/ffiec-crm) lista todos os objetivos de segurança exigidos pelo FFIEC. Essa matriz detalha se a implementação de cada objetivo é de responsabilidade da Microsoft, do cliente ou é compartilhada pelos dois.

O [Blueprint de segurança e conformidade do Azure – matriz de implementação do aplicativo Web de PaaS do FFIEC](https://aka.ms/ffiec-paaswa-cim) fornece informações sobre quais requisitos do FFIEC são atendidos pela arquitetura do aplicativo Web de PaaS, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada objetivo abordado.

## <a name="deploy-this-solution"></a>Implantar essa solução
Esta Automação de Segurança e Conformidade do Azure Blueprint é composta de arquivos de configuração JSON e scripts do PowerShell tratados pelo serviço de API do Azure Resource Manager para implantar recursos no Azure. As instruções detalhadas de implantação estão disponíveis [aqui](https://aka.ms/ffiec-paaswa-repo).

#### <a name="quickstart"></a>Início rápido
1. Clone ou baixe [este](https://aka.ms/ffiec-paaswa-repo) repositório GitHub na sua estação de trabalho local.

2. Examine 0-Setup-AdministrativeAccountAndPermission.md e execute os comandos fornecidos.

3. Implante uma solução de teste com dados de exemplo da Contoso ou crie o piloto de um ambiente de produção inicial.
    - 1A-ContosoWebStoreDemoAzureResources.ps1
        - Esse script implanta os recursos do Azure para demonstrar uma loja da Web usando dados de exemplo da Contoso.
    - 1-DeployAndConfigureAzureResources.ps1
        - Esse script implanta os recursos do Azure necessários para dar suporte a um ambiente de produção para um aplicativo Web de propriedade do cliente. Esse ambiente deve ser mais personalizado pelo cliente com base nos requisitos organizacionais.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute

Um túnel de VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa ser configurado para estabelecer uma conexão segura com os recursos implantados como parte dessa arquitetura de referência de aplicativo Web de PaaS. Configurando adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Implementando um túnel de VPN seguro com o Azure, é possível criar uma conexão privada virtual entre uma rede local e uma Rede Virtual do Azure. Essa conexão ocorre na Internet e permite que os clientes enviem informações por &quot;túnel&quot; com segurança usando um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. O [modo de túnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego do túnel de VPN passa pela Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link de WAN dedicado entre o Azure e a instalação local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não ocorrem pela Internet, elas oferecem mais confiabilidade, mais velocidade, latências mais baixas e maior segurança que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicações do cliente, os dados não passam pela Internet e, portanto, não são expostos a ela.

Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) práticas recomendadas para a implementação de uma rede híbrida segura que estende uma rede local para o Azure.

## <a name="disclaimer"></a>Isenção de responsabilidade

- Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido &quot;no estado em que se encontra&quot;. As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.
- Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.
- Os clientes podem copiar e usar este documento para fins de consulta interna.
- Determinadas recomendações neste documento podem resultar no aumento do uso de recursos de computação, de rede ou de dados no Azure e podem aumentar os custos de licença ou de assinatura do Azure para o cliente.
- Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
- Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
