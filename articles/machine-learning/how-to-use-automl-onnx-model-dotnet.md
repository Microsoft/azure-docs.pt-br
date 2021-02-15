---
title: Fazer previsões com o modelo AutoML ONNX no .NET
description: Saiba como fazer previsões usando um modelo AutoML ONNX no .NET com ML.NET
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.date: 10/30/2020
ms.topic: conceptual
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.custom: how-to, automl
ms.openlocfilehash: 4fb147dc5c57c3a98607a025f566fa583bf87460
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358806"
---
# <a name="make-predictions-with-an-automl-onnx-model-in-net"></a>Fazer previsões com um modelo AutoML ONNX no .NET

Neste artigo, você aprende a usar um modelo de intercâmbio de rede neural (ONNX) aberto do ML (AutoML) para fazer previsões em um aplicativo de console do .NET Core do C# com ML.NET.

O [ml.net](/dotnet/machine-learning/) é uma estrutura de aprendizado de máquina de software livre e de plataforma cruzada para o ecossistema do .NET que permite treinar e consumir modelos de aprendizado de máquina personalizados usando uma abordagem de código-primeira em C# ou F #, bem como por meio de ferramentas de código baixo, como o [Construtor de modelos](/dotnet/machine-learning/automate-training-with-model-builder) e a CLI do [ml.net](/dotnet/machine-learning/automate-training-with-cli). A estrutura também é extensível e permite que você aproveite outras estruturas de aprendizado de máquina populares, como TensorFlow e ONNX.

