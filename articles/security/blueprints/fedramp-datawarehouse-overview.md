---
title: Blueprint de Segurança e Conformidade do Azure – Data Warehouse para Automação do FedRAMP
description: Blueprint de Segurança e Conformidade do Azure – Data Warehouse para Automação do FedRAMP
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 3c78aed2f30ea85f5bc16a8c0fb270bb1c761be8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585994"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Blueprint de Segurança e Conformidade do Azure: Data Warehouse para a automação de FedRAMP

## <a name="overview"></a>Visão geral

O [FedRAMP (Federal Risk and Authorization Management Program)](https://www.fedramp.gov/) é um programa do governo dos Estados Unidos que fornece uma abordagem padronizada para a avaliação, a autorização e o monitoramento contínuo de segurança para produtos e serviços de nuvem. Este Blueprint de Segurança e Conformidade do Azure fornece diretrizes para fornecer uma arquitetura de data warehouse do Microsoft Azure que ajuda a implementar um subconjunto de controles da linha de base FedRAMP High. Esta solução fornece diretrizes sobre a implantação e a configuração de recursos do Azure para uma arquitetura de referência comum, demonstrando maneiras pelas quais os clientes podem atender a requisitos específicos de segurança e conformidade, e serve como uma base para os clientes criarem e configurarem suas próprias soluções de data warehouse no Azure.

Essa arquitetura de referência, os guias de implementação de controle associados e os modelos de risco são destinados a servir como uma base que os clientes devem ajustar a seus requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção. Implantar um aplicativo nesse ambiente sem modificações é insuficiente para atender completamente aos requisitos da linha de base FedRAMP High. Observe o seguinte:
- A arquitetura fornece uma linha de base para ajudar os clientes a implantarem cargas de trabalho no Azure de forma compatível com o FedRAMP.
- Os clientes são responsáveis por realizar as devidas avaliações de segurança e conformidade de qualquer solução criada usando essa arquitetura, uma vez que os requisitos podem variar com base nas particularidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura

Essa solução fornece uma arquitetura de referência de data warehouse que implementa um data warehouse de nuvem seguro e de alto desempenho. Há duas camadas de dados separadas na arquitetura: uma em que os dados são importados, armazenados e preparados dentro de um ambiente de SQL clusterizado e outra para o SQL Data Warehouse do Azure em que os dados são carregados usando uma ferramenta de ETL (por exemplo, consultas T-SQL do [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)) para processamento. Após os dados serem armazenados no SQL Data Warehouse do Azure, você poderá executar análises em grande escala.

O Microsoft Azure oferece uma variedade de serviços de relatórios e análises para o cliente. Essa solução inclui o SSRS (SQL Server Reporting Services) para a criação rápida de relatórios do SQL Data Warehouse do Azure. Todo o tráfego SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como melhor prática, o Azure recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.

No SQL Data Warehouse do Azure, os dados são armazenados em tabelas relacionais com armazenamento em colunas, um formato que reduz significativamente os custos de armazenamento de dados enquanto melhora o desempenho das consultas.  Dependendo dos requisitos de uso, os recursos de computação do SQL Data Warehouse do Azure poderão ser escalados ou reduzidos verticalmente ou desligados completamente se não houver processos ativos que demandem recursos de computação.

Um balanceador de carga SQL gerencia o tráfego SQL, garantindo o alto desempenho. Todas as máquinas virtuais nesta arquitetura de referência são implantadas em um conjunto de disponibilidade com instâncias do SQL Server configuradas em um grupo de disponibilidade AlwaysOn para funcionalidades de alta disponibilidade e de recuperação de desastre.

Esta arquitetura de referência de data warehouse também inclui uma camada do AD (Active Directory) para o gerenciamento de recursos dentro da arquitetura. A sub-rede do Active Directory permite a fácil adoção em uma estrutura de floresta do AD maior, permitindo a operação contínua do ambiente mesmo quando o acesso à floresta maior não está disponível. Todas as máquinas virtuais são ingressadas no domínio na camada do Active Directory e usam as políticas de grupo do Active Directory para impor configurações de conformidade e segurança no nível do sistema operacional.

Uma máquina virtual atua como host bastião de gerenciamento, fornecendo uma conexão segura para os administradores acessarem recursos implantados. Os dados são carregados nessa área de preparo por meio do host bastião de gerenciamento. **O Azure recomenda configurar uma conexão VPN ou do Azure ExpressRoute para gerenciamento e importação de dados para a sub-rede da arquitetura de referência.**

![Diagrama de arquitetura de referência de Data Warehouse para FedRAMP](images/fedramp-datawarehouse-architecture.png?raw=true "Diagrama de arquitetura de referência de Data Warehouse para FedRAMP")

A solução usa os serviços do Azure a seguir. Há detalhes da arquitetura de implantação na seção [Arquitetura de implantação](#deployment-architecture).

Máquinas Virtuais do Azure
-   (1) Host bastião
-   (2) Controlador de domínio do Active Directory
-   (2) Nó de cluster do SQL Server
-   (1) Testemunha do SQL Server

Conjuntos de Disponibilidade
-   (1) controladores de domínio do Active Directory
-   (1) testemunha e nós de cluster SQL

Rede Virtual
-   (4) Sub-redes
-   (4) Grupos de Segurança de Rede

SQL Data Warehouse

SQL Server Reporting Services

Balanceador de carga SQL do Azure

Azure Active Directory

Cofre dos Serviços de Recuperação

Cofre da Chave do Azure

Logs do Azure Monitor

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir fornece detalhes sobre os elementos de desenvolvimento e implementação.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) é um Enterprise Data Warehouse (EDW) que aproveita o MPP processamento paralelo () para executar rapidamente consultas complexas em petabytes de dados. Importe Big Data para o SQL Data Warehouse com consultas T-SQL simples do PolyBase e use o poder do MPP para executar análises de alto desempenho.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) permite a rápida criação de relatórios com tabelas, gráficos, mapas, medidores, matrizes e mais para o Azure SQL Data Warehouse.

