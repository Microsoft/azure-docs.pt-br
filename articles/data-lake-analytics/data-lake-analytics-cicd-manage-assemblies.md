---
title: Gerenciar assemblies U-SQL em um pipeline de CI/CD-Azure Data Lake
description: Conheça as práticas recomendadas para gerenciar assemblies de C# do U-SQL em um pipeline de CI/CD com o Azure DevOps.
author: liudan66
ms.author: liud
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/30/2018
ms.openlocfilehash: e88616f45c69d33234aa35333e0d82ad8cc59bb6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015274"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Práticas recomendadas para gerenciar assemblies do U-SQL em um pipeline de CI/CD

Neste artigo, você aprenderá como gerenciar o código-fonte do assembly U-SQL com o projeto de banco de dados U-SQL recém introduzido. Você também aprenderá a configurar um pipeline de integração e implantação contínua (CI/CD) para registro de assembly usando o Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Usar o projeto de banco de dados U-SQL para gerenciar o código-fonte do assembly

[O projeto de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) é um tipo de projeto do Visual Studio que ajuda os desenvolvedores a desenvolver, gerenciar e implantar seus bancos de dados U-SQL de forma simples e rápida. Você pode gerenciar todos os objetos de banco de dados U-SQL (exceto pelas credenciais) com o projeto de banco de dados U-SQL.

Para gerenciar o código-fonte de assembly C# e os scripts de DDL U-SQL de registro de assembly, use o:

- Projeto de banco de dados U-SQL para gerenciar scripts U-SQL de registro de assembly.
- Biblioteca de classes (para aplicativo U-SQL) para gerenciar o código-fonte C# e as dependências para operadores, funções e agregadores (UDOs, UDFs e UDAGs) definidos pelo usuário.
- Projeto de banco de dados U-SQL para fazer referência ao projeto de biblioteca de classes.

Um projeto de banco de dados U-SQL pode fazer referência a um projeto de biblioteca de classes (para aplicativo U-SQL). Você pode criar assemblies registrados no banco de dados U-SQL usando o código-fonte C# referenciado deste projeto de biblioteca de classes (para aplicativo U-SQL).

Siga estas etapas para criar projetos e adicionar referências.

1. Crie um projeto de biblioteca de classes (para aplicativo U-SQL) selecionando **arquivo**  >  **novo**  >  **projeto**. O projeto está sob o nó **Azure Data Lake > U-SQL**.

   ![Ferramentas do Data Lake para Visual Studio -- criar um projeto de biblioteca de classes C#](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)

1. Adicione seu código C# definido pelo usuário no projeto de biblioteca de classes (para aplicativo U-SQL).

1. Crie um projeto do U-SQL selecionando **arquivo**  >  **novo**  >  **projeto**. O projeto está no nó **Azure data Lake**  >  **U-SQL** .

   ![Ferramentas do Data Lake para Visual Studio -- criar um projeto de banco de dados U-SQL](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)

1. Adicione uma referência ao projeto de biblioteca de classes C# para o projeto de banco de dados U-SQL.

   ![Ferramentas de Data Lake para Visual Studio – Adicionar referência](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png)

   ![Ferramentas de Data Lake para Visual Studio-Adicionar referência de projeto de banco de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

1. Crie um script de assembly no projeto de banco de dados do U-SQL clicando com o botão direito do mouse no projeto e selecionando **Adicionar Novo Item**.

   ![Ferramentas do Data Lake para Visual Studio -- adicionar script de assembly](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Abra o script de assembly na exibição de design de assembly. Selecione o assembly referenciado no menu suspenso **Criar assembly de referência**.

   ![Ferramentas do Data Lake para Visual Studio criam assembly a partir da referência](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

1. Adicione **Dependências Gerenciadas** e **Arquivos Adicionais**, se houver. Ao adicionar arquivos adicionais, a ferramenta usará o caminho relativo para garantir que encontrará os assemblies no computador local e no computador de build.

**\@ _DeployTempDirectory** na janela do editor na parte inferior é uma variável predefinida que aponta a ferramenta para a pasta de saída da compilação. Na pasta de saída de compilação, cada assembly possui uma subpasta nomeada com o nome do assembly. Todas as DLLs e arquivos adicionais estão na subpasta.

## <a name="build-a-u-sql-database-project"></a>Compilar um projeto de banco de dados U-SQL

A saída de build do projeto de banco de dados U-SQL é um pacote de implantação do banco de dados U-SQL. Ela é nomeada com o sufixo `.usqldbpack`. O pacote `.usqldbpack` é um arquivo .zip que inclui todas as instruções DDL em um único script U-SQL na pasta DDL. Todos os arquivos .dll compilados e arquivos adicionais para assemblies estão na pasta Temp.

## <a name="deploy-a-u-sql-database"></a>Implantar um banco de dados U-SQL

O pacote `.usqldbpack` pode ser implantado na conta local ou na conta do Azure Data Lake Analytics. Use o Visual Studio ou o SDK de implantação.

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Implantar um banco de dados U-SQL no Visual Studio

Você pode implantar um banco de dados U-SQL usando um projeto de banco de dados U-SQL ou um pacote `.usqldbpack` no Visual Studio.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Implantar usando um projeto de banco de dados U-SQL

1. Clique com o botão direito do mouse no projeto de banco de dados U-SQL e selecione **Implantar**.

1. No assistente **Implantar Banco de Dados U-SQL**, escolha a **Conta do ADLA** na qual você gostaria de implantar o banco de dados. Há suporte para as contas locais e ADLA.

1. **Origem do Banco de Dados** é preenchida automaticamente. Ela aponta para o pacote .usqldbpack na pasta de saída de compilação do projeto.

1. Insira um nome em **Nome do Banco de Dados** para criar um banco de dados. Se houver um banco de dados com o mesmo nome na conta do Azure Data Lake Analytics de destino, todos os objetos que são definidos no projeto de banco de dados serão criados sem recriar o banco de dados.

1. Para implantar o banco de dados U-SQL, selecione **Enviar**. Todos os recursos, como assemblies e arquivos adicionais, são carregados. Um trabalho de U-SQL que inclui todas as instruções de DDL é enviado.

   ![Ferramentas do Data Lake para Visual Studio -- Implantam um projeto de banco de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

   ![Ferramentas do Data Lake para Visual Studio -- Implantar um assistente do projeto de banco de dados U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Implantar um banco de dados U-SQL no Azure DevOps

O `PackageDeploymentTool.exe` oferece as interfaces de programação e de linha de comando que ajudam a implantar os bancos de dados U-SQL. O SDK está incluído no [pacote do Nuget do SDK do U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) localizado em `build/runtime/PackageDeploymentTool.exe`.

No Azure DevOps, você pode usar uma tarefa de linha de comando e esse SDK para configurar um pipeline de automação para a atualização do banco de dados U-SQL. [Saiba mais sobre o SDK e como configurar um pipeline de CI/CD para implantação do banco de dados U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Próximas etapas

- [Configurar um pipeline de CI/CD para o Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Testar o código do Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Executar um script U-SQL no computador local](data-lake-analytics-data-lake-tools-local-run.md)
