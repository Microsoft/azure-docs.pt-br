---
title: Descobrir, identificar e classificar dados pessoais no Microsoft Azure | Microsoft Docs
description: Saiba mais sobre como pesquisar, classificar, descobrir e identificar dados
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 6ccb064a9a76e7041d4f365b3997673dc9182e0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="discover-identify-and-classify-personal-data-in-microsoft-azure"></a>Descobrir, identificar e classificar dados pessoais no Microsoft Azure

Este artigo fornece diretrizes sobre como descobrir, identificar e classificar dados pessoais em várias ferramentas e serviços do Azure, incluindo o uso do Catálogo de Dados do Azure, Azure Active Directory, Banco de Dados SQL, Power Query para clusters Hadoop no Azure HDInsight, Proteção de Informações do Azure, Azure Search e consultas SQL para o Azure Cosmos DB.

## <a name="scenario-problem-statement-and-goal"></a>Cenário, declaração do problema e meta

Uma empresa de material esportivo localizada nos EUA coleta uma variedade de dados pessoais e outros dados. Isso inclui dados de clientes e funcionários. A empresa mantém esses dados em vários bancos de dados e armazena-os em várias localizações diferentes em seu ambiente do Azure. Além de vender equipamentos esportivos, eles também hospedam e gerenciam o registro de eventos esportivos de elite no mundo todo, incluindo na UE e, em alguns casos, os dados de clientes coletados incluem informações médicas.

Como a empresa hospeda muitos passeios de bicicletas internacionais todos os anos e tem funcionários temporários em locais no mundo todo, alguns dos conjuntos de dados são muito grandes. A empresa também tem aplicativos criados pelo desenvolvedor que são usados por clientes e funcionários.

A empresa deseja resolver os seguintes problemas:

- Os dados pessoais de clientes e funcionários devem ser classificados/diferenciados dos outros dados que a empresa coleta para garantir a segurança e o acesso apropriado.
- O administrador de dados precisa descobrir com facilidade o local dos dados pessoais de clientes em várias áreas do ambiente do Azure.
- Os dados pessoais de clientes e funcionários que aparecem em documentos compartilhados e comunicações por email devem ser rotulados para ajudar a garantir que são mantidos em segurança.
- Os desenvolvedores de aplicativos da empresa precisam de uma maneira de pesquisar com facilidade os dados pessoais de clientes e funcionários em seus aplicativos Web e móveis.
- Os desenvolvedores também precisam consultar seu banco de dados de documentos para obter dados pessoais.

### <a name="company-goals"></a>Metas da empresa

- Todos os dados pessoais de clientes e funcionários devem ser marcados/anotados no Catálogo de Dados do Azure, de modo que possam ser encontrados com facilidade. O ideal é que os dados pessoais de clientes e funcionários sejam marcados/anotados separadamente.
- Os dados pessoais de perfis do usuário e as informações de trabalho de clientes e funcionários que residem no Azure Active Directory devem ser localizados com facilidade.
- Os dados pessoais que residem em vários bancos de dados SQL devem ser consultados com facilidade. 
- Alguns dos conjuntos de dados grandes da empresa são gerenciados por meio do Azure HDInsight e armazenados no Hadoop. Eles devem ser importados para o Excel, de modo que possam ser consultados para obter dados pessoais.
- Os dados pessoais compartilhados em documentos e comunicações por email devem ser classificados, rotulados e mantidos em segurança com a Proteção de Informações do Azure.
- Os desenvolvedores de aplicativos da empresa devem conseguir descobrir dados pessoais de clientes e funcionários nos aplicativos criados, o que pode ser feito com o Azure Search.
- Os desenvolvedores devem conseguir encontrar dados pessoais em seu banco de dados de documentos.

## <a name="azure-active-directory-data-discovery"></a>Azure Active Directory: descoberta de dados

