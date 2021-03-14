---
title: Publicar pipelines ML
titleSuffix: Azure Machine Learning
description: Execute fluxos de trabalho de Machine Learning com pipelines do Machine Learning e o SDK do Azure Machine Learning para Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: efedb21a1ec1ed53a8c6bfadf337d23a89c04383
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520169"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Publicar e acompanhar pipelines do Machine Learning



Este artigo mostrará como compartilhar um pipeline de aprendizado de máquina com seus colegas ou clientes.

Pipelines de Machine Learning são fluxos de trabalho reutilizáveis para tarefas de aprendizado de máquina. Um benefício dos pipelines é o aumento da colaboração. Você também pode gerar pipelines de versão, permitindo que os clientes usem o modelo atual enquanto você estiver trabalhando em uma nova versão. 

## <a name="prerequisites"></a>Pré-requisitos

* Criar um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md) para manter todos os seus recursos de pipeline

* [Configurar seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o sdk do Azure Machine Learning ou usar uma [instância de computação Azure Machine Learning](concept-compute-instance.md) com o SDK já instalado

* Crie e execute um pipeline de Machine Learning, como o seguinte [tutorial: criar um pipeline de Azure Machine Learning para Pontuação de lote](tutorial-pipeline-batch-scoring-classification.md). Para outras opções, consulte [criar e executar pipelines do Machine Learning com o SDK do Azure Machine Learning](./how-to-create-machine-learning-pipelines.md)

## <a name="publish-a-pipeline"></a>Publicar um pipeline

Quando você tiver um pipeline em funcionamento, poderá publicar um pipeline para que ele seja executado com entradas diferentes. Para o ponto de extremidade REST de um pipeline já publicado para aceitar parâmetros, você deve configurar seu pipeline para usar `PipelineParameter` objetos para os argumentos que irão variar.

1. Para criar um parâmetro de pipeline, use um objeto [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter) com um valor padrão.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Adicione esse objeto `PipelineParameter` como um parâmetro a qualquer uma das etapas no pipeline da seguinte maneira:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publique este pipeline que aceitará um parâmetro quando invocado.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>Execute um pipeline publicado

Todos os pipelines publicados têm um ponto de extremidade REST. Com o ponto de extremidade do pipeline, você pode disparar uma execução do pipeline de qualquer sistema externo, incluindo clientes não Python. Esse ponto de extremidade permite a "repetibilidade gerenciada" em cenários de retreinamento e pontuação de lote.

> [!IMPORTANT]
> Se você estiver usando o controle de acesso baseado em função do Azure (RBAC do Azure) para gerenciar o acesso ao seu pipeline, [defina as permissões para seu cenário de pipeline (treinamento ou pontuação)](how-to-assign-roles.md#common-scenarios).

Para invocar a execução do pipeline anterior, você precisa de um token de cabeçalho de autenticação Azure Active Directory. Obter esse token é descrito na referência da [classe AzureCliAuthentication](/python/api/azureml-core/azureml.core.authentication.azurecliauthentication) e na [autenticação no Azure Machine Learning](https://aka.ms/pl-restep-auth) notebook.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

O `json` argumento para a solicitação POST deve conter, para a `ParameterAssignments` chave, um dicionário que contém os parâmetros de pipeline e seus valores. Além disso, o `json` argumento pode conter as seguintes chaves:

| Chave | Descrição |
| --- | --- | 
| `ExperimentName` | O nome do experimento associado a este ponto de extremidade |
| `Description` | Texto de forma livre que descreve o ponto de extremidade | 
| `Tags` | Pares chave-valor de forma livre que podem ser usados para rotular e anotar solicitações  |
| `DataSetDefinitionValueAssignments` | Dicionário usado para alterar conjuntos de valores sem novo treinamento (consulte a discussão abaixo) | 
| `DataPathAssignments` | Dicionário usado para alterar os caminhos de caminho sem novo treinamento (consulte a discussão abaixo) | 

### <a name="run-a-published-pipeline-using-c"></a>Executar um pipeline publicado usando C # 

O código a seguir mostra como chamar um pipeline de forma assíncrona do C#. O trecho de código parcial mostra apenas a estrutura de chamada e não faz parte de um exemplo da Microsoft. Ele não mostra as classes completas ou o tratamento de erros. 

```csharp
[DataContract]
public class SubmitPipelineRunRequest
{
    [DataMember]
    public string ExperimentName { get; set; }

    [DataMember]
    public string Description { get; set; }

    [DataMember(IsRequired = false)]
    public IDictionary<string, string> ParameterAssignments { get; set; }
}

// ... in its own class and method ... 
const string RestEndpoint = "your-pipeline-endpoint";

using (HttpClient client = new HttpClient())
{
    var submitPipelineRunRequest = new SubmitPipelineRunRequest()
    {
        ExperimentName = "YourExperimentName", 
        Description = "Asynchronous C# REST api call", 
        ParameterAssignments = new Dictionary<string, string>
        {
            {
                // Replace with your pipeline parameter keys and values
                "your-pipeline-parameter", "default-value"
            }
        }
    };

    string auth_key = "your-auth-key"; 
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", auth_key);

    // submit the job
    var requestPayload = JsonConvert.SerializeObject(submitPipelineRunRequest);
    var httpContent = new StringContent(requestPayload, Encoding.UTF8, "application/json");
    var submitResponse = await client.PostAsync(RestEndpoint, httpContent).ConfigureAwait(false);
    if (!submitResponse.IsSuccessStatusCode)
    {
        await WriteFailedResponse(submitResponse); // ... method not shown ...
        return;
    }

    var result = await submitResponse.Content.ReadAsStringAsync().ConfigureAwait(false);
    var obj = JObject.Parse(result);
    // ... use `obj` dictionary to access results
}
```

### <a name="run-a-published-pipeline-using-java"></a>Executar um pipeline publicado usando Java

O código a seguir mostra uma chamada para um pipeline que requer autenticação (consulte [Configurar a autenticação para Azure Machine Learning recursos e fluxos de trabalho](how-to-setup-authentication.md)). Se seu pipeline for implantado publicamente, você não precisará das chamadas que produzem `authKey` . O trecho de código parcial não mostra a classe Java e o clichê do tratamento de exceções. O código usa `Optional.flatMap` para encadear funções que podem retornar um vazio `Optional` . O uso de `flatMap` encurta e esclarece o código, mas observe que o `getRequestBody()` assimila exceções.

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.Optional;
// JSON library
import com.google.gson.Gson;

String scoringUri = "scoring-endpoint";
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";
String dataToBeScored = "{ \"ExperimentName\" : \"My_Pipeline\", \"ParameterAssignments\" : { \"pipeline_arg\" : \"20\" }}";

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);
Optional<String> authBody = getRequestBody(client, tokenAuthenticationRequest);
Optional<String> authKey = authBody.flatMap(body -> Optional.of(gson.fromJson(body, AuthenticationBody.class).access_token);;
Optional<HttpRequest> scoringRequest = authKey.flatMap(key -> Optional.of(scoringRequest(key, scoringUri, dataToBeScored)));
Optional<String> scoringResult = scoringRequest.flatMap(req -> getRequestBody(client, req));
// ... etc (`scoringResult.orElse()`) ... 

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl)
{
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(authUrl))
        .POST(HttpRequest.BodyPublishers.ofString(bodyString))
        .build();
    return request;
}

static HttpRequest scoringRequest(String authKey, String scoringUri, String dataToBeScored)
{
    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(scoringUri))
        .header("Authorization", String.format("Token %s", authKey))
        .POST(HttpRequest.BodyPublishers.ofString(dataToBeScored))
        .build();
    return request;

}