**Host bastião**: O host bastião é o único ponto de entrada que permite que os usuários acessem os recursos implantados nesse ambiente. O host bastião fornece uma conexão segura com os recursos implantados, permitindo apenas tráfego remoto de endereços IP públicos em uma lista de segurança. Para permitir o tráfego RDP (área de trabalho remota), a origem do tráfego precisa ser definida no NSG (Grupo de Segurança de Rede).

Uma máquina virtual foi criada como um host bastião ingressado em domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [O Azure Monitor registra a extensão](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Extensão de Diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usando o Azure Key Vault (obedece a requisitos do Azure Governamental, do PCI DSS e do HIPAA, entre outros)
-   Uma [política de desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos de máquina virtual quando eles não estiverem em uso
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) habilitado, para que as credenciais e outras informações secretas sejam executadas em um ambiente protegido isolado do sistema operacional em execução

### <a name="virtual-network"></a>Rede virtual
Esta arquitetura de referência define uma rede virtual privada com um espaço de endereço de 10.0.0.0/16.

**Grupos de segurança de rede**: [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm ACLs (Listas de Controle de Acesso) que permitem ou negam o tráfego dentro de uma VNet. Os NSGs podem ser usados para proteger o tráfego em nível individual de VM ou sub-rede. Os seguintes NSGs existem:
  - Um NSG para a camada de dados (Clusters do SQL Server, Testemunha do SQL Server e Balanceador de Carga SQL)
  - Um NSG para o host bastião de gerenciamento
  - Um NSG para o Active Directory
  - Um NSG para o SQL Server Reporting Services

Cada NSG tem portas e protocolos específicos abertos para que a solução possa funcionar segura e corretamente. Além disso, as seguintes configurações estão habilitadas para cada NSG:
  - [Eventos e logs de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
  - Os logs do Azure Monitor está conectado ao [diagnóstico do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**sub-redes**: Cada sub-rede está associada ao seu NSG correspondente.

### <a name="data-at-rest"></a>Dados em repouso
A arquitetura protege dados em repouso usando criptografia, auditoria de banco de dados e outras medidas.

**Armazenamento do Azure** Para atender aos requisitos de criptografia de dados em repouso, todo o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption**
[O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para o sistema operacional e os discos de dados. A solução é integrada ao Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de Dados SQL do Azure** A instância do Banco de Dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:
-   A [autenticação e a autorização do AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades dos usuários de banco de dados e de outros serviços da Microsoft em uma única localização central.
-   A [auditoria do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) controla os eventos de banco de dados e grava-os em um log de auditoria em uma conta de armazenamento do Azure.
-   O Banco de Dados SQL está configurado para usar [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia de dados e arquivos de log em tempo real para proteger as informações em repouso. O TDE fornece a garantia de que os dados armazenados não tenham ficado sujeitos a acesso não autorizado.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem o acesso aos servidores do banco de dados até que as permissões apropriadas sejam concedidas. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.
-   A [Detecção de Ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite detectar e responder a possíveis ameaças conforme elas ocorrem, fornecendo alertas de segurança sobre atividades suspeitas no banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões anormais de acesso ao banco de dados.
-   As [colunas Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que dados confidenciais nunca sejam exibidos como texto não criptografado dentro do sistema do banco de dados. Após a habilitação da criptografia de dados, somente aplicativos cliente ou servidores de aplicativo com acesso às chaves poderão acessar dados de texto não criptografado.
-   A [Máscara de Dados Dinâmicos do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) pode ser usada após a implantação da arquitetura de referência. Os clientes precisarão ajustar as configurações da Máscara de Dados Dinâmicos para seguir o esquema de banco de dados que usam.

### <a name="business-continuity"></a>Continuidade dos negócios
**Alta disponibilidade**: Cargas de trabalho de servidor são agrupadas em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a garantir a alta disponibilidade de máquinas virtuais no Azure. Pelo menos uma máquina virtual fica disponível durante um evento de manutenção planejada ou não planejada, atendendo ao SLA do Azure de 99,95%.

**Cofre dos Serviços de Recuperação**: o [Cofre dos Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) armazena dados de backup e protege todas as configurações das Máquinas Virtuais do Azure nesta arquitetura. Com um Cofre dos Serviços de Recuperação, os clientes podem restaurar arquivos e pastas de uma VM de IaaS sem restaurar a VM inteira, acelerando o tempo de restauração.

### <a name="logging-and-audit"></a>Registro em log e auditoria
[Os logs do Azure Monitor](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) fornece registro em log extensivo de atividade do sistema e usuário, bem como a integridade do sistema. O [registra em log do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) solução coleta e analisa os dados gerados pelos recursos no Azure e ambientes locais.
- **Logs de Atividades**: Os [logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem insights sobre as operações executadas em recursos em uma assinatura.
- **Logs de Diagnóstico**: Os [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registros emitidos por todos os recursos. Eles incluem logs do sistema de eventos do Windows e logs de fila, de tabelas e do Armazenamento de Blobs do Azure.
- **Logs de Firewall**: O Gateway de Aplicativo fornece logs de diagnóstico e de acesso completos. Os logs de firewall estão disponíveis para recursos do Gateway de Aplicativo com o WAF habilitado.
- **Arquivamento de Log**: Todos os logs de diagnóstico são gravados em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento, com um período de retenção definido de 2 dias. Esses logs conectem-se aos logs do Azure Monitor para processar, armazenar e relatórios de painel.

Além disso, as seguintes soluções de monitoramento são incluídas como parte dessa arquitetura:
-   [Avaliação do AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de Verificação de Integridade do Active Directory avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Avaliação de Antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A solução de Antimalware fornece informações sobre malware, ameaças e status de proteção.
-   [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): A solução de Automação do Azure armazena, executa e gerencia runbooks.
-   [Segurança e Auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): O painel de Segurança e Auditoria fornece insights de alto nível sobre o estado de segurança dos recursos, fornecendo métricas sobre domínios de segurança, problemas importantes, detecções, inteligência contra ameaças e consultas de segurança comuns.
-   [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de Verificação de Integridade do SQL avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Gerenciamento de Atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): A solução de Gerenciamento de Atualizações permite que os clientes gerenciem as atualizações de segurança do sistema operacional, incluindo o status das atualizações disponíveis e o processo de instalação das atualizações necessárias.
-   [Integridade do Agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de Integridade do Agente informa quantos agentes são implantados e a distribuição geográfica deles, além de quantos agentes estão sem resposta e o número de agentes que estão enviando dados operacionais.
-   [Logs de Atividades do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução de Análise do Log de Atividades auxilia na análise dos logs de atividades do Azure em todas as assinaturas do Azure para um cliente.
-   [Controle de Alterações](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução de Controle de Alterações permite que os clientes facilmente identifiquem alterações no ambiente.

### <a name="identity-management"></a>Gerenciamento de identidades
As tecnologias a seguir fornecem funcionalidades de gerenciamento de identidades no ambiente do Azure:
-   O [AD (Active Directory)](https://azure.microsoft.com/services/active-directory/) pode ser o serviço de gerenciamento de identidades e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários da solução foram criados no Azure Active Directory, incluindo os usuários que acessam o Banco de Dados SQL.
-   A autenticação para o aplicativo é executada usando o Azure AD. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a criptografia de coluna do banco de dados usa o Azure AD para autenticar o aplicativo no Banco de Dados SQL do Azure. Para obter mais informações, veja como [proteger dados confidenciais no Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   O [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as identidades da organização, configura as respostas automatizadas para detectar ações suspeitas relacionadas a essas identidades e investiga incidentes suspeitos e toma a medida apropriada para resolvê-los.
-   O [RBAC (Controle de Acesso Baseado em Função) do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite o gerenciamento de acesso detalhado no Azure. O acesso à assinatura é limitado ao administrador da assinatura.

Para saber mais sobre como usar os recursos de segurança do Banco de Dados SQL do Azure, confira o exemplo [Aplicativo de demonstração da Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample) exemplo.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: A solução usa o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem.

**Proteção contra malware**: O [antimalware da Microsoft](https://docs.microsoft.com/azure/security/azure-security-antimalware) para Máquinas Virtuais oferece uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados, com alertas configuráveis quando se sabe que software mal-intencionado ou indesejado tenta se instalar ou executar em máquinas virtuais protegidas.

**Gerenciamento de patches**: Máquinas Virtuais do Windows implantadas como parte dessa arquitetura de referência são configuradas por padrão para receber atualizações automáticas do serviço Windows Update. Essa solução também inclui o serviço [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) por meio do qual as implantações atualizadas podem ser criadas para aplicar patch em máquinas virtuais quando necessário.


## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações
### <a name="expressroute-and-vpn"></a>ExpressRoute e VPN
O [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou um túnel VPN seguro precisa ser configurado para estabelecer uma conexão de forma segura com os recursos implantados como parte dessa arquitetura de referência de data warehouse. Como as conexões do ExpressRoute não ocorrem pela Internet, elas oferecem mais confiabilidade, mais velocidade, latências mais baixas e maior segurança que as conexões típicas pela Internet. Configurando adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

### <a name="extract-transform-load-etl-process"></a>Processo ETL (extração, transformação e carregamento)
O [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) pode carregar dados no SQL Data Warehouse do Azure sem a necessidade uma ferramenta separada de ETL ou importação. O PolyBase permite acessar dados por meio de consultas T-SQL. É possível usar a pilha de análise de business intelligence da Microsoft com o PolyBase, bem como ferramentas de terceiros compatíveis com o SQL Server.

### <a name="azure-active-directory-setup"></a>Configuração do Azure Active Directory
O [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é essencial para gerenciar a implantação e provisionar acesso para pessoas que interagem com o ambiente. Um Active Directory existente do Windows Server pode ser integrado ao AAD em [quatro cliques](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Os clientes também podem associar a infraestrutura (controladores de domínio) do Active Directory implantado a um AAD existente fazendo da infraestrutura do Active Directory implantado um subdomínio de uma floresta do AAD.

### <a name="additional-services"></a>Serviços adicionais
Embora essa arquitetura de data warehouse não seja destinada a implantação no ambiente do [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/), é possível chegar a objetivos semelhantes usando os serviços descritos nesta arquitetura de referência, bem como serviços adicionais disponíveis no ambiente do Azure Commercial. Observe que o Azure Comercial mantém o FedRAMP JAB P-ATO no Nível de Impacto Moderado, permitindo que agências governamentais e parceiros implantem informações moderadamente confidenciais na nuvem usando o ambiente do Azure Commercial.

O Azure Commercial oferece uma ampla gama de serviços que lidam com o armazenamento e a preparação de dados formatados e não formatados a serem usados em data warehouse, incluindo:
-   O [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) é um serviço de nuvem gerenciado criado para projetos híbridos complexos de ETL (extração, transformação, carregamento), ELT (extração, carregamento, transformação) e integração de dados. Usando o Azure Data Factory, os clientes podem criar e agendar fluxos de trabalho controlados por dados, chamados de pipelines, que ingerem dados de diferentes armazenamentos de dados. Em seguida, os clientes podem processar e transformar os dados para saída em armazenamentos de dados, como o SQL Data Warehouse do Azure.
-   O [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) permite capturar dados com qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análises operacionais e exploratórias. O Azure Data Lake Store é compatível com a maioria dos componentes de software livre no ecossistema do Hadoop e se integra bem com outros serviços do Azure, como o SQL Data Warehouse do Azure.

## <a name="threat-model"></a>Modelo de ameaça

O DFD (diagrama de fluxo de dados) dessa arquitetura de referência está disponível para [download](https://aka.ms/blueprintdwthreatmodel) ou pode ser encontrado abaixo:

![Modelo de risco de data warehouse para FedRAMP](images/fedramp-datawarehouse-threat-model.png?raw=true "Modelo de risco de data warehouse para FedRAMP")

## <a name="compliance-documentation"></a>Documentação de conformidade
O [Blueprint de Segurança e Conformidade do Azure – Matriz de Responsabilidades do Cliente FedRAMP High](https://aka.ms/blueprinthighcrm) lista todos os controles de segurança necessários para a linha de base FedRAMP High. De forma semelhante, o [Blueprint de Segurança e Conformidade do Azure – Matriz de Responsabilidades do Cliente FedRAMP Moderate](https://aka.ms/blueprintcrmmod) lista todos os controles de segurança necessários para a linha de base FedRAMP Moderate. Os dois documentos informam se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou é compartilhada pelos dois.

O [Blueprint de Segurança e Conformidade do Azure – Matriz de Implementação de Controle de FedRAMP High](https://aka.ms/blueprintdwcimhigh) e o [Blueprint de Segurança e Conformidade do Azure – Matriz de Implementação de Controle de FedRAMP Moderate](https://aka.ms/blueprintdwcimmod) fornecem informações sobre quais controles são cobertos pela arquitetura de data warehouse para cada linha de base do FedRAMP, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada controle coberto.

## <a name="disclaimer"></a>Isenção de responsabilidade

 - Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de consulta interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.
 - Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
 - Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
