---
title: Blueprint de segurança e conformidade do Azure - Data Warehouse para NIST SP 800-171
description: Blueprint de segurança e conformidade do Azure - Data Warehouse para NIST SP 800-171
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: a1850ecfbb21eb9495bb0e6de362dc8dee3026a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609571"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Blueprint de segurança e conformidade do Azure - Data Warehouse para NIST SP 800-171

## <a name="overview"></a>Visão geral
A [Publicação Especial do NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornece diretrizes para proteção de CUI (Controlled Unclassified Information) que reside em organizações e sistemas de informações não federais. O NIST SP 800-171 estabelece 14 famílias de requisitos de segurança para proteger a confidencialidade de CUI.

Esse blueprint de segurança e conformidade do Azure fornece diretrizes para ajudar os clientes a implantar uma arquitetura de data warehouse no Azure que implementa um subconjunto de controles do NIST SP 800-171. Esta solução demonstra maneiras em que os clientes podem atender a requisitos de conformidade e segurança específicos. Ela também funciona como base para os clientes criarem e configurarem suas próprias soluções de data warehouse no Azure.

Essa arquitetura de referência, o guia de implementação associado e o modelo de risco devem funcionar como uma base que clientes adaptarão às suas necessidades específicas. Eles não devem ser usados no estado em que se encontram em um ambiente de produção. Os clientes são responsáveis por realizar avaliações de conformidade e segurança apropriadas de qualquer solução criada usando essa arquitetura. Os requisitos podem variar com base nas especificações da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
Essa solução fornece uma arquitetura de referência que implementa um data warehouse em nuvem seguro e de alto desempenho. Há duas camadas de dados separadas nesta arquitetura. Uma camada é onde os dados são importados, armazenados e testados dentro de um ambiente SQL clusterizado. É outra camada para o SQL Data Warehouse. Com esta camada, os dados são carregados usando uma ferramenta de ETL (extrair, transformar e carregar) (por exemplo, consultas T-SQL [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)) para processamento. Após os dados serem armazenados no SQL Data Warehouse, você poderá executar análises em grande escala.

O Azure oferece uma variedade de serviços de relatórios e análises para o cliente. Essa solução inclui o SQL Server Reporting Services para a criação rápida de relatórios do SQL Data Warehouse. Todo o tráfego SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como melhor prática, recomendamos o uso de uma autoridade de certificação confiável para aumentar a segurança.

O SQL Data Warehouse do Azure armazena dados em tabelas relacionais com armazenamento colunar. Esse formato reduz consideravelmente os custos de armazenamento de dados e melhora o desempenho da consulta. Dependendo dos requisitos de uso, os recursos de computação do SQL Data Warehouse poderão ser escalados ou reduzidos verticalmente ou desligados completamente se não houver processos ativos que demandem recursos de computação.

Um balanceador de carga do SQL Server gerencia o tráfego SQL, garantindo o alto desempenho. Todas as VMs (máquinas virtuais) nessa arquitetura de referência são implantadas em um conjunto de disponibilidade com instâncias do SQL Server configuradas em um grupo de disponibilidade Always On. Essa configuração fornece funcionalidades de recuperação de desastre e alta disponibilidade.

Essa arquitetura de referência de data warehouse também inclui uma camada do Active Directory para o gerenciamento de recursos dentro da arquitetura. A sub-rede do Active Directory permite fácil adoção em uma estrutura maior de floresta do Active Directory. Dessa forma, o ambiente pode funcionar continuamente, mesmo quando o acesso à floresta maior não estiver disponível. Todas as VMs são ingressadas no domínio na camada do Active Directory. Elas usam políticas de grupo do Active Directory para aplicar configurações de conformidade e segurança no nível do sistema operacional.

A solução usa contas de Armazenamento do Azure, que os clientes podem configurar para usar a Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O Azure armazena três cópias dos dados dentro de um data center selecionado do cliente para garantir a resiliência. O armazenamento com redundância geográfica garante que os dados sejam replicados para um data center secundário a centenas de milhas de distância e armazenados novamente como três cópias dentro desse data center. Essa disposição impede que um evento prejudicial no data center primário do cliente resulte em perda de dados.

Para maior segurança, todos os recursos nessa solução são gerenciados como um grupo de recursos por meio do Azure Resource Manager. O RBAC (controle de acesso baseado em função) do Azure AD (Azure Active Directory) é usado para controlar o acesso aos recursos implantados. Esses recursos incluem as chaves do cliente no Azure Key Vault. A integridade do sistema é monitorada por meio da Central de Segurança do Azure e do Azure Monitor. Os clientes configuram ambos os serviços de monitoramento para capturar os logs. A integridade do sistema é exibida em um único painel fácil de usar.

Uma VM serve como um host de bastiões de gerenciamento. Ele fornece uma conexão segura para os administradores acessarem os recursos implantados. Os dados são carregados nessa área de preparo por meio do host bastião de gerenciamento. *Recomendamos configurar uma conexão VPN ou do Azure ExpressRoute para gerenciamento e importação de dados para a sub-rede da arquitetura de referência.*

![Data Warehouse para diagrama de arquitetura de referência do NIST SP 800-171](images/nist171-dw-architecture.png "Data Warehouse para diagrama de arquitetura de referência do NIST SP 800-171")

A solução usa os serviços do Azure a seguir. Para obter mais informações, confira a seção [Arquitetura de implantação](#deployment-architecture).

- Conjuntos de disponibilidade
    - Controladores de domínio do Active Directory
    - Testemunha e nós de cluster do SQL Server
- Azure Active Directory
- Catálogo de Dados do Azure
- Cofre de Chaves Azure
- O Azure Monitor (logs)
- Central de Segurança do Azure
- Azure Load Balancer
- Armazenamento do Azure
- Máquinas Virtuais do Azure
    - (1) Host bastião
    - (2) Controlador de domínio do Active Directory
    - (2) Nó de cluster do SQL Server
    - (1) Testemunha do SQL Server
- Rede Virtual do Azure
    - (1) /16 rede
    - (4) /24 redes
    - (4) Grupos de segurança de rede
- Cofre dos Serviços de Recuperação
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir fornece detalhes sobre os elementos de implantação e implementação.

**Azure SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) é um data warehouse corporativo que tira proveito de processamento paralelo para executar rapidamente consultas complexas em petabytes de dados. Os usuários podem usar consultas T-SQL PolyBase simples para importar Big Data no SQL Data Warehouse e utilizar o poder do processamento paralelo massivo para executar análises de alto desempenho.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) fornece a rápida criação de relatórios com tabelas, gráficos, mapas, medidores, matrizes e mais para o SQL Data Warehouse.

