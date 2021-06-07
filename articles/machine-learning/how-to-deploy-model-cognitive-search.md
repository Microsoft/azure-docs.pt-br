---
title: Implantar um modelo para ser usado com Cognitive Search
titleSuffix: Azure Machine Learning
description: Saiba como usar Azure Machine Learning para implantar um modelo para uso com Pesquisa Cognitiva. O modelo é usado como uma habilidade personalizada para enriquecer a experiência de pesquisa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.reviewer: larryfr
ms.date: 03/11/2021
ms.custom: deploy
ms.openlocfilehash: 22c8880cbcde1f1a55fa66beee0323e0348e1164
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149606"
---
# <a name="deploy-a-model-for-use-with-cognitive-search"></a>Implantar um modelo para ser usado com Cognitive Search


Este artigo ensina como usar Azure Machine Learning para implantar um modelo para uso com o [pesquisa cognitiva do Azure](../search/search-what-is-azure-search.md).

Pesquisa Cognitiva executa o processamento de conteúdo sobre conteúdo heterogêneo, para torná-lo passível de consulta por seres humanos ou aplicativos. Esse processo pode ser aprimorado usando um modelo implantado de Azure Machine Learning.

Azure Machine Learning pode implantar um modelo treinado como um serviço Web. O serviço Web é inserido em uma Pesquisa Cognitiva _habilidade_, que se torna parte do pipeline de processamento.

