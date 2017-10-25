---
title: "Novidade no Catálogo de Dados do Azure | Microsoft Docs"
description: "Este artigo fornece uma visão geral dos novos recursos adicionados ao Catálogo de Dados do Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 09/06/2017
ms.author: maroche
ms.openlocfilehash: 7259505b68202f3741e64e86555d2e3f968b8951
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="whats-new-in-azure-data-catalog"></a>O que há de novo no Catálogo de Dados do Azure
As atualizações do **Catálogo de Dados do Azure** são liberadas periodicamente. Nem todas as versões incluem novos recursos voltados para o usuário; algumas versões se concentram em funcionalidades de serviço de back-end. Esta página destaca as novas funcionalidades voltadas para o usuário adicionadas ao serviço Catálogo de Dados do Azure.

## <a name="whats-new-for-september-2017"></a>Novidades para setembro de 2017 
Em setembro de 2017, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:

* Suporte para extrair metadados de relação de união de fontes de dados do DB2 ao registrar tabelas relacionadas usando a ferramenta de registro de fonte de dados.
* Suporte para o registro de fontes de dados do MongoDB versão 3.4 usando a ferramenta de registro da fonte de dados.
* Suporte para a exclusão de todos os metadados para os objetos contidos em uma única operação durante a remoção de um banco de dados ou outro contêiner do Catálogo de Dados.
* Suporte para exibir até 1.000 marcas, termos do glossário de negócios ou outras facetas de pesquisa ao refinar uma pesquisa no portal do Catálogo de Dados.


## <a name="whats-new-for-august-2017"></a>Novidades para agosto de 2017 
Em agosto de 2017, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:

*   Um novo exemplo de desenvolvedor está disponível para criar e gerenciar metadados de relação usando a API REST do Catálogo de Dados. O exemplo *Importar informações de relação para o Catálogo de Dados* está disponível na [Página de exemplos de código do Catálogo de Dados](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0). 
* Suporte para extrair metadados de relação de união de fontes de dados de Teradata ao registrar tabelas relacionados usando a ferramenta de registro de fonte de dados.
* Suporte para objetos TVF (função com valor de tabela) do SQL Server ao registrar fontes de dados do SQL Server usando a ferramenta de registro da fonte de dados.
* Várias atualizações e aprimoramentos para melhorar o desempenho e a usabilidade do portal do Catálogo de Dados.

## <a name="whats-new-for-july-2017"></a>Novidades para julho de 2017 
Em julho de 2017, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:
*   Suporte para um controle mais granular de operações de metadados permitidas, incluindo:
    - Os administradores do Catálogo podem restringir a capacidade dos usuários de contribuir com marcas e metadados relacionados para o catálogo, habilitando o acesso somente leitura ao catálogo.
    - Os administradores do Catálogo podem restringir a capacidade dos usuários de registrar novas fontes de dados no catálogo.
    - Os administradores do Catálogo podem restringir a capacidade dos usuários de assumir a propriedade de metadados de ativos de dados no catálogo.
    - Permissões podem ser concedidas a usuários e grupos de segurança do Azure Active Directory para facilitar o gerenciamento de permissões.
* Suporte para relações entre ativos de dados registrados e descoberta de ativos de dados relacionados no portal do Catálogo de Dados, incluindo:
    - Extração de metadados de relação de fontes de dados do SQL Server (incluindo o Banco de Dados SQL do Azure), Oracle e MySQL ao usar a ferramenta de registro de fontes de dados do Catálogo de Dados.
    - Descoberta de ativos de dados relacionados ao exibir metadados de ativos no portal do Catálogo de Dados.
    - Operações para definir, descobrir e gerenciar relações entre ativos de dados usando a API REST do Catálogo de Dados.

Para obter detalhes adicionais sobre como gerenciar permissões no Catálogo de Dados, consulte [Como proteger o acesso ao catálogo de dados e ativos de dados](data-catalog-how-to-secure-catalog.md).
Para obter detalhes adicionais sobre relacionamentos no Catálogo de Dados, consulte [Como exibir ativos de dados relacionados no Catálogo de Dados do Azure](data-catalog-how-to-view-related-data-assets.md).

