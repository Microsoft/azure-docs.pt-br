---
title: Executar e testar trabalhos de U-SQL localmente usando o SDK do U-SQL do Azure Data Lake
description: Saiba como executar e testar trabalhos de U-SQL localmente usando a linha de comando e as interfaces de programação na estação de trabalho local.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 14908225e78b79cb748e712ae23643ddde4a4242
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813500"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Executar e testar U-SQL com o SDK do U-SQL do Azure Data Lake

Ao desenvolver o script U-SQL, é comum executar e testá-lo localmente antes enviá-lo para a nuvem. O Azure Data Lake fornece um pacote NuGet chamado SDK do U-SQL do Azure Data Lake para este cenário, por meio do qual é possível escalar a execução e o teste do U-SQL com facilidade. Também é possível integrar esse teste do U-SQL ao sistema de CI (Integração Contínua) para automatizar a compilação e o teste.

Se você se preocupa em como executar e depurar o script U-SQL no local manualmente com ferramentas de GUI, é possível usar as Ferramentas do Azure Data Lake para Visual Studio para essa finalidade. Saiba mais [aqui](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Instalar o SDK do U-SQL do Azure Data Lake

É possível obter o SDK do U-SQL do Azure Data Lake [aqui](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) em Nuget.org. Antes de usá-lo, você precisa verificar se tem as dependências a seguir.

### <a name="dependencies"></a>Dependências

O SDK para U-SQL do Data Lake exige as seguintes dependências:

- [Microsoft .NET Framework 4.6 ou mais recente](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 e SDK do Windows 10.0.10240.0 ou mais novo (que é chamado CppSDK neste artigo). Há duas maneiras de obter o CppSDK:

  - Instalar o [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Você terá uma pasta \Windows Kits\10 na pasta Arquivos de Programa – por exemplo, C:\Program Files (x86) \Windows Kits\10\. Você também encontrará a versão do SDK do Windows 10 em \Windows Kits\10\Lib. Se você não vir essas pastas, reinstale o Visual Studio e selecione o SDK do Windows 10 durante a instalação. Se você tiver isso instalado com o Visual Studio, o compilador local do U-SQL o encontrará automaticamente.

    ![SDK para Windows 10 da execução local das Ferramentas do Data Lake para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Instalar [Ferramentas do Data Lake para Visual Studio](https://aka.ms/adltoolsvs). Você pode encontrar os arquivos pré-empacotados do SDK do Windows e Visual C++ em C:\Arquivos de Programas (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. Nesse caso, o compilador local U-SQL não pode localizar as dependências automaticamente. Você precisa especificar o caminho CppSDK para ele. Você pode copiar os arquivos para outro local ou usá-los como estão.

## <a name="understand-basic-concepts"></a>Entender os conceitos básicos

### <a name="data-root"></a>Raiz de dados

A pasta raiz de dados é um "repositório local" para a conta de computador local. Ela equivale à conta do Azure Data Lake Store de uma conta do Data Lake Analytics. Alternar para uma pasta raiz de dados diferente é como alternar para uma conta de repositório diferente. Se você quiser acessar dados compartilhados normalmente com pastas raiz de dados diferentes, deverá usar caminhos absolutos em seus scripts. Ou criar links simbólicos de sistema de arquivo (por exemplo, **mklink** em NTFS) na pasta raiz de dados para apontar para os dados compartilhados.

A pasta raiz de dados é usada para:

- Armazenar metadados locais, incluindo bancos de dados, tabelas, TVFs (funções com valor de tabela) e assemblies.
- Pesquisar os caminhos de entrada e saída que são definidos como caminhos relativos no U-SQL. Usar caminhos relativos facilita a implantação dos projetos U-SQL no Azure.

### <a name="file-path-in-u-sql"></a>Caminho de arquivo no U-SQL

Você pode usar um caminho relativo e um caminho absoluto local em scripts U-SQL. O caminho relativo é relativo ao caminho da pasta raiz de dados especificado. Recomendamos que você use "/" como o separador de caminho para tornar seus scripts compatíveis com o lado do servidor. A seguir, alguns exemplos de caminhos relativos e os respectivos caminhos absolutos equivalentes. Nesses exemplos, C:\LocalRunDataRoot é a pasta raiz de dados.

|Caminho relativo|Caminho absoluto|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Diretório de trabalho

Ao executar o script U-SQL localmente, um diretório de trabalho é criado durante a compilação no diretório de execução atual. Além das saídas de compilação, os arquivos de tempo de execução necessários para execução local são copiados em sombra para esse diretório de trabalho. A pasta raiz do diretório de trabalho é chamada “ScopeWorkDir” e os arquivos no diretório de trabalho são os seguintes:

|Diretório/arquivo|Diretório/arquivo|Diretório/arquivo|Definição|DESCRIÇÃO|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Cadeia de caracteres de hash da versão do tempo de execução|Cópia de sombra dos arquivos de tempo de execução necessários para execução local|
| |Script_66AE4909AA0ED06C| |Nome do script + cadeia de caracteres de hash do caminho do script|Saídas da compilação e log das etapas de execução|
| | |\_script\_.abr|Saída do compilador|Arquivo de álgebra|
| | |\_ScopeCodeGen\_.*|Saída do compilador|Código gerenciado gerado|
| | |\_ScopeCodeGenEngine\_.*|Saída do compilador|Código nativo gerado|
| | |assemblies referenciados|Referência de assembly|Arquivos de assembly referenciado|
| | |deployed_resources|Implantação de recursos|Arquivos da implantação de recursos|
| | |xxxxxxxx.xxx[1..n]\_\*.\*|Log de execução|Log das etapas de execução|


## <a name="use-the-sdk-from-the-command-line"></a>Usar o SDK da linha de comando

### <a name="command-line-interface-of-the-helper-application"></a>Interface de linha de comando do aplicativo auxiliar

Em directory\build\runtime do SDK, LocalRunHelper.exe é o aplicativo auxiliar de linha de comando que fornece interfaces para a maioria das funções de execução local frequentemente usadas. Observe que as opções de argumentos e de comando diferenciam maiúsculas de minúsculas. Para invocá-lo:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Execute LocalRunHelper.exe sem argumentos ou com a opção **help** para mostrar as informações de ajuda:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

Nas informações de ajuda:

-  **Command** fornece o nome do comando.  
-  **Required Argument** lista argumentos que devem ser fornecidos.  
-  **Optional Argument** lista argumentos que são opcionais e com valores padrão.  Os argumentos boolianos opcionais não têm parâmetros e as respectivas aparências são negativas para o respectivo valor padrão.

### <a name="return-value-and-logging"></a>Valor de retorno e registro em log

O aplicativo auxiliar retorna **0** em caso de sucesso e **-1** em caso de falha. Por padrão, o auxiliar enviará todas as mensagens para o console atual. No entanto, a maioria dos comandos dá suporte ao argumento opcional **-MessageOut path_to_log_file**, que redireciona as saídas para um arquivo de log.

### <a name="environment-variable-configuring"></a>Configurando variáveis de ambiente

A execução local do U-SQL precisa de uma raiz de dados especificada como a conta de armazenamento local, bem como um caminho do CppSDK especificado para as dependências. É possível definir o argumento na linha de comando ou definir a variável de ambiente para elas.

- Defina a variável de ambiente **SCOPE_CPP_SDK**.

    Se obtiver o SDK do Windows e Microsoft Visual C++ instalando as Ferramentas do Data Lake para Visual Studio, verifique se você tem a seguinte pasta:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Defina uma nova variável de ambiente chamada **SCOPE_CPP_SDK** para pontar para esse diretório. Ou copie a pasta para outro local e especifique **SCOPE_CPP_SDK** dessa forma.

    Além de definir a variável de ambiente, você também pode especificar o argumento **-CppSDK** ao usar a linha de comando. Esse argumento substitui a variável de ambiente CppSDK padrão.

- Defina a variável de ambiente **LOCALRUN_DATAROOT**.

    Defina uma nova variável de ambiente chamada **LOCALRUN_DATAROOT** que aponta para a raiz de dados.

    Além de definir a variável de ambiente, você pode especificar o argumento **-DataRoot** com o caminho raiz de dados ao usar a linha de comando. Esse argumento substitui a variável de ambiente de raiz de dados padrão. Você precisa adicionar esse argumento para cada linha de comando em execução para que você possa substituir a variável de ambiente de raiz de dados padrão em todas as operações.

### <a name="sdk-command-line-usage-samples"></a>Amostras de uso da linha de comando do SDK

#### <a name="compile-and-run"></a>Compilar e executar

O comando **run** é usado para compilar o script e executar resultados compilados. Seus argumentos de linha de comando são uma combinação dos argumentos de **compile** e **execute**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Estes são os argumentos opcionais para **run**:


|Argumento|Valor padrão|DESCRIÇÃO|
|--------|-------------|-----------|
|-CodeBehind|Falso|O script tem o code-behind .cs|
|-CppSDK| |Diretório do CppSDK|
|-DataRoot| Variável de ambiente DataRoot|DataRoot para execução local, padrão para a variável de ambiente “LOCALRUN_DATAROOT”|
|-MessageOut| |Mensagens de despejo no console para um arquivo|
|-Parallel|1|Executar o plano com o paralelismo especificado|
|-References| |Lista de caminhos para os assemblies de referência extra ou arquivos de dados do code-behind, separados por ';'|
|-UdoRedirect|Falso|Gerar configuração de redirecionamento de assembly UDO|
|-UseDatabase|mestre|Banco de dados a ser usado para registro do assembly temporário do code-behind|
|-Verbose|Falso|Mostrar saídas detalhadas do tempo de execução|
|-WorkDir|Diretório atual|Diretório para uso do compilador e saídas|
|-RunScopeCEP|0|Modo ScopeCEP a ser usado|
|-ScopeCEPTempPath|temp|Caminho temporário a ser usado para transmissão de dados|
|-OptFlags| |Lista separada por vírgula de sinalizadores do otimizador|


Aqui está um exemplo:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Além de combinar **compile** e **execute**, é possível compilar e executar os executáveis compilados separadamente.

#### <a name="compile-a-u-sql-script"></a>Compilar um script U-SQL

O comando **compile** é usado para compilar um script U-SQL para executáveis.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Estes são os argumentos opcionais para **compile**:


|Argumento|DESCRIÇÃO|
|--------|-----------|
| -CodeBehind [valor padrão 'False']|O script tem o code-behind .cs|
| -CppSDK [valor padrão '']|Diretório do CppSDK|
| -DataRoot [valor padrão “variável de ambiente DataRoot”]|DataRoot para execução local, padrão para a variável de ambiente “LOCALRUN_DATAROOT”|
| -MessageOut [valor padrão '']|Mensagens de despejo no console para um arquivo|
| -References [valor padrão '']|Lista de caminhos para os assemblies de referência extra ou arquivos de dados do code-behind, separados por ';'|
| -Shallow [valor padrão 'False']|Compilação superficial|
| -UdoRedirect [valor padrão 'False']|Gerar configuração de redirecionamento de assembly UDO|
| -UseDatabase [valor padrão 'master']|Banco de dados a ser usado para registro do assembly temporário do code-behind|
| -WorkDir [valor padrão “Diretório Atual”]|Diretório para uso do compilador e saídas|
| -RunScopeCEP [valor padrão “0”]|Modo ScopeCEP a ser usado|
| -ScopeCEPTempPath [valor padrão “temp”]|Caminho temporário a ser usado para transmissão de dados|
| -OptFlags [valor padrão '']|Lista separada por vírgula de sinalizadores do otimizador|


Veja alguns exemplos de uso.

Compilar um script U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Compilar um script U-SQL e definir a pasta raiz de dados. Observe que isso substituirá a variável de ambiente do conjunto.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Compilar um script U-SQL e definir um diretório de trabalho, o assembly de referência e o banco de dados:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Executar resultados compilados

O comando **execute** é usado para executar os resultados compilados.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Estes são os argumentos opcionais para **execute**:

|Argumento|Valor padrão|DESCRIÇÃO|
|--------|-------------|-----------|
|-DataRoot | '' |Raiz de dados para a execução de metadados. A variável de ambiente **LOCALRUN_DATAROOT** passa a ser o padrão.|
|-MessageOut | '' |Despeje as mensagens do console em um arquivo.|
|-Parallel | '1' |Indicador para executar as etapas de execução local geradas com o nível de paralelismo especificado.|
|-Verbose | 'False' |Indicador para mostrar saídas detalhadas do tempo de execução.|

Aqui está um exemplo de uso:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Usar o SDK com interfaces de programação

As interfaces de programação estão localizadas no LocalRunHelper.exe. Você pode usá-los para integrar a funcionalidade do SDK para U-SQL e a estrutura de teste C# a fim de dimensionar o teste local do script U-SQL. Neste artigo, usarei o projeto de teste de unidade padrão do C# para mostrar como usar essas interfaces para testar o script U-SQL.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Etapa 1: Criar C# configuração e projeto de teste de unidade

- Crie um projeto de teste de unidade do C# por meio de Arquivo > Novo > Projeto > Visual C# > Teste > Projeto de Teste de Unidade.
- Adicione LocalRunHelper.exe como uma referência do projeto. O LocalRunHelper.exe está localizado em \build\runtime\LocalRunHelper.exe no pacote NuGet.

    ![SDK do U-SQL do Azure Data Lake – Adicionar referência](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- O SDK do U-SQL dá suporte **apenas** ao ambiente x64; portanto, lembre-se de definir o destino da plataforma de build como x64. É possível definir isso por meio de Propriedade do Projeto > Build > Destino da plataforma.

    ![SDK do U-SQL do Azure Data Lake – Configurar projeto do x64](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Lembre-se de definir o ambiente de teste como x64. No Visual Studio, é possível defini-lo por meio de Teste > Configurações de Teste > Arquitetura do Processador Padrão > x64.

    ![SDK do U-SQL do Azure Data Lake – Configurar ambiente de teste x64](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Lembre-se de copiar todos os arquivos de dependência em NugetPackage\build\runtime\ para o diretório de trabalho do projeto que, normalmente, está em ProjectFolder\bin\x64\Debug.

### <a name="step-2-create-u-sql-script-test-case"></a>Etapa 2: Criar caso de teste do script U-SQL

Veja abaixo o código de exemplo para o teste de script U-SQL. Para testar, você precisa preparar scripts, arquivos de entrada e os arquivos de saída esperados.

    using System;
    using Microsoft.VisualStudio.TestTools.UnitTesting;
    using System.IO;
    using System.Text;
    using System.Security.Cryptography;
    using Microsoft.Analytics.LocalRun;

    namespace UnitTestProject1
    {
        [TestClass]
        public class USQLUnitTest
        {
            [TestMethod]
            public void TestUSQLScript()
            {
                //Specify the local run message output path
                StreamWriter MessageOutput = new StreamWriter("../../../log.txt");

                LocalRunHelper localrun = new LocalRunHelper(MessageOutput);

                //Configure the DateRoot path, Script Path and CPPSDK path
                localrun.DataRoot = "../../../";
                localrun.ScriptPath = "../../../Script/Script.usql";
                localrun.CppSdkDir = "../../../CppSDK";

                //Run U-SQL script
                localrun.DoRun();

                //Script output 
                string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");

                //Expected script output
                string ExpectedResult = "../../../ExpectedOutput/result.csv";

                Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);

                //Don't forget to close MessageOutput to get logs into file
                MessageOutput.Close();
            }
        }
    }

    namespace Test.Helpers
    {
        public static class FileAssert
        {
            static string GetFileHash(string filename)
            {
                Assert.IsTrue(File.Exists(filename));

                using (var hash = new SHA1Managed())
                {
                    var clearBytes = File.ReadAllBytes(filename);
                    var hashedBytes = hash.ComputeHash(clearBytes);
                    return ConvertBytesToHex(hashedBytes);
                }
            }

            static string ConvertBytesToHex(byte[] bytes)
            {
                var sb = new StringBuilder();

                for (var i = 0; i < bytes.Length; i++)
                {
                    sb.Append(bytes[i].ToString("x"));
                }
                return sb.ToString();
            }

            public static void AreEqual(string filename1, string filename2)
            {
                string hash1 = GetFileHash(filename1);
                string hash2 = GetFileHash(filename2);

                Assert.AreEqual(hash1, hash2);
            }
        }
    }


### <a name="programming-interfaces-in-localrunhelperexe"></a>Interfaces de programação em LocalRunHelper.exe

O LocalRunHelper.exe fornece as interfaces de programação para a compilação e execução locais do U-SQL, etc. As interfaces são listadas a seguir.

**Construtor**

public LocalRunHelper([System.IO.TextWriter messageOutput = null])

|Parâmetro|Type|DESCRIÇÃO|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|para mensagens de saída, definido como nulo para usar o Console|

**Propriedades**

|Propriedade|Type|DESCRIÇÃO|
|--------|----|-----------|
|AlgebraPath|string|O caminho para o arquivo de álgebra (o arquivo de álgebra é um dos resultados da compilação)|
|CodeBehindReferences|string|Se o script tiver referências code-behind adicionais, especifique os caminhos separados por “;”|
|CppSdkDir|string|Diretório do CppSDK|
|CurrentDir|string|Diretório atual|
|DataRoot|string|Caminho da raiz de dados|
|DebuggerMailPath|string|O caminho para o slot de correio do depurador|
|GenerateUdoRedirect|bool|Se quisermos gerar a configuração de substituição do redirecionamento de carregamento do assembly|
|HasCodeBehind|bool|Se o script tiver code-behind|
|InputDir|string|Diretório dos dados de entrada|
|MessagePath|string|Caminho do arquivo de despejo da mensagem|
|OutputDir|string|Diretório dos dados de saída|
|Paralelismo|int|Paralelismo para executar a álgebra|
|ParentPid|int|PID do pai no qual o serviço monitora a saída, definido como 0 ou negativo para ignorar|
|ResultPath|string|Caminho do arquivo de despejo do resultado|
|RuntimeDir|string|Diretório do tempo de execução|
|ScriptPath|string|Local em que o script pode ser encontrado|
|Shallow|bool|Compilação superficial ou não|
|TempDir|string|Diretório temporário|
|UseDataBase|string|Especifique o banco de dados a ser usado para o registro de assembly temporário code-behind, mestre por padrão|
|WorkDir|string|Diretório de trabalho preferencial|


**Método**

|Método|DESCRIÇÃO|Retorno|Parâmetro|
|------|-----------|------|---------|
|public bool DoCompile()|Compilar o script U-SQL|Verdadeiro se tiver êxito| |
|public bool DoExec()|Executar o resultado compilado|Verdadeiro se tiver êxito| |
|public bool DoRun()|Executar o script U-SQL (Compilar + Executar)|Verdadeiro se tiver êxito| |
|public bool IsValidRuntimeDir(string path)|Verificar se o caminho fornecido é um caminho de tempo de execução válido|Verdadeiro para válido|O caminho do diretório de tempo de execução|


## <a name="faq-about-common-issue"></a>Perguntas frequentes sobre um problema comum

### <a name="error-1"></a>Erro 1:
E_CSC_SYSTEM_INTERNAL: Erro interno! Não foi possível carregar o arquivo ou o assembly “ScopeEngineManaged.dll” ou uma de suas dependências. O módulo especificado não pôde ser encontrado.

Verifique o seguinte:

- Verifique se você tem o ambiente x64. A plataforma de destino de compilação e o ambiente de teste deve ser x64, consulte **etapa 1: Criar C# configuração e projeto de teste de unidade** acima.
- Verifique se você copiou todos os arquivos de dependência em NugetPackage\build\runtime\ para o diretório de trabalho do projeto.


## <a name="next-steps"></a>Próximas etapas

* Para aprender a usar o U-SQL, veja [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para registrar em log as informações de diagnóstico, veja [Acessando os logs de diagnóstico para o Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Para ver uma consulta mais complexa, consulte [Analisar logs de site usando o Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Para ver detalhes do trabalho, confira [Usar o Navegador de Trabalhos e o Modo de Exibição de Trabalho para trabalhos do Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Para usar o modo de exibição de execução de vértice, veja [Usar o Modo de Exibição de Execução de Vértice nas Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