**Catálogo de Dados do Azure**: O [Catálogo de Dados](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) torna as fontes de dados facilmente identificáveis e compreensíveis para os usuários que gerenciam os dados. Fontes de dados comuns podem ser registradas, marcadas e pesquisadas por dados. Os dados permanecem no local existente, mas uma cópia de seus metadados é adicionada ao Catálogo de Dados. Uma referência ao local de fonte de dados é incluída. Os metadados são indexados para tornar cada fonte de dados fácil de descobrir por meio de pesquisa. A indexação também torna compreensível para os usuários que descobrirem.

**Host bastião**: O host de bastiões é o único ponto de entrada que os usuários podem usar para acessar os recursos implantados nesse ambiente. O host bastião fornece uma conexão segura aos recursos implantados, permitindo apenas o tráfego remoto de endereços IP públicos em uma lista segura. Para permitir tráfego de área de trabalho remota, a origem do tráfego precisa ser definida no grupo de segurança de rede.

Essa solução cria uma VM como um host bastião ingressado no domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Extensão de Diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usando o Key Vault.
-   Uma [política de desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos da VM quando eles não estão em uso.
-   O [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) é habilitado para que as credenciais e outros segredos sejam executados em um ambiente protegido e isolado do sistema operacional em execução.

### <a name="virtual-network"></a>Rede virtual
Esta arquitetura de referência define uma rede virtual privada com um espaço de endereço de 10.0.0.0/16.

**Grupo de segurança de rede**: Os [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Grupo de Segurança de Rede) contêm listas de controle de acesso que permitem ou negam tráfego dentro de uma rede virtual. Os NSGs podem ser usados para proteger o tráfego em nível individual de VM ou sub-rede. Os seguintes NSGs existem:
  - Um NSG para a camada de dados (Clusters do SQL Server, Testemunha do SQL Server e Balanceador de Carga SQL)
  - Um NSG para o host bastião de gerenciamento
  - Um NSG para o Active Directory
  - Um NSG para o SQL Server Reporting Services

Cada NSG tem portas e protocolos específicos abertos para que a solução possa funcionar corretamente e com segurança. Além disso, as seguintes configurações estão habilitadas para cada NSG:
  - Os [eventos e logs de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) estão habilitados e são armazenados em uma conta de armazenamento.
  - Os logs do Azure Monitor está conectado para o [diagnóstico do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**sub-redes**: Cada sub-rede está associada ao seu NSG correspondente.

### <a name="data-at-rest"></a>Dados em repouso
A arquitetura protege dados em repouso por meio de várias medidas. Essas medidas incluem criptografia e auditoria do banco de dados.

**Armazenamento do Azure**: Para atender aos requisitos de dados em repouso criptografados, todo o [Armazenamento](https://azure.microsoft.com/services/storage/) usa [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption). Esse recurso ajuda a proteger e preservar os dados em suporte aos compromissos de segurança organizacional e aos requisitos de conformidade.

**Azure Disk Encryption**: O [Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usa o recurso BitLocker do Windows para fornecer criptografia de volume para o sistema operacional e os discos de dados. A solução integra-se ao Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de Dados SQL do Azure**: A instância do Banco de Dados SQL usa as seguintes medidas de segurança do banco de dados:
-   A [autenticação e a autorização do Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidade dos usuários de banco de dados e de outros serviços da Microsoft em uma única localização central.
-   A [auditoria do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) controla os eventos de banco de dados e grava-os em um log de auditoria em uma conta de armazenamento do Azure.
-   O Banco de Dados SQL é configurado para usar a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Ela executa criptografia e descriptografia em tempo real do banco de dados, dos backups associados e dos arquivos de log de transações para proteger as informações em repouso. A Transparent Data Encryption oferece a garantia de que os dados armazenados não estejam sujeitos a acesso não autorizado.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem o acesso aos servidores do banco de dados até que as permissões apropriadas sejam concedidas. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.
-   A [Detecção de Ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) habilita a detecção e a resposta a possíveis ameaças conforme elas ocorrem. Ela fornece alertas de segurança sobre atividades suspeitas do banco de dados, possíveis vulnerabilidades, ataques de injeção de SQL e padrões anormais de acesso ao banco de dados.
-   As [colunas criptografadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca sejam exibidos como texto sem formatação dentro do sistema de banco de dados. Depois que a criptografia de dados estiver habilitada, somente os aplicativos clientes ou servidores de aplicativos com acesso às chaves poderão acessar dados de texto sem formatação.
- As [propriedades estendidas](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) podem ser usadas para descontinuar o processamento de titulares de dados. Os usuários podem adicionar propriedades personalizadas a objetos de banco de dados. Eles também podem marcar os dados como "Descontinuados" para dar suporte à lógica do aplicativo para evitar o processamento de dados financeiros associados.
- A [Segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite aos usuários definir políticas para restringir o acesso aos dados para interromper o processamento.
- A [Máscara de Dados Dinâmicos do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos não privilegiados. Ela pode descobrir automaticamente dados possivelmente confidenciais e sugerir as máscaras apropriadas a serem aplicadas. A Máscara de Dados Dinâmicos ajuda a reduzir o acesso para que os dados confidenciais não saiam do banco de dados por meio de acesso não autorizado. *Os clientes são responsáveis por ajustar as configurações para seguir seu próprio esquema de banco de dados.*

### <a name="identity-management"></a>Gerenciamento de identidades
As seguintes tecnologias oferecem funcionalidades para gerenciar o acesso a dados no ambiente do Azure:
-   O [Azure AD](https://azure.microsoft.com/services/active-directory/) é o serviço multilocatário de gerenciamento de identidade e de diretório baseado em nuvem da Microsoft. Todos os usuários dessa solução são criados no Azure AD e incluem os usuários que acessam o Banco de Dados SQL.
-   A autenticação para o aplicativo é executada por meio do Azure AD. Para obter mais informações, confira como [integrar aplicativos ao Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). A criptografia de coluna do banco de dados também usa o Azure AD para autenticar o aplicativo no Banco de Dados SQL. Para obter mais informações, veja como [proteger dados confidenciais no Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   O [RBAC do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) pode ser usado por administradores para definir permissões de acesso refinadas. Com ele, eles podem conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos. Em vez de conceder a todos os usuários acesso irrestrito aos recursos do Azure, os administradores podem permitir apenas determinadas ações para acessar recursos e dados. O acesso à assinatura é limitado ao administrador da assinatura.
- O [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) pode ser usado pelos clientes para minimizar o número de usuários com acesso a determinadas informações, como dados. Os administradores podem usar o Azure AD Privileged Identity Management para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor o acesso administrativo Just-In-Time sob demanda, quando necessário.
- O [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as identidades da organização. Ele configura respostas automatizadas para detectar ações suspeitas relacionadas às identidades da organização. Ele também investiga incidentes suspeitos para tomar as devidas providências para resolvê-los.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: A solução usa o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Key Vault ajuda a proteger chaves de criptografia e segredos usados por aplicativos e serviços de nuvem. As seguintes funcionalidades do Key Vault ajudam os clientes a proteger os dados:
- Políticas de acesso avançadas são configuradas com base na necessidade.
- As políticas de acesso do Key Vault são definidas com o mínimo de permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault têm datas de validade.
- Todas as chaves no Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma chave RSA de 2048 bits protegida pelo módulo de segurança de hardware.
- Todos os usuários e identidades recebem as permissões mínimas necessárias quando o RBAC é usado.
- Os Logs de diagnóstico para Key Vault são habilitados com um período de retenção de pelo menos 365 dias.
- As operações criptográficas permitidas para chaves são restritas às necessárias.

**Gerenciamento de patches**: VMs do Windows implantadas como parte dessa arquitetura de referência são configuradas por padrão para receberem atualizações automáticas do serviço do Windows Update. Essa solução também inclui o serviço [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) por meio do qual as implantações atualizadas podem ser criadas para aplicar patch em VMs quando necessário.

**Proteção contra malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para VMs fornece funcionalidade de proteção em tempo real que ajuda a identifica e remove vírus, spyware e outros softwares mal-intencionados. Os clientes podem configurar alertas que são gerados quando software mal-intencionado ou indesejado tenta se instalar ou executar em VMs protegidas.

**Central de Segurança do Azure**: Com a [Central de Segurança](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem aplicar e gerenciar políticas de segurança em cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques de forma centralizada. A Central de Segurança também acessa as configurações existentes dos serviços do Azure para fornecer recomendações de configuração e de serviço para ajudar a melhorar a situação de segurança e proteger os dados.

A Central de Segurança usa uma variedade de funcionalidades de detecção para alertar os clientes sobre possíveis ataques direcionados a seus ambientes. Esses alertas contêm informações valiosas sobre o que disparou o alerta, os recursos de destino e a origem do ataque. A Central de Segurança tem um conjunto de [alertas de segurança predefinidos](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), que é disparado em caso de ameaça ou de atividade suspeita. Os clientes podem usar as [regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) para definir novos alertas de segurança com base nos dados que já foram coletados do ambiente.

A Central de Segurança fornece alertas e incidentes de segurança priorizados. A Central de Segurança facilita a descoberta e a resolução de possíveis problemas de segurança para os clientes. Um [relatório de inteligência de ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada. As equipes de resposta a incidentes podem usar os relatórios ao investigarem e corrigirem ameaças.

Essa arquitetura de referência também usa a funcionalidade de [avaliação de vulnerabilidade](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) na Central de Segurança. Depois de configurado, um agente parceiro (por exemplo, Qualys) reporta dados de vulnerabilidade para a plataforma de gerenciamento do parceiro. por sua vez, fornecerá dados de monitoramento de vulnerabilidade e integridade de volta para a Central de Segurança. Os clientes podem usar essas informações para identificar rapidamente as VMs vulneráveis.

### <a name="business-continuity"></a>Continuidade dos negócios
**Alta disponibilidade**: Cargas de trabalho de servidor são agrupadas em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a garantir a alta disponibilidade de máquinas virtuais no Azure. Pelo menos uma VM fica disponível durante um evento de manutenção planejada ou não planejada, atendendo ao SLA do Azure de 99,95%.

**Cofre dos serviços de recuperação**: O [cofre dos Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda os dados de backup e protege todas as configurações de VMs nesta arquitetura. Com um Cofre dos Serviços de Recuperação, os clientes podem restaurar arquivos e pastas de uma VM de IaaS sem restaurar a VM inteira. Esse processo acelera o tempo de restauração.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

Os serviços do Azure registram em log de forma extensiva as atividades do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividades**: Os [logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem insights sobre as operações executadas em recursos em uma assinatura. Os logs de atividade podem ajudar a determinar o iniciador, o horário da ocorrência e o status de uma operação.
- **Logs de diagnóstico**: Os [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registros emitidos por todos os recursos. Esses logs são logs do sistema de eventos do Windows, logs de armazenamento, logs de auditoria do Key Vault e logs de acesso e firewall do Gateway de Aplicativo do Azure. Todos os logs de diagnóstico são gravados em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. Os usuários podem configurar um período de retenção de até 730 dias para atender às suas necessidades específicas.

**Logs do Azure Monitor**: Esses logs são consolidados [registra em log do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e emissão de relatórios do painel. Depois que os dados são coletados, eles são organizados em tabelas separadas para cada tipo de dados nos espaços de trabalho do Log Analytics. Dessa forma, todos os dados podem ser analisados juntos, independentemente de sua fonte original. A Central de segurança se integra com os logs do Azure Monitor. Os clientes podem usar consultas Kusto para acessar seus dados de evento de segurança e combiná-lo com dados de outros serviços.

O Azure a seguir [soluções de monitoramento](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) são incluídos como parte dessa arquitetura:
-   [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de Verificação de Integridade do Active Directory avalia o risco e a integridade dos ambientes de servidor em um intervalo regular. Ela fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de Verificação de Integridade do SQL avalia o risco e a integridade dos ambientes do servidor em um intervalo regular. Ela fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do Agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de Integridade do Agente informa quantos agentes estão implantados e sua distribuição geográfica. Ela também relata quantos agentes não estão respondendo e o número de agentes que enviam dados operacionais.
-   [Análise do Log de Atividades](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução de Análise do Log de Atividades auxilia na análise dos logs de atividades do Azure em todas as assinaturas do Azure para um cliente.

**Automação do Azure**: A [Automação](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nessa solução, os runbooks ajudam a coletar logs do Banco de Dados SQL. Os clientes podem usar a solução de [Controle de Alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking) da Automação para identificar facilmente as alterações no ambiente.

**Azure Monitor**: O [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências. As organizações podem usá-lo para auditar, criar alertas e arquivar dados. Elas também podem acompanhar as chamadas à API em seus recursos do Azure.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados dessa arquitetura de referência está disponível para [download](https://aka.ms/nist171-dw-tm) ou pode ser encontrado aqui. Esse modelo pode ajudar os clientes a entenderem os possíveis pontos de risco na infraestrutura do sistema ao fazer modificações.

![Data Warehouse para modelo de risco do NIST SP 800-171](images/nist171-dw-threat-model.png "Data Warehouse para modelo de risco do NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentação de conformidade
O [Blueprint de segurança e conformidade do Azure – Matriz de responsabilidade do cliente para NIST SP 800-171](https://aka.ms/nist171-crm) lista todos os controles de segurança exigidos pelo NIST SP 800-171. Essa matriz detalha se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou compartilhada entre os dois.

O [Blueprint de Segurança e Conformidade do Azure – Matriz de Implementação de Controle do Data Warehouse do NIST SP 800-171](https://aka.ms/nist171-dw-cim) fornece informações sobre quais controles do NIST SP 800-171 são cobertos pela arquitetura do data warehouse. Ele inclui descrições detalhadas de como a implementação atende aos requisitos de cada controle coberto.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel VPN seguro ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa ser configurado para estabelecer uma conexão de forma segura com os recursos implantados como parte dessa arquitetura de referência de data warehouse. Configurando adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Implementando um túnel de VPN seguro com o Azure, é possível criar uma conexão privada virtual entre uma rede local e uma rede virtual do Azure. Essa conexão ocorre pela Internet. Os clientes podem usar essa conexão para enviar informações por “túnel” com segurança usando um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. O [modo de túnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego do túnel de VPN passa pela Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O ExpressRoute é um link de WAN dedicado entre o Azure e uma localização local ou um provedor de hospedagem do Exchange. As conexões do ExpressRoute conectam-se diretamente ao provedor de telecomunicações do cliente. Como resultado, os dados não passam pela Internet e não são expostos a ela. Essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências mais baixas e segurança maior do que as conexões típicas.

Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) práticas recomendadas para a implementação de uma rede híbrida segura que estende uma rede local para o Azure.

### <a name="extract-transform-load-process"></a>Processo de extração, transformação e carregamento
O [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) pode carregar dados no SQL Data Warehouse sem a necessidade de uma ferramenta separada de ETL ou importação. O PolyBase permite acessar dados por meio de consultas T-SQL. A pilha de análise de business intelligence da Microsoft e ferramentas de terceiros compatíveis com o SQL Server podem ser usadas com o PolyBase.

### <a name="azure-ad-setup"></a>Configuração do Azure AD
O [Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é essencial para gerenciar a implantação e provisionar acesso para pessoas que interagem com o ambiente. Um Active Directory local pode ser integrado ao Azure AD em [quatro cliques](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Os clientes também podem vincular a infraestrutura do Active Directory implantada (controladores de domínio) para o Azure AD. Para fazer isso, torne a infraestrutura do Active Directory implantada em um subdomínio de uma floresta do Azure AD.

### <a name="optional-services"></a>Serviços opcionais
O Azure oferece uma variedade de serviços para ajudar com o armazenamento e a preparação de dados formatados e não formatados. Os serviços a seguir podem ser adicionados a esta arquitetura de referência com base nos requisitos do cliente:
-   O [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) é um serviço de nuvem gerenciado criado para projetos híbridos complexos de ETL (extrair, transformar e carregar) e de integração de dados. O Data Factory tem recursos para ajudar a rastrear e localizar os dados. Ferramentas de monitoramento e visualização identificam quando os dados chegam e de onde eles vieram. Os clientes podem criar e agendar fluxos de trabalhos orientados a dados, chamados pipelines, que ingerem dados provenientes de armazenamentos de dados diferentes. Eles podem usar os pipelines para ingerir dados de fontes internas e externas. Em seguida, os clientes podem processar e transformar os dados para saída em armazenamentos de dados, como o SQL Data Warehouse.
- Os clientes podem arranjar os dados não estruturados no [Azure Data Lake Storage](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) para capturar dados com qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análises operacionais e exploratórias. O Azure Data Lake tem recursos que permitem a extração e a conversão de dados. O Data Lake Storage é compatível com a maioria dos componentes de software livre no ecossistema do Hadoop. Ele também se integra perfeitamente com outros serviços do Azure, como o SQL Data Warehouse.

## <a name="disclaimer"></a>Isenção de responsabilidade

 - Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de consulta interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.
 - Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
 - Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