## <a name="whats-new-for-june-2017"></a>Novidades para junho de 2017 
Em junho de 2017, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:
*   Suporte para fontes de dados Sybase, Apache Cassandra e MongoDB. Os usuários agora podem registrar e descobrir bancos de dados e tabelas Cassandra e MongoDB e bancos de dados, tabelas e exibições Sybase.

> [!NOTE]
> Ao registrar tabelas MongoDB e Cassandra que incluam colunas com tipos de dados complexos, como registros e matrizes, essas colunas não serão incluídas nos metadados adicionados ao Catálogo de Dados.

## <a name="whats-new-for-may-2017"></a>Novidades para maio de 2017 
Em maio de 2017, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:
*   Um novo exemplo de desenvolvedor está disponível para a importação em massa de termos do glossário de negócios. O exemplo de Importação em Massa de Glossário está disponível na [página de exemplos do desenvolvedor do Catálogo de Dados](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples). 
*   Suporte para a edição de informações de conexão do ODBC no portal do Catálogo de Dados do Azure. Os proprietários dos ativos de dados e os administradores do Catálogo de Dados agora podem editar as informações de conexão para fontes de dados ODBC registradas sem necessidade de registrar novamente as fontes de dados.
*   Suporte para URLs clicáveis em definições e descrições de glossário de negócios. Quando URLs forem incluídas nas propriedades de descrição e definição para termos do glossário de negócios, as URLs serão exibidas como hiperlinks no portal do Catálogo de Dados.
*   Suporte à exibição de nomes de especialistas além dos endereços de email de especialistas. Ao exibir os especialistas nas propriedades de um ativo de dados no portal do Catálogo de Dados, o nome completo do especialista do Azure Active Directory será exibido na dica de ferramenta.

## <a name="whats-new-for-april-2017"></a>Novidades para abril de 2017 
Em abril de 2017, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:
*   Suporte para fontes de dados ODBC. Os usuários agora podem registrar e descobrir bancos de dados, tabelas e exibições ODBC usando a ferramenta de registro de fonte de dados do Catálogo de Dados.

## <a name="whats-new-for-march-2017"></a>Novidades para março de 2017 
Em março de 2017, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:
*   Suporte ao uso de grupos de segurança do AAD para administradores de Catálogo de Dados.
*   O Catálogo de Dados do Azure agora está disponível em uma nova região do Azure. Os clientes podem provisionar o Catálogo de Dados do Azure nas regiões do Centro-Oeste dos EUA, além do Leste dos EUA, Oeste dos EUA, Europa Ocidental, Leste da Austrália, Europa Setentrional e Sudeste Asiático. Para obter mais informações, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

## <a name="whats-new-for-february-2017"></a>Novidades para fevereiro de 2017 
Em fevereiro de 2017, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:
*   Suporte para configurações avançadas na ferramenta de registro de fonte de dados do Catálogo de Dados. Os usuários podem especificar valores de tempo limite de comando ao registrarem fontes de dados grandes.
*   Suporte para fontes de dados PostgreSQL e FTP. Os usuários agora podem registrar e descobrir arquivos e diretórios FTP e bancos de dados, tabelas e exibições PostgreSQL.

## <a name="whats-new-for-january-2017"></a>Novidades para janeiro de 2017 
Em janeiro de 2017, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:
*   O Catálogo de Dados do Azure agora está em conformidade com [CSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification).
*   Integração com [Obter e Transformar no Excel 2016 e o Power Query para Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605). Os usuários do Excel podem compartilhar consultas e descobrir consultas usando o Catálogo de Dados do Azure de dentro do Excel. Essa funcionalidade está disponível a usuários com licenças Power BI Pro.

