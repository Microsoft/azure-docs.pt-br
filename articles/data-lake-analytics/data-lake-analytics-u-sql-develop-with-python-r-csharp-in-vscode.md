---
title: Desenvolver U-SQL com Python, R e C# para Azure Data Lake Analytics no Visual Studio Code
description: Saiba como usar o code-behind com Python, R e C# ao enviar o trabalho no Azure Data Lake.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: 6c234ad6756f4e65e172bf0ffc0ae5a1d35d109b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814080"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Desenvolver U-SQL com Python, R e C# para Azure Data Lake Analytics no Visual Studio Code
Saiba como usar o Visual Studio Code (VSCode) para gravar code behind em Python, R e C# com U-SQL e enviar trabalhos ao serviço do Azure Data Lake. Para saber mais sobre Azure Data Lake Tools para VSCode, confira [Usar as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Antes de escrever código personalizado do code-behind, você precisa abrir uma pasta ou um workspace no VSCode.


## <a name="prerequisites-for-python-and-r"></a>Pré-requisitos para Python e R
Registre assemblies de extensões de Python e R para sua conta do ADL. 
1. Abra sua conta no portal.
   - Selecione **Visão geral**. 
   - Clique em **Script de Exemplo**.
2. Clique em **Mais**.
3. Selecione **Instalar Extensões U-SQL**. 
4. A mensagem de confirmação é exibida após a instalação das extensões de U-SQL. 

   ![Configurar o ambiente para python e R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Para obter a melhor experiência no serviço de linguagem Python e R, instale a extensão para VSCode de Python e R. 

## <a name="develop-python-file"></a>Desenvolver arquivo em Python
1. Clique em **Novo Arquivo** em seu workspace.
2. Escreva seu código em U-SQL. Veja a seguir um exemplo de código.
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
    @t  = 
        SELECT * FROM 
        (VALUES
            ("D1","T1","A1","@foo Hello World @bar"),
            ("D2","T2","A2","@baz Hello World @beer")
        ) AS 
            D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. Um arquivo de script e, em seguida, selecione **ADL: Gerar Python arquivo Code-Behind**. 
4. O arquivo **xxx.usql.py** é gerado na pasta de trabalho. Escreva seu código no arquivo Python. Veja a seguir um exemplo de código.

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. Clique com botão direito no arquivo **USQL**, você pode clicar em **Compilar Script** ou **Enviar Trabalho** para execução do trabalho.

## <a name="develop-r-file"></a>Desenvolver arquivo em R
1. Clique em **Novo Arquivo** em seu workspace.
2. Escreva seu código no arquivo U-SQL. Veja a seguir um exemplo de código.
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. Clique com botão direito em **USQL** do arquivo e, em seguida, selecione **ADL: Gerar R arquivo Code-Behind**. 
4. O arquivo **xxx.usql.r** é gerado na pasta de trabalho. Escreva seu código no arquivo R. Veja a seguir um exemplo de código.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Clique com botão direito no arquivo **USQL**, você pode clicar em **Compilar Script** ou **Enviar Trabalho** para execução do trabalho.

## <a name="develop-c-file"></a>Desenvolver arquivo em C#
Um arquivo code-behind é um arquivo em C# associado a um único script U-SQL. Você pode definir um script dedicado a UDO, UDA, UDT e UDF no arquivo code-behind. UDO, UDA, UDT e UDF podem ser usados diretamente no script sem registrar o assembly primeiro. O arquivo code-behind é colocado na mesma pasta que seu arquivo de script U-SQL de emparelhamento. Se o script for chamado de xxx.usql, o code-behind será chamado de xxx.usql.cs. Se você excluir manualmente o arquivo code-behind, o recurso code-behind será desabilitado para seu script U-SQL associado. Para obter mais informações sobre como escrever o código do cliente para script U-SQL, consulte [gravando e usando a código personalizado no U-SQL: Funções definidas pelo usuário]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Clique em **Novo Arquivo** em seu workspace.
2. Escreva seu código no arquivo U-SQL. Veja a seguir um exemplo de código.
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. Clique com botão direito em **USQL** do arquivo e, em seguida, selecione **ADL: Gerar CS arquivo Code-Behind**. 
4. O arquivo **xxx.usql.cs** é gerado na pasta de trabalho. Escreva seu código no arquivo CS. Veja a seguir um exemplo de código.

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. Clique com botão direito no arquivo **USQL**, você pode clicar em **Compilar Script** ou **Enviar Trabalho** para execução do trabalho.

## <a name="next-steps"></a>Próximas etapas
* [Usar as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Execução local e depuração local do U-SQL com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Introdução à Análise Data Lake usando o PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução à Análise Data Lake usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Usar as Ferramentas do Data Lake para Visual Studio para desenvolver aplicativos do U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Usar o catálogo do Data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
