---
title: Implantar um modelo de aprendizado de máquina para Azure Functions com o cache do Azure para Redis
description: Neste artigo, você implantará um modelo de Azure Machine Learning como um aplicativo de funções no Azure Functions usando um cache do Azure para a instância do Redis. O cache do Azure para Redis é extremamente funcional e escalonável – quando emparelhado com um modelo de Azure Machine Learning, você obterá baixa latência e alta taxa de transferência em seu aplicativo.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: c2241d738a43c6891ee4bea0829400fdc51a664b
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734225"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-with-azure-cache-for-redis"></a>Implantar um modelo de aprendizado de máquina para Azure Functions com o cache do Azure para Redis 

Neste artigo, você implantará um modelo de Azure Machine Learning como um aplicativo de funções no Azure Functions usando um cache do Azure para a instância do Redis.  

O cache do Azure para Redis é extremamente funcional e escalonável – quando emparelhado com um modelo de Azure Machine Learning, você obterá baixa latência e alta taxa de transferência em seu aplicativo. Alguns cenários em que um cache é particularmente benéfico é quando inferência os dados e para os resultados de inferência de modelo real. Em qualquer cenário, os metadados ou os resultados são armazenados na memória, o que leva a um desempenho maior. 

> [!NOTE]
> Embora os Azure Machine Learning e Azure Functions estejam geralmente disponíveis, a capacidade de empacotar um modelo do serviço de Machine Learning para o Functions está em versão prévia.  
>

