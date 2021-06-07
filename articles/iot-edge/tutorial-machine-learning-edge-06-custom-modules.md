---
title: 'Tutorial: Criar e implantar módulos personalizados – Machine Learning no Azure IoT Edge'
description: Este tutorial mostra como criar e implantar módulos do IoT Edge que processam dados de dispositivos folha por meio de um modelo de machine learning e, em seguida, enviam os insights para o Hub IoT.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 5c096b26f31bdf92bb5ab91c8dad7876f228ff14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462789"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Tutorial: Criar e implantar módulos do IoT Edge personalizados

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Neste artigo, criamos três módulos do IoT Edge que recebem mensagens de dispositivos IoT folha, executamos os dados por meio do seu modelo de machine learning e encaminhamos os insights para o Hub IoT.

O hub do IoT Edge facilita a comunicação de módulo para módulo. Usar o hub do IoT Edge como um agente de mensagem mantém os módulos independentes uns dos outros. Os módulos precisam apenas especificar as entradas nas quais eles aceitam mensagens e as saídas nas quais eles gravam mensagens.

Queremos que o dispositivo IoT Edge realize quatro coisas para nós:

* Receber dados dos dispositivos folha.
* Prever a RUL (vida útil restante) do dispositivo que enviou os dados.
* Enviar uma mensagem com o RUL do dispositivo para o Hub IoT. Essa função poderia ser modificada para enviar dados somente se o RUL chegasse a um valor inferior a um nível especificado.
* Salvar os dados do dispositivo folha para um arquivo local no dispositivo IoT Edge. Esse arquivo de dados é carregado periodicamente para o Hub IoT para refinar o treinamento do modelo de machine learning. Usar o upload de arquivos em vez do streaming de mensagens constantes é mais econômico.

Para realizar essas tarefas, usamos três módulos personalizados:

* **Classificador de RUL:** o módulo turboFanRulClassifier criado em [Treinar e implantar um modelo do Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md) é um módulo de machine learning padrão, que expõe uma entrada chamada “amlInput” e uma saída chamada “amlOutput”. O "amlInput" espera que sua entrada tenha a aparência exata da entrada que enviamos para o serviço Web baseado em ACI. Da mesma forma, "amlOutput" retorna os mesmos dados que o serviço Web.

* **Gravador Avro:** este módulo recebe mensagens na entrada “avroModuleInput” e persiste a mensagem no formato Avro em disco para upload posterior para o Hub IoT.

* **Módulo do roteador:** O módulo do roteador recebe mensagens de dispositivos folha downstream e, em seguida, formata e envia as mensagens para o classificador. O módulo recebe as mensagens do classificador e encaminha a mensagem para o módulo do gravador Avro. Finalmente, o módulo envia apenas a previsão da RUL para o Hub IoT.

  * Entradas:
    * **deviceInput**: recebe mensagens de dispositivos folha
    * **rulInput:** recebe mensagens do "amlOutput"

  * Saídas:
    * **classify:** envia mensagens para "amlInput"
    * **writeAvro:** envia mensagens para "avroModuleInput"
    * **toIotHub:** envia mensagens para $upstream, que as passa para o Hub IoT conectado

O seguinte diagrama mostra os módulos, as entradas, as saídas e as rotas do hub do IoT Edge para a solução completa:

