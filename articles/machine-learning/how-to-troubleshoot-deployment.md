---
title: Solucionando problemas de implantação de modelo remoto
titleSuffix: Azure Machine Learning
description: Saiba como solucionar problemas, resolver e solucionar alguns erros comuns de implantação do Docker com o serviço kubernetes do Azure e as instâncias de contêiner do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: contperf-fy20q4, devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 8bec083e62bec6a0311487c1e64e780ad14f451b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518256"
---
# <a name="troubleshooting-remote-model-deployment"></a>Solucionando problemas de implantação de modelo remoto 

Saiba como solucionar problemas e resolver, ou contornar, erros comuns que você pode encontrar ao implantar um modelo em ACI (instâncias de contêiner do Azure) e AKS (serviço kubernetes do Azure) usando o Azure Machine Learning.

> [!NOTE]
> Se você estiver implantando um modelo no AKS (serviço kubernetes do Azure), aconselharemos a habilitar [Azure monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) para esse cluster. Isso ajudará você a entender a integridade geral do cluster e o uso de recursos. Você também pode encontrar os seguintes recursos úteis:
>
> * [Verificar Resource Health eventos que afetam o cluster AKS](../aks/aks-resource-health.md)
> * [Diagnóstico do serviço kubernetes do Azure](../aks/concepts-diagnostics.md)
>
> Se você estiver tentando implantar um modelo em um cluster não íntegro ou sobrecarregado, espera-se que eles tenham problemas. Se precisar de ajuda para solucionar problemas de cluster AKS, entre em contato com o suporte do AKS.

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura do Azure**. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* O [SDK do Azure Machine Learning](/python/api/overview/azure/ml/install).
* O [CLI do Azure](/cli/azure/install-azure-cli).
* A [Extensão da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Etapas para a implantação do Docker de modelos de aprendizado de máquina

Quando você implanta um modelo em uma computação não local no Azure Machine Learning, ocorre o seguinte:

1. O Dockerfile que você especificou em seu objeto de ambiente em seu InferenceConfig é enviado para a nuvem, juntamente com o conteúdo do seu diretório de origem
1. Se uma imagem criada anteriormente não estiver disponível no registro de contêiner, uma nova imagem do Docker será criada na nuvem e armazenada no registro de contêiner padrão do seu espaço de trabalho.
1. A imagem do Docker do registro de contêiner é baixada para seu destino de computação.
1. O repositório de blob padrão do seu espaço de trabalho é montado em seu destino de computação, dando a você acesso a modelos registrados
1. O servidor Web é inicializado executando a função do script de entrada `init()`
1. Quando o modelo implantado recebe uma solicitação, sua `run()` função manipula essa solicitação

A principal diferença ao usar uma implantação local é que a imagem de contêiner é criada em seu computador local, motivo pelo qual você precisa ter o Docker instalado para uma implantação local.

Entender essas etapas de alto nível deve ajudá-lo a entender onde estão ocorrendo erros.

## <a name="get-deployment-logs"></a>Obter logs de implantação

A primeira etapa na depuração de erros é obter seus logs de implantação. Primeiro, siga as [instruções aqui](how-to-deploy-and-where.md#connect-to-your-workspace) para se conectar ao seu espaço de trabalho.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

Para obter os logs de um serviço Web implantado, faça o:

```bash
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


Supondo que você tenha um objeto do tipo `azureml.core.Workspace` chamado `ws` , você pode fazer o seguinte:

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>Depurar Localmente

Se você tiver problemas ao implantar um modelo em ACI ou AKS, implante-o como um serviço Web local. Usar um serviço Web local facilita a solução de problemas. Para solucionar problemas de uma implantação localmente, consulte o [artigo solução local de problemas](./how-to-troubleshoot-deployment-local.md).

## <a name="container-cannot-be-scheduled"></a>Não é possível agendar o contêiner

Ao implantar um serviço em um destino de computação do Serviço de Kubernetes do Azure, o Azure Machine Learning tentará agendar o serviço com a quantidade solicitada de recursos. Se não houver nós disponíveis no cluster com a quantidade apropriada de recursos após 5 minutos, a implantação falhará. A mensagem de falha é `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Você pode resolver esse erro adicionando mais nós, alterando a SKU de seus nós ou alterando os requisitos de recursos do seu serviço. 

A mensagem de erro normalmente indicará a qual recurso você precisa mais, por exemplo, se você vir uma mensagem de erro indicando `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` que significa que o serviço requer GPUs e que há três nós no cluster que não têm GPUs disponíveis. Isso pode ser resolvido adicionando mais nós, se você estiver usando um SKU de GPU, alternando para um SKU habilitado para GPU, se não estiver, ou alterando o ambiente para não exigir GPUs.  

## <a name="service-launch-fails"></a>Falhas na inicialização do serviço

Depois que a imagem é criada com êxito, o sistema tenta iniciar um contêiner usando a configuração de implantação. Como parte do processo de inicialização do contêiner, a função `init()` em seu script de pontuação é invocada pelo sistema. Se houver exceções não capturadas na função `init()`, você poderá ver o erro **CrashLoopBackOff** na mensagem de erro.

Use as informações no artigo [inspecionar o log do Docker](how-to-troubleshoot-deployment-local.md#dockerlog) .

## <a name="function-fails-get_model_path"></a>Falha de função: get_model_path()

Geralmente, na função `init()` no script de pontuação, a função [Model.get_model_path()](/python/api/azureml-core/azureml.core.model.model#get-model-path-model-name--version-none---workspace-none-) é chamada para localizar um arquivo de modelo ou uma pasta de arquivos de modelo no contêiner. Se o arquivo ou pasta do modelo não puder ser encontrado, a função falhará. A maneira mais fácil para depurar esse erro é executar o código do Python no shell do contêiner abaixo:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Esse exemplo imprime o caminho local (relativo ao `/var/azureml-app`) no contêiner em que o seu script de pontuação está esperando encontrar o arquivo ou a pasta de modelo. Em seguida, você pode verificar se o arquivo ou a pasta realmente está no local que deveria.

Definir o nível de log como DEBUG poderá fazer com que as informações adicionais sobre a causa sejam registradas, o que podem ser útil para identificar a falha.

## <a name="function-fails-runinput_data"></a>Falha de função: run(input_data)

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

## <a name="http-status-code-502"></a>Código de status HTTP 502

Um código de status 502 indica que o serviço gerou uma exceção ou falhou no método `run()` do arquivo score.py. Use as informações neste artigo para depurar o arquivo.

## <a name="http-status-code-503"></a>Código de status HTTP 503

As implantações do Serviço de Kubernetes do Azure dão suporte ao dimensionamento automático, que permite que as réplicas sejam adicionadas para dar suporte à carga adicional. O dimensionador automático foi projetado para lidar com alterações **graduais** na carga. Se você receber grandes picos em solicitações por segundo, os clientes podem receber um código de status HTTP 503. Mesmo que o dimensionador reatue rapidamente, leva AKS uma quantidade significativa de tempo para criar contêineres adicionais.

As decisões de expansão/redução baseiam-se na utilização das réplicas de contêiner atuais. O número de réplicas ocupadas (processando uma solicitação) dividido pelo número total de réplicas atuais é a utilização atual. Se esse número exceder `autoscale_target_utilization` , mais réplicas serão criadas. Se for menor, as réplicas serão reduzidas. As decisões para adicionar réplicas são ansiosos e rápidas (cerca de 1 segundo). As decisões para remover réplicas são conservadoras (cerca de 1 minuto). Por padrão, a utilização de destino de dimensionamento automático é definida como **70%**, o que significa que o serviço pode lidar com picos em solicitações por segundo (RPS) de **até 30%**.

As duas ações a seguir podem evitar códigos de status 503:

> [!TIP]
> Essas duas abordagens podem ser usadas individualmente ou em combinação.

* Altere o nível de utilização no qual o dimensionamento automático cria novas réplicas. Você pode ajustar o destino de utilização, definindo o `autoscale_target_utilization` como um valor mais baixo.

    > [!IMPORTANT]
    > Essa alteração não faz com que as réplicas sejam criadas *mais rapidamente*. Em vez disso, elas são criadas no limite inferior de utilização. Em vez de aguardar até que o serviço seja 70% utilizado, alterar o valor para 30% faz com que as réplicas sejam criadas quando ocorrer uma utilização de 30%.
    
    Se o serviço Web já estiver usando as réplicas máximas atuais e você ainda estiver vendo os códigos de status 503, aumente o valor `autoscale_max_replicas` para aumentar o número máximo de réplicas.

* Altere o número mínimo de réplicas. Aumentar as réplicas mínimas fornece um pool maior para lidar com os picos de entrada.

    Para aumentar o número mínimo de réplicas, defina `autoscale_min_replicas` como um valor maior. Você pode calcular as réplicas necessárias usando o código a seguir, substituindo os valores por valores específicos para o projeto:

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
    > Se você receber picos de solicitação maiores do que as novas réplicas mínimas podem suportar, você poderá receber códigos 503 novamente. Por exemplo, à medida que o tráfego para o serviço aumenta, talvez seja necessário aumentar as réplicas mínimas.

Para obter mais informações sobre como definir `autoscale_target_utilization`, `autoscale_max_replicas` e `autoscale_min_replicas`, confira a referência do módulo [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice).

## <a name="http-status-code-504"></a>Código de status HTTP 504

Um código de status 504 indica que a solicitação atingiu o tempo limite. O tempo limite padrão é 1 minuto.

Você pode aumentar o tempo limite ou tentar acelerar o serviço, modificando o score.py para remover as chamadas desnecessárias. Se essas ações não corrigirem o problema, use as informações neste artigo para depurar o arquivo score.py. O código pode estar em um estado sem resposta ou um loop infinito.

## <a name="other-error-messages"></a>Outras mensagens de erro

Execute estas ações para os seguintes erros:

|Erro  | Resolução  |
|---------|---------|
|Falha na criação da imagem ao implantar o serviço Web     |  Adicionar "pynacl = = 1.2.1" como uma dependência Pip ao arquivo Conda para configuração de imagem       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Altere a SKU para VMs usadas em sua implantação para uma que tenha mais memória. |
|Falha de FPGA     |  Não será possível implantar modelos em FPGAs até que você tenha solicitado e recebido aprovação para cota de FPGA. Para solicitar acesso, preencha o formulário de solicitação de cota: https://aka.ms/aml-real-time-ai       |

## <a name="advanced-debugging"></a>Depuração avançada

Talvez seja necessário depurar interativamente o código Python contido na implantação de modelo. Por exemplo, se o script de entrada estiver falhando e não for possível determinar o motivo por meio de um registro adicional. Usando Visual Studio Code e debugpy, você pode anexar ao código em execução dentro do contêiner do Docker.

Para obter mais informações, visite a [depuração interativa no guia vs Code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="model-deployment-user-forum"></a>[Fórum do usuário de implantação de modelo](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a implantação:

* [Como e onde implantar](how-to-deploy-and-where.md)
* [Tutorial: Treinar e implantar modelos](tutorial-train-models-with-aml.md)
* [Como executar e depurar experimentos localmente](./how-to-debug-visual-studio-code.md)