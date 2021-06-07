---
title: Implantar modelos de ML no serviço kubernetes
titleSuffix: Azure Machine Learning
description: Saiba como implantar seus modelos de Azure Machine Learning como um serviço Web usando o serviço kubernetes do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: ef9c03b687bbc9b8fe736c872bbde14b8daba899
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519377"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Implantar um modelo em um cluster do serviço kubernetes do Azure

Saiba como usar Azure Machine Learning para implantar um modelo como um serviço Web no AKS (serviço kubernetes do Azure). O serviço kubernetes do Azure é bom para implantações de produção de grande escala. Use o serviço kubernetes do Azure se você precisar de um ou mais dos seguintes recursos:

- __Tempo de resposta rápido__
- __Dimensionamento__ automático do serviço implantado
- __Logging__
- __Coleta de dados de modelo__
- __Autenticação__
- __Encerramento de TLS__
- Opções de __aceleração de hardware__ , como a GPU e as FPGA (matrizes de porta programável por campo)

Ao implantar no serviço kubernetes do Azure, você implanta em um cluster AKS que está __conectado ao seu espaço de trabalho__. Para obter informações sobre como conectar um cluster AKS ao seu espaço de trabalho, consulte [criar e anexar um cluster do serviço kubernetes do Azure](how-to-create-attach-kubernetes.md).

> [!IMPORTANT]
> Recomendamos que você depure localmente antes de implantar no serviço Web. Para obter mais informações, consulte [depurar localmente](./how-to-troubleshoot-deployment-local.md)
>
> Você também pode ver o Azure Machine Learning - [Implantar no notebook local](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Pré-requisitos

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).

- Um modelo de aprendizado de máquina registrado em seu espaço de trabalho. Se você não tiver um modelo registrado, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

