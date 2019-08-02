---
title: Guia de solução de problemas de implantação
titleSuffix: Azure Machine Learning service
description: Saiba como solucionar problemas, resolver e solucionar os erros comuns de implantação do Docker com o serviço kubernetes do Azure e as instâncias de contêiner do Azure usando o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 07/09/2019
ms.custom: seodec18
ms.openlocfilehash: 24716a9b9fa5174d899cf0678b83b2da0c59957c
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358662"
---
# <a name="troubleshooting-azure-machine-learning-service-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Solução de problemas de implantação Azure Machine Learning serviço kubernetes do Azure e instâncias de contêiner do Azure

Saiba como solucionar ou resolver erros comuns de implantação do Docker com ACI (instâncias de contêiner do Azure) e AKS (serviço de kubernetes do Azure) usando o serviço Azure Machine Learning.

Ao implantar um modelo de serviço do Azure Machine Learning, o sistema executa uma série de tarefas. As tarefas de implantação são:

1. Registre o modelo no Registro de modelo do workspace.

2. Crie uma imagem do Docker, incluindo:
    1. Baixe o modelo registrado do Registro. 
    2. Crie um dockerfile, com um ambiente Python baseado nas dependências especificadas no arquivo yaml do ambiente.
    3. Adicione seus arquivos de modelo e o script de pontuação que você fornece no dockerfile.
    4. Crie uma nova imagem do Docker usando o dockerfile.
    5. Registre a imagem do Docker com o Registro de Contêiner do Azure associado com o workspace.

    > [!IMPORTANT]
    > Dependendo do código, a criação da imagem ocorrerá automaticamente sem a sua entrada.

3. Implante a imagem do Docker para o serviço ACI (Instância de Contêiner do Azure) ou o AKS (Serviço de Kubernetes do Azure).

4. Inicialize um novo contêiner (ou contêineres) em ACI ou AKS. 

