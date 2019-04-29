---
title: Guia de solução de problemas de implantação
titleSuffix: Azure Machine Learning service
description: Saiba como contornar, resolver e solucionar erros comuns de implantação do Docker com o AKS e ACI usando o serviço de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 815be7400e0a0560ace7e07b317aeb25c2feacd5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817426"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Solucionando problemas de implantações de serviço AKS e ACI do Azure Machine Learning

Neste artigo, você aprenderá a contornar ou resolver erros comuns de implantação do Docker com as Instâncias de Contêiner do Azure (ACI) e Serviço de Kubernetes do Azure (AKS) usando o Serviço do Azure Machine Learning.

Ao implantar um modelo de serviço do Azure Machine Learning, o sistema executa uma série de tarefas. Essa é uma sequência complexa de eventos e, às vezes, surgem problemas. As tarefas de implantação são:

1. Registre o modelo no Registro de modelo do workspace.

2. Crie uma imagem do Docker, incluindo:
    1. Baixe o modelo registrado do Registro. 
    2. Crie um dockerfile, com um ambiente Python baseado nas dependências especificadas no arquivo yaml do ambiente.
    3. Adicione seus arquivos de modelo e o script de pontuação que você fornece no dockerfile.
    4. Crie uma nova imagem do Docker usando o dockerfile.
    5. Registre a imagem do Docker com o Registro de Contêiner do Azure associado com o workspace.

3. Implante a imagem do Docker para o serviço ACI (Instância de Contêiner do Azure) ou o AKS (Serviço de Kubernetes do Azure).

4. Inicialize um novo contêiner (ou contêineres) em ACI ou AKS. 