ONNX é um formato de código aberto para modelos de ia. O ONNX é compatível com a interoperabilidade entre estruturas. Isso significa que você pode treinar um modelo em uma das muitas estruturas de aprendizado de máquina populares, como PyTorch, convertê-la em formato ONNX e consumir o modelo de ONNX em uma estrutura diferente, como ML.NET. Para saber mais, visite o [site do ONNX](https://onnx.ai/).

## <a name="prerequisites"></a>Pré-requisitos

- [SDK do .NET Core 3,1 ou superior](https://dotnet.microsoft.com/download)
- Editor de texto ou IDE (como o [Visual Studio](https://visualstudio.microsoft.com/vs/) ou [Visual Studio Code](https://code.visualstudio.com/Download))
- Modelo ONNX. Para saber como treinar um modelo AutoML ONNX, consulte o seguinte [bloco de anotações de classificação de marketing bancário](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb).
- [Netron](https://github.com/lutzroeder/netron) (opcional)

## <a name="create-a-c-console-application"></a>Criação de um aplicativo de console em C#

Neste exemplo, você usa o CLI do .NET Core para compilar seu aplicativo, mas você pode fazer as mesmas tarefas usando o Visual Studio. Saiba mais sobre o [CLI do .NET Core](/dotnet/core/tools/).

1. Abra um terminal e crie um novo aplicativo de console do .NET Core em C#. Neste exemplo, o nome do aplicativo é `AutoMLONNXConsoleApp` . Um diretório é criado com esse mesmo nome com o conteúdo do seu aplicativo.

    ```dotnetcli
    dotnet new console -o AutoMLONNXConsoleApp
    ```

1. No terminal, navegue até o diretório *AutoMLONNXConsoleApp*

    ```bash
    cd AutoMLONNXConsoleApp
    ```

## <a name="add-software-packages"></a>Adicionar pacotes de software

1. Instale os pacotes NuGet **Microsoft.ml** , **Microsoft. ml. OnnxRuntime** e **Microsoft. ML. OnnxTransformer** usando o CLI do .NET Core.

    ```dotnetcli
    dotnet add package Microsoft.ML
    dotnet add package Microsoft.ML.OnnxRuntime
    dotnet add package Microsoft.ML.OnnxTransformer
    ```

    Esses pacotes contêm as dependências necessárias para usar um modelo ONNX em um aplicativo .NET. O ML.NET fornece uma API que usa o [tempo de execução ONNX](https://github.com/Microsoft/onnxruntime) para previsões.

1. Abra o arquivo *Program.cs* e adicione as instruções a seguir `using` na parte superior para fazer referência aos pacotes apropriados.

    ```csharp
    using System.Linq;
    using Microsoft.ML;
    using Microsoft.ML.Data;
    using Microsoft.ML.Transforms.Onnx;
    ```

## <a name="add-a-reference-to-the-onnx-model"></a>Adicionar uma referência ao modelo ONNX

Uma maneira para o aplicativo de console acessar o modelo ONNX é adicioná-lo ao diretório de saída de compilação.  Para saber mais sobre os itens comuns do MSBuild, consulte o [Guia do MSBuild](/visualstudio/msbuild/common-msbuild-project-items).

Adicionar uma referência ao arquivo de modelo ONNX em seu aplicativo

1. Copie o modelo ONNX para o diretório raiz *AutoMLONNXConsoleApp* do seu aplicativo.
1. Abra o arquivo *AutoMLONNXConsoleApp. csproj* e adicione o conteúdo a seguir dentro do `Project` nó.

    ```xml
    <ItemGroup>
        <None Include="automl-model.onnx">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

    Nesse caso, o nome do arquivo de modelo ONNX é *automl-Model. ONNX*.

1. Abra o arquivo *Program.cs* e adicione a linha a seguir dentro da `Program` classe.

    ```csharp
    static string ONNX_MODEL_PATH = "automl-model.onnx";
    ```

## <a name="initialize-mlcontext"></a>Inicializar MLContext

Dentro do `Main` método da sua `Program` classe, crie uma nova instância do [`MLContext`](xref:Microsoft.ML.MLContext) .

```csharp
MLContext mlContext = new MLContext();
```

A [`MLContext`](xref:Microsoft.ML.MLContext) classe é um ponto de partida para todas as operações de ml.net e a inicialização `mlContext` cria um novo ambiente ml.NET que pode ser compartilhado no ciclo de vida do modelo. É semelhante, conceitualmente, a DbContext em Entity Framework.

## <a name="define-the-model-data-schema"></a>Definir o esquema de dados do modelo

Seu modelo espera os dados de entrada e saída em um formato específico. O ML.NET permite que você defina o formato dos seus dados por meio de classes. Às vezes, talvez você já saiba a aparência desse formato. Em casos em que você não conhece o formato de dados, pode usar ferramentas como Netron para inspecionar seu modelo ONNX.

O modelo usado neste exemplo usa dados do conjunto de NYC TLC táxi Trip. Um exemplo dos dados pode ser visto abaixo:

|vendor_id|rate_code|passenger_count|trip_time_in_secs|trip_distance|payment_type|fare_amount|
|---|---|---|---|---|---|---|
|VTS|1|1|1140|3,75|CRD|15.5|
|VTS|1|1|480|2.72|CRD|10.0|
|VTS|1|1|1680|7.8|CSH|26,5|

### <a name="inspect-the-onnx-model-optional"></a>Inspecionar o modelo ONNX (opcional)

Use uma ferramenta como Netron para inspecionar as entradas e saídas do modelo.

1. Abra Netron.
1. Na barra de menus superior, selecione **arquivo > abrir** e use o navegador de arquivos para selecionar o modelo.
1. Seu modelo é aberto. Por exemplo, a estrutura do modelo *automl-Model. onnx* é semelhante ao seguinte:

    :::image type="content" source="media/how-to-use-automl-onnx-model-dotnet/netron-automl-onnx-model.png" alt-text="Modelo Netron AutoML ONNX":::

1. Selecione o último nó na parte inferior do grafo ( `variable_out1` nesse caso) para exibir os metadados do modelo. As entradas e saídas na barra lateral mostram as entradas, as saídas e os tipos de dados esperados do modelo. Use essas informações para definir o esquema de entrada e saída do seu modelo.

### <a name="define-model-input-schema"></a>Definir esquema de entrada de modelo

Crie uma nova classe chamada `OnnxInput` com as propriedades a seguir dentro do arquivo *Program.cs* .

```csharp
public class OnnxInput
{
    [ColumnName("vendor_id")]
    public string VendorId { get; set; }

    [ColumnName("rate_code"),OnnxMapType(typeof(Int64),typeof(Single))]
    public Int64 RateCode { get; set; }

    [ColumnName("passenger_count"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 PassengerCount { get; set; }

    [ColumnName("trip_time_in_secs"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 TripTimeInSecs { get; set; }

    [ColumnName("trip_distance")]
    public float TripDistance { get; set; }

    [ColumnName("payment_type")]
    public string PaymentType { get; set; }
}
```

Cada uma das propriedades é mapeada para uma coluna no DataSet. As propriedades são anotadas com mais detalhes com atributos.

O [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) atributo permite especificar como ml.NET deve referenciar a coluna ao operar nos dados. Por exemplo, embora a `TripDistance` Propriedade siga as convenções de nomenclatura padrão do .net, o modelo só conhece uma coluna ou um recurso conhecido como `trip_distance` . Para resolver essa discrepância de nomenclatura, o [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) atributo mapeia a `TripDistance` propriedade para uma coluna ou recurso pelo nome `trip_distance` .
  
Para valores numéricos, ML.NET só opera em [`Single`](xref:System.Single) tipos de valor. No entanto, o tipo de dados original de algumas das colunas são inteiros. O [`OnnxMapType`](xref:Microsoft.ML.Transforms.Onnx.OnnxMapTypeAttribute) atributo mapeia tipos entre ONNX e ml.net.

Para saber mais sobre atributos de dados, consulte o [Guia de dados de carga do ml.net](/dotnet/machine-learning/how-to-guides/load-data-ml-net).

### <a name="define-model-output-schema"></a>Definir esquema de saída do modelo

Depois que os dados são processados, ele produz uma saída de um determinado formato. Defina o esquema de saída de dados. Crie uma nova classe chamada `OnnxOutput` com as propriedades a seguir dentro do arquivo *Program.cs* .

```csharp
public class OnnxOutput
{
    [ColumnName("variable_out1")]
    public float[] PredictedFare { get; set; }
}
```

Semelhante ao `OnnxInput` , use o [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) atributo para mapear a `variable_out1` saída para um nome mais descritivo `PredictedFare` .

## <a name="define-a-prediction-pipeline"></a>Definir um pipeline de previsão

Um pipeline no ML.NET normalmente é uma série de transformações encadeadas que operam nos dados de entrada para produzir uma saída. Para saber mais sobre transformações de dados, consulte o [Guia de transformação de dados do ml.net](/dotnet/machine-learning/resources/transforms).

1. Criar um novo método chamado `GetPredictionPipeline` dentro da `Program` classe

    ```csharp
    static ITransformer GetPredictionPipeline(MLContext mlContext)
    {

    }
    ```

1. Defina o nome das colunas de entrada e saída. Adicione o seguinte código dentro do método `GetPredictionPipeline`.

    ```csharp
    var inputColumns = new string []
    {
        "vendor_id", "rate_code", "passenger_count", "trip_time_in_secs", "trip_distance", "payment_type"
    };

    var outputColumns = new string [] { "variable_out1" };
    ```

1. Defina seu pipeline. Um [`IEstimator`](xref:Microsoft.ML.IEstimator%601) fornece um plano gráfico dos esquemas de operações, de entrada e de saída de seu pipeline.

    ```csharp
    var onnxPredictionPipeline =
        mlContext
            .Transforms
            .ApplyOnnxModel(
                outputColumnNames: outputColumns,
                inputColumnNames: inputColumns,
                ONNX_MODEL_PATH);
    ```

    Nesse caso, [`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel%2A) é a única transformação no pipeline, que usa os nomes das colunas de entrada e saída, bem como o caminho para o arquivo de modelo ONNX.

1. Um [`IEstimator`](xref:Microsoft.ML.IEstimator%601) só define o conjunto de operações a serem aplicadas aos seus dados. O que opera em seus dados é conhecido como um [`ITransformer`](xref:Microsoft.ML.ITransformer) . Use o `Fit` método para criar um do seu `onnxPredictionPipeline` .

    ```csharp
    var emptyDv = mlContext.Data.LoadFromEnumerable(new OnnxInput[] {});

    return onnxPredictionPipeline.Fit(emptyDv);
    ```

    O [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit%2A) método espera uma [`IDataView`](xref:Microsoft.ML.IDataView) entrada as para executar as operações no. Um [`IDataView`](xref:Microsoft.ML.IDataView) é uma maneira de representar dados no ml.NET usando um formato tabular. Como nesse caso o pipeline é usado apenas para previsões, você pode fornecer um vazio [`IDataView`](xref:Microsoft.ML.IDataView) para fornecer as [`ITransformer`](xref:Microsoft.ML.ITransformer) informações necessárias de esquema de entrada e saída. O ajustado [`ITransformer`](xref:Microsoft.ML.ITransformer) é então retornado para uso posterior em seu aplicativo.

    > [!TIP]
    > Neste exemplo, o pipeline é definido e usado dentro do mesmo aplicativo. No entanto, é recomendável que você use aplicativos separados para definir e usar seu pipeline para fazer previsões. No ML.NET, seus pipelines podem ser serializados e salvos para uso adicional em outros aplicativos de usuário final do .NET. O ML.NET dá suporte a vários destinos de implantação, como aplicativos de área de trabalho, serviços Web, aplicativos Webassembly * e muito mais. Para saber mais sobre como salvar pipelines, consulte o [Guia de modelos treinados para salvar e carregar ml.net](/dotnet/machine-learning/how-to-guides/save-load-machine-learning-models-ml-net).
    >
    > * O Webassembly só tem suporte no .NET Core 5 ou superior

1. Dentro do `Main` método, chame o `GetPredictionPipeline` método com os parâmetros necessários.

    ```csharp
    var onnxPredictionPipeline = GetPredictionPipeline(mlContext);
    ```

## <a name="use-the-model-to-make-predictions"></a>Usar o modelo para fazer previsões

Agora que você tem um pipeline, é hora de usá-lo para fazer previsões. O ML.NET fornece uma API de conveniência para fazer previsões em uma única instância de dados chamada [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) .

1. Dentro do `Main` método, crie um [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) usando o [`CreatePredictionEngine`](xref:Microsoft.ML.ModelOperationsCatalog.CreatePredictionEngine%2A) método.

    ```csharp
    var onnxPredictionEngine = mlContext.Model.CreatePredictionEngine<OnnxInput, OnnxOutput>(onnxPredictionPipeline);
    ```

1. Crie uma entrada de dados de teste.

    ```csharp
    var testInput = new OnnxInput
    {
        VendorId = "CMT",
        RateCode = 1,
        PassengerCount = 1,
        TripTimeInSecs = 1271,
        TripDistance = 3.8f,
        PaymentType = "CRD"
    };
    ```

1. Use o `predictionEngine` para fazer previsões com base nos novos `testInput` dados usando o [`Predict`](xref:Microsoft.ML.PredictionEngineBase%602.Predict%2A) método.

    ```csharp
    var prediction = onnxPredictionEngine.Predict(testInput);
    ```

1. Gere o resultado de sua previsão para o console.

    ```csharp
    Console.WriteLine($"Predicted Fare: {prediction.PredictedFare.First()}");
    ```

1. Use o CLI do .NET Core para executar o aplicativo.

    ```dotnetcli
    dotnet run
    ```

    O resultado deve ser semelhante à seguinte saída:

    ```text
    Predicted Fare: 15.621523
    ```

Para saber mais sobre como fazer previsões no ML.NET, consulte o [guia usar um modelo para fazer previsões](/dotnet/machine-learning/how-to-guides/machine-learning-model-predictions-ml-net).

## <a name="next-steps"></a>Próximas etapas

- [Implantar seu modelo como uma API Web ASP.NET Core](/dotnet/machine-learning/how-to-guides/serve-model-web-api-ml-net)
- [Implantar seu modelo como uma função do .NET Azure sem servidor](/dotnet/machine-learning/how-to-guides/serve-model-serverless-azure-functions-ml-net)