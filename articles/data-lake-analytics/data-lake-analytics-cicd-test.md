---
title: Como testar seu código do Azure Data Lake Analytics
description: Saiba como adicionar casos de teste para código C# estendido e U-SQL para Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 4532e0c6e8095c9d64897410e0492e2135d8a478
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630049"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Testar o código do Azure Data Lake Analytics

O Azure Data Lake fornece a linguagem U-SQL, que combina SQL declarativo com C# imperativo para processar dados em qualquer escala. Neste documento, você aprenderá como criar casos de teste para código UDO (operador definido pelo usuário) do C# estendido e U-SQL.

## <a name="test-u-sql-scripts"></a>Testar scripts U-SQL

O script U-SQL é compilado e otimizado para que o código executável seja executado em computadores na nuvem ou no computador local. O processo de compilação e otimização trata o script U-SQL inteiro como um todo. Não é possível fazer um "teste de unidade" tradicional para cada instrução. No entanto, usando o SDK de teste do U-SQL e o SDK de execução local, será possível fazer testes em nível de script.

### <a name="create-test-cases-for-u-sql-script"></a>Criar casos de teste para o script U-SQL

As Ferramentas do Azure Data Lake para Visual Studio permitem que você crie casos de teste de script U-SQL.

1.  Clique com o botão direito do mouse em um script U-SQL no Gerenciador de Soluções e selecione **Criar Teste de Unidade**.
2.  Crie um novo projeto de teste ou insira o caso de teste em um projeto de teste existente.

    ![Ferramentas do Data Lake para Visual Studio - criar um projeto de teste U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Ferramentas do Data Lake para Visual Studio - criar uma configuração de projeto de teste U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-the-test-data-source"></a>Gerenciar a fonte de dados de teste

Ao testar scripts U-SQL, serão necessários arquivos de entrada de teste. É possível gerenciar os dados de teste, configurando **Testar Fonte de Dados** nas propriedades do projeto U-SQL. 

Ao chamar a interface `Initialize()` no SDK de teste de U-SQL, uma pasta raiz de dados local temporária será criada no diretório de trabalho do projeto de teste e todos os arquivos e subpastas (e arquivos em subpastas) na pasta de origem de dados de teste serão copiados para a pasta raiz de dados locais temporários antes de executar os casos de teste do script U-SQL. É possível adicionar mais pastas de fonte de dados de teste, separando o caminho da pasta de dados de teste com um ponto e vírgula.

![Ferramentas do Data Lake para Visual Studio - configurar fonte de dados de teste do projeto](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-the-database-environment-for-testing"></a>Gerenciar o ambiente de banco de dados para testes

Se os scripts U-SQL usarem ou consultarem objetos do banco de dados U-SQL (por exemplo, ao chamar procedimentos armazenados), será necessário inicializar o ambiente do banco de dados antes de executar os casos de teste de U-SQL. A interface `Initialize()` no SDK de teste de U-SQL ajuda a implantar todos os bancos de dados referenciados pelo projeto U-SQL à pasta raiz de dados locais temporários no diretório de trabalho do projeto de teste. 

Saiba mais sobre [como gerenciar referências de projeto de banco de dados U-SQL para um projeto U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Verificar resultados do teste

A interface `Run()` retorna um resultado de execução de trabalho. 0 significa com êxito e 1 significa com falha. Você também pode usar funções assert do C# para verificar as saídas. 

### <a name="run-test-cases-in-visual-studio"></a>Executar casos de teste no Visual Studio

Um projeto de teste de script U-SQL é compilado sobre uma estrutura de teste de unidade do C#. Após compilar o projeto, será possível executar todos os casos de teste por meio de **Gerenciador de Testes > Playlist**. Como alternativa, clique com o botão direito do mouse no arquivo .cs e selecione **Executar Testes**.

## <a name="test-c-udos"></a>Testar UDOs de C#

### <a name="create-test-cases-for-c-udos"></a>Criar casos de teste para UDOs de C#

É possível usar uma estrutura de teste de unidade C# para testar os UDOs (operadores definidos pelo usuário) do C#. Para testar UDOs, será necessário preparar os objetos **IRowset** como entradas.

Há duas maneiras de criar um objeto IRowset:

- Carregar dados de um arquivo para criar IRowset:

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Use dados de uma coleção de dados para criar IRowset:

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Verificar resultados do teste

Após chamar funções UDO, será possível verificar os resultados por meio da verificação de valor do Conjunto de Linhas e esquema, usando funções assert de C#. É possível usar código de exemplo em um projeto de exemplo de teste de unidade de UDO do C# de U-SQL por meio de **Arquivo > Novo > Projeto** no Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Executar casos de teste no Visual Studio

Após compilar o projeto de teste, execute todos os casos de teste em **Gerenciador de Testes > Playlist** ou clique com o botão direito no arquivo .cs e escolha **Executar Testes**.

## <a name="run-test-cases-in-azure-devops"></a>Executar casos de teste no Azure DevOps

Ambos os **projetos de teste do script U-SQL** e **projetos de teste do UDO de C#** herdam os projetos de teste de unidade de C#. A [tarefa de teste do Visual Studio](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) no Azure DevOps pode executar esses casos de teste. 

### <a name="run-u-sql-test-cases-in-azure-devops"></a>Executar casos de teste U-SQL no Azure DevOps

Para um teste U-SQL, certifique-se de carregar `CPPSDK` na sua máquina de compilação e, em seguida, passe o caminho `CPPSDK` para USqlScriptTestRunner (cppSdkFolderFullPath: \@ "").

**O que é CPPSDK?**

O CPPSDK é um pacote que inclui o Microsoft Visual C++ 14 e SDK do Windows 10.0.10240.0. Esse é o ambiente necessário para o tempo de execução do U-SQL. É possível obter esse pacote na pasta de instalação das Ferramentas do Azure Data Lake para Visual Studio:

- No Visual Studio 2015, ela está em `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- No Visual Studio 2017, ela está em `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Preparar o CPPSDK no agente de build do Azure DevOps**

A maneira mais comum de preparar a dependência CPPSDK no Azure DevOps é a seguinte:

1.  Compacte a pasta que inclui as bibliotecas do CPPSDK.
2.  Verifique no arquivo .zip o sistema de controle do código-fonte. (O arquivo .zip garante que você verifique todas as bibliotecas sob a pasta CPPSDK para que alguns arquivos não sejam ignorados por ".gitignore".)   
3.  Descompacte o arquivo .zip no pipeline de build.
4.  Aponte o `USqlScriptTestRunner` para essa pasta descompactada no computador de compilação.

### <a name="run-c-udo-test-cases-in-azure-devops"></a>Executar casos de teste UDO do C# no Azure DevOps

Para um teste de UDO do C#, certifique-se de referenciar os seguintes assemblies, os quais são necessários para UDOs. Se você fizer referência a eles por meio [do pacote do Nuget Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), não se esqueça de adicionar uma tarefa de restauração do NuGet em seu pipeline de build.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>Próximas etapas

- [Como configurar o pipeline de CI/CD para o Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Executar um script U-SQL no computador local](data-lake-analytics-data-lake-tools-local-run.md)
- [Usar o projeto de banco de dados U-SQL para desenvolver o banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