## <a name="whats-new-for-december-2016"></a>Novidades para dezembro de 2016
Em dezembro de 2016, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:
*   O Catálogo de Dados do Azure agora está em conformidade com [HIPAA](https://www.microsoft.com/trustcenter/Compliance/HIPAA) e [Cláusulas de Modelo da UE](https://www.microsoft.com/TrustCenter/Compliance/EU-Model-Clauses).
*   Suporte para edição de informações de conexão de fonte de dados. Os proprietários de ativos de dados e os administradores do Catálogo de Dados agora podem editar as informações de conexão para fontes de dados registradas sem necessidade de registrar novamente as fontes de dados.
*   Suporte para fontes de dados do Salesforce.com. Os usuários agora podem registrar e descobrir objetos do Salesforce.


## <a name="whats-new-for-november-2016"></a>Novidades para novembro de 2016
Em novembro de 2016, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:
*   O Catálogo de Dados do Azure agora está em conformidade com [ISO/IEC 27001](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001) e [ISO/IEC 27018](https://www.microsoft.com/TrustCenter/Compliance/iso-iec-27018).
*   Suporte para o registro manual de fontes de dados ODBC usando o portal do Catálogo de Dados e a API REST.

## <a name="whats-new-for-september-2016"></a>Novidades para setembro de 2016
Em setembro de 2016, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:

* Suporte para as fontes de dados do IBM DB2. Agora, os usuários podem registrar e descobrir os bancos de dados DB2, tabelas e exibições.
* Suporte para fontes de dados do Azure Cosmos DB. Agora os usuários podem se registrar e descobrir as coleções e os bancos de dados do Cosmos DB.
* Suporte para personalizar o nome do catálogo no portal do Catálogo de Dados. Os administradores do catálogo agora podem fornecer o texto que é exibido no título do portal, como o nome da organização.

## <a name="whats-new-for-august-2016"></a>Novidades para agosto de 2016
Em agosto de 2016, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:

* Melhorias para o registro das fontes de dados dos Serviços de Dados Mestre do SQL Server (MDS). Agora, os usuários podem incluir as visualizações e perfis dos dados ao registrar as entidades MDS usando a ferramenta de registro da fonte de dados do Catálogo de Dados.
* Suporte para as pesquisas organizacionais salvas definidas pelo administrador. Ao salvar uma pesquisa no portal do Catálogo de Dados, os administradores do Catálogo agora podem optar por salvar as pesquisas para o uso pessoal ou para todos os usuários do catálogo. As pesquisas organizacionais salvas são compartilhadas com todos os usuários do catálogo e podem fornecer pontos de partida padronizados para a descoberta da fonte de dados.
* Atualizações para a exibição das propriedades no portal do Catálogo de Dados. Agora, todas as propriedades de ativos de dados são exibidas e gerenciadas em um único painel redimensionável para fornecer uma experiência mais consistente e detectável.

## <a name="whats-new-for-july-2016"></a>Novidades para julho de 2016
Em julho de 2016, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:

* Suporte para as fontes de dados dos Serviços de Dados Mestre do SQL Server (MDS). Agora, os usuários podem registrar e descobrir as entidades e modelos do MDS.
* Suporte para procedimentos armazenados do SQL Server. Agora, os usuários podem registrar e descobrir os objetos de procedimento armazenados nas fontes de dados do SQL Server.
* Suporte para idiomas adicionais no portal do Catálogo de Dados do Azure e para a ferramenta de registro da fonte de dados, com um total de 18 idiomas com suporte. A experiência de usuário do Catálogo de Dados do Azure é localizada de acordo com as preferências de idioma definidas no Windows ou no navegador da Web.
* Atualizações e refinamento da home page do portal do Catálogo de Dados, incluindo os aprimoramentos do desempenho e uma experiência de usuário simplificada.

## <a name="whats-new-for-june-2016"></a>Novidades para junho de 2016
Em junho de 2016, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:

* Suporte para redimensionar as colunas na exibição de lista ao descobrir os ativos de dados no portal do Catálogo de Dados. Agora, os usuários podem redimensionar as colunas individuais para ler mais facilmente os metadados de ativo longos, como marcações e descrições.
* O Power Query para Excel foi adicionado ao menu "Abrir em" no portal do Catálogo de Dados. Agora, os usuários podem abrir as fontes de dados com suporte no Excel 2016 ou no Excel 2010 e no Excel 2013 com o suplemento [Power Query para Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) instalado.
* Suporte para as fontes de dados do Armazenamento de Tabelas do Azure. Agora, os usuários podem registrar e descobrir os objetos da Tabela nas fontes de dados de Armazenamento do Azure.

## <a name="whats-new-for-may-2016"></a>Novidades para maio de 2016
Em maio de 2016, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:

* O Glossário de Negócios que permite que os administradores de catálogo definam termos e hierarquias de negócios para criar um vocabulário comum ao negócio. Os usuários podem marcar ativos de dados registrados com os termos do glossário para facilitar sua descoberta e a compreensão do conteúdo do catálogo. Para saber mais, confira [Como configurar o glossário de negócios para Marcação Governada](data-catalog-how-to-business-glossary.md)  
* Aprimoramentos no Glossário de Negócios do Catálogo de Dados que permitem aos usuários atualizar vários termos do glossário em uma única operação. Os usuários podem selecionar vários termos para editar os seguintes campos:
  * Termo Pai: o usuário pode escolher um novo termo pai e todos os termos selecionados são atualizados para serem filhos do termo pai selecionado. Se todos os termos selecionados tiverem o mesmo pai, esse pai será mostrado na caixa de texto. Caso contrário, o campo de termo pai será deixado em branco.   
  * Marcas e Participantes: os usuários podem adicionar e remover marcas e participantes para vários termos do glossário usando a mesma experiência que a marcação de vários ativos de dados.

> [!NOTE]
> O glossário de negócios está disponível somente na Standard Edition do Catálogo de Dados do Azure. A Edição Gratuita não fornece recursos para marcação controlada ou um glossário de negócios.

## <a name="whats-new-for-march-2016"></a>Novidades para março de 2016
Em março de 2016, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:

* Um ponto de extremidade de API REST consolidado para acessar de modo programático os recursos de pesquisa e os recursos de gerenciamento de ativos do catálogo do serviço Catálogo de Dados do Azure. Esses pontos de extremidade de API de pesquisa e de catálogo foram preteridos e descontinuados em 21 de março de 2016. Não há nenhuma alteração na semântica da API. Somente o URI do ponto de extremidade foi alterado. Para obter informações adicionais, consulte a [Referência da API REST do Catálogo de Dados do Azure](https://msdn.microsoft.com/library/azure/mt267595.aspx). Para obter exemplos da API, consulte [Exemplos de desenvolvedor do Catálogo de Dados do Azure](data-catalog-samples.md).

## <a name="whats-new-for-february-2016"></a>Novidades para fevereiro de 2016
Em fevereiro de 2016, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:

* Uma nova e reformulada experiência de seleção de fonte de dados na ferramenta de registro de fonte de dados do Catálogo de Dados do Azure. A ferramenta de registro de fonte de dados foi atualizada para facilitar a localização e seleção nas fontes de dados com suporte no Catálogo de Dados do Azure.
* Suporte para 10 idiomas adicionais no portal do Catálogo de Dados do Azure e para a ferramenta de registro de fonte de dados. Além do inglês, a experiência do Catálogo de Dados do Azure agora está disponível em alemão, espanhol, francês, italiano, japonês, coreano, português do Brasil, russo, chinês simplificado e chinês tradicional. A experiência de usuário do Catálogo de Dados do Azure é localizada de acordo com as preferências de idioma definidas no Windows ou no navegador da Web do usuário.
* Suporte para replicação geográfica dos dados do Catálogo de Dados do Azure para recuperação de desastre e continuidade dos negócios. Todo o conteúdo do Catálogo de Dados do Azure, incluindo os metadados de fonte de dados e as anotações de crowdsourcing, agora é replicado entre duas regiões do Azure sem custo adicional para os clientes. As regiões do Azure são pré-emparelhadas, em pelo menos 800 km de distância, e seguem o mapeamento descrito em [BCDR (Continuidade dos negócios e recuperação de desastres): Regiões Emparelhadas do Azure](../best-practices-availability-paired-regions.md).
* Suporte para alterar a assinatura do Azure usada pelo Catálogo de Dados do Azure. Os administradores do Catálogo de Dados do Azure podem usar a página Configurações no portal do Catálogo de Dados do Azure para selecionar uma assinatura diferente do Azure para a cobrança.

## <a name="whats-new-for-january-2016"></a>Novidades para janeiro de 2016
Em janeiro de 2016, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:

* Suporte para registrar manualmente fontes de dados adicionais. Agora é possível usar “Criar Entrada Manual” no portal do Catálogo de Dados do Azure ou usar a API REST do Catálogo de Dados do Azure para registrar as seguintes fontes de dados:
  * OData - Função, Conjunto de Entidades e Contêiner de Entidade
  * HTTP - Arquivo, Ponto de Extremidade, Relatório e Site
  * Sistema de Arquivos - Arquivo
  * SharePoint - Lista
  * FTP - Arquivo e Diretório
  * SalesForce.com - Objeto
  * DB2 - Tabela, Exibição e Banco de Dados
  * PostgreSQL - Tabela, Exibição e Banco de Dados
* Suporte para "Abrir nas Ferramentas de Dados do SQL Server" para fontes de dados do SQL Server (incluindo o Azure SQL DB e o Azure SQL Data Warehouse).  
* Suporte ao registro e à descoberta de modos de exibição e pacotes do HANA SAP. É possível registrar fontes de dados do SAP HANA usando a ferramenta de registro de fonte de dados do Catálogo de Dados do Azure, bem como anotar e descobrir fontes de dados do SAP HANA registradas usando o portal do Catálogo de Dados do Azure.
* A capacidade de fixar e desafixar ativos de dados no portal do Catálogo de Dados do Azure. É possível optar por fixar os ativos de dados para facilitar a redescoberta e reutilização.
* Uma home page nova e reformulada no portal do Catálogo de Dados do Azure. A nova home page inclui informações sobre a atividade atual dos usuários – incluindo ativos publicados recentemente, ativos fixados e pesquisas salvas – bem como informações sobre a atividade no Catálogo como um todo.
* Suporte para configurações de usuários persistentes no portal do Catálogo de Dados do Azure. Configurações de experiência do usuário - incluindo o modo de exibição de grade ou lado a lado, o número de resultados por página e ativar ou desativar o realce de ocorrências - persistem entre sessões de usuário.
* O Catálogo de Dados do Azure agora está disponível em duas novas regiões do Azure. Os clientes podem provisionar o Catálogo de Dados do Azure nas regiões da Europa Setentrional e Sudeste Asiático, além do Leste dos EUA, Oeste dos EUA, Europa Ocidental e Leste da Austrália. Para obter mais informações, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

> [!NOTE]
> "Abrir nas Ferramentas de Dados do SQL Server" requer a instalação do Visual Studio 2013 com a Atualização 4 e das Ferramentas do SQL Server. Para instalar a versão mais recente das Ferramentas de Dados do SQL Server, acesse [Baixar o SSDT (Ferramentas de Dados do SQL Server)](https://msdn.microsoft.com/library/mt204009.aspx).


## <a name="whats-new-for-december-2015"></a>Novidades para dezembro de 2015
Em dezembro de 2015, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:

* Suporte aos perfis de dados para fontes de dados de SQL Data Warehouse do Azure. Ao registrar as tabelas e exibições do SQL Data Warehouse do Azure, os usuários podem optar por incluir métricas de perfil de dados com os metadados extraídos da fonte de dados.
* Suporte ao registro e à descoberta de bancos de dados e objetos do MySQL. Os usuários podem registrar fontes de dados MySQL usando a ferramenta de registro de fonte de dados Catálogo de Dados do Azure, assim como podem anotar e descobrir fontes de dados MySQL registrados usando o portal do Catálogo de Dados do Azure.
* Suporte à autenticação SPNEGO e Windows para fontes de dados Teradata. Ao registrar tabelas e exibições do Teradata, os usuários podem optar por se conectarem ao Teradata usando o SPNEGO e o Windows, bem como a autenticação LDAP e TD2.
* Suporte às fontes de dados do repositório Azure Data Lake. Agora os usuários podem registrar e descobrir fontes de dados do repositório Azure Data Lake usando o Catálogo de Dados do Azure.
* Suporte para especificar manualmente configurações de proxy de rede na ferramenta de registro de fonte de dados Catálogo de Dados do Azure. Os usuários podem escolher "Modificar configurações de proxy" na página inicial da ferramenta e especificar o endereço do proxy e a porta a serem usados pela ferramenta.

## <a name="whats-new-for-november-2015"></a>Novidades para novembro de 2015
Em novembro de 2015, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:

* A capacidade de exibir e copiar as cadeias de conexão de dentro do portal do Catálogo de Dados do Azure para fontes de dados SQL Server (incluindo o Banco de Dados SQL do Azure) e Oracle. Os usuários podem clicar no link “Exibir Cadeias de Conexão” nas informações de conexão de uma tabela, exibição ou banco de dados do SQL Server ou Oracle para ver as cadeias de conexão usadas para se conectar à fonte de dados. As cadeias de conexão ADO.NET, ODBC, OLEDB e JDBC são fornecidas para fontes de dados do SQL Server. As cadeias de conexão do OLEDB e ODBC são fornecidas para fontes de dados do Oracle.
* Suporte para inclusão de perfis de dados ao registrar tabelas e exibições do Teradata.
* Suporte para "Abrir no Power BI Desktop" para fontes do SQL Server (incluindo o Azure SQL DB e o Azure SQL Data Warehouse) SQL Server Analysis Services, Azure Storage e HDFS.  
* Suporte para autenticação LDAP para fontes de dados Teradata. Ao registrar tabelas e exibições do Teradata, os usuários podem optar por se conectarem ao Teradata usando o LDAP, bem como a autenticação TD2.
* Suporte para "Abrir no Excel" em fontes de dados Teradata.
* Suporte aos termos de pesquisa recentes no portal do Catálogo de Dados do Azure. Ao pesquisar no portal, os usuários podem selecionar termos de pesquisa usados recentemente para acelerar a experiência de descoberta.
* Suporte para visualização de fontes de dados Teradata. Ao registrar as tabelas Teradata e exibições, os usuários podem optar por incluir registros de instantâneo com os metadados extraídos da fonte de dados.
* Suporte para "Abrir no Excel" para fontes de dados de SQL Data Warehouse do Azure.
* Suporte para definição e edição de esquemas de nível de coluna para ativos de dados registradas manualmente. Depois de criar manualmente um ativo de dados usando o portal do Catálogo de Dados do Azure, os usuários podem adicionar definições de coluna nas propriedades de ativos de dados.
* Suporte para consultas "has" ao pesquisar no Catálogo de Dados do Azure para habilitar a descoberta de ativos de dados registrados que têm metadados específicos. A sintaxe de consulta do Catálogo de Dados do Azure agora inclui:

| Sintaxe de consulta | Finalidade |
| --- | --- |
| `has:previews` |Localiza os ativos de dados que incluem uma visualização |
| `has:documentation` |Localiza os ativos de dados para qual documentação foi fornecida |
| `has:tableDataProfiles` |Localiza os ativos de dados com informações de perfil de dados de nível de tabela |
| `has:columnsDataProfiles` |Localiza ativos de dados com informações de perfil de dados ao nível de coluna |


> [!NOTE]
> A opção "Abrir no Power BI Desktop" requer a instalação de uma versão atual do aplicativo Power BI Desktop. Caso tenha problemas ou receba erros ao usar esse recurso, verifique se você tem a última versão do Power BI Desktop de [PowerBI.com](https://powerbi.com).


## <a name="whats-new-for-october-2015"></a>Novidades para outubro de 2015
Em outubro de 2015, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:

* Suporte para criptografia no restante das visualizações de dados e perfis de dados para fontes de dados registradas. O Catálogo de Dados do Azure criptografa de modo transparente todos os registros de visualização e fontes de dados de perfis de dados registrados no serviço, sem a necessidade de gerenciamento de chaves pelos administradores do Catálogo.
* Suporte para fontes de dados Teradata. Agora os usuários podem registrar e descobrir tabelas e exibições do Teradata.
* Suporte para fontes de dados de Hive locais. Agora, os usuários podem registrar e descobrir tabelas Hive para Apache Hive no Hadoop em fontes de dados locais.
* Suporte a pesquisas salvas no portal do Catálogo de Dados do Azure. Os usuários podem salvar os termos de pesquisa e filtrar seleções para repetir facilmente pesquisas anteriores e definir exibições úteis do conteúdo do Catálogo. O usuário também pode marcar uma pesquisa salva como sua pesquisa padrão. Quando um usuário clica no ícone de pesquisa “lupa” na home page do portal do Catálogo de Dados do Azure ou na página de “introdução”, ele é levado diretamente para a pesquisa salva sinalizada como padrão.
* Suporte para documentação RTF para ativos de dados registrados e contêineres no portal do Catálogo de Dados do Azure. Os usuários agora podem fornecer documentação para ativos de dados como tabelas, visualizações e relatórios e contêineres, como bancos de dados e modelos para cenários nos quais as marcas e descrições não são suficientes.
* Suporte ao registro manual de tipos conhecidos de fonte de dados. Os usuários podem inserir manualmente as informações de fonte de dados usando o portal do Catálogo de Dados do Azure para todos os tipos de fonte de dados com suporte pelo Catálogo de Dados do Azure.
* Suporte para a autorização de grupos de segurança do Active Directory do Azure. Os administradores do catálogo podem habilitar o acesso ao catálogo para grupos de segurança e contas de usuário, facilitando o gerenciamento do acesso ao Catálogo de Dados do Azure.
* Suporte para abrir fontes de dados do Hive no Excel por meio do portal do Catálogo de Dados do Azure.

> [!NOTE]
> Para a versão atual, há suporte para apenas a autenticação Teradata TD2. Haverá suporte para mecanismos de autenticação adicionais em versões futuras.

> [!NOTE]
> Para usar o recurso "Abrir no Excel" para fontes de dados de Hive, os usuários devem ter o driver ODBC para Hive instalado.

## <a name="whats-new-for-september-2015"></a>Novidades para setembro de 2015
Em setembro de 2015, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:

* Suporte para inclusão de perfis de dados ao registrar fontes de dados do Hive.
* Suporte para a descoberta programática da API de Catálogo, facilitando a integração de aplicativos com o Catálogo de Dados do Azure.
* Uma nova experiência de descoberta da fonte de dados de "introdução" no portal do Catálogo de Dados do Azure. Quando os usuários entram na página “Descobrir” do portal do Catálogo de Dados do Azure sem inserir um termo de pesquisa, é apresentada uma visão geral do conteúdo do catálogo, incluindo as marcas usadas com mais frequência, além de especialistas, tipos de fontes de dados e tipos de objeto.
* Suporte para registro e descoberta de bancos de dados e objetos do SQL Data Warehouse do Azure. Para saber mais sobre o SQL Data Warehouse do Azure, confira [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
* Suporte para registro e descoberta de modelos do SQL Server Analysis Services e servidores do SQL Server Reporting Services como contêineres. Ao registrar objetos SSAS e SSRS, o Catálogo de Dados do Azure cria uma entrada para o modelo SSAS e o servidor SSRS, bem como para os relatórios e outros objetos. Os contêineres podem ser descobertos e anotados usando o portal do Catálogo de Dados do Azure. Os usuários também podem pesquisar e filtrar o conteúdo de um modelo ou servidor, além de pesquisar e filtrar o conteúdo do catálogo.
* Suporte para registro e descoberta de objetos do SQL Server Analysis Services via HTTP/HTTPS. Agora, os usuários podem conectar os servidores SSAS usando uma URL (como https://servername/olap/msmdpump.dll), em vez de um nome do servidor, e podem usar a autenticação Básica e conexões Anônimas, além da autenticação do Windows. Para obter mais informações sobre conexões HTTP/HTTPS para SSAS, veja [Configurar o acesso HTTP para Serviços de Análise](https://msdn.microsoft.com/library/gg492140.aspx).
* Suporte para fontes de dados Hive no HDInsight. Agora, os usuários podem registrar e descobrir tabelas Hive para Apache Hive no Hadoop em fontes de dados HDInsight. Para obter mais informações sobre o Hive no HDInsight, confira o [centro de documentação do HDInsight](../hdinsight/hdinsight-use-hive.md).
* Suporte ao registro e à descoberta de bancos de dados Oracle e clusters HDFS como contêineres. Ao registrar tabelas e exibições do Oracle ou HDFS, o Catálogo de Dados do Azure cria uma entrada para o banco de dados, as tabelas e as exibições. O banco de dados pode ser descoberto e anotado por meio do portal do Catálogo de Dados do Azure. Os usuários também podem pesquisar e filtrar o conteúdo de um banco de dados ou cluster, além de pesquisar e filtrar o conteúdo do catálogo.
* Suporte ao registro manual de tipos desconhecidos de fonte de dados. Os usuários podem inserir manualmente informações de fonte de dados usando o portal do Catálogo de Dados do Azure, de modo que as fontes de dados para as quais não há suporte explícito pela ferramenta de registro de fonte de dados possam ser anotadas e descobertas.
* Suporte ao registro e à descoberta de bancos de dados do SQL Server como contêineres. Ao registrar tabelas e exibições do SQL Server, o Catálogo de Dados do Azure cria uma entrada para o banco de dados, as tabelas e as exibições. O banco de dados pode ser descoberto e anotado por meio do portal do Catálogo de Dados do Azure. Os usuários também podem pesquisar e filtrar o conteúdo de um banco de dados, além de pesquisar e filtrar o conteúdo do catálogo.

> [!NOTE]
> Os objetos SSAS e SSRS que foram registrados antes da versão de 18 de setembro devem ser registrados novamente usando a ferramenta de registro de fonte de dados antes que a entrada do modelo ou do servidor seja adicionada ao catálogo. Registrar novamente uma fonte de dados não afetará nenhuma anotação que tenha sido adicionada pelos usuários no portal do Catálogo de Dados do Azure.

> [!NOTE]
> As tabelas e exibições do Oracle, bem como os arquivos e diretórios HDFS que foram registrados antes da versão de 11 de setembro, devem ser registrados novamente usando a ferramenta de registro de fonte de dados antes que a entrada do banco de dados ou do cluster seja adicionada ao catálogo. Registrar novamente uma fonte de dados não afetará nenhuma anotação que tenha sido adicionada pelos usuários no portal do Catálogo de Dados do Azure.

> [!NOTE]
> As tabelas e exibições do SQL Server que foram registradas antes da versão de 4 de setembro devem ser registradas novamente usando a ferramenta de registro de fonte de dados antes que a entrada do banco de dados seja adicionada ao catálogo. Registrar novamente uma fonte de dados não afetará nenhuma anotação que tenha sido adicionada pelos usuários no portal do Catálogo de Dados do Azure.

## <a name="whats-new-for-august-2015"></a>Novidades para agosto de 2015
Em agosto de 2015, os seguintes recursos foram adicionados ao Catálogo de Dados do Azure:

* Suporte para criação de perfil de dados de fontes de dados do SQL Server e Oracle. Ao registrar tabelas e exibições do SQL Server e Oracle, os usuários podem optar por incluir informações de perfil de dados para os objetos que estão sendo registrados. O perfil de dados inclui estatísticas no nível de objeto e no nível de coluna.
* Suporte para fontes de dados HDFS do Hadoop. Agora, os usuários podem registrar e descobrir os diretórios e arquivos do HDFS.
* Suporte para fornecer informações de solicitação de acesso de fontes de dados registradas. Para qualquer ativo de dados registrado, os usuários agora podem fornecer instruções para solicitação de acesso, incluindo links de email ou URLs, a fim de integrar facilmente com ferramentas e processos existentes.
* Dicas de ferramenta para marcas e especialistas, para facilitar a descoberta de quais usuários forneceram quais metadados para os ativos de dados registrados.
* Adicionamos um novo botão "Usuário" e o menu à nossa barra de navegação superior. Esse menu permite que o usuário veja a conta usada para fazer logon no Catálogo de Dados do Azure e para sair, se desejado. Esse menu também exibe o nome do catálogo, que é importante para os desenvolvedores que usam o API REST do Catálogo de Dados do Azure.
* Somente para Standard Edition: ao adicionar proprietários aos ativos de dados, o Catálogo de Dados do Azure agora dá suporte a contas de usuário e grupos de segurança como proprietários. Para adicionar um grupo de segurança como um proprietário de ativos de dados selecionados, você pode inserir o nome para exibição do grupo ou o endereço de email do UPN do grupo, se houver.
* Suporte para fontes de dados do Armazenamento do Blobs do Azure. Agora, os usuários podem registrar e descobrir os blobs e diretórios de armazenamento do Azure.

