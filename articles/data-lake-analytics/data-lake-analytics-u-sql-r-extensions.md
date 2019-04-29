---
title: Estender scripts U-SQL com R no Azure Data Lake Analytics
description: Saiba como executar código R em scripts de U-SQL usando o Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 59a52b2aeb83732a608f1fcf5bc4de907d25dfd1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813750"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>Estender scripts U-SQL com código R no Azure Data Lake Analytics

O exemplo a seguir ilustra as etapas básicas para implantar código R:
* Use a instrução `REFERENCE ASSEMBLY` para habilitar as extensões R para o Script U-SQL.
* Use o `REDUCE` operação para os dados de entrada em uma chave de partição.
* As extensões R para U-SQL incluem um redutor interno (`Extension.R.Reducer`) que executa o código R em cada vértice atribuído ao redutor. 
* Uso de dedicado denominado quadros de dados chamados `inputFromUSQL` e `outputToUSQL` respectivamente para passar dados entre U-SQL e R. entrada e saída DataFrame nomes de identificador são fixos (ou seja, os usuários não é possível alterar esses nomes predefinidos de entrada e DataFrame de saída identificadores).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Inserindo código R no script U-SQL

Você pode embutir o código R no script U-SQL usando o parâmetro de comando do `Extension.R.Reducer`. Por exemplo, você pode declarar o script R como uma variável de cadeia de caracteres e passá-lo como um parâmetro para o Redutor.


    REFERENCE ASSEMBLY [ExtR];
    
    DECLARE @myRScript = @"
    inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
    lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
    #do not return readonly columns and make sure that the column names are the same in usql and r scripts,
    outputToUSQL=data.frame(summary(lm.fit)$coefficients)
    colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
    outputToUSQL
    ";
    
    @RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, rReturnType:"dataframe");

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Manter o código R em um arquivo separado e fazer referência a ela no script U-SQL

O exemplo a seguir ilustra um uso mais complexo. Nesse caso, o código R é implantado como um RESOURCE que é o script U-SQL.

Salve esse código R como um arquivo separado.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Use um script U-SQL para implantar esse script R com a instrução DEPLOY RESOURCE.

    REFERENCE ASSEMBLY [ExtR];

    DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT 
            SepalLength double,
            SepalWidth double,
            PetalLength double,
            PetalWidth double,
            Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT 
            Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput = REDUCE @ExtendedData ON Par
        PRODUCE Par, fit double, lwr double, upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
        OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();

## <a name="how-r-integrates-with-u-sql"></a>Como o R se integra com o U-SQL

### <a name="datatypes"></a>Tipos de dados
* As colunas numéricas e de cadeia de caracteres do U-SQL são convertidas no estado em que se encontram entre R DataFrame e U-SQL [tipos com suporte: `double`, `string`, `bool`, `integer`, `byte`].
* Não há suporte para o tipo de dados `Factor` em U-SQL.
* `byte[]` deve ser serializado como uma `string` codificada em base64.
* As cadeias de caracteres de U-SQL poderão ser convertidas em fatores no código R depois que U-SQL criar quadro de dados de entrada R ou configurando o parâmetro redutor `stringsAsFactors: true`.

### <a name="schemas"></a>Esquemas
* Conjuntos de dados U-SQL não podem ter nomes de coluna duplicados.
* Nomes de coluna de conjuntos de dados U-SQL devem ser cadeias de caracteres.
* Nomes de coluna devem ser iguais em scripts U-SQL e R.
* A coluna somente leitura não pode fazer parte do dataframe de saída. Porque colunas somente leitura são injetadas automaticamente na tabela U-SQL caso ela faça parte do esquema de saída do UDO.

### <a name="functional-limitations"></a>Limitações funcionais
* O mecanismo R não pode ser instanciado duas vezes no mesmo processo. 
* No momento, o U-SQL não dá suporte a UDOs combinadores para previsão usando modelos particionados gerados com UDOs redutores. Os usuários podem declarar os modelos particionados como recurso e usá-los no Script de R (consulte o código de exemplo `ExtR_PredictUsingLMRawStringReducer.usql`)

### <a name="r-versions"></a>Versões do R
Somente o R 3.2.2 tem suporte.

### <a name="standard-r-modules"></a>Módulos de R padrão

    base
    boot
    Class
    Cluster
    codetools
    compiler
    datasets
    doParallel
    doRSR
    foreach
    foreign
    Graphics
    grDevices
    grid
    iterators
    KernSmooth
    lattice
    MASS
    Matrix
    Methods
    mgcv
    nlme
    Nnet
    Parallel
    pkgXMLBuilder
    RevoIOQ
    revoIpe
    RevoMods
    RevoPemaR
    RevoRpeConnector
    RevoRsrConnector
    RevoScaleR
    RevoTreeView
    RevoUtils
    RevoUtilsMath
    Rpart
    RUnit
    spatial
    splines
    Stats
    stats4
    survival
    Tcltk
    Tools
    translations
    utils
    XML

### <a name="input-and-output-size-limitations"></a>Limitações de tamanho de Entrada e Saída
Cada vértice tem uma quantidade limitada de memória atribuída a ele. Já que os DataFrames de entrada e de saída devem existir na memória no código R, o tamanho total para a entrada e saída não pode exceder os 500 MB.

### <a name="sample-code"></a>Exemplo de código
Mais código de exemplo está disponível em sua conta do Data Lake Store depois de você instalar as Extensões de Análise Avançada do U-SQL. O caminho para mais código de exemplo é: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Implantar módulos R personalizados com o U-SQL

Primeiro, crie um módulo R personalizado, compacte-o e, em seguida, carregue o arquivo de módulo R personalizado compactado em seu repositório do ADL. No exemplo, carregaremos magittr_1.5.zip na raiz da conta do ADLS padrão para a conta do ADLA que estamos usando. Depois que você carregar o módulo no repositório do ADL, declare-o e use DEPLOY RESOURCE para disponibilizá-lo em seu script U-SQL e chame `install.packages` para instalá-lo.

    REFERENCE ASSEMBLY [ExtR];
    DEPLOY RESOURCE @"/magrittr_1.5.zip";

    DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFileModelSummary string = @"/R/Output/CustomPackages.txt";

    // R script to run
    DECLARE @myRScript = @"
    # install the magrittr package,
    install.packages('magrittr_1.5.zip', repos = NULL),
    # load the magrittr package,
    require(magrittr),
    # demonstrate use of the magrittr package,
    2 %>% sqrt
    ";

    @InputData =
    EXTRACT SepalLength double,
    SepalWidth double,
    PetalLength double,
    PetalWidth double,
    Species string
    FROM @IrisData
    USING Extractors.Csv();

    @ExtendedData =
    SELECT 0 AS Par,
    *
    FROM @InputData;

    @RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, RowId int, ROutput string
    READONLY Par
    USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");

    OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();

## <a name="next-steps"></a>Próximas etapas
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Usar funções da janela do U-SQL para trabalhos de análise do Azure Data Lake](data-lake-analytics-use-window-functions.md)