Saiba mais sobre esse processo na introdução a [Gerenciamento de Modelos](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Antes de começar

Se você tiver qualquer problema, a primeira medida a adotar é dividir a tarefa de implantação (descrita anteriormente) em etapas individuais para isolar o problema.

A interrupção da implantação em tarefas será útil se você estiver usando a API [WebService. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) ou a API [WebService. deploy_from_model ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) , pois ambas as funções executam as etapas mencionadas anteriormente como uma única ação. Normalmente, essas APIs são convenientes, mas ajuda a dividir as etapas durante a solução de problemas, substituindo-as pelas chamadas de API abaixo.

1. Registre o modelo. Aqui está um exemplo de código:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Crie a imagem. Aqui está um exemplo de código:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      entry_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Implante a imagem como serviço. Aqui está um exemplo de código:

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Depois de termos dividido o processo de implantação em tarefas individuais, podemos ver alguns dos erros mais comuns.

## <a name="image-building-fails"></a>Falhas na criação da imagem

Se a imagem do Docker não puder ser criada, a chamada [Image. wait_for_creation ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) ou [Service. wait_for_deployment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) falhará com algumas mensagens de erro que podem oferecer algumas pistas. Você também pode encontrar mais detalhes sobre os erros no log de build da imagem. Abaixo está um código de exemplo mostrando como descobrir o URI do log de build da imagem.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print(img.name, img.version, img.image_build_log_uri)
```

O URI do log de imagem é uma URL SAS que aponta para um arquivo de log armazenado no Armazenamento de Blobs do Azure. Basta copiar e colar o URI em uma janela do navegador para poder baixar e exibir o arquivo de log.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Política de acesso de Azure Key Vault e modelos de Azure Resource Manager

A compilação da imagem também pode falhar devido a um problema com a política de acesso na Azure Key Vault. Essa situação pode ocorrer quando você usa um modelo de Azure Resource Manager para criar o espaço de trabalho e os recursos associados (incluindo Azure Key Vault), várias vezes. Por exemplo, usando o modelo várias vezes com os mesmos parâmetros como parte de um pipeline de implantação e integração contínua.

A maioria das operações de criação de recursos por meio de modelos é idempotente, mas Key Vault limpa as políticas de acesso toda vez que o modelo é usado. Limpar as políticas de acesso interrompe o acesso ao Key Vault para qualquer espaço de trabalho existente que o esteja usando. Essa condição resulta em erros quando você tenta criar novas imagens. Veja a seguir exemplos dos erros que você pode receber:

__Portal__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

Para evitar esse problema, recomendamos uma das seguintes abordagens:

* Não implante o modelo mais de uma vez para os mesmos parâmetros. Ou exclua os recursos existentes antes de usar o modelo para recriá-los.
* Examine as políticas de acesso do Key Vault e use essas políticas para definir `accessPolicies` a propriedade do modelo.
* Verifique se o recurso de Key Vault já existe. Se tiver, não a recrie por meio do modelo. Por exemplo, adicione um parâmetro que permita desabilitar a criação do recurso de Key Vault se ele já existir.

## <a name="debug-locally"></a>Depurar localmente

Se você encontrar problemas ao implantar um modelo para ACI ou AKS, tente implantá-lo como um serviço Web local. Usar um serviço Web local torna mais fácil solucionar problemas. A imagem do Docker que contém o modelo é baixada e iniciada no sistema local.

> [!IMPORTANT]
> As implantações de serviço Web local exigem uma instalação de Docker em funcionamento no sistema local. O Docker deve estar em execução antes de você implantar um serviço Web local. Para obter informações sobre como instalar e usar o Docker, consulte [https://www.docker.com/](https://www.docker.com/).

> [!WARNING]
> Não há suporte para implantações de serviço Web local para cenários de produção.

Para implantar localmente, modifique seu código para usar `LocalWebservice.deploy_configuration()` para criar uma configuração de implantação. Em seguida `Model.deploy()` , use para implantar o serviço. O exemplo a seguir implanta um modelo (contido na `model` variável) como um serviço Web local:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Neste ponto, você pode trabalhar com o serviço normalmente. Por exemplo, o código a seguir demonstra o envio de dados para o serviço:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>Atualizar o serviço

Durante os testes locais, talvez seja necessário atualizar o `score.py` arquivo para adicionar o log ou tentar resolver os problemas que você descobriu. Para recarregar as alterações no `score.py` arquivo, use `reload()`. Por exemplo, o código a seguir recarrega o script para o serviço e, em seguida, envia dados a ele. Os dados são pontuados usando o arquivo `score.py` atualizado:

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> O script é recarregado a partir do local especificado pelo `InferenceConfig` objeto usado pelo serviço.

Para alterar o modelo, as dependências Conda ou a configuração de implantação, use [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). O exemplo a seguir atualiza o modelo usado pelo serviço:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Excluir o serviço

Para excluir o serviço, use [Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a>Inspecionar o log do Docker

Você pode imprimir mensagens de log do mecanismo do Docker detalhadas do objeto de serviços. Você pode exibir o log para implantações ACI, AKS e local. O exemplo a seguir demonstra como imprimir os logs.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Falhas na inicialização do serviço

Depois que a imagem for criada com êxito, o sistema tentará iniciar um contêiner usando sua configuração de implantação. Como parte do processo de inicialização do contêiner, a função `init()` em seu script de pontuação é invocada pelo sistema. Se houver exceções não capturadas na função `init()`, você poderá ver o erro **CrashLoopBackOff** na mensagem de erro.

Use as informações na seção [inspecionar o log do Docker](#dockerlog) para verificar os logs.

## <a name="function-fails-getmodelpath"></a>Falha de função: get_model_path()

Geralmente, na `init()` função na função script de pontuação, [Model. Get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) é chamado para localizar um arquivo de modelo ou uma pasta de arquivos de modelo no contêiner. Se a pasta ou o arquivo de modelo não puder ser encontrado, a função falhará. A maneira mais fácil para depurar esse erro é executar o código do Python no shell do contêiner abaixo:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Este exemplo imprime o caminho local (em relação `/var/azureml-app`a) no contêiner em que o script de Pontuação espera encontrar o arquivo ou a pasta do modelo. Em seguida, você pode verificar se o arquivo ou a pasta realmente está no local que deveria.

Definir o nível de log para depurar pode fazer com que informações adicionais sejam registradas, o que pode ser útil para identificar a falha.

## <a name="function-fails-runinputdata"></a>Falha de função: run(input_data)

Se o serviço for implantado com êxito, mas falhar quando você publicar dados no ponto de extremidade de pontuação, você poderá adicionar o erro capturando instrução na função `run(input_data)` de modo que ele retorne a mensagem de erro detalhada em vez disso. Por exemplo:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Observação**: O retorno de mensagens de erro da chamada `run(input_data)` deve ser feito para apenas para fins de depuração. Por motivos de segurança, você não deve retornar mensagens de erro dessa maneira em um ambiente de produção.

## <a name="http-status-code-503"></a>Código de status HTTP 503

As implantações do serviço kubernetes do Azure dão suporte ao dimensionamento automático, que permite que as réplicas sejam adicionadas para dar suporte à carga adicional. No entanto, o dimensionador automático foi projetado  para lidar com alterações graduais na carga. Se você receber grandes picos em solicitações por segundo, os clientes poderão receber um código de status HTTP 503.

Há duas coisas que podem ajudar a evitar códigos de status 503:

* Altere o nível de utilização no qual o dimensionamento automático cria novas réplicas.
    
    Por padrão, a utilização de destino de dimensionamento automático é definida como 70%, o que significa que o serviço pode lidar com picos em solicitações por segundo (RPS) de até 30%. Você pode ajustar o destino de utilização definindo o `autoscale_target_utilization` para um valor mais baixo.

    > [!IMPORTANT]
    > Essa alteração não faz com que as réplicas sejam criadas *mais rapidamente*. Em vez disso, eles são criados em um limite de utilização mais baixo. Em vez de aguardar até que o serviço seja 70% utilizado, alterar o valor para 30% faz com que as réplicas sejam criadas quando ocorre uma utilização de 30%.
    
    Se o serviço Web já estiver usando as réplicas máximas atuais e você ainda estiver vendo os códigos de status 503 `autoscale_max_replicas` , aumente o valor para aumentar o número máximo de réplicas.

* Altere o número mínimo de réplicas. Aumentar as réplicas mínimas fornece um pool maior para lidar com os picos de entrada.

    Para aumentar o número mínimo de réplicas, defina `autoscale_min_replicas` como um valor mais alto. Você pode calcular as réplicas necessárias usando o código a seguir, substituindo valores por valores específicos do seu projeto:

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

    > [!NOTE]
    > Se você receber picos de solicitação maiores do que as novas réplicas mínimas podem lidar, você pode receber 503s novamente. Por exemplo, à medida que o tráfego para o serviço aumenta, talvez seja necessário aumentar as réplicas mínimas.

Para obter mais informações sobre `autoscale_target_utilization`a `autoscale_max_replicas`configuração, `autoscale_min_replicas` e para, consulte a referência do módulo [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) .


## <a name="advanced-debugging"></a>Depuração avançada

Em alguns casos, talvez seja necessário depurar interativamente o código Python contido em sua implantação de modelo. Por exemplo, se o script de entrada estiver falhando e o motivo não puder ser determinado por log adicional. Usando Visual Studio Code e o Ferramentas Python para Visual Studio (PTVSD), você pode anexar ao código em execução dentro do contêiner do Docker.

> [!IMPORTANT]
> Esse método de depuração não funciona ao usar `Model.deploy()` o e `LocalWebservice.deploy_configuration` o para implantar um modelo localmente. Em vez disso, você deve criar uma imagem usando a classe [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) . 
>
> As implantações de serviço Web local exigem uma instalação de Docker em funcionamento no sistema local. O Docker deve estar em execução antes de você implantar um serviço Web local. Para obter informações sobre como instalar e usar o Docker, consulte [https://www.docker.com/](https://www.docker.com/).

### <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Para instalar o Ferramentas Python para Visual Studio (PTVSD) no ambiente de desenvolvimento de VS Code local, use o seguinte comando:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Para obter mais informações sobre como usar o PTVSD com VS Code, consulte [depuração remota](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Para configurar VS Code para se comunicar com a imagem do Docker, crie uma nova configuração de depuração:

    1. Em VS Code, selecione o menu __depurar__ e, em seguida, selecione __configurações abertas__. Um arquivo chamado __Launch. JSON__ é aberto.

    1. No arquivo __Launch. JSON__ , localize a linha que contém `"configurations": [`e insira o seguinte texto após:

        ```json
        {
            "name": "Azure Machine Learning service: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Se já houver outras entradas na seção Configurações, adicione uma vírgula (,) após o código que você inseriu.

        Esta seção anexa o contêiner do Docker usando a porta 5678.

    1. Salve o arquivo __Launch. JSON__ .

### <a name="create-an-image-that-includes-ptvsd"></a>Criar uma imagem que inclui PTVSD

1. Modifique o ambiente Conda para sua implantação para que ele inclua o PTVSD. O exemplo a seguir demonstra como adicioná- `pip_packages` lo usando o parâmetro:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 
    
    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.17', 'ptvsd'])
    
    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Para iniciar o PTVSD e aguardar uma conexão quando o serviço for iniciado, adicione o seguinte à parte superior `score.py` do arquivo:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Durante a depuração, talvez você queira fazer alterações nos arquivos da imagem sem precisar recriá-los. Para instalar um vim (editor de texto) na imagem do Docker, crie um novo arquivo de `Dockerfile.steps` texto chamado e use o seguinte como o conteúdo do arquivo:

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    Um editor de texto permite que você modifique os arquivos dentro da imagem do Docker para testar alterações sem criar uma nova imagem.

1. Para criar uma imagem que usa o `Dockerfile.steps` arquivo, use o `docker_file` parâmetro ao criar uma imagem. O exemplo a seguir demonstra como fazer isso:

    > [!NOTE]
    > Este exemplo pressupõe que `ws` aponta para seu espaço de trabalho Azure Machine Learning e `model` que é o modelo que está sendo implantado. O `myenv.yml` arquivo contém as dependências de Conda criadas na etapa 1.

    ```python
    from azureml.core.image import Image, ContainerImage
    image_config = ContainerImage.image_configuration(runtime= "python",
                                 execution_script="score.py",
                                 conda_file="myenv.yml",
                                 docker_file="Dockerfile.steps")

    image = Image.create(name = "myimage",
                     models = [model],
                     image_config = image_config, 
                     workspace = ws)
    # Print the location of the image in the repository
    print(image.image_location)
    ```

Depois que a imagem tiver sido criada, o local da imagem no registro será exibido. O local é semelhante ao seguinte texto:

```text
myregistry.azurecr.io/myimage:1
```

Neste exemplo de texto, o nome do registro `myregistry` é e a imagem é `myimage`nomeada. A versão da imagem `1`é.

### <a name="download-the-image"></a>Baixar a imagem

1. Abra um prompt de comando, terminal ou outro shell e use o seguinte comando [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para se autenticar na assinatura do Azure que contém o espaço de trabalho Azure Machine Learning:

    ```azurecli
    az login
    ```

1. Para autenticar o ACR (registro de contêiner do Azure) que contém a imagem, use o comando a seguir. Substitua `myregistry` por um retornado quando você registrou a imagem:

    ```azurecli
    az acr login --name myregistry
    ```

1. Para baixar a imagem para o Docker local, use o comando a seguir. Substituir `myimagepath` pelo local retornado quando você registrou a imagem:

    ```bash
    docker pull myimagepath
    ```

    O caminho da imagem deve ser semelhante `myregistry.azurecr.io/myimage:1`a. Onde `myregistry` é o registro, `myimage` é a imagem e `1` é a versão da imagem.

    > [!TIP]
    > A autenticação da etapa anterior não é a última para sempre. Se você aguardar tempo suficiente entre o comando de autenticação e o comando pull, receberá uma falha de autenticação. Se isso acontecer, reautenticar.

    O tempo necessário para concluir o download depende da velocidade da sua conexão com a Internet. Um status de download é exibido durante o processo. Quando o download for concluído, você poderá usar o `docker images` comando para verificar se ele foi baixado.

1. Para facilitar o trabalho com a imagem, use o comando a seguir para adicionar uma marca. Substitua `myimagepath` pelo valor local da etapa 2.

    ```bash
    docker tag myimagepath debug:1
    ```

    Para o restante das etapas, você pode se referir à imagem local como `debug:1` em vez do valor do caminho da imagem completa.

### <a name="debug-the-service"></a>Depurar o serviço

> [!TIP]
> Se você definir um tempo limite para a conexão PTVSD no `score.py` arquivo, deverá se conectar vs Code à sessão de depuração antes que o tempo limite expire. Inicie vs Code, abra a cópia local do `score.py`, defina um ponto de interrupção e prepare-o antes de usar as etapas nesta seção.
>
> Para obter mais informações sobre depuração e definição de pontos de interrupção, consulte [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

1. Para iniciar um contêiner do Docker usando a imagem, use o seguinte comando:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Para anexar VS Code ao PTVSD dentro do contêiner, abra VS Code e use a tecla F5 ou selecione __depurar__. Quando solicitado, selecione o serviço __de Azure Machine Learning: Configuração de depuração__ do Docker. Você também pode selecionar o ícone de depuração na barra lateral, o __serviço Azure Machine Learning: Entrada de depuração__ do Docker no menu suspenso de depuração e, em seguida, use a seta verde para anexar o depurador.

    ![O ícone de depuração, o botão Iniciar Depuração e o seletor de configuração](media/how-to-troubleshoot-deployment/start-debugging.png)

Neste ponto, VS Code se conecta ao PTVSD dentro do contêiner do Docker e para o ponto de interrupção definido anteriormente. Agora você pode percorrer o código conforme ele é executado, exibir variáveis, etc.

Para obter mais informações sobre como usar VS Code para depurar o Python, consulte [depurar seu código Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Modificar os arquivos de contêiner

Para fazer alterações nos arquivos da imagem, você pode anexar ao contêiner em execução e executar um shell bash. A partir daí, você pode usar o vim para editar arquivos:

1. Para se conectar ao contêiner em execução e iniciar um shell bash no contêiner, use o seguinte comando:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Para localizar os arquivos usados pelo serviço, use o seguinte comando do shell bash no contêiner:

    ```bash
    cd /var/azureml-app
    ```

    A partir daqui, você pode usar o vim para `score.py` editar o arquivo. Para obter mais informações sobre como usar o vim, consulte [usando o editor de vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. As alterações em um contêiner normalmente não são mantidas. Para salvar as alterações feitas, use o comando a seguir, antes de sair do Shell iniciado na etapa anterior (ou seja, em outro Shell):

    ```bash
    docker commit debug debug:2
    ```

    Este comando cria uma nova imagem chamada `debug:2` que contém as edições.

    > [!TIP]
    > Você precisará parar o contêiner atual e começar a usar a nova versão antes que as alterações entrem em vigor.

1. Certifique-se de manter as alterações feitas nos arquivos no contêiner em sincronia com os arquivos locais que o VS Code usa. Caso contrário, a experiência do depurador não funcionará conforme o esperado.

### <a name="stop-the-container"></a>Parar o contêiner

Para parar o contêiner, use o seguinte comando:

```bash
docker stop debug
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a implantação:

* [Como e onde implantar](how-to-deploy-and-where.md)
* [Tutorial: Treinar e implantar modelos](tutorial-train-models-with-aml.md)