O [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Localize os perfis do usuário de clientes e funcionários e as informações de trabalho dos usuários que contêm dados pessoais no ambiente do [AAD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) usando o [portal do Azure](https://portal.azure.com/).

Isso é particularmente útil se você deseja localizar ou alterar os dados pessoais de um usuário específico. Também adicione ou altere o perfil do usuário e as informações de trabalho. Entre com uma conta que seja um administrador global do diretório.

### <a name="how-do-i-locate-or-view-user-profile-and-work-information"></a>Como fazer para localizar ou exibir o perfil do usuário e as informações de trabalho?

1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.

2. Escolha **Mais serviços**, insira **Usuários e grupos** na caixa de texto e selecione **Enter**.

   ![como fazer para localizar o perfil do usuário e as informações de trabalho](media/how-to-discover-classify-personal-data-azure/user-profile.png)

3. Na folha **Usuários e grupos**, selecione **Usuários**.

  ![Abrindo usuários e grupos](media/how-to-discover-classify-personal-data-azure/users-groups.png)

4. Na folha **Usuários e grupos – Usuários**, selecione um usuário na lista e, em seguida, na folha do usuário selecionado, selecione **Perfil** para exibir as informações de perfil do usuário que podem conter dados pessoais.

  ![selecionar usuário](media/how-to-discover-classify-personal-data-azure/select-user.png)

5. Se precisar adicionar ou alterar informações de perfil do usuário, faça isso e, em seguida, na barra de comandos, selecione **Salvar.**
6. Na folha do usuário selecionado, selecione **Informações de Trabalho** para exibir informações de trabalho dos usuários que podem conter dados pessoais.

 ![exibindo informações de trabalho](media/how-to-discover-classify-personal-data-azure/work-info.png)

7. Se precisar adicionar ou alterar informações de trabalho dos usuários, faça isso e, em seguida, na barra de comandos, selecione **Salvar.**

## <a name="azure-sql-database-data-discovery"></a>Banco de Dados SQL do Azure: descoberta de dados

O [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) é um banco de dados de nuvem que ajuda os desenvolvedores a criar e manter aplicativos. Os dados pessoais podem ser encontrados no [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) usando consultas SQL padrão. A consulta elástica do SQL do Azure (versão prévia) permite aos usuários executar consultas entre bancos de dados.

Um tutorial detalhado do [banco de dados SQL](../sql-database/sql-database-technical-overview.md) explica os muitos aspectos do uso de um banco de dados SQL, incluindo como criar um e como executar consultas de dados. A seguir, veja um resumo das informações disponíveis no tutorial com links para seções específicas.

### <a name="how-do-i-build-a-sql-database"></a>Como fazer para criar um banco de dados SQL?

Há três maneiras de fazer isso:

- Um banco de dados SQL do Azure pode ser criado no [portal do Azure](https://portal.azure.com/). No tutorial, você usará um conjunto específico de recursos de computação e armazenamento em um grupo de recursos e servidor lógico. Você usará dados de exemplo de uma empresa fictícia chamada AdventureWorks. Você também criará uma regra de firewall no nível do servidor. Para saber como fazer isso, visite o tutorial [Criar um banco de dados SQL do Azure no portal do Azure](../sql-database/sql-database-get-started-portal.md).

  ![Criar um Banco de Dados SQL do Azure](media/how-to-discover-classify-personal-data-azure/create-database.png)
- Um banco de dados SQL também pode ser criado na CLI do [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), uma ferramenta de linha de comando baseada em navegador. A ferramenta está disponível no portal do Azure e pode ser executada diretamente no portal. Neste tutorial, você inicia a ferramenta, define as variáveis de script, cria um grupo de recursos e servidor lógico e configura uma regra de firewall do servidor. Em seguida, você cria um banco de dados com os dados de exemplo. Para saber como criar seu banco de dados dessa forma, visite o tutorial [Criar um banco de dados SQL do Azure individual usando a CLI do Azure](../sql-database/sql-database-get-started-cli.md).

  ![Tutorial da CLI](media/how-to-discover-classify-personal-data-azure/cli-tutorial.png)

>[!NOTE]
A CLI do Azure é normalmente usada por desenvolvedores e administradores do Linux. Alguns usuários consideram a CLI mais fácil e intuitiva que o PowerShell, que é a terceira opção.

- Por fim, você pode criar um banco de dados SQL usando o PowerShell, que é uma ferramenta de linha de comando/script usada para criar e gerenciar o Azure e outros recursos. Neste tutorial, você inicia a ferramenta, define as variáveis de script, cria um grupo de recursos e servidor lógico e configura uma regra de firewall do servidor. Em seguida, você criará um banco de dados com os dados de exemplo.

O tutorial exige o módulo do Azure PowerShell versão 4.0 ou posterior. Execute Get-Module -ListAvailable AzureRM para encontrar sua versão. Se precisar instalar ou fazer upgrade, consulte Instalar o módulo do Azure PowerShell.

```PowerShell
New-AzureRmSQLDatabase -ResourceGroupName $resourcegroupname `
-ServerName $servername `
-DatabaseName $databasename `
-RequestedServiceObjectiveName "s0"
```

Para saber como criar seu banco de dados dessa forma, visite o tutorial [Criar um banco de dados SQL do Azure individual usando o PowerShell](../sql-database/sql-database-get-started-powershell.md).

>[!Note]
Os administradores do Windows tendem a usar o PowerShell, mas alguns preferem a CLI do Azure.

### <a name="how-do-i-search-for-personal-data-in-sql-database-in-the-azure-portal"></a>Como fazer para pesquisar dados pessoais no banco de dados SQL no portal do Azure?**

Use a ferramenta interna do editor de consultas no portal do Azure para pesquisar dados pessoais. Você fará logon na ferramenta usando o logon de administrador e a senha do SQL Server e, em seguida, inserirá uma consulta.

  ![pesquisar o SQL usando o portal](media/how-to-discover-classify-personal-data-azure/search-sql-portal.png)

A Etapa 5 do tutorial mostra uma consulta de exemplo no painel do editor de consultas, mas não se concentra em informações pessoais ou confidenciais (também combina dados de duas tabelas e cria aliases para a coluna de origem no conjunto de dados que está sendo retornado). A seguinte captura de tela mostra a consulta na Etapa 5, bem como o painel de resultados retornado:

  ![editor de consultas](media/how-to-discover-classify-personal-data-azure/query-editor.png)

Se o banco de dados era chamado MyTable, uma consulta de exemplo de informações pessoais poderá incluir o nome, o número do seguro social e o número de identificação e terá esta aparência:

“SELECT nome, SSN, número de identificação FROM MyTable”

Você executará a consulta e, em seguida, verá os resultados no painel **Resultados**.

Para obter mais informações sobre como consultar um banco de dados SQL no portal do Azure, visite a seção [Consultar o banco de dados SQL](../sql-database/sql-database-get-started-portal.md) do tutorial.

### <a name="how-do-i-search-for-data-across-multiple-databases"></a>Como fazer para pesquisar dados em vários bancos de dados?

A consulta elástica do SQL (versão prévia) permite executar consultas entre bancos de dados e consultas de vários bancos de dados e retornar um único resultado. A [visão geral do tutorial](../sql-database/sql-database-elastic-query-overview.md) inclui uma descrição detalhada dos cenários e explica a diferença entre o particionamento vertical e horizontal de banco de dados. O particionamento horizontal é chamado “fragmentação”.

  ![Particionamento vertical](media/how-to-discover-classify-personal-data-azure/vertical-partition.png)

  ![particionamento horizontal](media/how-to-discover-classify-personal-data-azure/horizontal.png)

Para começar, visite a página [Visão geral da consulta elástica do Banco de Dados SQL do Azure (versão prévia)](../sql-database/sql-database-elastic-query-overview.md).

#### <a name="power-query-for-importing-azure-hdinsight-hadoop-clusters-data-discovery-for-large-data-sets"></a>Power Query (para importação de clusters Hadoop do Azure HDInsight): descoberta de dados para conjuntos de dados grandes

O Hadoop é um serviço de armazenamento e processamento de software livre do Apache para conjuntos de dados grandes, que são analisados e armazenados em clusters Hadoop. O [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) permite aos usuários trabalhar com clusters Hadoop no Azure. O Power Query é um suplemento do Excel que, entre outras coisas, ajuda os usuários a descobrir dados de origens diferentes.

Os dados pessoais associados aos clusters Hadoop no Azure HDInsight podem ser importados para o Excel com o Power Query. Depois que os dados estiverem no Excel, use uma consulta para identificá-los.

#### <a name="how-do-i-use-excel-power-query-to-import-hadoop-clusters-in-azure-hdinsight-into-excel"></a>Como fazer para usar o Excel Power Query para importar clusters Hadoop no Azure HDInsight para o Excel?

Um tutorial do HDInsight orientará você durante todo esse processo. Ele explica os pré-requisitos e inclui um link para um tutorial [Introdução ao Azure HDInsight](../hdinsight/hdinsight-hadoop-linux-tutorial-get-started.md). As instruções abrangem o Excel 2016, bem como 2013 e 2010 (as etapas são ligeiramente diferentes para as versões mais antigas do Excel). Se você não tiver o suplemento Excel Power Query, o tutorial mostrará como obtê-lo. Você iniciará o tutorial no Excel e precisará ter uma conta de armazenamento de Blobs do Azure associada ao cluster.

  ![Consulta no Excel](media/how-to-discover-classify-personal-data-azure/excel.png)

Para saber como fazer isso, visite o tutorial [Conectar o Excel ao Hadoop usando o Power Query](../hdinsight/hdinsight-connect-excel-power-query.md).

Fonte: [Conectar o Excel ao Hadoop usando o Power Query](../hdinsight/hdinsight-connect-excel-power-query.md)

## <a name="azure-information-protection-personal-data-classification-for-documents-and-email"></a>Proteção de Informações do Azure: classificação de dados pessoais para emails e documentos

A [Proteção de Informações do Azure](https://www.microsoft.com/cloud-platform/azure-information-protection) ajuda os clientes do Azure a aplicar rótulos para classificar e proteger documentos compartilhados e comunicações por email interna ou externamente. Alguns desses itens podem conter informações pessoais de clientes ou funcionários. As regras e as condições podem ser definidas automática ou manualmente, pelos administradores ou usuários. Por exemplo, se um usuário estiver salvando um documento que inclui informações de cartão de crédito, ele verá uma recomendação de rótulo que foi configurada pelo administrador.

### <a name="how-do-i-try-it"></a>Como fazer para experimentá-la?

Se desejar experimentar a Proteção de Informações do Azure para ver se ela pode ser adequada à sua organização, visite o [tutorial do Guia de início rápido](https://docs.microsoft.com/information-protection/get-started/infoprotect-quick-start-tutorial). Ele orienta você pelas cinco etapas básicas – desde instalar e configurar a política até ver a classificação, a rotulagem e o compartilhamento em ação – e deve levar menos de meia hora.

### <a name="how-do-i-deploy-it"></a>Como fazer para implantá-la?

Se desejar implantar a Proteção de Informações do Azure para sua organização, visite o [roteiro de implantação para classificação, rotulagem e proteção](https://docs.microsoft.com/information-protection/plan-design/deployment-roadmap).

### <a name="is-there-anything-else-i-should-know"></a>Há mais alguma coisa que devo saber?

Para obter informações complementares que ajudarão você a planejar como configurá-la, visite a postagem no blog [Ready, set, protect!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/) (Atenção, preparar, proteger!)
. Além disso, confira os links Saiba mais abaixo para obter mais informações sobre a Proteção de Informações do Azure.

## <a name="azure-search-data-discovery-for-developer-apps"></a>Azure Search: descoberta de dados para aplicativos do desenvolvedor

O [Azure Search](https://azure.microsoft.com/services/search/) é uma solução de pesquisa em nuvem para desenvolvedores e fornece uma experiência avançada de pesquisa de dados para os aplicativos. O Azure Search permite localizar dados em índices definidos pelo usuário, obtidos do Azure Cosmos DB, Banco de Dados SQL do Azure, Armazenamento de Blobs do Azure, armazenamento de Tabelas do Azure ou dados JSON de clientes personalizados. Também estruture consultas Lucene usando a API REST do Azure Search para pesquisar tipos de dados pessoais ou os dados pessoais de indivíduos específicos. Os recursos incluem a pesquisa de texto completo, a sintaxe de consulta simples e a sintaxe de consulta Lucene. 

## <a name="how-do-i-use-sql-to-query-data"></a>Como fazer para usar o SQL para consultar dados?

Para começar com os conceitos básicos, visite o tutorial [Azure Cosmos DB: Como consultar usando o SQL](../cosmos-db/tutorial-query-documentdb.md). O tutorial fornece um documento de exemplo e duas consultas SQL e resultados de exemplo.

Para obter diretrizes detalhadas sobre como criar consultas SQL, visite [Consultas SQL para a API do DocumentDB do Azure Cosmos DB.](../cosmos-db/documentdb-sql-query.md)

Se estiver conhecendo o Azure Cosmos DB agora e gostaria de saber como criar um banco de dados, adicionar uma coleção e adicionar dados, visite o tutorial do Guia de início rápido [Azure Cosmos DB: Criar um aplicativo Web da API do DocumentDB](../cosmos-db/create-documentdb-dotnet.md). Se desejar fazer isso em uma linguagem diferente do .NET, como Java ou Python, basta escolher sua linguagem preferida depois de acessar o site.

## <a name="next-steps"></a>Próximas etapas

[Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/?v=16.50)

[O que é o Banco de Dados SQL?](../sql-database/sql-database-technical-overview.md)

[Editor de Consultas do Banco de Dados SQL disponível no portal do Azure] (https://azure.microsoft.com/blog/t-sql-query-editor-in-browser-azure-portal/)

[O que é a Proteção de Informações do Azure?](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)

[O que é o Azure Rights Management?](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms)

[Proteção de Informações do Azure: Atenção, preparar, proteger!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
