---
title: Blueprint de Segurança e Conformidade do Azure – Análise para FedRAMP
description: Blueprint de Segurança e Conformidade do Azure – Análise para FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: fa10ff14bf893c268d6b6b1a0d181d11a3f27dc4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586249"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Blueprint de Segurança e Conformidade do Azure: Análise para FedRAMP

## <a name="overview"></a>Visão geral

O [FedRAMP (Federal Risk and Authorization Management Program)](https://www.fedramp.gov/) é um programa do governo dos Estados Unidos que fornece uma abordagem padronizada para a avaliação, a autorização e o monitoramento contínuo de segurança para produtos e serviços de nuvem. Este Blueprint de Segurança e Conformidade do Azure traz diretrizes de como fornecer uma arquitetura de análise do Microsoft Azure que ajuda a implementar um subconjunto de controles da linha de base FedRAMP High. Essa solução fornece diretrizes sobre a implantação e a configuração de recursos do Azure para uma arquitetura de referência comum, demonstrando maneiras pelas quais os clientes podem atender a requisitos específicos de segurança e conformidade, e serve como uma base para os clientes criarem e configurarem suas próprias soluções de análise no Azure.

Essa arquitetura de referência, os guias de implementação de controle associados e os modelos de risco são destinados a servir como uma base que os clientes devem ajustar a seus requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção. Implantar um aplicativo nesse ambiente sem modificações é insuficiente para atender completamente aos requisitos da linha de base FedRAMP High. Observe o seguinte:
- A arquitetura fornece uma linha de base para ajudar os clientes a implantarem cargas de trabalho no Azure de forma compatível com o FedRAMP.
- Os clientes são responsáveis por realizar as devidas avaliações de segurança e conformidade de qualquer solução criada usando essa arquitetura, uma vez que os requisitos podem variar com base nas particularidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura

Esta solução fornece uma plataforma de análise na qual os clientes podem criar suas próprias ferramentas de análise. A arquitetura de referência descreve um caso de uso genérico em que os clientes inserem dados por meio de importações em massa de dados pelo Administrador de dados/SQL ou por meio de atualizações de dados operacionais de um Usuário operacional. Os dois fluxos de trabalho incorporam o [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) para importar dados para o Banco de Dados SQL. O Azure Functions precisa ser configurado pelo cliente por meio do portal do Azure para lidar com as tarefas de importação exclusivas aos requisitos de análise de cada cliente.

O Microsoft Azure oferece uma variedade de serviços de relatórios e de análise para o cliente. No entanto, esta solução incorpora o Azure Analysis Services em conjunto com o Banco de Dados SQL do Azure para procurar dados rapidamente e fornecer resultados com maior velocidade usando uma modelagem mais inteligente dos dados do cliente. O Azure Analytics Services é uma forma de aprendizado de máquina cuja finalidade é aumentar a velocidade de consulta descobrindo novas relações entre conjuntos de dados. Após os dados terem sido treinados usando várias funções estatísticas, até sete pools de consulta adicionais (total de oito incluindo o servidor do cliente) podem ser sincronizados com os mesmos modelos de tabela para distribuir a carga de trabalho de consulta e reduzir os tempos de resposta.

Para criação de relatórios e análise avançada, Bancos de Dados SQL podem ser configurados com índices columnstore. O Azure Analytics Services e os Bancos de Dados SQL podem ser escalados ou reduzidos verticalmente ou desligados completamente em resposta ao uso do cliente. Todo o tráfego SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como melhor prática, o Azure recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.

Após serem carregados no Banco de Dados SQL do Azure e treinados pelo Azure Analysis Services, os dados são ingeridos pelo Usuário operacional e pelo Administrador de dados/SQL com o Power BI. O Power BI exibe dados intuitivamente e reúne informações em vários conjuntos de dados para gerar insights mais amplos. Seu alto nível de adaptabilidade e fácil integração com o Banco de Dados SQL do Azure garantem que os clientes possam configurá-lo para lidar com uma ampla gama de cenários, conforme exigido por suas necessidades de negócio.

Toda a solução é baseada em um Armazenamento do Azure que os clientes da conta configuram no portal do Azure. O Armazenamento do Azure criptografa todos os dados com a Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso.  O GRS (armazenamento com redundância geográfica) garante que eventos adversos no data center primário do cliente não causem perda de dados, pois uma segunda cópia será armazenada em um local separado a centenas de quilômetros de distância.

Para maior segurança, essa arquitetura gerencia recursos usando o Azure Active Directory e o Azure Key Vault. A integridade do sistema é monitorada por meio do Azure Monitor. Os clientes configuram os dois serviços de monitoramento para capturar logs e exibir a integridade do sistema em um único painel facilmente navegável.

O Banco de Dados SQL do Azure normalmente é gerenciado por meio do SSMS (SQL Server Management Studio), que é executado de um computador local configurado para acessar o Banco de Dados SQL do Azure usando uma conexão segura do ExpressRoute ou VPN. **O Azure recomenda configurar uma conexão VPN ou do Azure ExpressRoute para gerenciamento e importação de dados para o grupo de recursos da arquitetura de referência.**

![Diagrama de arquitetura de referência de análise para FedRAMP](images/fedramp-analytics-reference-architecture.png?raw=true "Diagrama de arquitetura de referência de análise para FedRAMP")

### <a name="roles"></a>Funções
O blueprint de análise descreve um cenário com três tipos de uso gerais: o Usuário operacional, o Administrador de dados/SQL e o Engenheiro de sistemas. O RBAC (controle de acesso baseado em função) do Azure permite implementar o gerenciamento de acesso preciso usando funções personalizadas internas. Há recursos disponíveis para configurar o [Controle de acesso baseado em função](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) e definir e implementar [funções predefinidas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

#### <a name="systems-engineer"></a>Engenheiro de sistemas
O Engenheiro de sistemas tem a assinatura do cliente do Azure e configura a implantação da solução por meio do portal do Azure.

#### <a name="sqldata-administrator"></a>Administrador de dados/SQL
O Administrador de dados/SQL estabelece a função de importação de dados em massa e a função de atualização de dados operacionais para carregamento no Banco de Dados SQL do Azure. O Administrador de dados/SQL não é responsável por atualizações de dados operacionais no banco de dados, mas poderá exibir os dados por meio do Power BI.

#### <a name="operational-user"></a>Usuário operacional
O Usuário operacional atualiza os dados regularmente e é responsável pela geração de dados diária. O Usuário operacional também interpreta os resultados por meio do Power BI.

### <a name="azure-services"></a>Serviços do Azure

A solução usa os serviços do Azure a seguir. Há detalhes da arquitetura de implantação na seção [Arquitetura de implantação](#deployment-architecture).
- Funções do Azure
- Banco de Dados SQL do Azure
- Azure Analysis Services
- Azure Active Directory
- Cofre da Chave do Azure
- O Azure Monitor (logs)
- Armazenamento do Azure
- ExpressRoute/Gateway de VPN
- Painel do Power BI

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir fornece detalhes sobre os elementos de desenvolvimento e implementação.

![texto Alt](images/fedramp-analytics-components.png?raw=true "Diagrama de componentes de análise para FedRAMP")

**Azure Functions**: [O Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) são soluções para pequenas partes de código em execução na nuvem por meio da maioria das linguagens de programação. As funções nesta solução são integradas com o Armazenamento do Azure para efetuar pull automaticamente dos dados do cliente para a nuvem, facilitando a integração com outros serviços do Azure. As funções podem ser dimensionadas facilmente e geram custos apenas quando executadas.

**Serviço de análise do Azure**: [Serviço de análise do Azure](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) fornece modelagem de dados empresariais e integração com serviços de plataforma de dados do Azure. O Azure Analysis Services acelera a navegação em grandes quantidades de dados combinando dados de várias fontes em um único modelo de dados.

**Power BI**: [Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) fornece análise e funcionalidade de relatórios para clientes tentando puxar os conhecimentos sobre fora de seus esforços de processamento de dados.

### <a name="networking"></a>Rede
**Grupos de segurança de rede**: [Os NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) são configurados para gerenciar o tráfego direcionado aos recursos implantados e serviços. Os Grupos de Segurança de Rede são definidos em um esquema de negar por padrão e só permitem o tráfego contido na ACL (lista de controle de acesso) pré-configurada.

Cada NSG tem portas e protocolos específicos abertos para que a solução possa funcionar segura e corretamente. Além disso, as seguintes configurações estão habilitadas para cada NSG:
  - [Eventos e logs de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
  - [Os logs do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) está conectado a logs de diagnóstico do NSG.

### <a name="data-at-rest"></a>Dados em repouso
A arquitetura protege dados em repouso usando criptografia, auditoria de banco de dados e outras medidas.

**Replicação de dados** O Azure Governamental tem duas opções de [replicação de dados](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - A configuração padrão para replicação de dados é o **GRS (armazenamento com redundância geográfica)**, que armazena de forma assíncrona os dados do cliente em um data center separado fora da região primária. Isso garante a recuperação de dados em caso de perda total no data center primário.
 - Como alternativa, o **LRS (armazenamento com redundância local)** também pode ser configurado por meio da Conta de Armazenamento do Azure. O LRS replica os dados dentro de uma unidade de escala de armazenamento, que é hospedada na mesma região em que o cliente cria sua conta. Todos os dados são replicados simultaneamente, garantindo que nenhum dado de backup seja perdido em uma falha da unidade de escala de armazenamento principal.

**Armazenamento do Azure** Para atender aos requisitos de dados em repouso criptografados, todos os serviços implantados nessa da arquitetura de referência usam o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/), que armazena dados com a [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption).

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

### <a name="logging-and-audit"></a>Registro em log e auditoria
O [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) gera uma exibição completa dos dados de monitoramento, incluindo logs de atividade, métricas e dados de diagnóstico, permitindo que os clientes criem uma visão completa da integridade do sistema.  
[Os logs do Azure Monitor](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) fornece registro em log extensivo de atividade do sistema e usuário, bem como a integridade do sistema. Ele coleta e analisa dados gerados por recursos no Azure e em ambientes locais.
- **Logs de Atividades**: Os [logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem insights sobre as operações executadas em recursos em uma assinatura.
- **Logs de Diagnóstico**: Os [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registros emitidos por todos os recursos. Eles incluem logs do sistema de eventos do Windows e logs de fila, de tabelas e do Armazenamento de Blobs do Azure.
- **Logs de Firewall**: O Gateway de Aplicativo fornece logs de diagnóstico e de acesso completos. Os logs de firewall estão disponíveis para recursos do Gateway de Aplicativo com o WAF habilitado.
- **Arquivamento de Log**: Todos os logs de diagnóstico são gravados em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento, com um período de retenção definido de 2 dias. Esses logs conectem-se aos logs do Azure Monitor para processar, armazenar e relatórios de painel.

Além disso, as seguintes soluções de monitoramento são incluídas como parte dessa arquitetura:
-   [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): A solução de Automação do Azure armazena, executa e gerencia runbooks.
-   [Segurança e Auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): O painel de Segurança e Auditoria fornece insights de alto nível sobre o estado de segurança dos recursos, fornecendo métricas sobre domínios de segurança, problemas importantes, detecções, inteligência contra ameaças e consultas de segurança comuns.
-   [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de Verificação de Integridade do SQL avalia o risco e a integridade dos ambientes do servidor em intervalos regulares e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Logs de Atividades do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução de Análise do Log de Atividades auxilia na análise dos logs de atividades do Azure em todas as assinaturas do Azure para um cliente.

### <a name="identity-management"></a>Gerenciamento de identidades
-   A autenticação para o aplicativo é executada usando o Azure AD. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a criptografia de coluna do banco de dados usa o Azure AD para autenticar o aplicativo no Banco de Dados SQL do Azure. Para obter mais informações, veja como [proteger dados confidenciais no Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   O [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as identidades da organização, configura as respostas automatizadas para detectar ações suspeitas relacionadas a essas identidades e investiga incidentes suspeitos e toma a medida apropriada para resolvê-los.
-   O [RBAC (Controle de Acesso Baseado em Função) do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite o gerenciamento de acesso detalhado no Azure. O acesso à assinatura é limitado ao administrador da assinatura.

Para saber mais sobre como usar os recursos de segurança do Banco de Dados SQL do Azure, confira o exemplo [Aplicativo de demonstração da Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample) exemplo.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: A solução usa o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="expressroute-and-vpn"></a>ExpressRoute e VPN
O [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou um túnel VPN seguro precisa ser configurado para estabelecer uma conexão de forma segura com os recursos implantados como parte dessa arquitetura de referência de análise de dados. Como as conexões do ExpressRoute não ocorrem pela Internet, elas oferecem mais confiabilidade, mais velocidade, latências mais baixas e maior segurança que as conexões típicas pela Internet. Configurando adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

### <a name="azure-active-directory-setup"></a>Configuração do Azure Active Directory
O [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é essencial para gerenciar a implantação e provisionar acesso para pessoas que interagem com o ambiente. Um Active Directory existente do Windows Server pode ser integrado ao AAD em [quatro cliques](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Os clientes também podem associar a infraestrutura (controladores de domínio) do Active Directory implantado a um AAD existente fazendo da infraestrutura do Active Directory implantado um subdomínio de uma floresta do AAD.

### <a name="additional-services"></a>Serviços adicionais
#### <a name="iaas---vm-considerations"></a>IaaS – considerações sobre VM
Esta solução de PaaS não incorpora nenhuma VM de IaaS do Azure. Um cliente pode criar uma VM do Azure para executar muitos desses serviços de PaaS. Nesse caso, recursos específicos e serviços para continuidade dos negócios e os logs do Azure Monitor poderia ser aproveitados:

##### <a name="business-continuity"></a>Continuidade dos negócios
- **Alta disponibilidade**: Cargas de trabalho de servidor são agrupadas em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a garantir a alta disponibilidade de máquinas virtuais no Azure. Pelo menos uma máquina virtual fica disponível durante um evento de manutenção planejada ou não planejada, atendendo ao SLA do Azure de 99,95%.

- **Cofre dos Serviços de Recuperação**: o [Cofre dos Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) armazena dados de backup e protege todas as configurações das Máquinas Virtuais do Azure nesta arquitetura. Com um Cofre dos Serviços de Recuperação, os clientes podem restaurar arquivos e pastas de uma VM de IaaS sem restaurar a VM inteira, acelerando o tempo de restauração.

##### <a name="monitoring-solutions"></a>Soluções de monitoramento
-   [Avaliação do AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de Verificação de Integridade do Active Directory avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Avaliação de Antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A solução de Antimalware fornece informações sobre malware, ameaças e status de proteção.
-   [Gerenciamento de Atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): A solução de Gerenciamento de Atualizações permite que os clientes gerenciem as atualizações de segurança do sistema operacional, incluindo o status das atualizações disponíveis e o processo de instalação das atualizações necessárias.
-   [Integridade do Agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de Integridade do Agente informa quantos agentes são implantados e sua distribuição geográfica, além de quantos agentes sem resposta e o número de agentes que estão enviando dados operacionais.
-   [Controle de Alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking): A solução de Controle de Alterações permite que os clientes facilmente identifiquem alterações no ambiente.

##### <a name="security"></a>Segurança
- **Proteção contra malware**: O [antimalware da Microsoft](https://docs.microsoft.com/azure/security/azure-security-antimalware) para Máquinas Virtuais oferece uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados, com alertas configuráveis quando se sabe que software mal-intencionado ou indesejado tenta se instalar ou executar em máquinas virtuais protegidas.
- **Gerenciamento de patches**: Máquinas Virtuais do Windows implantadas como parte dessa arquitetura de referência são configuradas por padrão para receber atualizações automáticas do serviço Windows Update. Essa solução também inclui o serviço [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) por meio do qual as implantações atualizadas podem ser criadas para aplicar patch em máquinas virtuais quando necessário.

#### <a name="azure-commercial"></a>Azure Commercial
Embora essa arquitetura de análise de dados não seja destinada a implantação no ambiente do [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/), é possível chegar a objetivos semelhantes usando os serviços descritos nela, bem como serviços adicionais disponíveis no ambiente do Azure Commercial. Observe que o Azure Comercial mantém o FedRAMP JAB P-ATO no Nível de Impacto Moderado, permitindo que agências governamentais e parceiros implantem informações moderadamente confidenciais na nuvem usando o ambiente do Azure Commercial.

O Azure Commercial oferece uma ampla variedade de serviços de análise para gerar insights de grandes quantidades de dados:
-   O [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), um componente do [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), desenvolve um modelo de análise preditiva usando uma ou mais fontes de dados. Funções estatísticas são usadas em várias iterações para gerar um modelo eficaz que aplicativos como o Power BI podem consumir.
-   O [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) permite capturar dados com qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análises operacionais e exploratórias. O Azure Data Lake Store é compatível com a maioria dos componentes de software livre no ecossistema do Hadoop e se integra bem com outros serviços do Azure.

## <a name="threat-model"></a>Modelo de ameaça

O DFD (diagrama de fluxo de dados) dessa arquitetura de referência está disponível para [download](https://aka.ms/blueprintanalyticsthreatmodel) ou pode ser encontrado abaixo:

## <a name="compliance-documentation"></a>Documentação de conformidade
O [Blueprint de Segurança e Conformidade do Azure – Matriz de Responsabilidades do Cliente FedRAMP High](https://aka.ms/blueprinthighcrm) lista todos os controles de segurança necessários para a linha de base FedRAMP High. De forma semelhante, o [Blueprint de Segurança e Conformidade do Azure – Matriz de Responsabilidades do Cliente FedRAMP Moderate](https://aka.ms/blueprintanalyticscimmod) lista todos os controles de segurança necessários para a linha de base FedRAMP Moderate. Os dois documentos informam se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou é compartilhada pelos dois.

O [Blueprint de Segurança e Conformidade do Azure – Matriz de Implementação de Controle de FedRAMP High](https://aka.ms/blueprintanalyticscimhigh) e o [Blueprint de Segurança e Conformidade do Azure – Matriz de Implementação de Controle de FedRAMP Moderate](https://aka.ms/blueprintanalyticscimmod) fornecem informações sobre quais controles são cobertos pela arquitetura de análise para cada linha de base do FedRAMP, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada controle coberto.

## <a name="disclaimer"></a>Isenção de responsabilidade

 - Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de consulta interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.
 - Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
 - Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
