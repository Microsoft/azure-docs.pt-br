---
title: Depuração interativa com Visual Studio Code
titleSuffix: Azure Machine Learning
description: Depurar interativamente Azure Machine Learning código, pipelines e implantações usando Visual Studio Code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 09/30/2020
ms.openlocfilehash: e572f1f6a9452ccab9deddb62a5e219a81df5d47
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739987"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Depuração interativa com Visual Studio Code



Saiba como depurar interativamente Azure Machine Learning testes, pipelines e implantações usando Visual Studio Code (VS Code) e [debugpy](https://github.com/microsoft/debugpy/).

## <a name="run-and-debug-experiments-locally"></a>Executar e depurar experimentos localmente

Use a extensão Azure Machine Learning para validar, executar e depurar seus experimentos de aprendizado de máquina antes de enviá-los para a nuvem.

### <a name="prerequisites"></a>Pré-requisitos

* Extensão de VS Code de Azure Machine Learning (versão prévia). Para obter mais informações, consulte [configurar Azure Machine Learning vs Code extensão](tutorial-setup-vscode-extension.md).
* [Docker](https://www.docker.com/get-started)
  * Área de trabalho do Docker para Mac e Windows
  * Mecanismo do Docker para Linux.
* [Python 3](https://www.python.org/downloads/)

> [!NOTE]
> No Windows, certifique-se de [Configurar o Docker para usar contêineres do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

> [!TIP]
> Para o Windows, embora não seja necessário, é altamente recomendável [usar o Docker com o subsistema do Windows para Linux (WSL) 2](/windows/wsl/tutorials/wsl-containers#install-docker-desktop).

> [!IMPORTANT]
> Antes de executar o experimento localmente, verifique se o Docker está em execução.

### <a name="debug-experiment-locally"></a>Depurar experimento localmente

1. Em VS Code, abra a exibição de extensão de Azure Machine Learning.
1. Expanda o nó de assinatura que contém seu espaço de trabalho. Se você ainda não tiver uma, poderá [criar um Azure Machine Learning espaço de trabalho](how-to-manage-resources-vscode.md#create-a-workspace) usando a extensão.
1. Expanda o nó do espaço de trabalho.
1. Clique com o botão direito do mouse no nó **experimentos** e selecione **criar experimento**. Quando o prompt for exibido, forneça um nome para o experimento.
1. Expanda o nó **experimentos** , clique com o botão direito do mouse no experimento que você deseja executar e selecione **executar experimento**.
1. Na lista de opções para executar seu experimento, selecione **localmente**.
1. **Primeira vez, use somente no Windows**. Quando for solicitado a permitir o compartilhamento de arquivos, selecione **Sim**. Quando você habilita o compartilhamento de arquivos, ele permite que o Docker monte o diretório que contém o script no contêiner. Além disso, ele também permite que o Docker armazene os logs e as saídas de sua execução em um diretório temporário em seu sistema.
1. Selecione **Sim** para depurar seu experimento. Caso contrário, selecione **Não**. Selecionar não executará seu experimento localmente sem anexar ao depurador.
1. Selecione **criar nova configuração de execução** para criar sua configuração de execução. A configuração de execução define o script que você deseja executar, as dependências e os conjuntos de valores usados. Como alternativa, se você já tiver um, selecione-o na lista suspensa.
    1. Escolha seu ambiente. Você pode escolher entre qualquer uma das [Azure Machine Learning organizadas](resource-curated-environments.md) ou criar suas próprias.
    1. Forneça o nome do script que você deseja executar. O caminho é relativo ao diretório aberto em VS Code.
    1. Escolha se deseja usar um conjunto de Azure Machine Learning ou não. Você pode criar [Azure Machine Learning conjuntos de valores](how-to-manage-resources-vscode.md#create-dataset) usando a extensão.
    1. Debugpy é necessário para anexar o depurador ao contêiner que está executando o experimento. Para adicionar debugpy como uma dependência, selecione **Adicionar debugpy**. Caso contrário, selecione **ignorar**. Não adicionar debugpy como uma dependência executa seu experimento sem anexar ao depurador.
    1. Um arquivo de configuração que contém as definições de configuração de execução é aberto no editor. Se você estiver satisfeito com as configurações, selecione **Enviar experimento**. Como alternativa, abra a paleta de comandos (**exibir > paleta de comandos**) na barra de menus e insira o `Azure ML: Submit experiment` comando na caixa de texto.
1. Depois que o teste for enviado, uma imagem do Docker que contém o script e as configurações especificadas na sua configuração de execução serão criadas.

    Quando o processo de criação de imagem do Docker é iniciado, o conteúdo do `60_control_log.txt` fluxo de arquivos para o console de saída no vs Code.

    > [!NOTE]
    > A primeira vez que a imagem do Docker é criada pode levar vários minutos.

1. Depois que a imagem for criada, um prompt será exibido para iniciar o depurador. Defina seus pontos de interrupção em seu script e selecione **Iniciar depurador** quando você estiver pronto para iniciar a depuração. Isso anexa o depurador de VS Code ao contêiner que executa o experimento. Como alternativa, na extensão Azure Machine Learning, passe o mouse sobre o nó da sua execução atual e selecione o ícone de reprodução para iniciar o depurador.

    > [!IMPORTANT]
    > Você não pode ter várias sessões de depuração para um único experimento. No entanto, você pode depurar dois ou mais experimentos usando várias instâncias de VS Code.

Neste ponto, você deve ser capaz de percorrer e depurar seu código usando VS Code.

Se a qualquer momento você quiser cancelar a execução, clique com o botão direito do mouse no nó de execução e selecione **Cancelar execução**.

Assim como as execuções de experimento remoto, você pode expandir o nó de execução para inspecionar os logs e as saídas.

> [!TIP]
> As imagens do Docker que usam as mesmas dependências definidas em seu ambiente são reutilizadas entre as execuções. No entanto, se você executar um experimento usando um ambiente novo ou diferente, uma nova imagem será criada. Como essas imagens são salvas no armazenamento local, é recomendável remover imagens antigas ou não utilizadas do Docker. Para remover imagens do sistema, use a [CLI do Docker](https://docs.docker.com/engine/reference/commandline/rmi/) ou a [extensão vs Code Docker](https://code.visualstudio.com/docs/containers/overview).

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Depurar e solucionar problemas de pipelines do aprendizado de máquina

Em alguns casos, talvez seja necessário depurar interativamente o código Python usado em seu pipeline de ML. Usando VS Code e debugpy, você pode anexar ao código conforme ele é executado no ambiente de treinamento.

### <a name="prerequisites"></a>Pré-requisitos

* Um __espaço de trabalho Azure Machine Learning__ configurado para usar uma __rede virtual do Azure__.
* Um __pipeline Azure Machine Learning__ que usa scripts Python como parte das etapas do pipeline. Por exemplo, um PythonScriptStep.
* Um cluster de computação Azure Machine Learning, que está __na rede virtual__ e é __usado pelo pipeline para treinamento__.
* Um __ambiente de desenvolvimento__ que está __na rede virtual__. O ambiente de desenvolvimento pode ser um dos seguintes:

  * Uma máquina virtual do Azure na rede virtual
  * Uma instância de computação da VM do notebook na rede virtual
  * Um computador cliente que tem conectividade de rede privada com a rede virtual, seja por VPN ou via ExpressRoute.

Para obter mais informações sobre como usar uma rede virtual do Azure com Azure Machine Learning, consulte [visão geral de isolamento de rede virtual e privacidade](how-to-network-security-overview.md).

> [!TIP]
> Embora você possa trabalhar com Azure Machine Learning recursos que não estão atrás de uma rede virtual, é recomendável usar uma rede virtual.

### <a name="how-it-works"></a>Como ele funciona

Suas etapas de pipeline ML executam scripts Python. Esses scripts são modificados para executar as seguintes ações:

1. Registre o endereço IP do host no qual eles estão sendo executados. Você usa o endereço IP para conectar o depurador ao script.

2. Inicie o componente de depuração debugpy e aguarde até que um depurador se conecte.

3. Em seu ambiente de desenvolvimento, você monitora os logs criados pelo processo de treinamento para localizar o endereço IP em que o script está sendo executado.

4. Você informa VS Code o endereço IP para conectar o depurador usando um `launch.json` arquivo.

5. Anexe o depurador e percorra o script interativamente.

### <a name="configure-python-scripts"></a>Configurar scripts do Python

Para habilitar a depuração, faça as seguintes alterações no (s) script (es) do Python usados por etapas em seu pipeline de ML:

1. Adicione as seguintes instruções de importação:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Adicione os argumentos a seguir. Esses argumentos permitem que você habilite o depurador conforme necessário e defina o tempo limite para anexar o depurador:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. Adicione as instruções a seguir. Essas instruções carregam o contexto de execução atual para que você possa registrar em log o endereço IP do nó em que o código está sendo executado:

    ```python
    global run
    run = Run.get_context()
    ```

1. Adicione uma `if` instrução que inicia debugpy e aguarda a anexação de um depurador. Se nenhum depurador for anexado antes do tempo limite, o script continuará normalmente. Certifique-se de substituir `HOST` os `PORT` valores e é a `listen` função com o seu próprio.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

O exemplo de Python a seguir mostra um `train.py` arquivo simples que habilita a depuração:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Configurar pipeline ML

Para fornecer os pacotes do python necessários para iniciar o debugpy e obter o contexto de execução, crie um ambiente e defina `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` . Altere a versão do SDK para corresponder à que você está usando. O trecho de código a seguir demonstra como criar um ambiente:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

Na seção [Configurar scripts Python](#configure-python-scripts) , novos argumentos foram adicionados aos scripts usados pelas etapas de pipeline do ml. O trecho de código a seguir demonstra como usar esses argumentos para habilitar a depuração para o componente e definir um tempo limite. Ele também demonstra como usar o ambiente criado anteriormente por meio da configuração `runconfig=run_config` :

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
                         compute_target=aml_compute,
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

Quando o pipeline é executado, cada etapa cria uma execução filho. Se a depuração estiver habilitada, o script modificado registrará informações semelhantes ao seguinte texto no `70_driver_log.txt` para a execução do filho:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Salve o `ip_address` valor. Ele é usado na próxima seção.

> [!TIP]
> Você também pode encontrar o endereço IP na etapa executar logs para a execução do filho para este pipeline. Para obter mais informações sobre como exibir essas informações, consulte [monitorar execuções e métricas de experimento do Azure ml](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Para instalar o debugpy em seu ambiente de desenvolvimento VS Code, use o seguinte comando:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Para obter mais informações sobre como usar o debugpy com VS Code, consulte [depuração remota](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Para configurar VS Code para se comunicar com a computação Azure Machine Learning que está executando o depurador, crie uma nova configuração de depuração:

    1. No VS Code, selecione o menu __Depurar__ e depois selecione __Configurações abertas__. Um arquivo chamado __launch.json__ será aberto.

    1. Na __launch.jsno__ arquivo, localize a linha que contém `"configurations": [` e insira o texto a seguir depois dela. Altere a `"host": "<IP-ADDRESS>"` entrada para o endereço IP retornado nos logs da seção anterior. Altere a `"localRoot": "${workspaceFolder}/code/step"` entrada para um diretório local que contenha uma cópia do script que está sendo depurado:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Se já houver outras entradas na seção Configurações, adicione uma vírgula (,) após o código inserido.

        > [!TIP]
        > A prática recomendada, especialmente para pipelines, é manter os recursos de scripts em diretórios separados para que o código seja relevante apenas para cada uma das etapas. Neste exemplo, o `localRoot` valor de exemplo faz referência a `/code/step1` .
        >
        > Se você estiver Depurando vários scripts, em diretórios diferentes, crie uma seção de configuração separada para cada script.

    1. Salve o arquivo __launch.json__.

### <a name="connect-the-debugger"></a>Conectar o depurador

1. Abra VS Code e abra uma cópia local do script.
2. Defina os pontos de interrupção onde você deseja que o script Pare depois de anexado.
3. Enquanto o processo filho estiver executando o script e o `Timeout for debug connection` for exibido nos logs, use a tecla F5 ou selecione __depurar__. Quando solicitado, selecione o __Azure Machine Learning computação: configuração de depuração remota__ . Você também pode selecionar o ícone de depuração na barra lateral, a __Azure Machine Learning: entrada de depuração remota__ no menu suspenso de depuração e, em seguida, usar a seta verde para anexar o depurador.

    Neste ponto, VS Code se conecta ao debugpy no nó de computação e pára no ponto de interrupção definido anteriormente. Agora você pode percorrer o código enquanto ele é executado, exibir variáveis, ​​etc.

    > [!NOTE]
    > Se o log exibir uma entrada informando `Debugger attached = False` , o tempo limite expirou e o script continuou sem o depurador. Envie o pipeline novamente e conecte o depurador após a `Timeout for debug connection` mensagem e antes do tempo limite expirar.

## <a name="debug-and-troubleshoot-deployments"></a>Depurar e solucionar problemas de implantações

Em alguns casos, talvez seja necessário depurar interativamente o código Python contido na implantação de modelo. Por exemplo, se o script de entrada estiver falhando e não for possível determinar o motivo por meio de um registro adicional. Usando VS Code e debugpy, você pode anexar ao código em execução dentro do contêiner do Docker.

> [!IMPORTANT]
> Esse método de depuração não funciona ao usar `Model.deploy()` e `LocalWebservice.deploy_configuration` para implantar um modelo localmente. Em vez disso, você deve criar uma imagem usando o método [Model.package()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truepackage-workspace--models--inference-config-none--generate-dockerfile-false-).

As implantações de serviço Web local exigem uma instalação do Docker em funcionamento no sistema local. Para obter mais informações, confira a [Documentação do Docker](https://docs.docker.com/). Observe que, ao trabalhar com instâncias de computação, o Docker já está instalado.

### <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

1. Para instalar o debugpy em seu ambiente de desenvolvimento de VS Code local, use o seguinte comando:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Para obter mais informações sobre como usar o debugpy com VS Code, consulte [depuração remota](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Para configurar o VS Code para se comunicar com a imagem do Docker, crie uma nova configuração de depuração:

    1. Em VS Code, selecione o menu __depurar__ na extensão de __execução__ e, em seguida, selecione __abrir configurações__. Um arquivo chamado __launch.json__ será aberto.

    1. Na __launch.jsno__ arquivo, localize o item __"configurações"__ (a linha que contém `"configurations": [` ) e insira o texto a seguir após ele. 

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```
        Após a inserção, o __launch.jsno__ arquivo deve ser semelhante ao seguinte:
        ```json
        {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python: Current File",
                "type": "python",
                "request": "launch",
                "program": "${file}",
                "console": "integratedTerminal"
            },
            {
                "name": "Azure Machine Learning Deployment: Docker Debug",
                "type": "python",
                "request": "attach",
                "connect": {
                    "port": 5678,
                    "host": "0.0.0.0"
                    },
                "pathMappings": [
                    {
                        "localRoot": "${workspaceFolder}",
                        "remoteRoot": "/var/azureml-app"
                    }
                ]
            }
            ]
        }
        ```

        > [!IMPORTANT]
        > Se já houver outras entradas na seção Configurações, adicione uma vírgula ( __,__ ) após o código que você inseriu.

        Esta seção anexa o contêiner do Docker usando a porta __5678__.

    1. Salve o arquivo __launch.json__.

### <a name="create-an-image-that-includes-debugpy"></a>Criar uma imagem que inclui debugpy

1. Modifique o ambiente Conda para sua implantação para que ele inclua o debugpy. O seguinte exemplo demonstra como adicioná-lo usando o parâmetro `pip_packages`:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Para iniciar o debugpy e aguardar uma conexão quando o serviço for iniciado, adicione o seguinte à parte superior do `score.py` arquivo:

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Crie uma imagem com base na definição de ambiente e extraia a imagem para o registro local. 

    > [!NOTE]
    > Este exemplo pressupõe que `ws` aponta para o workspace do Azure Machine Learning e que `model` é o modelo implantado. O arquivo `myenv.yml` contém as dependências do Conda criadas na etapa 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Depois que a imagem tiver sido criada e baixada (esse processo pode levar mais de 10 minutos, aguarde o paciente), o caminho da imagem (inclui repositório, nome e marca, que nesse caso também é seu resumo), finalmente exibido em uma mensagem semelhante à seguinte:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Para facilitar o trabalho com a imagem localmente, você pode usar o comando a seguir para adicionar uma marca para essa imagem. Substitua `myimagepath` no comando a seguir pelo valor de local da etapa anterior.

    ```bash
    docker tag myimagepath debug:1
    ```

    Para o restante das etapas, você pode ver a imagem local como `debug:1`, em vez do valor do caminho da imagem completa.

### <a name="debug-the-service"></a>Depurar o serviço

> [!TIP]
> Se você definir um tempo limite para a conexão debugpy no `score.py` arquivo, deverá se conectar vs Code à sessão de depuração antes que o tempo limite expire. Inicie o VS Code, abra a cópia local de `score.py`, defina um ponto de interrupção e prepare-o antes de usar as etapas nesta seção.
>
> Para obter mais informações sobre depuração e definição de pontos de interrupção, confira [Depuração](https://code.visualstudio.com/Docs/editor/debugging).

1. Para iniciar um contêiner do Docker usando a imagem, use o seguinte comando:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_local_path_to_score.py>:/var/azureml-app/score.py debug:1 /bin/bash
    ```

    Isso anexa o `score.py` local a um no contêiner. Portanto, todas as alterações feitas no editor são refletidas automaticamente no contêiner

2. Para uma melhor experiência, você pode entrar no contêiner com uma nova interface do VS Code. Selecione a `Docker` extensão na barra lateral vs Code, localize seu contêiner local criado, nesta documentação `debug:1` . Clique com o botão direito do mouse nesse contêiner e selecione `"Attach Visual Studio Code"` , uma nova interface de vs Code será aberta automaticamente e essa interface mostrará o interior do seu contêiner criado.

    ![A interface VS Code do contêiner](./media/how-to-troubleshoot-deployment/container-interface.png)

3. Dentro do contêiner, execute o seguinte comando no Shell

    ```bash
    runsvdir /var/runit
    ```
    Em seguida, você pode ver a seguinte saída no Shell dentro do seu contêiner:

    ![A saída do console de execução do contêiner](./media/how-to-troubleshoot-deployment/container-run.png)

4. Para anexar VS Code ao debugpy dentro do contêiner, abra VS Code e use a tecla F5 ou selecione __depurar__. Quando solicitado, selecione o __Azure Machine Learning implantação: configuração de depuração do Docker__ . Você também pode selecionar o ícone de extensão de __execução__ na barra lateral, a __Azure Machine Learning implantação: entrada de depuração do Docker__ no menu suspenso depurar e, em seguida, usar a seta verde para anexar o depurador.

    ![O ícone de depuração, o botão Iniciar depuração e o seletor de configuração](./media/how-to-troubleshoot-deployment/start-debugging.png)
    
    Depois de clicar na seta verde e anexar o depurador, na interface VS Code de contêiner, você poderá ver algumas informações novas:
    
    ![As informações anexadas do depurador de contêiner](./media/how-to-troubleshoot-deployment/debugger-attached.png)
    
    Além disso, na sua interface de VS Code principal, o que você pode ver é o seguinte:

    ![O ponto de interrupção VS Code em score.py](./media/how-to-troubleshoot-deployment/local-debugger.png)

E agora, o local `score.py` que está anexado ao contêiner já parou nos pontos de interrupção onde você definiu. Neste ponto, VS Code se conecta ao debugpy dentro do contêiner do Docker e interrompe o contêiner do Docker no ponto de interrupção definido anteriormente. Agora você pode percorrer o código enquanto ele é executado, exibir variáveis, ​​etc.

Para obter mais informações sobre como usar o VS Code para depurar o Python, confira [Depurar o código Python](https://code.visualstudio.com/docs/python/debugging).

### <a name="stop-the-container"></a>Parar o contêiner

Para parar o contêiner, use o seguinte comando:

```bash
docker stop debug
```

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou o VS Code remoto, é possível usar uma instância de computação como computação remota do VS Code para depurar interativamente seu código. 

Saiba mais sobre solução de problemas:

* [Implantação de modelo local](how-to-troubleshoot-deployment-local.md)
* [Implantação de modelo remoto](how-to-troubleshoot-deployment.md)
* [Pipelines do aprendizado de máquina](how-to-debug-pipelines.md)
* [ParallelRunStep](how-to-debug-parallel-run-step.md)