- A [extensão CLI do Azure para o serviço Machine Learning](reference-azure-machine-learning-cli.md), o [SDK do Azure Machine Learning Python](/python/api/overview/azure/ml/intro)ou a [extensão Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Os trechos de código __Python__ neste artigo pressupõem que as seguintes variáveis sejam definidas:

    * `ws` -Defina para seu espaço de trabalho.
    * `model` -Defina para o modelo registrado.
    * `inference_config` – Defina para a configuração de inferência para o modelo.

    Para obter mais informações sobre como definir essas variáveis, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

- Os trechos de código da __CLI__ neste artigo pressupõem que você criou um `inferenceconfig.json` documento. Para obter mais informações sobre como criar este documento, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

- Um cluster do serviço kubernetes do Azure conectado ao seu espaço de trabalho. Para obter mais informações, consulte [criar e anexar um cluster do serviço kubernetes do Azure](how-to-create-attach-kubernetes.md).

    - Se você quiser implantar modelos em nós de GPU ou nós FPGA (ou qualquer SKU específico), deverá criar um cluster com o SKU específico. Não há suporte para a criação de um pool de nós secundário em um cluster existente e a implantação de modelos no pool de nós secundário.

## <a name="understand-the-deployment-processes"></a>Entender os processos de implantação

A palavra "implantação" é usada em kubernetes e em Azure Machine Learning. "Implantação" tem significados diferentes nesses dois contextos. No kubernetes, uma `Deployment` é uma entidade concreta, especificada com um arquivo YAML declarativo. Um kubernetes `Deployment` tem um ciclo de vida definido e relações concretas com outras entidades kubernetes, como `Pods` e `ReplicaSets` . Você pode aprender sobre o kubernetes de documentos e vídeos em [o que é o kubernetes?](https://aka.ms/k8slearning).

No Azure Machine Learning, a "implantação" é usada no sentido mais geral de disponibilizar e limpar os recursos do projeto. As etapas que Azure Machine Learning consideram parte da implantação são:

1. Compactando os arquivos na pasta do projeto, ignorando aqueles especificados em. amlignore ou. gitignore
1. Escalar verticalmente o cluster de cálculo (relacionado a kubernetes)
1. Criando ou baixando o dockerfile para o nó de computação (relacionado ao kubernetes)
    1. O sistema calcula um hash de: 
        - A imagem base 
        - Etapas personalizadas do Docker (consulte [implantar um modelo usando uma imagem de base do Docker personalizada](./how-to-deploy-custom-docker-image.md))
        - A definição de Conda YAML (consulte [criar & usar ambientes de software em Azure Machine Learning](./how-to-use-environments.md))
    1. O sistema usa esse hash como a chave em uma pesquisa do ACR (registro de contêiner do Azure) do espaço de trabalho
    1. Se não for encontrado, ele procurará uma correspondência no ACR global
    1. Se não for encontrado, o sistema criará uma nova imagem (que será armazenada em cache e enviada por push para o ACR do espaço de trabalho)
1. Baixando seu arquivo de projeto compactado para armazenamento temporário no nó de computação
1. Descompactando o arquivo de projeto
1. O nó de computação em execução `python <entry script> <arguments>`
1. Salvando logs, arquivos de modelo e outros arquivos gravados na `./outputs` conta de armazenamento associada ao espaço de trabalho
1. Reduzindo a computação, incluindo a remoção de armazenamento temporário (relacionado a kubernetes)

### <a name="azure-ml-router"></a>Roteador do Azure ML

O componente de front-end (azureml-FE) que roteia as solicitações de inferência de entrada para os serviços implantados dimensiona automaticamente conforme necessário. O dimensionamento do azureml-Fe é baseado na finalidade e no tamanho do cluster AKS (número de nós). A finalidade do cluster e os nós são configurados quando você [cria ou anexa um cluster AKs](how-to-create-attach-kubernetes.md). Há um serviço azureml-FE por cluster, que pode estar em execução em vários pods.

> [!IMPORTANT]
> Ao usar um cluster configurado como __desenvolvimento/teste__, o autodimensionador é **desabilitado**.

O Azureml-Fe é dimensionado verticalmente para usar mais núcleos e out (horizontalmente) para usar mais pods. Ao tomar a decisão de escalar verticalmente, o tempo necessário para rotear solicitações de inferência de entrada é usado. Se esse tempo exceder o limite, ocorrerá uma escala vertical. Se o tempo para rotear solicitações de entrada continuar a exceder o limite, ocorrerá uma escala horizontal.

Ao escalar verticalmente e horizontalmente, o uso da CPU é usado. Se o limite de uso da CPU for atingido, o front-end será reduzido primeiro. Se o uso da CPU cair para o limite de redução, ocorrerá uma operação de redução horizontal. Escalar verticalmente só ocorrerá se houver recursos de cluster suficientes disponíveis.

## <a name="understand-connectivity-requirements-for-aks-inferencing-cluster"></a>Entenda os requisitos de conectividade para o cluster de inferência do AKS

Quando Azure Machine Learning cria ou anexa um cluster AKS, o cluster AKS é implantado com um dos dois modelos de rede a seguir:
* Rede Kubenet – os recursos de rede normalmente são criados e configurados à medida que o cluster AKS é implantado.
* Rede da CNI (Interface de rede de contêiner) do Azure – o cluster AKS é conectado a recursos e configurações de rede virtual existentes.

Para o primeiro modo de rede, a rede é criada e configurada corretamente para Azure Machine Learning serviço. Para o segundo modo de rede, como o cluster está conectado à rede virtual existente, especialmente quando o DNS personalizado é usado para a rede virtual existente, o cliente precisa prestar atenção extra aos requisitos de conectividade para o cluster AKS inferência e garantir a resolução de DNS e a conectividade de saída para AKS inferência.

O diagrama a seguir captura todos os requisitos de conectividade para AKS inferência. As setas pretas representam a comunicação real e as setas azuis representam os nomes de domínio, que o DNS controlado pelo cliente deve resolver.

 ![Requisitos de conectividade para AKS inferência](./media/how-to-deploy-aks/aks-network.png)

### <a name="overall-dns-resolution-requirements"></a>Requisitos gerais de resolução de DNS
A resolução DNS na VNET existente está sob o controle do cliente. As seguintes entradas DNS devem ser resolvidas:
* Servidor de API AKS na forma de \<cluster\> . HCP. \<region\> . azmk8s.io
* MCR (registro de contêiner da Microsoft): mcr.microsoft.com
* O registro de contêiner do Azure do cliente (ARC) na forma de \<ACR name\> . azurecr.Io
* Conta de armazenamento do Azure na forma de \<account\> . Table.Core.Windows.net e \<account\> . blob.Core.Windows.net
* Adicional Para autenticação do AAD: api.azureml.ms
* Nome de domínio do ponto de extremidade de pontuação, gerado automaticamente pelo Azure ML ou nome de domínio personalizado. O nome de domínio gerado automaticamente teria a seguinte aparência: \<leaf-domain-label \+ auto-generated suffix\> . \<region\> . cloudapp.azure.com

### <a name="connectivity-requirements-in-chronological-order-from-cluster-creation-to-model-deployment"></a>Requisitos de conectividade em ordem cronológica: da criação do cluster para a implantação de modelo

No processo de criação ou anexação de AKS, o roteador am do Azure (azureml-FE) é implantado no cluster AKS. Para implantar o roteador do Azure ML, o nó AKS deve ser capaz de:
* Resolver o DNS para o servidor de API AKS
* Resolver o DNS para MCR a fim de baixar imagens do Docker para o roteador do Azure ML
* Baixar imagens do MCR, onde a conectividade de saída é necessária

Logo após o azureml-FE ser implantado, ele tentará iniciar e isso exigirá:
* Resolver o DNS para o servidor de API AKS
* Consultar o servidor de API do AKS para descobrir outras instâncias de si mesmo (é um serviço multipod)
* Conectar-se a outras instâncias de si mesmo

Depois que o azureml-FE for iniciado, ele exigirá conectividade adicional para funcionar corretamente:
* Conectar-se ao armazenamento do Azure para baixar a configuração dinâmica
* Resolva o DNS para o servidor de autenticação do AAD api.azureml.ms e comunique-se com ele quando o serviço implantado usar a autenticação do AAD.
* Consultar o servidor de API do AKS para descobrir modelos implantados
* Comunicar-se com o PODs do modelo implantado

No momento da implantação do modelo, para um nó AKS de implantação de modelo bem-sucedido deve ser capaz de: 
* Resolver o DNS para o ACR do cliente
* Baixar imagens do ACR do cliente
* Resolver o DNS para BLOBs do Azure em que o modelo é armazenado
* Baixar modelos de BLOBs do Azure

Depois que o modelo for implantado e o serviço for iniciado, o azureml-FE o descobrirá automaticamente usando a API do AKS e estará pronto para rotear a solicitação para ele. Ele deve ser capaz de se comunicar com os PODs de modelo.
>[!Note]
>Se o modelo implantado exigir conectividade (por exemplo, consultar o banco de dados externo ou outro serviço REST, baixar um BLOG, etc.), a resolução de DNS e a comunicação de saída para esses serviços deverão ser habilitadas.

## <a name="deploy-to-aks"></a>Implantar no AKS

Para implantar um modelo no serviço kubernetes do Azure, crie uma __configuração de implantação__ que descreva os recursos de computação necessários. Por exemplo, número de núcleos e memória. Você também precisa de uma __configuração de inferência__, que descreve o ambiente necessário para hospedar o modelo e o serviço Web. Para obter mais informações sobre como criar a configuração de inferência, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

> [!NOTE]
> O número de modelos a serem implantados é limitado a modelos de 1.000 por implantação (por contêiner).

<a id="using-the-cli"></a>

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Para obter mais informações sobre as classes, os métodos e os parâmetros usados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute)
* [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration)
* [Modelo. implantar](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para implantar usando a CLI, use o comando a seguir. Substitua `myaks` pelo nome do destino de computação AKs. Substituir `mymodel:1` pelo nome e versão do modelo registrado. Substitua `myservice` pelo nome para fornecer este serviço:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Para obter mais informações, consulte a referência de [implantação do modelo AZ ml](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Para obter informações sobre como usar VS Code, consulte [implantar no AKs por meio da extensão vs Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> A implantação por meio do VS Code requer que o cluster AKS seja criado ou anexado ao seu espaço de trabalho com antecedência.

---

### <a name="autoscaling"></a>Dimensionamento automático

O componente que lida com o dimensionamento automático para implantações de modelo do Azure ML é o azureml-Fe, que é um roteador de solicitação inteligente. Como todas as solicitações de inferência passam por ela, ela tem os dados necessários para dimensionar automaticamente os modelos implantados.

> [!IMPORTANT]
> * **Não habilite o kubernetes horizontal Pod AutoScaler (hPa) para implantações de modelo**. Fazer isso faria com que os dois componentes de dimensionamento automático concorram uns com os outros. O Azureml-FE foi projetado para dimensionar automaticamente os modelos implantados pelo Azure ML, em que HPA teria de adivinhar ou de utilização de modelo aproximada de uma métrica genérica, como o uso da CPU ou uma configuração de métrica personalizada.
> 
> * **O Azureml-FE não dimensiona o número de nós em um cluster AKs**, pois isso pode levar a aumentos de custos inesperados. Em vez disso, **ele dimensiona o número de réplicas para o modelo** dentro dos limites do cluster físico. Se você precisar dimensionar o número de nós dentro do cluster, poderá dimensionar manualmente o cluster ou [Configurar o dimensionamento automática do cluster AKs](../aks/cluster-autoscaler.md).

O dimensionamento automático pode ser controlado por configuração `autoscale_target_utilization` , `autoscale_min_replicas` e `autoscale_max_replicas` para o serviço Web AKs. O exemplo a seguir demonstra como habilitar o dimensionamento automático:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

As decisões de expansão/redução baseiam-se na utilização das réplicas de contêiner atuais. O número de réplicas ocupadas (processando uma solicitação) dividido pelo número total de réplicas atuais é a utilização atual. Se esse número exceder `autoscale_target_utilization` , mais réplicas serão criadas. Se for menor, as réplicas serão reduzidas. Por padrão, a utilização de destino é de 70%.

As decisões para adicionar réplicas são ansiosos e rápidas (cerca de 1 segundo). As decisões para remover réplicas são conservadoras (cerca de 1 minuto).

Você pode calcular as réplicas necessárias usando o seguinte código:

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

Para obter mais informações sobre `autoscale_target_utilization` a configuração, `autoscale_max_replicas` e `autoscale_min_replicas` , consulte a referência do módulo [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice) .

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Implantar modelos no AKS usando a distribuição controlada (versão prévia)

Analise e promova versões de modelo de maneira controlada usando pontos de extremidade. Você pode implantar até seis versões por trás de um único ponto de extremidade. Os pontos de extremidade fornecem os seguintes recursos:

* Configure a __porcentagem de tráfego de pontuação enviada para cada ponto de extremidade__. Por exemplo, encaminhe 20% do tráfego para o ponto de extremidade ' test ' e 80% para ' Production '.

    > [!NOTE]
    > Se você não considerar 100% do tráfego, qualquer porcentagem restante será roteada para a versão do ponto de extremidade __padrão__ . Por exemplo, se você configurar a versão de ponto de extremidade ' teste ' para obter 10% do tráfego e ' prod ' para 30%, o% de 60% restante será enviado para a versão de ponto de extremidade padrão.
    >
    > A primeira versão de ponto de extremidade criada é configurada automaticamente como o padrão. Você pode alterar isso definindo `is_default=True` ao criar ou atualizar uma versão de ponto de extremidade.
     
* Marque uma versão de ponto de extremidade como __controle__ ou __tratamento__. Por exemplo, a versão do ponto de extremidade de produção atual pode ser o controle, enquanto novos modelos potenciais são implantados como versões de tratamento. Depois de avaliar o desempenho das versões de tratamento, se um supera o controle atual, ele pode ser promovido para a nova produção/controle.

    > [!NOTE]
    > Você só pode ter __um__ controle. Você pode ter vários tratamentos.

Você pode habilitar o app insights para exibir as métricas operacionais de pontos de extremidade e versões implantadas.

### <a name="create-an-endpoint"></a>Criar um ponto de extremidade
Quando você estiver pronto para implantar seus modelos, crie um ponto de extremidade de Pontuação e implante sua primeira versão. O exemplo a seguir mostra como implantar e criar o ponto de extremidade usando o SDK. A primeira implantação será definida como a versão padrão, o que significa que o percentil de tráfego não especificado em todas as versões vai para a versão padrão.  

> [!TIP]
> No exemplo a seguir, a configuração define a versão inicial do ponto de extremidade para lidar com 20% do tráfego. Como esse é o primeiro ponto de extremidade, também é a versão padrão. E, como não temos outras versões para o outro% de 80% do tráfego, ele também é roteado para o padrão. Até que outras versões que usam um percentual de tráfego sejam implantadas, isso recebe efetivamente 100% do tráfego.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')

# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Atualizar e adicionar versões a um ponto de extremidade

Adicione outra versão ao ponto de extremidade e configure o percentual de tráfego de Pontuação indo para a versão. Há dois tipos de versões, um controle e uma versão de tratamento. Pode haver várias versões de tratamento para ajudar a comparar com uma única versão de controle.

> [!TIP]
> A segunda versão, criada pelo trecho de código a seguir, aceita 10% do tráfego. A primeira versão está configurada para 20%, portanto, somente 30% do tráfego está configurado para versões específicas. O restante de 70% é enviado para a primeira versão de ponto de extremidade, porque também é a versão padrão.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

Atualize as versões existentes ou exclua-as em um ponto de extremidade. Você pode alterar o tipo padrão da versão, o tipo de controle e o percentual de tráfego. No exemplo a seguir, a segunda versão aumenta seu tráfego para 40% e agora é o padrão.

> [!TIP]
> Após o trecho de código a seguir, a segunda versão é agora padrão. Agora, ele está configurado para 40%, enquanto a versão original ainda está configurada para 20%. Isso significa que 40% do tráfego não é contabilizado por configurações de versão. O tráfego restante será roteado para a segunda versão, porque agora ele é o padrão. Ele recebe efetivamente 80% do tráfego.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```

## <a name="web-service-authentication"></a>Autenticação de serviço Web

Ao implantar no serviço kubernetes do Azure, a autenticação __baseada em chave__ é habilitada por padrão. Você também pode habilitar __a autenticação baseada em token__ . A autenticação baseada em token exige que os clientes usem uma conta de Azure Active Directory para solicitar um token de autenticação, que é usado para fazer solicitações ao serviço implantado.

Para __desabilitar__ a autenticação, defina o `auth_enabled=False` parâmetro ao criar a configuração de implantação. O exemplo a seguir desabilita a autenticação usando o SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Para obter informações sobre como autenticar de um aplicativo cliente, consulte o [modelo consumir um Azure Machine Learning implantado como um serviço Web](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autenticação com chaves

Se a autenticação de chave estiver habilitada, você poderá usar o `get_keys` método para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se você precisar regenerar uma chave, use [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29)

### <a name="authentication-with-tokens"></a>Autenticação com tokens

Para habilitar a autenticação de token, defina o `token_auth_enabled=True` parâmetro ao criar ou atualizar uma implantação. O exemplo a seguir habilita a autenticação de token usando o SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Se a autenticação de token estiver habilitada, você poderá usar o `get_token` método para recuperar um token JWT e o tempo de expiração desse token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Será necessário solicitar um novo token após a hora do token `refresh_by` .
>
> A Microsoft recomenda enfaticamente que você crie seu espaço de trabalho Azure Machine Learning na mesma região que o cluster do serviço kubernetes do Azure. Para autenticar com um token, o serviço Web fará uma chamada para a região em que o workspace do Azure Machine Learning foi criado. Se a região do seu espaço de trabalho estiver indisponível, você não poderá buscar um token para o serviço Web mesmo que o cluster esteja em uma região diferente do seu espaço de trabalho. Isso efetivamente resulta na indisponibilidade da autenticação baseada em token até que a região do seu espaço de trabalho esteja disponível novamente. Além disso, quanto maior a distância entre a região do cluster e a região do seu espaço de trabalho, mais tempo será levado para buscar um token.
>
> Para recuperar um token, você deve usar o SDK do Azure Machine Learning ou o comando [AZ ml Service Get-Access-token](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-get-access-token) .


### <a name="vulnerability-scanning"></a>Verificação de vulnerabilidade

A Central de Segurança do Azure fornece um gerenciamento de segurança unificado e proteção avançada contra ameaças nas cargas de trabalho de nuvem híbrida. Você deve permitir que a central de segurança do Azure verifique seus recursos e siga suas recomendações. Para obter mais informações, consulte [integração dos serviços Kubernetess do Azure com a central de segurança](../security-center/defender-for-kubernetes-introduction.md).

## <a name="next-steps"></a>Próximas etapas

* [Usar o RBAC do Azure para autorização kubernetes](../aks/manage-azure-rbac.md)
* [Proteger o ambiente inferência com a rede virtual do Azure](how-to-secure-inferencing-vnet.md)
* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Atualizar serviço Web](how-to-deploy-update-web-service.md)
* [Use o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md)
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