static Optional<String> getRequestBody(HttpClient client, HttpRequest request) {
    try {
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        if (response.statusCode() != 200) {
            System.out.println(String.format("Unexpected server response %d", response.statusCode()));
            return Optional.empty();
        }
        return Optional.of(response.body());
    }catch(Exception x)
    {
        System.out.println(x.toString());
        return Optional.empty();
    }
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

### <a name="changing-datasets-and-datapaths-without-retraining"></a>Alterando conjuntos de valores e caminhos de caminho sem readaptação

Talvez você queira treinar e inferência em diferentes conjuntos de valores e caminhos de data. Por exemplo, você pode desejar treinar em um conjunto de um DataSet menor, mas inferência no conjunto de conjuntos completo. Você alterna os conjuntos de os com a `DataSetDefinitionValueAssignments` chave no argumento da solicitação `json` . Você alterna os caminhos de computador com `DataPathAssignments` . A técnica para ambos é semelhante:

1. No script de definição de pipeline, crie um `PipelineParameter` para o conjunto de um. Crie um `DatasetConsumptionConfig` ou `DataPath` no `PipelineParameter` :

    ```python
    tabular_dataset = Dataset.Tabular.from_delimited_files('https://dprepdata.blob.core.windows.net/demo/Titanic.csv')
    tabular_pipeline_param = PipelineParameter(name="tabular_ds_param", default_value=tabular_dataset)
    tabular_ds_consumption = DatasetConsumptionConfig("tabular_dataset", tabular_pipeline_param)
    ```

1. Em seu script ML, acesse o conjunto de os especificado dinamicamente usando `Run.get_context().input_datasets` :

    ```python
    from azureml.core import Run
    
    input_tabular_ds = Run.get_context().input_datasets['tabular_dataset']
    dataframe = input_tabular_ds.to_pandas_dataframe()
    # ... etc ...
    ```

    Observe que o script ML acessa o valor especificado para o `DatasetConsumptionConfig` ( `tabular_dataset` ) e não o valor de `PipelineParameter` ( `tabular_ds_param` ).

1. Em seu script de definição de pipeline, defina `DatasetConsumptionConfig` como um parâmetro para `PipelineScriptStep` :

    ```python
    train_step = PythonScriptStep(
        name="train_step",
        script_name="train_with_dataset.py",
        arguments=["--param1", tabular_ds_consumption],
        inputs=[tabular_ds_consumption],
        compute_target=compute_target,
        source_directory=source_directory)
    
    pipeline = Pipeline(workspace=ws, steps=[train_step])
    ```

1. Para alternar conjuntos de itens dinamicamente em sua chamada REST inferência, use `DataSetDefinitionValueAssignments` :
    
    ```python
    tabular_ds1 = Dataset.Tabular.from_delimited_files('path_to_training_dataset')
    tabular_ds2 = Dataset.Tabular.from_delimited_files('path_to_inference_dataset')
    ds1_id = tabular_ds1.id
    d22_id = tabular_ds2.id
    
    response = requests.post(rest_endpoint, 
                             headers=aad_token, 
                             json={
                                "ExperimentName": "MyRestPipeline",
                               "DataSetDefinitionValueAssignments": {
                                    "tabular_ds_param": {
                                        "SavedDataSetReference": {"Id": ds1_id #or ds2_id
                                    }}}})
    ```

Os notebooks que apresentam o [conjunto de e-PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb) e a apresentação do caminho de banco de e [PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) têm exemplos completos dessa técnica.

## <a name="create-a-versioned-pipeline-endpoint"></a>Criar um ponto de extremidade de pipeline com versão

Você pode criar um ponto de extremidade de pipeline com vários pipelines publicados por trás dele. Essa técnica fornece um ponto de extremidade REST fixo conforme você itera e atualiza seus pipelines de ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>Enviar um trabalho para um ponto de extremidade de pipeline

Você pode enviar um trabalho para a versão padrão de um ponto de extremidade de pipeline:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

Você também pode enviar um trabalho para uma versão específica:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

O mesmo pode ser feito usando a API REST:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>Usar pipelines publicados no estúdio

Você também pode executar um pipeline publicado no estúdio:

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com).

1. [Exiba seu espaço de trabalho](how-to-manage-workspace.md#view).

1. À esquerda, selecione **pontos de extremidade**.

1. Na parte superior, selecione **pontos de extremidade do pipeline**.
 ![lista de pipelines publicados do Machine Learning](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Selecione um pipeline específico para executar, consumir ou examinar os resultados das execuções anteriores do ponto de extremidade do pipeline.

## <a name="disable-a-published-pipeline"></a>Desabilitar um pipeline publicado

Para ocultar um pipeline da lista de pipelines publicados, você o desabilita, no estúdio ou no SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Você pode habilitá-lo novamente com `p.enable()` . Para obter mais informações, consulte referência de [classe PublishedPipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline) .

## <a name="next-steps"></a>Próximas etapas

- Use [esses Jupyter Notebooks no GitHub](https://aka.ms/aml-pipeline-readme) para explorar ainda mais pipelines de machine learning.
- Consulte a ajuda de referência do SDK para o pacote [azureml-pipelines-Core](/python/api/azureml-pipeline-core/) e o pacote [azureml-pipelines-Steps](/python/api/azureml-pipeline-steps/) .
- Consulte a [instruções](how-to-debug-pipelines.md) para obter dicas sobre como depurar e solucionar problemas de pipelines.