## <a name="prerequisites"></a>Pré-requisitos
* Assinatura do Azure – [Crie uma gratuitamente](https://azure.microsoft.com/free/).
* Um Workspace do Azure Machine Learning. Para obter mais informações, consulte o artigo [criar um espaço de trabalho](../machine-learning/how-to-manage-workspace.md) .
* [CLI do Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* Um modelo de aprendizado de máquina treinado registrado em seu espaço de trabalho. Se você não tiver um modelo, use o [tutorial de classificação de imagem: treinar modelo](../machine-learning/tutorial-train-models-with-aml.md) para treinar e registrar um.

> [!IMPORTANT]
> Os trechos de código neste artigo pressupõem que você definiu as seguintes variáveis:
>
> * `ws` -Seu espaço de trabalho do Azure Machine Learning.
> * `model` -O modelo registrado que será implantado.
> * `inference_config` -A configuração de inferência para o modelo.
>
> Para obter mais informações sobre como definir essas variáveis, consulte [implantar modelos com Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

## <a name="create-an-azure-cache-for-redis-instance"></a>Criar uma instância de Cache do Azure para Redis 
Você poderá implantar um modelo de aprendizado de máquina para Azure Functions com qualquer instância de cache Basic, Standard ou Premium. Para criar uma instância de cache, siga estas etapas.  

1. Vá para a home page do portal do Azure ou abra o menu da barra lateral e, em seguida, selecione **criar um recurso**. 
   
1. Na página **Novo**, selecione **Bancos de dados** e, em seguida, **Cache do Azure para Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecionar o Cache do Azure para Redis.":::
   
1. Na página **Novo Cache Redis**, defina as configurações para o novo cache.
   
   | Configuração      | Valor sugerido  | DESCRIÇÃO |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Insira um nome global exclusivo. | O nome de cache precisa ser uma cadeia de caracteres com 1 a 63 caracteres que contém somente números, letras ou hifens. O nome precisa começar e terminar com um número ou uma letra e não pode conter hifens consecutivos. O *nome do host* de sua instância de cache será *\<DNS name>.redis.cache.windows.net*. | 
   | **Assinatura** | Clique na lista suspensa e selecione sua assinatura. | A assinatura na qual essa nova instância do Cache do Azure para Redis será criada. | 
   | **Grupo de recursos** | Clique na lista suspensa e selecione um grupo de recursos ou selecione **Criar** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual o cache e outros recursos serão criados. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
   | **Localidade** | Clique na lista suspensa e selecione uma localização. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão o cache. |
   | **Tipo de preços** | Clique na lista suspensa e selecione um [Tipo de preço](https://azure.microsoft.com/pricing/details/cache/). |  O tipo de preço determina o tamanho, o desempenho e os recursos disponíveis para o cache. Para obter mais informações, confira [Visão geral do Cache do Azure para Redis](cache-overview.md). |

1. Selecione a guia **Rede** ou clique no botão **Rede** na parte inferior da página.

1. Na guia **Rede**, escolha o método de conectividade.

1. Selecione **Próximo: Avançado** ou clique no botão **Próximo: Avançado** na parte inferior da página.

1. Na guia **Avançado** de uma instância de cache Básico ou Standard, selecione a alternância Habilitar se desejar habilitar uma porta não TLS.

1. Na guia **Avançado** de uma instância de cache Premium, defina as configurações da porta não TLS, do clustering e da persistência de dados.

1. Selecione **Próximo: Marcas** ou clique no botão **Próximo: Botão** Categorias na parte inferior da página.

1. Opcionalmente, na guia **Marcas**, insira o nome e o valor caso deseje categorizar o recurso. 

1. Selecione **Examinar + criar**. Você será levado para a guia Examinar + criar, na qual o Azure validará a configuração.

1. Depois que a mensagem em verde Validação aprovada for exibida, selecione **Criar**.

A criação do cache demora um pouco. Monitore o progresso na página **Visão Geral** do Cache do Azure para Redis. Quando o **Status** for mostrado como **Em execução**, o cache estará pronto para uso. 

## <a name="prepare-for-deployment"></a>Preparar para a implantação

Antes de implantar o, você deve definir o que é necessário para executar o modelo como um serviço Web. A lista a seguir descreve os principais itens necessários para uma implantação:

* Um __script de entrada__. Esse script aceita solicitações, pontua a solicitação usando o modelo e retorna os resultados.

    > [!IMPORTANT]
    > O script de entrada é específico para seu modelo; Ele deve entender o formato dos dados de solicitação de entrada, o formato dos dados esperados pelo seu modelo e o formato dos dados retornados aos clientes.
    >
    > Se os dados da solicitação estiverem em um formato que não pode ser usado pelo seu modelo, o script poderá transformá-lo em um formato aceitável. Ele também pode transformar a resposta antes de retorná-la ao cliente.
    >
    > Por padrão, ao empacotar para funções, a entrada é tratada como texto. Se você estiver interessado em consumir os bytes brutos da entrada (por exemplo, para gatilhos de BLOB), deverá usar [AMLRequest para aceitar dados brutos](../machine-learning/how-to-deploy-advanced-entry-script.md#binary-data).

Para a função Run, verifique se ela se conecta a um ponto de extremidade Redis.

```python
import json
import numpy as np
import os
import redis
from sklearn.externals import joblib

def init():
    global model
    global azrediscache
    azrediscache = redis.StrictRedis(host='<host_url>', port=6380, password="<access_key>", ssl=True)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        input = azrediscache.get(data)
        result = model.predict(input)
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Para obter mais informações sobre o script de entrada, consulte [definir código de pontuação.](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-entry-script)

* **Dependências**, como scripts auxiliares ou pacotes python/Conda necessários para executar o script ou modelo de entrada

Essas entidades são encapsuladas em uma __configuração de inferência__. A configuração de inferência faz referência ao script de entrada e a outras dependências.

> [!IMPORTANT]
> Ao criar uma configuração de inferência para uso com Azure Functions, você deve usar um objeto de [ambiente](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py) . Observe que, se você estiver definindo um ambiente personalizado, deverá adicionar o azureml-padrões com a versão >= 1.0.45 como uma dependência Pip. Esse pacote contém a funcionalidade necessária para hospedar o modelo como um serviço Web. O exemplo a seguir demonstra como criar um objeto de ambiente e usá-lo com uma configuração de inferência:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults', 'redis'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Para obter mais informações sobre ambientes, consulte [criar e gerenciar ambientes para treinamento e implantação](../machine-learning/how-to-use-environments.md).

Para obter mais informações sobre a configuração de inferência, consulte [implantar modelos com Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-inference-configuration).

> [!IMPORTANT]
> Ao implantar o para o functions, você não precisa criar uma __configuração de implantação__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Instalar o pacote de visualização do SDK para o suporte do Functions

Para criar pacotes para Azure Functions, você deve instalar o pacote de visualização do SDK.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Criar a imagem

Para criar a imagem do Docker que é implantada no Azure Functions, use [azureml. contrib. Functions. Package](/python/api/azureml-contrib-functions/azureml.contrib.functions?preserve-view=true&view=azure-ml-py) ou a função de pacote específica para o gatilho que você está interessado em usar. O trecho de código a seguir demonstra como criar um novo pacote com um gatilho HTTP do modelo e a configuração de inferência:

> [!NOTE]
> O trecho de código pressupõe que `model` contém um modelo registrado e que `inference_config` contém a configuração para o ambiente de inferência. Para obter mais informações, consulte [implantar modelos com Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import HTTP_TRIGGER
model_package = package(ws, [model], inference_config, functions_enabled=True, trigger=HTTP_TRIGGER)
model_package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(model_package.location)
```

Quando `show_output=True` , a saída do processo de Build do Docker é mostrada. Quando o processo for concluído, a imagem terá sido criada no registro de contêiner do Azure para seu espaço de trabalho. Depois que a imagem tiver sido criada, o local no registro de contêiner do Azure será exibido. O local retornado está no formato `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> O empacotamento para funções atualmente dá suporte a gatilhos HTTP, gatilhos de BLOB e gatilhos do barramento de serviço Para obter mais informações sobre gatilhos, consulte [associações de Azure Functions](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns).

> [!IMPORTANT]
> Salve as informações de local, pois elas são usadas durante a implantação da imagem.

## <a name="deploy-image-as-a-web-app"></a>Implantar imagem como um aplicativo Web

1. Use o comando a seguir para obter as credenciais de logon para o registro de contêiner do Azure que contém a imagem. Substituir `<myacr>` pelo valor retornado anteriormente de `package.location` : 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    A saída desse comando é semelhante ao seguinte documento JSON:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Salve o valor de __nome de usuário__ e uma das __senhas__.

1. Se você ainda não tiver um grupo de recursos ou um plano do serviço de aplicativo para implantar o serviço, os comandos a seguir demonstrarão como criar ambos:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    Neste exemplo, um tipo de preço _básico do Linux_ ( `--sku B1` ) é usado.

    > [!IMPORTANT]
    > As imagens criadas por Azure Machine Learning usam o Linux, portanto, você deve usar o `--is-linux` parâmetro.

1. Crie a conta de armazenamento a ser usada para o armazenamento de trabalhos da Web e obtenha sua cadeia de conexão. Substituir `<webjobStorage>` pelo nome que você deseja usar.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Para criar o aplicativo de funções, use o comando a seguir. Substituir `<app-name>` pelo nome que você deseja usar. Substitua `<acrinstance>` e `<imagename>` pelos valores de retornado `package.location` anteriormente. Substitua `<webjobStorage>` pelo nome da conta de armazenamento da etapa anterior:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > Neste ponto, o aplicativo de funções foi criado. No entanto, como você não forneceu a cadeia de conexão para o gatilho HTTP ou as credenciais para o registro de contêiner do Azure que contém a imagem, o aplicativo de funções não está ativo. Nas próximas etapas, você fornecerá a cadeia de conexão e as informações de autenticação para o registro de contêiner. 

1. Para fornecer ao aplicativo de funções as credenciais necessárias para acessar o registro de contêiner, use o comando a seguir. Substituir `<app-name>` pelo nome do aplicativo de funções. Substitua `<acrinstance>` e `<imagetag>` pelos valores da chamada AZ CLI na etapa anterior. Substitua `<username>` e `<password>` pelas informações de logon do ACR recuperadas anteriormente:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Esse comando retorna informações semelhantes ao seguinte documento JSON:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

Neste ponto, o aplicativo de funções começa a carregar a imagem.

> [!IMPORTANT]
> Pode levar vários minutos para que a imagem seja carregada. Você pode monitorar o progresso usando o portal do Azure.

## <a name="test-azure-functions-http-trigger"></a>Teste Azure Functions gatilho HTTP 

Agora, executaremos e testaremos nosso gatilho HTTP Azure Functions.

1. Vá para seu aplicativo de funções no portal do Azure.
1. Em desenvolvedor, selecione **código + teste**. 
1. No lado direito, selecione a guia **entrada** . 
1. Clique no botão **executar** para testar o gatilho http Azure functions. 

Agora você implantou com êxito um modelo de Azure Machine Learning como um aplicativo de funções usando um cache do Azure para a instância Redis. Saiba mais sobre o cache do Azure para Redis navegando até os links na seção abaixo.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende continuar até o próximo tutorial, pode manter os recursos criados neste início rápido e reutilizá-los.

Caso contrário, se você tiver concluído o início rápido, poderá excluir os recursos do Azure criados neste guia de início rápido para evitar cobranças. 

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível. Ao excluir o grupo de recursos, todos os recursos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. Se tiver criado os recursos para hospedar este exemplo dentro de um grupo de recursos existente que contém recursos que você quer manter, exclua cada recurso individualmente de suas respectivas folhas, em vez de excluir o grupo de recursos.

### <a name="to-delete-a-resource-group"></a>Para excluir um grupo de recursos

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

2. Na caixa **Filtrar por nome...**, digite o nome do seu grupo de recursos. Em seu grupo de recursos, na lista de resultados, selecione **...**, depois selecione **Excluir grupo de recursos**.

Você receberá uma solicitação para confirmar a exclusão do grupo de recursos. Digite o nome do grupo de recursos para confirmar e selecione **Excluir**.

Após alguns instantes, o grupo de recursos, e todos os recursos nele são excluídos.

## <a name="next-steps"></a>Próximas etapas 

* Saiba mais sobre o [cache do Azure para Redis](./cache-overview.md)
* Saiba como configurar seu aplicativo de funções na documentação do [Functions](../azure-functions/functions-create-function-linux-custom-image.md) .
* [Referência da API](/python/api/azureml-contrib-functions/azureml.contrib.functions?preserve-view=true&view=azure-ml-py) 
* Criar um [aplicativo Python que usa o cache do Azure para Redis](./cache-python-get-started.md)