> [!IMPORTANT]
> As informações neste artigo são específicas para a implantação do modelo. Ele fornece informações sobre as configurações de implantação com suporte que permitem que o modelo seja usado pelo Pesquisa Cognitiva.
>
> Para obter informações sobre como configurar Pesquisa Cognitiva para usar o modelo implantado, consulte o tutorial [criar e implantar uma habilidade personalizada com Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md) .
>
> Para obter o exemplo no qual o tutorial se baseia, consulte [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

Ao implantar um modelo para uso com o Azure Pesquisa Cognitiva, a implantação deve atender aos seguintes requisitos:

* Use o serviço kubernetes do Azure para hospedar o modelo para a inferência.
* Habilite o protocolo TLS para o serviço kubernetes do Azure. O TLS é usado para proteger comunicações HTTPS entre Pesquisa Cognitiva e o modelo implantado.
* O script de entrada deve usar o `inference_schema` pacote para gerar um esquema openapi (Swagger) para o serviço.
* O script de entrada também deve aceitar dados JSON como entrada e gerar JSON como saída.


## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).

* Um ambiente de desenvolvimento do Python com o SDK do Azure Machine Learning instalado. Para obter mais informações, consulte [Azure Machine Learning SDK](/python/api/overview/azure/ml/install).  

* Um modelo registrado. Se você não tiver um modelo, use o bloco de anotações de exemplo em [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

* Uma compreensão geral de [como e onde implantar modelos](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Conectar-se ao workspace

Um espaço de trabalho Azure Machine Learning fornece um local centralizado para trabalhar com todos os artefatos que você cria ao usar o Azure Machine Learning. O espaço de trabalho mantém um histórico de todas as execuções de treinamento, incluindo logs, métricas, saída e um instantâneo de seus scripts.

Para se conectar a um espaço de trabalho existente, use o seguinte código:

> [!IMPORTANT]
> Esse snippet de código espera que a configuração do workspace seja salva no diretório atual ou no pai dele. Para obter mais informações, consulte [criar e gerenciar espaços de trabalho do Azure Machine Learning](how-to-manage-workspace.md). Para obter mais informações sobre como salvar a configuração no arquivo, confira [Criar um arquivo de configuração de workspace](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

try:
    # Load the workspace configuration from local cached inffo
    ws = Workspace.from_config()
    print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
    print('Library configuration succeeded')
except:
    print('Workspace not found')
```

## <a name="create-a-kubernetes-cluster"></a>Criar um cluster do Kubernetes

**Tempo estimado**: aproximadamente 20 minutos.

Um cluster kubernetes é um conjunto de instâncias de máquina virtual (chamadas de nós) que são usadas para executar aplicativos em contêineres.

Quando você implanta um modelo de Azure Machine Learning para o serviço kubernetes do Azure, o modelo e todos os ativos necessários para hospedá-lo como um serviço Web são empacotados em um contêiner do Docker. Esse contêiner é então implantado no cluster.

O código a seguir demonstra como criar um novo cluster AKS (serviço kubernetes do Azure) para seu espaço de trabalho:

> [!TIP]
> Você também pode anexar um serviço de kubernetes do Azure existente ao seu espaço de trabalho Azure Machine Learning. Para obter mais informações, consulte [como implantar modelos no serviço kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md).

> [!IMPORTANT]
> Observe que o código usa o `enable_ssl()` método para habilitar o protocolo TLS para o cluster. Isso é necessário quando você planeja usar o modelo implantado de serviços cognitivas.

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Create or attach to an AKS inferencing cluster

# Create the provisioning configuration with defaults
prov_config = AksCompute.provisioning_configuration()

# Enable TLS (sometimes called SSL) communications
# Leaf domain label generates a name using the formula
#  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
#  where "######" is a random series of characters
prov_config.enable_ssl(leaf_domain_label = "contoso")

cluster_name = 'amlskills'
# Try to use an existing compute target by that name.
# If one doesn't exist, create one.
try:
    
    aks_target = ComputeTarget(ws, cluster_name)
    print("Attaching to existing cluster")
except Exception as e:
    print("Creating new cluster")
    aks_target = ComputeTarget.create(workspace = ws, 
                                  name = cluster_name, 
                                  provisioning_configuration = prov_config)
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> O Azure cobrará você desde que o cluster AKS exista. Certifique-se de excluir o cluster AKS quando tiver terminado.

Para obter mais informações sobre como usar o AKS com Azure Machine Learning, consulte [como implantar no serviço kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Gravar o script de entrada

O script de entrada recebe dados enviados para o serviço Web, passa-os para o modelo e retorna os resultados da pontuação. O script a seguir carrega o modelo na inicialização e, em seguida, usa o modelo para pontuar dados. Esse arquivo é chamado às vezes `score.py` .

> [!TIP]
> O script de entrada é específico para cada modelo. Por exemplo, o script deve saber a estrutura a ser usada com seu modelo, formatos de dados, etc.

> [!IMPORTANT]
> Ao planejar o uso do modelo implantado dos serviços cognitivas do Azure, você deve usar o `inference_schema` pacote para habilitar a geração de esquema para a implantação. Esse pacote fornece decoradores que permitem que você defina o formato de dados de entrada e saída para o serviço Web que executa a inferência usando o modelo.

```python
from azureml.core.model import Model
from nlp_architect.models.absa.inference.inference import SentimentInference
from spacy.cli.download import download as spacy_download
import traceback
import json
# Inference schema for schema discovery
from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType

def init():
    """
    Set up the ABSA model for Inference  
    """
    global SentInference
    spacy_download('en')
    aspect_lex = Model.get_model_path('hotel_aspect_lex')
    opinion_lex = Model.get_model_path('hotel_opinion_lex') 
    SentInference = SentimentInference(aspect_lex, opinion_lex)

# Use inference schema decorators and sample input/output to
# build the OpenAPI (Swagger) schema for the deployment
standard_sample_input = {'text': 'a sample input record containing some text' }
standard_sample_output = {"sentiment": {"sentence": "This place makes false booking prices, when you get there, they say they do not have the reservation for that day.", 
                                        "terms": [{"text": "hotels", "type": "AS", "polarity": "POS", "score": 1.0, "start": 300, "len": 6}, 
                                                  {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 295, "len": 4}]}}
@input_schema('raw_data', StandardPythonParameterType(standard_sample_input))
@output_schema(StandardPythonParameterType(standard_sample_output))    
def run(raw_data):
    try:
        # Get the value of the 'text' field from the JSON input and perform inference
        input_txt = raw_data["text"]
        doc = SentInference.run(doc=input_txt)
        if doc is None:
            return None
        sentences = doc._sentences
        result = {"sentence": doc._doc_text}
        terms = []
        for sentence in sentences:
            for event in sentence._events:
                for x in event:
                    term = {"text": x._text, "type":x._type.value, "polarity": x._polarity.value, "score": x._score,"start": x._start,"len": x._len }
                    terms.append(term)
        result["terms"] = terms
        print("Success!")
        # Return the results to the client as a JSON document
        return {"sentiment": result}
    except Exception as e:
        result = str(e)
        # return error message back to the client
        print("Failure!")
        print(traceback.format_exc())
        return json.dumps({"error": result, "tb": traceback.format_exc()})
```

Para obter mais informações sobre scripts de entrada, consulte [como e onde implantar](how-to-deploy-and-where.md).

## <a name="define-the-software-environment"></a>Definir o ambiente de software

A classe de ambiente é usada para definir as dependências do Python para o serviço. Ele inclui dependências exigidas pelo modelo e pelo script de entrada. Neste exemplo, ele instala pacotes do índice PyPI regular, bem como de um repositório GitHub. 

```python
from azureml.core.conda_dependencies import CondaDependencies 
from azureml.core import Environment

conda = None
pip = ["azureml-defaults", "azureml-monitoring", 
       "git+https://github.com/NervanaSystems/nlp-architect.git@absa", 'nlp-architect', 'inference-schema',
       "spacy==2.0.18"]

conda_deps = CondaDependencies.create(conda_packages=None, pip_packages=pip)

myenv = Environment(name='myenv')
myenv.python.conda_dependencies = conda_deps
```

Para obter mais informações sobre ambientes, consulte [criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md).

## <a name="define-the-deployment-configuration"></a>Definir a configuração de implantação

A configuração de implantação define o ambiente de hospedagem do serviço kubernetes do Azure usado para executar o serviço Web.

> [!TIP]
> Se você não tiver certeza sobre a memória, a CPU ou as necessidades de GPU de sua implantação, poderá usar a criação de perfil para aprender isso. Para obter mais informações, consulte [como e onde implantar um modelo](how-to-deploy-and-where.md).

```python
from azureml.core.model import Model
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage
from azureml.core.webservice import AksWebservice, Webservice

# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                       autoscale_min_replicas=1, 
                                                       autoscale_max_replicas=3, 
                                                       autoscale_refresh_seconds=10, 
                                                       autoscale_target_utilization=70,
                                                       auth_enabled=True, 
                                                       cpu_cores=1, memory_gb=2, 
                                                       scoring_timeout_ms=5000, 
                                                       replica_max_concurrent_requests=2, 
                                                       max_request_wait_time=5000)
```

Para obter mais informações, consulte a documentação de referência para [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definir a configuração de inferência

A configuração de inferência aponta para o script de entrada e o objeto de ambiente:

```python
from azureml.core.model import InferenceConfig
inf_config = InferenceConfig(entry_script='score.py', environment=myenv)
```

Para obter mais informações, consulte a documentação de referência para [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig).

## <a name="deploy-the-model"></a>Implantar o modelo

Implante o modelo em seu cluster AKS e aguarde até que ele crie seu serviço. Neste exemplo, dois modelos registrados são carregados do registro e implantados no AKS. Após a implantação, o `score.py` arquivo na implantação carrega esses modelos e os utiliza para executar a inferência.

```python
from azureml.core.webservice import AksWebservice, Webservice

c_aspect_lex = Model(ws, 'hotel_aspect_lex')
c_opinion_lex = Model(ws, 'hotel_opinion_lex') 
service_name = "hotel-absa-v2"

aks_service = Model.deploy(workspace=ws,
                           name=service_name,
                           models=[c_aspect_lex, c_opinion_lex],
                           inference_config=inf_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           overwrite=True)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

Para obter mais informações, consulte a documentação de referência para o [modelo](/python/api/azureml-core/azureml.core.model.model).

## <a name="issue-a-sample-query-to-your-service"></a>Emitir uma consulta de exemplo para seu serviço

O exemplo a seguir usa as informações de implantação armazenadas na `aks_service` variável pela seção de código anterior. Ele usa essa variável para recuperar a URL de Pontuação e o token de autenticação necessários para se comunicar com o serviço:

```python
import requests
import json

primary, secondary = aks_service.get_keys()

# Test data
input_data = '{"raw_data": {"text": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend."}}'

# Since authentication was enabled for the deployment, set the authorization header.
headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ primary)} 

# Send the request and display the results
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)
print(resp.text)
```

O resultado retornado do serviço é semelhante ao JSON a seguir:

```json
{"sentiment": {"sentence": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend.", "terms": [{"text": "place", "type": "AS", "polarity": "POS", "score": 1.0, "start": 15, "len": 5}, {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 10, "len": 4}]}}
```

## <a name="connect-to-cognitive-search"></a>Conectar-se ao Pesquisa Cognitiva

Para obter informações sobre como usar esse modelo de Pesquisa Cognitiva, consulte o tutorial [criar e implantar uma habilidade personalizada com Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md) .

## <a name="clean-up-the-resources"></a>Limpar os recursos

Se você criou o cluster AKS especificamente para este exemplo, exclua seus recursos depois de terminar de testá-lo com Pesquisa Cognitiva.

> [!IMPORTANT]
> O Azure cobra com base em quanto tempo o cluster AKS é implantado. Certifique-se de limpá-lo depois de concluir a tarefa.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Próximas etapas

* [Criar e implantar uma habilidade personalizada com Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md)