![Diagrama de arquitetura de três módulos do IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

As etapas deste artigo normalmente são realizadas por um desenvolvedor de nuvem.

Nesta seção do tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Criar um módulo do IoT Edge com base em um código personalizado.
> * Gerar uma imagem do Docker com base no módulo personalizado.
> * Reconfigurar o roteamento do hub IoT para dar suporte aos módulos personalizados.
> * Criar, publicar e implantar seus módulos personalizados.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo faz parte de uma série para um tutorial sobre como usar o Azure Machine Learning no IoT Edge. Cada artigo da série se baseia no trabalho do artigo anterior. Se você chegou a este artigo diretamente, acesse o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série.

## <a name="create-a-new-iot-edge-solution"></a>Criar uma solução do IoT Edge

Durante a execução do segundo dos nossos dois Azure Notebooks, criamos e publicamos uma imagem de contêiner que contém nosso modelo de RUL. O Azure Machine Learning, como parte do processo de criação da imagem, empacotou esse modelo para tornar a imagem implantável como um módulo do Azure IoT Edge.

Nesta etapa, criaremos uma solução do Azure IoT Edge usando o módulo do “Azure Machine Learning” e apontaremos o módulo para a imagem que publicamos usando o Azure Notebooks.

1. Abra uma sessão da Área de Trabalho Remota na VM de desenvolvimento.

1. Abra a pasta **C:\\source\\IoTEdgeAndMlSample** no Visual Studio Code.

1. Clique com o botão direito do mouse no painel do explorador (no espaço em branco) e selecione **Nova Solução do IoT Edge**.

    ![Criar solução do IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

1. Aceite o nome da solução padrão **EdgeSolution**.

1. Escolha o **Azure Machine Learning** como o modelo de módulo.

1. Dê ao módulo o nome **turbofanRulClassifier**.

1. Escolha o Workspace do Machine Learning. Esse é o workspace do **turboFanDemo** que você criou em [Tutorial: Treinar e implantar um modelo do Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)

1. Selecione a imagem criada ao executar o Azure Notebook.

1. Examine a solução e observe os arquivos que foram criados:

   * **deployment.template.json:** esse arquivo contém a definição de cada um dos módulos na solução. Há três seções às quais você deve prestar atenção neste arquivo:

     * **Credenciais de Registro:** define o conjunto de registros de contêiner personalizados que você está usando em sua solução. Agora, ele deve conter o Registro do Workspace do Machine Learning, que é o local em que sua imagem do Azure Machine Learning foi armazenada. É possível ter qualquer número de registros de contêiner, mas, para simplificar, usaremos este registro para todos os módulos.

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Módulos:** esta seção contém o conjunto de módulos definidos pelo usuário que acompanham esta solução. A definição do módulo turbofanRulClassifier aponta para a imagem no registro de contêiner. Conforme adicionamos mais módulos à solução, eles serão exibidos nesta seção.

       ```json
        "modules": {
          "turbofanRulClassifier": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "turbofandemo2cd74296.azurecr.io/edgemlsample:1",
              "createOptions": {}
            }
          }
        }
       ```

     * **Rotas:** trabalharemos um pouco com rotas neste tutorial. As rotas definem como os módulos se comunicam uns com os outros. A rota existente definida pelo modelo não corresponde ao roteamento de que precisamos. Exclua a rota `turbofanRulClassifierToIoTHub`.

       ```json
        "$edgeHub": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "routes": {
              "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/* INTO $upstream"
            },
            "storeAndForwardConfiguration": {
              "timeToLiveSecs": 7200
            }
          }
        }
       ```

   * **deployment.debug.template.json:** este arquivo é a versão de depuração de deployment.template.json. Normalmente, devemos manter esse arquivo sincronizado com o conteúdo do arquivo deployment.template.json, mas isso não é necessário neste tutorial.

   * **.env:** é neste arquivo que você deve fornecer o nome de usuário e a senha para acessar seu Registro.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

1. Clique com o botão direito do mouse no arquivo deployment.template.json no explorador do Visual Studio Code e selecione **Criar Solução IoT Edge**.

1. Observe que esse comando cria uma pasta de configuração com um arquivo deployment.amd64.json. Este arquivo é o modelo de implantação concreto para a solução.

## <a name="add-router-module"></a>Adicionar módulo Roteador

Em seguida, adicionaremos o módulo Roteador à nossa solução. O módulo Roteador lida com várias responsabilidades para a nossa solução:

* **Receber mensagens de dispositivos folha:** as mensagens chegam ao dispositivo IoT Edge de dispositivos downstream, o módulo Roteador recebe a mensagem e começa a orquestrar o roteamento da mensagem.
* **Enviar mensagens para o módulo Classificador de RUL:** quando uma nova mensagem é recebida de um dispositivo downstream, o módulo Roteador transforma a mensagem no formato que o classificador de RUL espera. O Roteador envia a mensagem para o Classificador de RUL para uma previsão de RUL. Depois que o classificador fizer uma previsão, ele enviará a mensagem de volta para o módulo Roteador.
* **Enviar mensagens de RUL para o Hub IoT:** quando o Roteador recebe mensagens do classificador, ele transforma a mensagem para conter somente as informações essenciais, a ID e a RUL do dispositivo e envia a mensagem abreviada para o hub IoT. Um ajuste adicional, que não fizemos aqui, enviaria mensagens para o Hub IoT somente quando a previsão de RUL ficasse abaixo de um limite (por exemplo, quando a RUL for menor que 100 ciclos). Filtrar dessa maneira reduziria o volume de mensagens e o custo do hub IoT.
* **Enviar mensagem para o módulo Gravador Avro:** para preservar todos os dados enviados pelo dispositivo downstream, o módulo Roteador envia toda a mensagem recebida do classificador para o módulo Gravador Avro, que persistirá e fará upload dos dados usando o upload de arquivos do Hub IoT.

O módulo Roteador é uma parte importante da solução, que garante que as mensagens sejam processadas na ordem correta.

### <a name="create-the-module-and-copy-files"></a>Criar o módulo e copiar os arquivos

1. Clique com o botão direito do mouse na pasta de módulos no Visual Studio Code e escolha **Adicionar módulo do IoT Edge**.

1. Escolha **Módulo C#** como o modelo de módulo.

1. Dê ao módulo o nome **turbofanRouter**.

1. Quando solicitado para seu Repositório de Imagens do Docker, use o Registro do Workspace do Machine Learning (é possível localizar o Registro no nó registryCredentials do seu arquivo *deployment.template.json*). Esse valor é o endereço totalmente qualificado para o registro, como **\<your registry\>.azurecr.io/turbofanrouter**.

    > [!NOTE]
    > Neste artigo, usamos o Registro de Contêiner do Azure criado pelo Workspace do Azure Machine Learning. Isso destina-se meramente para sua conveniência. Poderíamos ter criado um registro de contêiner e publicado nossos módulos lá.

1. No Terminal, usando um shell de prompt de comando, copie os arquivos do módulo de exemplo para a solução.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

1. Aceite o prompt para substituir o arquivo program.cs.

### <a name="build-router-module"></a>Criar módulo do roteador

1. No Visual Studio Code, selecione **Terminal** > **Configurar Tarefa de Build Padrão**.

1. Selecione **Criar arquivo tasks.json com base em modelo**.

1. Selecione **.NET Core**.

1. Substitua o conteúdo do arquivo tasks.json pelo código a seguir.

    ```json
    {
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

1. Salve e feche o tasks.json.

1. Execute o build com `Ctrl + Shift + B` ou **Terminal** > **Executar tarefa de build**.

### <a name="set-up-module-routes"></a>Configurar rotas de módulo

Conforme mencionado acima, o runtime do IoT Edge usa rotas configuradas no arquivo *deployment.template.json* para gerenciar a comunicação entre módulos fracamente acoplados. Nesta seção, analisaremos detalhadamente como configurar as rotas para o módulo turbofanRouter. Abordaremos as rotas de entrada primeiro e, em seguida, passaremos para as saídas.

#### <a name="inputs"></a>Entradas

1. No método Init() de Program.cs, registramos dois retornos de chamada para o módulo:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. O primeiro retorno de chamada escuta as mensagens enviadas para o coletor **deviceInput**. No diagrama acima, vemos que convém rotear mensagens de qualquer dispositivo folha para essa entrada. No arquivo *deployment.template.json*, adicione uma rota que informa o hub de borda para rotear qualquer mensagem recebida pelo dispositivo IoT Edge que não foi enviada por um módulo do IoT Edge na entrada chamada “deviceInput” no módulo turbofanRouter:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Em seguida, adicione uma rota para mensagens do módulo rulClassifier no módulo turbofanRouter:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>outputs

Adicione mais quatro rotas ao parâmetro de rota $edgeHub para lidar com saídas do módulo Roteador.

1. Program.cs define o método SendMessageToClassifier(), que usa o cliente de módulo para enviar uma mensagem ao classificador de RUL usando a rota:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() usa o cliente de módulo para enviar apenas os dados de RUL do dispositivo para o Hub IoT por meio da rota:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() usa o cliente de módulo para enviar a mensagem com os dados de RUL adicionados ao módulo avroFileWriter.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() envia mensagens com falha upstream no Hub IoT em que elas podem ser roteadas futuramente.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Com todas as rotas usadas juntas, seu nó “$edgeHub” deve ter uma aparência como a do JSON a seguir:

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

  > [!NOTE]
  > Adicionar o módulo turbofanRouter criava a seguinte rota adicional: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`. Remova essa rota, deixando apenas as rotas listadas acima em seu arquivo deployment.template.json.

## <a name="add-avro-writer-module"></a>Adicionar módulo Gravador Avro

O módulo Gravador Avro tem duas responsabilidades em nossa solução, armazenar mensagens e fazer upload de arquivos.

* **Armazenar mensagens**: quando o módulo Gravador Avro recebe uma mensagem, ele grava a mensagem no sistema de arquivos local em formato Avro. Usamos uma montagem associada, que monta um diretório (nesse caso /data/avrofiles) em um caminho no contêiner do módulo. Essa montagem permite que o módulo seja gravado em um caminho local (/avrofiles) e que você faça esses arquivos serem acessíveis diretamente do dispositivo IoT Edge.

* **Fazer upload de arquivos**: o módulo Gravador Avro usa o recurso de upload de arquivos do Hub IoT do Azure para fazer upload de arquivos em uma conta de armazenamento do Azure. Depois que um arquivo é carregado com êxito, o módulo exclui o arquivo do disco

### <a name="create-module-and-copy-files"></a>Criar módulo e copiar arquivos

1. No Visual Studio Code, selecione **Exibição** > **Paleta de Comandos** e pesquise e escolha **Python: selecionar interpretador**.

1. Selecione seu Python versão 3.7 ou posterior instalado.

1. Clique com o botão direito do mouse na pasta de módulos no Visual Studio Code e escolha **Adicionar módulo do IoT Edge**.

1. Escolha **Módulo de Python**.

1. Nomeie o módulo `avroFileWriter`.

1. Quando solicitado para seu Repositório de Imagens do Docker, use o mesmo Registro que você usou ao adicionar o módulo Roteador.

1. Copie arquivos de exemplo de módulo para a solução.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Aceite a substituição de main.py.

1. Observe que filemanager.py e schema.py foram adicionados à solução e que main.py foi atualizado.

> [!NOTE]
> Quando você abre um arquivo Python, talvez precise instalar pylint. Não é necessário instalar o linter para concluir este tutorial.

### <a name="bind-mount-for-data-files"></a>Montagem associada para arquivos de dados

Conforme mencionado anteriormente, o módulo de gravador depende da presença de uma montagem associada para gravar arquivos do Avro no sistema de arquivos do dispositivo.

#### <a name="add-directory-to-device"></a>Adicionar diretório ao dispositivo

1. No portal do Azure, inicie a VM do dispositivo do IoT Edge se ela não estiver em execução. Conecte-se a ela usando SSH. A conexão requer o nome DNS que você pode copiar da página de visão geral da VM no portal do Azure.

   ```cmd
   ssh -l <user>@<vm name>.<region>.cloudapp.azure.com
   ```

1. Após fazer logon, crie o diretório que conterá as mensagens do dispositivo folha salvas.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

1. Atualize as permissões de diretório para torná-lo gravável pelo contêiner.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

1. Valide se o diretório agora tem a permissão w (gravação) para o usuário, o grupo e o proprietário.

   ```bash
   ls -la /data
   ```

   ![Permissões de diretório para avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Adicionar o diretório ao módulo

Para adicionar o diretório ao contêiner do módulo, modificaremos os Dockerfiles associados ao módulo avroFileWriter. Há três Dockerfiles associados ao módulo: Dockerfile.amd64, Dockerfile.amd64.debug e Dockerfile.arm32v7. Esses arquivos devem ser mantidos em sincronia caso desejemos depurar ou implantar em um dispositivo arm32. Neste artigo, concentre-se apenas em Dockerfile.amd64.

1. Em sua VM de desenvolvimento, abra o arquivo **C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\Dockerfile.amd64**.

1. Modifique o arquivo para que ele tenha a aparência do seguinte exemplo:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   Os comandos `mkdir` e `chown` instruem o processo de build do Docker a criar um diretório de nível superior chamado /avrofiles na imagem e, em seguida, a tornar o moduleuser o proprietário desse diretório. É importante que esses comandos sejam inseridos depois que o usuário do módulo for adicionado à imagem com o comando `useradd` e antes que o contexto alterne para o moduleuser (moduleuser USER).

1. Se necessário, faça as alterações correspondentes em Dockerfile.amd64.debug e Dockerfile.arm32v7.

#### <a name="add-bind-configuration-to-the-avrofilewriter"></a>Adicionar configuração de associação ao avroFileWriter

A etapa final da criação da associação é atualizar os arquivos deployment.template.json (e deployment.debug.template.json) com as informações de associação.

1. Abra deployment.template.json.

2. Modifique a definição de módulo do avroFileWriter adicionando o parâmetro `Binds` que aponta o diretório de contêiner /avrofiles para o diretório local no dispositivo de borda. Sua definição de módulo deve corresponder a este exemplo:

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

### <a name="bind-mount-for-access-to-configyaml"></a>Montagem associada para acesso a config.yaml

É necessário adicionar mais uma associação para o módulo de gravador. Essa associação fornece ao módulo acesso para ler a cadeia de conexão do arquivo /etc/iotedge/config.yaml no dispositivo IoT Edge. É necessário que a cadeia de conexão crie um IoTHubClient para podermos chamar o método upload\_blob\_async para fazer upload de arquivos para o hub IoT. As etapas para adicionar essa associação são semelhantes às da seção anterior.

#### <a name="update-directory-permission"></a>Atualizar permissão de diretório

1. Conecte-se ao seu dispositivo IoT Edge usando SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. Adicione permissão de leitura ao arquivo config.yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

1. Valide se as permissões foram definidas corretamente.

   ```bash
   ls -la /etc/iotedge/
   ```

1. Verifique se as permissões para config.yaml são **-r--r--r--** .

#### <a name="add-directory-to-module"></a>Adicionar diretório ao módulo

1. Em seu computador de desenvolvimento, abra o arquivo **Dockerfile.amd64**.

1. Adicione um conjunto adicional de comandos `mkdir` e `chown` ao arquivo para que ele tenha a seguinte aparência:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

1. Faça as alterações correspondentes em Dockerfile.amd64.debug e em Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Atualizar a configuração do módulo

1. Abra o arquivo **deployment.template.json**.

1. Modifique a definição de módulo de avroFileWriter adicionando uma segunda linha ao parâmetro `Binds` que aponta o diretório de contêiner (/app/iotconfig) para o diretório local no dispositivo (/etc/iotedge).

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

1. Faça as alterações correspondentes em deployment.debug.template.json.

## <a name="install-dependencies"></a>Instalar dependências

O módulo de gravador usa uma dependência em duas bibliotecas Python, fastavro e PyYAML. É necessário instalar as dependências em nosso computador de desenvolvimento e instruir o processo de build do Docker a instalá-las na imagem do nosso módulo.

### <a name="pyyaml"></a>PyYAML

1. No computador de desenvolvimento, abra o arquivo `C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\requirements.txt` e adicione "pyyaml" a uma nova linha no arquivo.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

1. Abra o arquivo **Dockerfile.amd64** e adicione um comando `pip install` para atualizar setuptools.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

1. Em um prompt de comando, instale o pyyaml em seu computador de desenvolvimento.

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. Em requirements.txt, adicione fastavro após pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

1. Instale o fastavro em seu computador de desenvolvimento.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Reconfigurar Hub IoT

Introduzindo o dispositivo e os módulos do IoT Edge no sistema, mudamos nossas expectativas sobre quais dados serão enviados ao hub e para qual finalidade. É necessário reconfigurar o roteamento no hub para lidar com nossa nova realidade.

> [!NOTE]
> Reconfiguramos o hub antes de implantar módulos, porque algumas configurações do hub, principalmente o upload de arquivo, precisam ser corretamente definidas para o módulo avroFileWriter ser executado devidamente

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Configurar rota para mensagens RUL no Hub IoT

Com o roteador e o classificador em vigor, esperamos receber mensagens granulares que contêm apenas a identificação do dispositivo e a previsão de RUL dele. Gostaríamos de rotear os dados de RUL para sua própria localização de armazenamento no qual podemos monitorar o status dos dispositivos, criar relatórios e disparar alertas, conforme necessário. Ao mesmo tempo, queremos que os dados de dispositivo que ainda estão sendo enviados diretamente por um dispositivo folha que ainda não foi anexado ao nosso dispositivo IoT Edge continuem sendo roteados para a localização de armazenamento atual.

#### <a name="create-a-rul-message-route"></a>Criar uma rota de mensagem RUL

1. No portal do Azure, navegue até o Hub IoT.

1. No menu do painel esquerdo, em **Mensagens**, selecione **Roteamentos de mensagens**.

1. Na guia **Rotas**, selecione **Adicionar**.

1. Dê a rota o nome **RulMessageRoute**.

1. Selecione **Adicionar ponto de extremidade** ao lado do seletor **Ponto de extremidade** e escolha **Armazenamento**.

1. Na página **Adicionar um ponto de extremidade de armazenamento**, dê ao ponto de extremidade o nome **ruldata**.

1. Selecione **Escolher um contêiner**.

1. Na página **Contas de armazenamento**, localize a conta de armazenamento que você está usando em todo este tutorial, que tem um nome semelhante a **iotedgeandml\<unique suffix\>** .

1. Selecione o contêiner **ruldata** e clique em **Selecionar**.

1. De volta à página **Adicionar um ponto de extremidade de armazenamento**, selecione **Criar** para criar o ponto de extremidade de armazenamento.

1. De volta à página **Adicionar uma rota**, para **Consulta de roteamento**, substitua `true` pela seguinte consulta:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

1. Expanda a seção **Teste** e, em seguida, a seção **Corpo da mensagem**. Substitua o corpo da mensagem por este exemplo de nossas mensagens esperadas:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

1. Selecione **Testar rota**. Se o teste for bem-sucedido, você verá “A mensagem correspondeu à consulta.”

1. Clique em **Save** (Salvar).

#### <a name="update-turbofandevicedatatostorage-route"></a>Atualize a rota turbofanDeviceDataToStorage

Não convém rotear os novos dados de previsão para nossa antiga localização de armazenamento; portanto, atualize a rota para impedir isso.

1. Na página **Roteamento de mensagens** do Hub IoT, selecione a guia **Rotas**.

1. Selecione **turbofanDeviceDataToStorage** ou qualquer nome que você tenha dado à sua rota inicial de dados do dispositivo.

1. Atualizar a consulta de roteamento para

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

1. Expanda a seção **Teste** e, em seguida, a seção **Corpo da mensagem**. Substitua a mensagem por este exemplo de nossas mensagens esperadas:

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

1. Selecione **Testar rota**. Se o teste for bem-sucedido, você verá “A mensagem correspondeu à consulta.”

1. Clique em **Salvar**.

### <a name="configure-file-upload"></a>Configurar o upload de arquivo

Configure o recurso de upload de arquivo do Hub IoT para permitir que o módulo de gravador de arquivos faça upload dos arquivos no armazenamento.

1. No menu do painel esquerdo do Hub IoT, em **Mensagens**, escolha **Upload de arquivo**.

1. Selecione **Contêiner de Armazenamento do Azure**.

1. Selecione sua conta de armazenamento na lista.

1. Selecione o contêiner que começa com **azureml-blobstore** acrescentado a um GUID e clique em **Selecionar**.

1. Clique em **Salvar**. O portal notificará você quando o salvamento for concluído.

> [!Note]
> Não ativaremos a notificação de upload para este tutorial, mas confira [Receber uma notificação de upload de arquivo](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) para obter detalhes sobre como manipular a notificação de upload de arquivo.

## <a name="build-publish-and-deploy-modules"></a>Criar, publicar e implantar módulos

Agora que fizemos as alterações de configuração, estamos prontos para criar as imagens e publicá-las no Registro de Contêiner do Azure. O processo de build usa o arquivo deployment.template.json para determinar quais módulos precisam ser criados. As configurações de cada módulo, incluindo a versão, são encontradas no arquivo module.json na pasta do módulo. O processo de build executa um build do Docker primeiro no Dockerfiles que corresponde à configuração atual encontrada no arquivo module.json para criar uma imagem. Em seguida, ele publica a imagem no Registro do arquivo module.json com uma tag de versão que corresponde ao arquivo module.json. Por fim, ele produz um manifesto de implantação específico de configuração (por exemplo, deployment.amd64.json), que implantaremos no dispositivo IoT Edge. O dispositivo IoT Edge lê as informações no manifesto de implantação e, com base nas instruções, baixará os módulos, configurará as rotas e definirá propriedades desejadas. Esse método de implantação tem dois efeitos colaterais aos quais você deve estar atento:

* **Atraso na implantação:** como o runtime do IoT Edge deve reconhecer a alteração em suas propriedades desejadas antes do começo da reconfiguração, pode levar algum tempo após você implantar seus módulos até que o runtime os selecione e inicie a atualização do dispositivo IoT Edge.

* **As versões do módulo importam:** se você publicar uma nova versão do contêiner de um módulo em seu registro de contêiner usando as mesmas marcas de versão que o módulo anterior, o runtime não baixará a nova versão do módulo. Ele faz uma comparação da tag de versão da imagem local e da imagem desejada do manifesto de implantação. Se essas versões coincidirem, o runtime não executará nenhuma ação. Portanto, será importante incrementar a versão do seu módulo sempre que você desejar implantar novas alterações. Incremente a versão alterando a propriedade **version** na propriedade **tag** no arquivo module.json para o módulo que você está alterando. Em seguida, crie e publique o módulo.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Criar e publicar

1. Em sua VM de desenvolvimento, inicie o Docker se ele não estiver em execução.

1. No Visual Studio Code, inicie um novo terminal com um prompt de comando e faça logon no seu ACR (Registro de Contêiner do Azure).

  Você pode encontrar os valores de nome de usuário, senha e servidor de logon necessários no portal do Azure. O nome do registro de contêiner tem o formato "turbofandemo\<unique id\>". No menu do painel esquerdo, em **Configurações**, selecione **Chaves de acesso** para exibi-las.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. No Visual Studio Code, clique com o botão direito do mouse em deployment.template.json e escolha **Criar e Efetuar Push da Solução IoT Edge**.

### <a name="view-modules-in-the-registry"></a>Exibir módulos no Registro

Quando o build for concluído com sucesso, poderemos usar o portal do Azure para examinar nossos módulos publicados.

1. Abra o Registro de Contêiner do Azure para este tutorial. O nome do registro de contêiner tem o formato "turbofandemo\<unique id\>". 

1. No menu do painel esquerdo, em **Serviços**, selecione **Repositórios**.

1. Observe que os dois módulos criados, **avrofilewriter** e **turbofanrouter**, são exibidos como repositórios.

1. Selecione **turbofanrouter** e observe que você publicou uma imagem marcada como 0.0.1-amd64.

   ![Exibir a primeira versão marcada de turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Implantar módulos no dispositivo IoT Edge

Criamos e configuramos os módulos em nossa solução. Agora, nós os implantaremos no dispositivo IoT Edge.

1. No Visual Studio Code, clique com o botão direito do mouse no arquivo **deployment.amd64.json** na pasta de configuração.

1. Escolha **Criar implantação para dispositivo único**.

1. Escolha seu dispositivo IoT Edge, **aaTurboFanEdgeDevice**.

1. Atualize o painel de dispositivos do Hub IoT do Azure no explorador do Visual Studio Code. Você deverá ver que os três novos módulos são implantados, mas ainda não estão em execução.

1. Atualize novamente após alguns minutos e você verá os módulos em execução.

   ![Exibir módulos em execução no Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Pode levar vários minutos para os módulos iniciarem e se estabilizarem em um estado de execução. Durante esse tempo, você pode ver os módulos iniciarem e pararem conforme eles tentam estabelecer uma conexão com o módulo de hub do IoT Edge.

## <a name="diagnosing-failures"></a>Diagnosticar falhas

Nesta seção, compartilhamos algumas técnicas para entender o que deu errado com um módulo ou módulos. Geralmente, uma falha pode ser identificada primeiro no status no Visual Studio Code.

### <a name="identify-failed-modules"></a>Identificar módulos com falha

* **Visual Studio Code:** examine o painel de dispositivos do Hub IoT do Azure. Se a maioria dos módulos estiver em um estado de execução, mas um deles for interrompido, será necessário investigar esse módulo interrompido mais a fundo. Se todos os módulos estiverem em um estado interrompido por um longo período de tempo, isso poderá indicar uma falha também.

* **Portal do Azure:** navegando até seu hub IoT no portal e, em seguida, localizando a página de detalhes do dispositivo (em IoT Edge, analisar seu dispositivo) você pode constatar que um módulo relatou um erro ou que nunca reportou nada para o hub IoT.

### <a name="diagnosing-from-the-device"></a>Diagnosticar com base no dispositivo

Ao fazer logon no dispositivo do IoT Edge (em nosso caso, a VM do Linux), é possível obter acesso a uma boa dose de informações sobre o status dos seus módulos. O principal mecanismo que usamos são os comandos do Docker que nos permitem examinar os contêineres e as imagens no dispositivo.

1. Faça logon no dispositivo do IoT Edge:

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. Liste todos os contêineres em execução. Esperamos ver um contêiner para cada módulo com um nome que corresponde ao módulo. Além disso, esse comando lista a imagem exata do contêiner, incluindo a versão, para que você possa atender às suas expectativas. Também é possível listar imagens substituindo “image” em “container” no comando.

   ```bash
   sudo docker container ls
   ```

1. Obtenha os logs de um contêiner. Este comando gera tudo o que foi escrito para StdErr e StdOut no contêiner. Esse comando funciona para contêineres que iniciaram e pararam por algum motivo. Também é útil para entender o que estava acontecendo com os contêineres edgeAgent e edgeHub.

   ```bash
   sudo docker container logs <container id>
   ```

1. Inspecione um contêiner. Esse comando oferece milhares de informações sobre a imagem. Os dados podem ser filtrados dependendo do que você está procurando. Como exemplo, se desejar ver se as associações no avroFileWriter estão corretas, é possível usar o comando:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

1. Conecte-se a um contêiner em execução. Este comando poderá ser útil se você desejar examinar o contêiner enquanto ele estiver em execução:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial faz parte de um conjunto em que cada artigo se baseia no trabalho feito nos anteriores. Aguarde para limpar todos os recursos até concluir o tutorial final.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, criamos uma Solução IoT Edge no Visual Studio Code com três módulos: um classificador, um roteador e um gravador/carregador de arquivos. Configuramos as rotas para permitir que os módulos se comuniquem entre si no dispositivo de borda. Modificamos a configuração do dispositivo de borda e atualizamos os Dockerfiles para instalar dependências e adicionar montagens associadas aos contêineres de módulos. 

Em seguida, atualizamos a configuração do Hub IoT para rotear nossas mensagens com base no tipo e para lidar com uploads de arquivo. Com tudo em vigor, implantamos os módulos no dispositivo IoT Edge e verificamos se os módulos estavam sendo executados corretamente.

Passe para o próximo artigo para começar a enviar dados e ver sua solução em ação.

> [!div class="nextstepaction"]
> [Enviar dados por meio do gateway transparente](tutorial-machine-learning-edge-07-send-data-to-hub.md)