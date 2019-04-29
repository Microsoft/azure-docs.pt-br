---
title: Blueprint de segurança e conformidade do Azure – análise de dados para o PCI DSS
description: Blueprint de segurança e conformidade do Azure – análise de dados para o PCI DSS
services: security
author: meladie
ms.assetid: 6c48cd8e-c548-44e8-a21a-17316c152cbb
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 6a2ffef9a1384ac2e2a678404171f33312b5d63a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609552"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-pci-dss"></a>Blueprint de Segurança e Conformidade do Azure: Análise para PCI DSS

## <a name="overview"></a>Visão Geral

Este Blueprint de conformidade e segurança do Azure fornece as diretrizes para a implantação de uma arquitetura de análise de dados no Azure que ajuda com os requisitos do PCI DSS 3.2 (Padrão de Segurança de Dados do Setor de Cartões de Pagamento). Ele demonstra uma arquitetura de referência comum e o tratamento adequado de dados de cartão de crédito (incluindo dados de número do cartão, validade e verificação) em um ambiente de várias camadas seguro e em conformidade. Este blueprint demonstra como os clientes podem atender a requisitos de segurança e conformidade específicos e funciona como uma base para os clientes criarem e configurarem suas próprias soluções de análise de dados no Azure.

Essa arquitetura de referência, o guia de implementação e o modelo de ameaça fornecem uma base para que os clientes fiquem em conformidade com os requisitos do PCI DSS 3.2. Essa solução fornece uma linha de base para ajudar os clientes a implantar cargas de trabalho no Azure em conformidade com o PCI DSS 3.2. No entanto, essa solução não deve ser usada no estado em que se encontra em um ambiente de produção porque uma configuração adicional é necessária.

A consecução da conformidade com PCI DSS exige a certificação de uma solução de cliente de produção por um QSA (Supervisor de Segurança Qualificado). Os clientes são responsáveis por realizar as devidas avaliações de segurança e conformidade de qualquer solução criada usando essa arquitetura, uma vez que os requisitos podem variar com base nas particularidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura

Este Blueprint de conformidade e segurança do Azure fornece uma plataforma de análise na qual os clientes podem criar suas próprias ferramentas de análise. A arquitetura de referência descreve um caso de uso genérico em que os clientes inserem dados por meio de importações em massa de dados pelo Administrador de dados/SQL ou por meio de atualizações de dados operacionais de um Usuário operacional. Os dois fluxos de trabalho incorporam o Azure Functions para importar dados para o Banco de Dados SQL do Azure. O Azure Functions precisa ser configurado pelo cliente por meio do portal do Azure para lidar com as tarefas de importação exclusivas aos requisitos de análise de cada cliente.

O Azure oferece uma variedade de serviços de relatórios e análises para os clientes. Essa solução incorpora os serviços do Azure Machine Learning em conjunto com o Banco de Dados SQL do Azure para navegar rapidamente pelos dados e fornecer resultados mais rápidos por meio de uma modelagem mais inteligente. O Azure Machine Learning aumenta a velocidade das consultas descobrindo novas relações entre os conjuntos de dados. Depois que os dados forem treinados usando várias funções estatísticas, até sete pools de consulta adicionais (total de oito incluindo o servidor do cliente) podem ser sincronizados com os mesmos modelos de tabela para distribuir a carga de trabalho de consulta e reduzir o tempo de resposta.

Para análises e relatórios aprimorados, os bancos de dados SQL do Azure podem ser configurados com índices columnstore. Os bancos de dados do Azure Machine Learning e SQL do Azure podem ser ampliados ou reduzidos ou desligados completamente em resposta ao uso do cliente. Todo o tráfego SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como melhor prática, o Azure recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.

Após serem carregador no Banco de Dados SQL do Azure e treinados pelo Azure Machine Learning, os dados são ingeridos pelo Usuário operacional e pelo Administrador de dados/SQL com o Power BI. O Power BI exibe dados intuitivamente e reúne informações em vários conjuntos de dados para gerar insights mais amplos. Seu alto nível de adaptabilidade e fácil integração com o Banco de Dados SQL do Azure garantem que os clientes possam configurá-lo para lidar com uma ampla gama de cenários, conforme exigido por suas necessidades de negócio.

A solução usa contas de Armazenamento do Azure, que os clientes podem configurar para usar a Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O Azure armazena três cópias dos dados dentro do datacenter selecionado do cliente para garantir a resiliência. O armazenamento com redundância geográfica garante que os dados sejam replicados para um datacenter secundário centenas de milhas de distância e armazenados novamente como três cópias nesse datacenter, impedindo que um evento prejudicial resulte em perda de dados no data center principal do cliente.

Para maior segurança, todos os recursos nessa solução são gerenciados como um grupo de recursos por meio do Azure Resource Manager. O controle de acesso baseado em função do Azure Active Directory é usado para controlar o acesso aos recursos implantados, incluindo as chaves no Azure Key Vault. A integridade do sistema é monitorada por meio da Central de Segurança do Azure e do Azure Monitor. Os clientes configuram os dois serviços de monitoramento para capturar logs e exibir a integridade do sistema em um único painel facilmente navegável.