Saiba mais sobre esse processo na introdução a [Gerenciamento de Modelos](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Antes de começar

Se você tiver qualquer problema, a primeira medida a adotar é dividir a tarefa de implantação (descrita anteriormente) em etapas individuais para isolar o problema. 

Isso é útil se você estiver usando o `Webservice.deploy` API, ou `Webservice.deploy_from_model` API, já que essas funções agrupam as etapas mencionadas anteriormente em uma única ação. Normalmente, essas APIs são convenientes, mas é útil para dividir as etapas na solução de problemas, substituindo-os com o abaixo de chamadas à API.

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
                                                      execution_script="score.py",
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
Se o sistema não puder compilar a imagem do Docker, a chamada `image.wait_for_creation()` falhará com algumas mensagens de erro que podem oferecer algumas dicas. Você também pode encontrar mais detalhes sobre os erros no log de build da imagem. Abaixo está um código de exemplo mostrando como descobrir o URI do log de build da imagem.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```
O URI do log de imagem é uma URL SAS que aponta para um arquivo de log armazenado no Armazenamento de Blobs do Azure. Basta copiar e colar o URI em uma janela do navegador para poder baixar e exibir o arquivo de log.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Política de acesso do Cofre de chaves do Azure e modelos do Azure Resource Manager

A compilação de imagem também pode falhar devido a um problema com a política de acesso no cofre de chaves do Azure. Isso pode ocorrer quando você usa um modelo do Azure Resource Manager para criar o espaço de trabalho e recursos associados (incluindo o Azure Key Vault), várias vezes. Por exemplo, usando o modelo várias vezes com os mesmos parâmetros como parte de um pipeline de implantação e integração contínua.

A maioria das operações de criação de recursos por meio de modelos são idempotentes, mas o Cofre de chaves limpa as políticas de acesso de cada vez que o modelo é usado. Isso interromperá o acesso ao Key Vault para qualquer espaço de trabalho existente que está sendo usado. Isso resulta em erros ao tentar criar novas imagens. A seguir estão exemplos dos erros que você pode receber:

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

Para evitar esse problema, recomendamos que uma das seguintes abordagens:

* Não implante o modelo mais de uma vez para os mesmos parâmetros. Ou exclua os recursos existentes antes de usar o modelo para recriá-los.
* Examinar as políticas de acesso do Key Vault e use isso para definir o `accessPolicies` propriedade do modelo.
* Verifique se o recurso de Cofre de chaves já existe. Se isso acontecer, não recriá-lo por meio do modelo. Por exemplo, adicione um parâmetro que permite que você desabilite a criação do recurso Cofre de chaves, se ele já existe.

## <a name="service-launch-fails"></a>Falhas na inicialização do serviço
Depois que a imagem é criada com êxito, o sistema tenta iniciar um contêiner no ACI ou AKS, dependendo da sua configuração de implantação. É recomendável tentar uma implantação de ACI primeiro, pois é uma implantação mais simples de contêiner único. Dessa forma, você pode descartar qualquer problema específico de AKS.

Como parte do processo de inicialização do contêiner, a função `init()` em seu script de pontuação é invocada pelo sistema. Se houver exceções não capturadas na função `init()`, você poderá ver o erro **CrashLoopBackOff** na mensagem de erro. Abaixo estão algumas dicas para ajudá-lo a solucionar o problema.

### <a name="inspect-the-docker-log"></a>Inspecionar o log do Docker
Você pode imprimir mensagens de log do mecanismo do Docker detalhadas do objeto de serviços.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>Depurar a imagem do Docker localmente
Algumas vezes, o log do Docker não emite informações suficientes sobre o que está errado. Você pode ir além e obter a imagem do Docker interna, iniciar um contêiner local e depurar diretamente dentro do contêiner ao vivo de modo interativo. Para iniciar um contêiner local, você deve ter um mecanismo do Docker em execução localmente, e seria muito mais fácil se você também tivesse a [azure-cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) instalada.

Primeiro, precisamos descobrir a localização da imagem:

```python
# print image location
print(image.image_location)
```

O local da imagem tem este formato: `<acr-name>.azurecr.io/<image-name>:<version-number>`, como `myworkpaceacr.azurecr.io/myimage:3`. 

Agora vá até a janela de linha de comando. Se você tiver a azure-cli instalada, poderá digitar os comandos a seguir para entrar no ACR (Registro de Contêiner do Azure) associado ao workspace em que a imagem está armazenada. 

```sh
# log on to Azure first if you haven't done so before
$ az login

# make sure you set the right subscription in case you have access to multiple subscriptions
$ az account set -s <subscription_name_or_id>

# now let's log in to the workspace ACR
# note the acr-name is the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
Se você não tiver a azure-cli instalada, poderá usar o comando `docker login` para fazer logon no ACR. Mas você precisará recuperar o nome de usuário e a senha do ACR no portal do Azure primeiro.

Depois de fazer logon no ACR, você pode obter a imagem do Docker e iniciar um contêiner localmente e, em seguida, inicializar uma sessão Bash para depuração usando o comando `docker run`:

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

Depois de inicializar uma sessão Bash para o contêiner em execução, você pode encontrar seus scripts de pontuação na pasta `/var/azureml-app`. Em seguida, você pode inicializar uma sessão do Python para depurar seus scripts de pontuação. 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
Caso você precise de um editor de texto para modificar seus scripts, poderá instalar o editor vim, nano, Emacs ou outro de sua preferência.

```sh
# update package index
apt-get update

# install a text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

Você também pode iniciar o serviço Web localmente e enviar o tráfego HTTP para ele. O servidor Flask no contêiner do Docker está em execução na porta 5001. Você pode mapear para qualquer outra porta disponível no computador host.
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>Falha de função: get_model_path()
Geralmente, na função `init()` no script de pontuação, a função `Model.get_model_path()` é chamada para localizar um arquivo de modelo ou uma pasta de arquivos de modelo no contêiner. Isso geralmente será uma fonte de falha se o arquivo de modelo ou a pasta não puder ser localizado. A maneira mais fácil para depurar esse erro é executar o código do Python no shell do contêiner abaixo:

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Isso imprime o caminho local (relativo ao `/var/azureml-app`) no contêiner em que o seu script de pontuação está esperando encontrar o arquivo ou a pasta de modelo. Em seguida, você pode verificar se o arquivo ou a pasta realmente está no local que deveria.

Definir o nível de log como DEBUG poderá fazer com que as informações adicionais sobre a causa sejam registradas, o que podem ser útil para identificar a falha.

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
**Observação**: O retorno de mensagens de erro da chamada `run(input_data)` deve ser feito para apenas para fins de depuração. Pode não ser uma boa ideia fazer isso em um ambiente de produção por motivos de segurança.

## <a name="http-status-code-503"></a>Código de status HTTP 503

Implantações de Kubernetes Service do Azure dão suporte a dimensionamento automático, que permite que as réplicas a ser adicionado para dar suporte à carga adicional. No entanto, o dimensionador automático foi projetado para tratar **gradual** as alterações na carga. Se você receber grandes picos nas solicitações por segundo, os clientes podem receber um código de status HTTP 503.

Há duas coisas que podem ajudar a impedir que os códigos de status 503:

* Alterar o nível de utilização em quais dimensionamento automático cria novas réplicas.
    
    Por padrão, a utilização de destino de dimensionamento automático é definida como 70%, o que significa que o serviço pode lidar com picos de solicitações por segundo (RPS) de até 30%. Você pode ajustar a meta de utilização, definindo o `autoscale_target_utilization` para um valor mais baixo.

    > [!IMPORTANT]
    > Essa alteração não faz com que as réplicas a ser criado *com mais rapidez*. Em vez disso, eles são criados em um limite inferior de utilização. Em vez de esperar até que o serviço seja 70% de utilização, alterando o valor como 30% faz com que as réplicas a ser criado quando ocorre 30% da utilização.
    
    Se o serviço web já está usando as réplicas máximo atuais e você ainda estiver vendo os códigos de status 503, aumente o `autoscale_max_replicas` valor para aumentar o número máximo de réplicas.

* Altere o número mínimo de réplicas. Aumentar as réplicas mínimas fornece um pool maior para lidar com os picos de entrada.

    Para aumentar o número mínimo de réplicas, defina `autoscale_min_replicas` para um valor mais alto. Você pode calcular as réplicas necessárias usando o código a seguir, substituindo os valores pelos valores específicos ao seu projeto:

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
    > Se você receber maior do que as novas réplicas mínimo podem lidar com picos de solicitação, você pode receber 503s ao novamente. Por exemplo, como o tráfego para seu serviço aumenta, você talvez precise aumentar as réplicas mínimo.

Para obter mais informações sobre a configuração `autoscale_target_utilization`, `autoscale_max_replicas`, e `autoscale_min_replicas` , consulte o [AksWebservice](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) referência de módulo.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a implantação: 
* [Como e onde implantar](how-to-deploy-and-where.md)

* [Tutorial: Treinar e implantar modelos](tutorial-train-models-with-aml.md)
