---
title: "Ferramenta de avaliação da solução do Cortana Intelligence | Microsoft Docs"
description: "Como parceiro da Microsoft, aqui estão todas as etapas que você precisa seguir para publicar sua Cortana Intelligence para o AppSource."
services: machine-learning
documentationcenter: 
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: anupams;v-bruham;garye
ms.openlocfilehash: 8340ae1231b7f40f739d7bbb63cee1bf0f095ab5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="cortana-intelligence-solution-evaluation-tool"></a>Ferramenta de avaliação da solução do Cortana Intelligence
## <a name="overview"></a>Visão geral
Você pode usar a ferramenta de avaliação da solução do Cortana Intelligence para ajudar as suas soluções de análise avançadas a estarem em conformidade com as melhores práticas recomendadas pela Microsoft. A Microsoft está ansiosa para trabalhar com os nossos parceiros (ISVs / SIs) para fornecer soluções de alta qualidade aos clientes, revendedores e para a sua implementação. Este guia orientará o processo de uso da ferramenta de avaliação da solução com o seu aplicativo e descreverá as melhores práticas específicas a serem verificadas.

## <a name="getting-started"></a>Introdução
Por favor, [ baixe](https://aka.ms/aa-evaluation-tool-download) e instale ferramenta de avaliação da solução do Cortana Intelligence.

Pré-requisitos:
- Windows 10: [Site oficial para o Windows 10](https://www.microsoft.com/en-us/windows)
- Azure Powershell: [Instale e configure o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).

## <a name="identifying-your-app"></a>Identificando seu aplicativo
Após completar a instalação, abra a ferramenta e comece a sua primeira avaliação.

![Abra a ferramenta de avaliação](./media/cortana-intelligence-appsource-evaluation-tool/1-open-evaluation-tool.png)

Forneça informações de identificação sobre sua solução.

![Conecte a assinatura do Azure](./media/cortana-intelligence-appsource-evaluation-tool/2-connect-azure-subscription.png)

Conecte-se à sua assinatura do Azure e forneça o grupo de recursos que contém seu aplicativo.

![Selecionar recursos](./media/cortana-intelligence-appsource-evaluation-tool/3-select-resources.png)

Assim que o grupo de recursos for carregado, escolha os recursos que estão incluídos na sua solução e identifique a acessibilidade de qualquer recurso de dados como:
- Ingestão
- Consumo
- Interna

Usamos essas informações para entender melhor como sua solução utiliza vários componentes e garantir que os componentes voltados para o usuário sejam consistentes com as melhores práticas.

### <a name="ingestion"></a>Ingestão
A ingestão neste caso significa quaisquer fontes de dados que são usadas para efetuar pull de dados de fora da solução ou quaisquer serviços fora da solução usados para enviar dados por push para ela.

### <a name="consumption"></a>Consumo
O consumo neste caso significa qualquer conjunto de dados que são utilizados para enviar dados por push aos usuários finais, direta ou indiretamente. Por exemplo:
- Conjuntos de dados usados em consultas diretas da PowerBI.
- Conjuntos de dados consultados em um aplicativo Web.

>[!NOTE]
Caso um recurso específico seja usado tanto para a ingestão quanto para o consumo, por favor, escolha **Consumo**.

### <a name="internal"></a>Interna
Use o interno para quaisquer recursos de dados que sejam usados somente no processo de aplicativo interno.

Em seguida, será solicitado a entrega de credenciais válidas para quaisquer bases de dados especificadas na etapa anterior:

![Defina os pré-requisitos para a realização do teste](./media/cortana-intelligence-appsource-evaluation-tool/4-set-test-prerequisites.png)

## <a name="solution-test-cases"></a>Casos de testes referentes à solução
A ferramenta de solução realizará uma coleção de testes automatizados na sua solução.

![Defina a execução do teste](./media/cortana-intelligence-appsource-evaluation-tool/5-set-test-execution.png)

Após a conclusão dos testes, você deverá fornecer uma explicação ou uma justificação sobre o motivo pelo qual a sua solução não se encontra em conformidade com o requisito.

![Fornecer uma justificação de negócios](./media/cortana-intelligence-appsource-evaluation-tool/6-provide-business-justification.png)

Por exemplo, caso a sua solução seja publicada no Azure SQL DW, os testes para avaliação exigirão que você também a publique nos Azure Analysis Services. 

Sua solução pode usar máquinas virtuais IaaS executando o SQL Server Analysis Services em vez do Azure Analysis Services. Este seria um motivo aceitável para a falha do teste.
## <a name="packaging-your-evaluation-results"></a>Guardando os resultados de avaliação
Após concluir os testes, o seu pacote de avaliação será exportado para um arquivo zipado e deverá oferecer um feedback para a ferramenta de avaliação. 

Você precisa compartilhar este arquivo zip de resultados de teste com a Microsoft para sua solução a ser avaliada antes de obter aprovação para ser adicionada ao AppSource

![Avalie a ferramenta de avaliação](./media/cortana-intelligence-appsource-evaluation-tool/7-grade-evaluation-tool.png)

A seção acima deste artigo abrange vários recursos da ferramenta; agora, examinaremos tipos de melhores práticas que essa ferramenta avalia.

## <a name="security-evaluation-considerations"></a>Considerações sobre a avaliação da segurança
### <a name="databases-should-use-azure-active-directory-authentication"></a>As bases de dados devem usar a autenticação do Azure Active Directory
Qualquer Azure SQL ou recurso do Azure SQL DW na solução deve ser habilitado com a autenticação do AAD (Azure Active Directory). A AAD fornece um único lugar para gerenciar todas as suas identidades e funções.

| Para saber mais sobre | Consulte este artigo |
| --- | --- |
| AAD com Banco de Dados SQL e SQL Data Warehouse | [Use a autenticação do Azure Active Directory com o banco de dados SQL ou com o SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication) |
| Configure e gerencie a AAD | [Configurar e gerenciar o Azure Active Directory para autenticação com o Banco de Dados SQL ou o SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure) |
| Autenticação dos Aplicativos Web do Azure | [Autenticação e autorização no Serviço de Aplicativo do Azure](https://docs.microsoft.com/en-us/azure/app-service/app-service-authentication-overview) |
| Configure os aplicativos Web com a AAD | [Como configurar seu aplicativo de Serviço de Aplicativo para usar o logon do Active Directory do Azure](https://docs.microsoft.com/en-us/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)|

### <a name="datasets-accessible-to-end-users-should-support-role-based-access-control"></a>Os conjuntos de dados acessíveis aos usuários finais devem suportar o controle de acesso baseado em função
Ao executar a ferramenta de avaliação, você deverá especificar qualquer relatório ou publicar os recursos. Assume-se que esses recursos servem para serem acessados pelos usuários finais, não pelos desenvolvedores. Esses recursos devem fornecer o controle de acesso baseado em função (RBAC) para garantir que os usuários finais possam apenas acessar os dados autorizados.

Especificamente, qualquer um dos seguintes recursos do Azure podem ser configurados com RBAC e são considerados aceitáveis:
- A segurança do HDInsight, consulte [ Uma introdução à segurança do Hadoop com clusters HDInsight ingressados no domínio](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-domain-joined-introduction)
- Azure SQL, consulte [ autenticação da AAD com Azure SQL]( https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication)
- Azure Analysis Services, consulte [ a gestão das funções e usuários da base de dados para os Serviços de Análise do Azure](https://docs.microsoft.com/azure/analysis-services/analysis-services-database-users)
- SQL Data Warehouse do Azure (Fique atento a isso porque o SQL DW suporta o RBAC, não é recomendado para o acesso direto do usuário final.)

Se você estiver usando um tipo de recurso diferente que suporte o RBAC, por favor, especifique na área de justificação do caso de teste.

### <a name="azure-data-lake-store-should-use-at-rest-encryption"></a>Azure Data Lake Store deve usar uma criptografia em repouso
Azure Data Lake Store (ADLS) suporta uma criptografia em repouso padrão usando chaves de criptografia gerenciadas do ADLS. Você também pode configurar a criptografia usando o Azure Key Vault.

Para mais informações sobre as configurações específicas de criptografia do ADLS, [crie uma conta do Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal#create-an-azure-data-lake-store-account).

### <a name="azure-sql-and-azure-sql-data-warehouse-should-use-encryption"></a>O SQL Azure e o SQL Data Warehouse do Azure devem usar a criptografia
Tanto o SQL do Azure e quanto o Azure SQL DW oferecem o suporte Transparent Data Encryption (TDE), que fornece criptografia e descriptografia em tempo real tanto nos dados quanto nos arquivos de log.

| Para saber mais sobre | Consulte este artigo |
| --- | --- |
| Transparent Data Encryption (TDE) | [Transparent Data Encryption](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| SQL Data Warehouse do Azure com o TDE | [ Criptografia do SQL Data Warehouse TDE TSQL](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-encryption-tde-tsql) |
| Configure o SQL do Azure com TDE | [Transparent Data Encryption com o Banco de Dados SQL do Azure](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) |
| Configure o SQL Azure sempre com Always Encrypted | [O banco de dados SQL Always Encrypted Azure Key Vault](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-always-encrypted-azure-key-vault)|

Além do TDE, o SQL do Azure também oferece suporte Always Encrypted, uma nova tecnologia de criptografia de dados que assegura que os dados sejam criptografados não apenas quando estiverem em repouso e durante a movimentação entre o cliente e o servidor, mas também enquanto os dados estão em uso executando comandos dentro do servidor.

### <a name="any-virtual-machines-must-be-deployed-from-the-azure-marketplace"></a>Quaisquer máquinas virtuais devem ser adquiridas na Microsoft Azure Marketplace
Para fornecer um nível consistente de segurança no AppSource, exigimos que quaisquer máquinas virtuais implantadas como parte de uma solução Cortana Intelligence seja certificado e publicado na Microsoft Azure Marketplace.

Para buscar a lista atual das imagens no Azure Marketplace, consulte, [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute).

Para mais informações sobre como publicar uma imagem de uma máquina virtual para Microsoft Azure Marketplace, consulte [Guia para criar uma imagem de uma máquina virtual para Microsoft Azure Marketplace](https://docs.microsoft.com/en-us/azure/marketplace-publishing/marketplace-publishing-vm-image-creation).

## <a name="scalability-evaluation-considerations"></a>Considerações sobre a avaliação de escalabilidade
### <a name="cortana-intelligence-solutions-should-include-a-scalable-big-data-platform"></a>Soluções Cortana Intelligence devem incluir uma plataforma Big Data escalonável
Soluções Cortana Intelligence devem escalonar para tamanhos de dados muito grandes. No Azure, isto significa que devem incluir uma ou duas plataformas de dados em escala de Petabytes:
- Repositório Azure Data Lake
- SQL Data Warehouse do Azure

Caso a sua solução não exija o suporte para estes tamanhos de dados ou caso você use uma plataforma alternativa de dados, por favor, explique isso na justificação dos casos de teste.
### <a name="cortana-intelligence-solutions-should-include-dedicated-ingestion-data-environments"></a>Soluções Cortana Intelligence devem incluir ambientes de dados dedicados à ingestão
Soluções Cortana Intelligence devem evitar a inserção direta de dados nas fontes de dados relacionais. Ao invés disso, os dados brutos devem ser armazenados em um ambiente não estruturado, com inserções/atualizações idempotentes em quaisquer armazenamentos relacionais usando o Azure Data Factory.

Para mais informações sobre os dados da Azure Data Factory, [Tutorial: crie um pipeline com uma atividade de Cópia usando um estúdio visual](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio).

### <a name="azure-sql-data-warehouse-should-use-polybase-for-data-ingestion"></a>O SQL Data Warehouse do Azure deve usar o PolyBase para a ingestão de dados
O SQL DW Azure suporta o PolyBase para a ingestão de dados altamente escalonável e paralela. O PolyBase permite que você use o DW SQL do Azure para emitir questões contra os conjuntos de dados externos armazenados no armazenamentos de Blobs ou no Azure Data Lake Store. Isso gera um desempenho superior para métodos alternativos de pacote de atualização de volume.

Para obter instruções sobre como começar com a PolyBase e o SQL DW da Azure, consulte [os dados de carga com a PolyBase no SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-get-started-load-with-polybase).

Para saber mais sobre as melhores práticas com a PolyBase e com o SQL DW da Azure, consulte [Guia para o uso da PolyBase no SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide).

## <a name="availability-evaluation-considerations"></a>Considerações sobre a avaliação da disponibilidade

### <a name="datasets-accessible-to-end-users-should-support-a-large-volume-of-concurrent-users"></a>Os conjuntos de dados acessíveis aos usuários finais devem suportar um grande volume de usuários simultâneos
Ao executar a ferramenta de avaliação, você deverá especificar qualquer relatório ou publicar os recursos. Assume-se que esses recursos servem para serem acessados pelos usuários finais, não pelos desenvolvedores. Esses recursos devem suportar um número médio/grande de usuários ao mesmo tempo.

Especificamente, o SQL Data Warehouse do Azure NÃO deverá ser a única fonte de dados disponível para os usuários finais. Se o SQL DW do Azure for fornecido como recurso para usuários avançados, o Azure Analysis Services deverá ser disponibilizados para os usuários comuns.

Para saber mais sobre os limites de simultaneidade da SQL DW do Azure, consulte [Gerenciamento e carga de trabalho no SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-develop-concurrency).

Para mais informações sobre Azure Analysis Services, consulte [Visão geral do Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview).

### <a name="azure-sql-resources-should-have-a-read-only-replica-for-failover"></a>Os recursos do SQL do Azure devem ter uma réplica somente leitura para failover
Os bancos de dados SQL do Azure suportam replicação geográficas para uma instância. Esta instância pode ser usada como uma instância failover para fornecer aplicativos de alta disponibilidade.

Para saber mais sobre a replicação geográfica para bancos de dados do SQL Azure, consulte [Visão geral da replicação Geográfica do banco de dados SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview).

Para obter instruções sobre como configurar a replicação geográfica para o SQL Azure, consulte [Configurar replicação geográfica ativa para banco de dados SQL do Azure com o Transact-SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-transact-sql).

### <a name="azure-sql-data-warehouse-should-have-geo-redundant-backups-enabled"></a>O SQL Data Warehouse do Azure deverá ter backups com redundância geográfica habilitados
O SQL DW do Azure suporta backups diários para o armazenamento com redundância geográfica. Essa replicação geográfica garante que você possa restaurar o data warehouse mesmo em situações onde você não tenha acesso ao armazenamento instantâneo em sua região primária. Esse recurso é ativado como padrão e não deve ser desabilitado para as soluções Cortana Intelligence.

Para ter mais informações sobre os backups e a restauração do SQL DW do Azure, consulte aqui [Backups do SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-backups).

### <a name="virtual-machines-should-be-configured-with-availability-sets"></a>As máquinas virtuais devem ser configuradas com conjuntos de disponibilidade
As máquinas virtuais do Azure devem ser configuradas em conjuntos de disponibilidade para minimizar o impacto de eventos de manutenção planejados e não planejados.

Para saber mais sobre a disponibilidade da máquina virtual do Azure, consulte [Gerenciar a disponibilidade das máquinas virtuais do Windows no Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability).

## <a name="other-evaluation-considerations"></a>Outras considerações sobre a avaliação
### <a name="cortana-intelligence-apps-should-use-a-centralized-tool-for-data-orchestration"></a>Os aplicativos da Cortana Intelligence devem usar uma ferramenta de orquestração de dados
Usar uma única ferramenta para gerenciar e planejar o movimento e a transformação dos dados garante a consistência em torno dos dados críticos da missão. Fornece uma lógica clara em torno da lógica de repetição, gestão de dependência, alerta/registro, etc. Nós recomendamos o uso da [ Azure Data Factory ](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-introduction) para a orquestração no Azure.

Se você estiver usando uma ferramenta diferente do Azure Data Factory para orquestração, descreva qual ferramenta ou ferramentas que você está usando.
### <a name="azure-machine-learning-models-should-be-retrained-using-azure-data-factory"></a>Os modelos da Azure Machine Learning devem ser requalificados usando Azure Data Factory
A Azure Machine Learning (AzureML) fornece ferramentas fáceis de usar para a criação e implantação do modelo preditivo e dos pipelines de aprendizagem do computador. No entanto, é importante que as implantações da produção desses modelos de AzureML não se baseiam num conjunto único de dados fixo, porém, ao invés disso, se adapta à dinâmica de mudança do fenômeno do mundo real.

Para saber mais sobre como criar reciclagem de serviços Web em AzureML, consulte [Recicle Machine Learning de forma programada](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-retrain-models-programmatically).

Para saber mais sobre o processo de treinamento do modelo de automatização usando o Azure Data Factory, consulte [Atualizando os modelos da Azure Machine Learning usando a atividade de Recurso de atualização](https://docs.microsoft.com/en-us/azure//data-factory/v1/data-factory-azure-ml-update-resource-activity).

## <a name="existing-documentation"></a>Documentação existente
[Expanda seu negócio em nuvem com o Microsoft Azure Certified](https://azure.microsoft.com/en-us/marketplace/programs/certified/)

[Microsoft Azure Certified para Cortana Intelligence](https://azure.microsoft.com/en-us/marketplace/programs/certified/cortana/)