O Banco de Dados SQL do Azure normalmente é gerenciado por meio do SSMS (SQL Server Management Studio), que é executado de um computador local configurado para acessar o Banco de Dados SQL do Azure usando uma conexão segura do ExpressRoute ou VPN. **A Microsoft recomenda configurar uma conexão VPN ou do ExpressRoute para o gerenciamento e a importação de dados para o grupo de recursos da arquitetura de referência**.

![Diagrama da arquitetura de referência de análise para o PCI DSS](images/pcidss-analytics-architecture.png "Diagrama da arquitetura de referência de análise para o PCI DSS")

A solução usa os serviços do Azure a seguir. Há detalhes da arquitetura de implantação na seção [Arquitetura de implantação](#deployment-architecture).

- Application Insights
- Azure Active Directory
- Catálogo de Dados do Azure
- Azure Disk Encryption
- Grade de Eventos do Azure
- Funções do Azure
- Cofre de Chaves Azure
- Azure Machine Learning
- Azure Monitor
- Central de Segurança do Azure
- Banco de Dados SQL do Azure
- Armazenamento do Azure
- Rede Virtual do Azure
    - (1) /16 rede
    - (2) /24 redes
    - (2) Grupos de segurança de rede
- Painel do Power BI

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir fornece detalhes sobre os elementos de implantação e implementação.

**Grade de Eventos do Azure**: A [Grade de Eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview) permite que os clientes compilem facilmente aplicativos com as arquiteturas baseadas em evento. Os usuários selecionam o recurso do Azure que desejam assinar e fornecem ao manipulador de eventos ou ao webhook um ponto de extremidade ao qual o evento deverá ser enviado. Os clientes podem proteger os pontos de extremidade do webhook adicionando parâmetros de consulta à URL do webhook ao criar uma Assinatura de Evento. A Grade de Eventos do Azure dá suporte apenas a pontos de extremidade do webhook HTTPS. A Grade de Eventos do Azure permite que os clientes controlem o nível de acesso concedido a usuários diferentes para execução de várias operações de gerenciamento, como listar assinaturas de evento, criar novos e gerar chaves. A Grade de Eventos utiliza o controle de acesso baseado em função do Azure.

**Azure Functions**: O [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) é um serviço de computação sem servidor que permite que os usuários executem o código sob demanda sem precisar provisionar explicitamente ou gerenciar a infraestrutura. Use o Azure Functions para executar um script ou parte do código em resposta a uma variedade de eventos.

**Serviço do Azure Machine Learning**: O [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) é uma técnica da ciência de dados que permite que os computadores usem os dados existentes para prever tendências, resultados e comportamentos futuros.

**Catálogo de Dados do Azure**: O [Catálogo de Dados](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) torna as fontes de dados facilmente detectáveis e compreensíveis pelos usuários que gerenciam os dados. Fontes de dados comum podem ser registradas, marcadas e pesquisadas em busca de dados financeiros. Os dados permanecem no local existente, mas uma cópia de seus metadados é adicionada ao Catálogo de Dados, juntamente com uma referência ao local da fonte de dados. Os metadados também são indexados para tornar cada fonte de dados fácil de descobrir por meio de pesquisa e compreensível para os usuários que os descobrirem.

### <a name="virtual-network"></a>Rede virtual

A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: Os [Grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm listas de controle de acesso que permitem ou negam tráfego dentro de uma rede virtual. Os grupos de segurança de rede podem ser usados para proteger o tráfego no nível da sub-rede ou da VM individual. Existem os seguintes grupos de segurança de rede:

  - Um grupo de segurança de rede para o Active Directory
  - Um grupo de segurança de rede para a carga de trabalho

Cada um dos grupos de segurança de rede têm portas e protocolos específicos abertos para que a solução possa funcionar corretamente e com segurança. Além disso, as configurações a seguir são habilitadas para cada grupo de segurança de rede:

- [Eventos e logs de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
- Os logs do Azure Monitor está conectado às [grupo de segurança de rede&#39;logs de diagnóstico s](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**sub-redes**: Cada sub-rede está associada ao seu grupo de segurança de rede correspondente.

### <a name="data-in-transit"></a>Dados em trânsito

O Azure criptografa todas as comunicações entre datacenters do Azure por padrão. Todas as transações para o Armazenamento do Azure por meio do portal do Azure ocorrem por HTTPS.

### <a name="data-at-rest"></a>Dados em repouso

A arquitetura protege dados em repouso usando criptografia, auditoria de banco de dados e outras medidas.

**Armazenamento do Azure**: Para atender aos requisitos de criptografia de dados em repouso, o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption).  Isso ajuda a proteger os dados do titular do cartão em apoio aos compromissos de segurança organizacional e aos requisitos de conformidade definidos pelo PCI DSS 3.2.

**Azure Disk Encryption**: O [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utiliza o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução é integrada ao Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de Dados SQL do Azure**: A instância do Banco de Dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:

- A [autenticação e a autorização do Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidade dos usuários de banco de dados e de outros serviços da Microsoft em uma única localização central.
- A [auditoria do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) controla os eventos de banco de dados e grava-os em um log de auditoria em uma conta de armazenamento do Azure.
- O Banco de Dados SQL do Azure está configurado para usar a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia em tempo real do banco de dados, dos backups associados e dos arquivos de log de transações para proteger as informações em repouso. A Transparent Data Encryption oferece a garantia de que os dados armazenados não fiquem sujeitos a acesso não autorizado.
- [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem o acesso aos servidores do banco de dados até que as permissões apropriadas sejam concedidas. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.
- A [Detecção de Ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite detectar e responder a possíveis ameaças conforme elas ocorrem, fornecendo alertas de segurança sobre atividades suspeitas no banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões anormais de acesso ao banco de dados.
- As [Colunas Criptografadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca sejam exibidos como texto não criptografado no sistema do banco de dados. Após a habilitação da criptografia de dados, somente aplicativos cliente ou servidores de aplicativo com acesso às chaves poderão acessar dados de texto não criptografado.
- As [Propriedades Estendidas](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) podem ser usadas para interromper o processamento de titulares dos dados, pois permitem que os usuários adicionem propriedades personalizadas em objetos de banco de dados e marquem os dados como "Descontinuados" para dar suporte à lógica de aplicativo e evitar o processamento de dados financeiros associados.
- A [Segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite aos usuários definir políticas para restringir o acesso aos dados para interromper o processamento.
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

**Application Insights**: O [Application Insights](https://docs.microsoft.com/azure/application-insights/) é um serviço APM (Gerenciamento de Desempenho de Aplicativos) extensível para desenvolvedores da Web em várias plataformas. Ele detecta anomalias de desempenho e inclui ferramentas de análise avançadas para ajudar a diagnosticar problemas e entender o que os usuários realmente fazem com o aplicativo. Ele foi projetado para ajudar os usuários a aprimorar continuamente o desempenho e a usabilidade.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados dessa arquitetura de referência está disponível para [download](https://aka.ms/PCIAnalyticsTM) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entenderem os pontos de risco em potencial na infraestrutura do sistema ao fazer modificações.

![Diagrama da arquitetura de referência de análise para o PCI DSS](images/pcidss-analytics-threat-model.png "Modelo de ameaça de análise para o PCI DSS")

## <a name="compliance-documentation"></a>Documentação de conformidade
O [Blueprint de Segurança e Conformidade do Azure – matriz de responsabilidades do cliente do PCI DSS](https://aka.ms/pcidss-analytics-tm) lista as responsabilidades em relação a todos os requisitos do PCI DSS 3.2.

O [Blueprint de segurança e conformidade do Azure – matriz de implementação de análise de dados do PCI DSS](https://aka.ms/pcidss-analytics-cim) fornece informações sobre quais requisitos do PCI DSS 3.2 são atendidos pela arquitetura de análise de dados, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada controle abordado.


## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute

Um túnel VPN ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) seguro precisa ser configurado para estabelecer uma conexão de forma segura com os recursos implantados como parte dessa arquitetura de referência de análise de dados. Configurando adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Implementando um túnel de VPN seguro com o Azure, é possível criar uma conexão privada virtual entre uma rede local e uma Rede Virtual do Azure. Essa conexão ocorre pela Internet e permite que os clientes enviem informações por "túnel” com segurança usando um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. O [modo de túnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego do túnel de VPN passa pela Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link de WAN dedicado entre o Azure e a instalação local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não ocorrem pela Internet, elas oferecem mais confiabilidade, mais velocidade, latências mais baixas e maior segurança que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicações do cliente, os dados não passam pela Internet e, portanto, não estão expostos a ela.

Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) práticas recomendadas para a implementação de uma rede híbrida segura que estende uma rede local para o Azure.

### <a name="extract-transform-load-process"></a>Processo de extração, transformação e carregamento
O [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) pode carregar dados no Banco de Dados SQL do Azure sem precisar de uma ferramenta separada de extração, transformação e carregamento ou de importação. O PolyBase permite acessar dados por meio de consultas T-SQL. É possível usar a pilha de análise de business intelligence da Microsoft com o PolyBase, bem como ferramentas de terceiros compatíveis com o SQL Server.

### <a name="azure-active-directory-setup"></a>Configuração do Azure Active Directory
O [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é essencial para gerenciar a implantação e provisionar acesso para pessoas que interagem com o ambiente. Um Windows Server Active Directory existente pode ser integrado ao Azure Active Directory com [quatro cliques](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Os clientes também podem associar a infraestrutura implantada (os controladores de domínio) do Active Directory a um Azure Active Directory existente fazendo da infraestrutura do Active Directory implantada um subdomínio de uma floresta do Azure Active Directory.

## <a name="disclaimer"></a>Aviso de isenção de responsabilidade

 - Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de consulta interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.
 - Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
 - Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
