---
title: Desenvolver e depurar os módulos no Visual Studio – Azure IoT Edge | Microsoft Docs
description: Usar o Visual Studio 2017 para desenvolver e depurar os módulos do Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/03/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f2228726d4edc25efe46a660d25d398959c3ea59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596012"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-modules-for-azure-iot-edge-preview"></a>Usar o Visual Studio 2017 para desenvolver e depurar os módulos do Azure IoT Edge (versão prévia)

Você pode transformar sua lógica de negócios em módulos do Azure IoT Edge. Este artigo mostra como usar o Visual Studio 2017 como a principal ferramenta para desenvolver e depurar os módulos.

As ferramentas do Azure IoT Edge para Visual Studio fornecem os seguintes benefícios:

- Criar, editar, compilar, executar e depurar soluções do Azure IoT Edge e módulos no seu computador de desenvolvimento local.
- Implantar a solução do Azure IoT Edge no dispositivo do Azure IoT Edge por meio do Hub IoT do Azure.
- Os módulos do IoT do Azure em C de código ou C# ao mesmo tempo, manter todos os benefícios de desenvolvimento do Visual Studio.
- Gerenciar dispositivos do Azure IoT Edge e módulos com interface do usuário.

Este artigo mostra como usar as ferramentas do Azure IoT Edge para Visual Studio 2017 para desenvolver seus módulos do IoT Edge. Você também aprenderá como implantar seu projeto no dispositivo Azure IoT Edge.

> [!TIP]
> A estrutura do projeto do IoT Edge criada pelo Visual Studio não é a mesma do Visual Studio Code.
  
## <a name="prerequisites"></a>Pré-requisitos

Este artigo presume que você esteja usando um computador ou uma máquina virtual que executa Windows como seu computador de desenvolvimento. Seu dispositivo do IoT Edge pode ser outro dispositivo físico.

Como este artigo usa o Visual Studio 2017 como ferramenta de desenvolvimento principal, instale o Visual Studio. Certifique-se de incluir a **desenvolvimento do Azure** e **desenvolvimento para Desktop com C++** cargas de trabalho em sua instalação do Visual Studio 2017. Você pode [modificar o Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) para adicionar as cargas de trabalho necessárias.

Depois que o Visual Studio 2017 estiver pronto, as ferramentas e os componentes a seguir também são necessários:

- Faça o download e instale a [extensão do Azure IoT Edge (Preview)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) no marketplace do Visual Studio para criar um projeto do IoT Edge no Visual Studio 2017.

- Fazer o download e instalar o [Docker Community Edition](https://docs.docker.com/install/) no computador de desenvolvimento para compilar e executar suas imagens de módulo. É necessário definir o Docker CE para ser executado no modo de contêiner do Linux ou no modo de contêiner do Windows.

- Para configurar o ambiente de desenvolvimento local para depurar, executar e testar a solução IoT Edge, instale a [Ferramenta de desenvolvimento do Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Instale o [Python (2.7/3.6) e o Pip](https://www.python.org/) e depois instale o pacote **iotedgehubdev** ao executar o comando a seguir em seu terminal. Certifique-se de que sua versão da ferramenta de desenvolvimento do Azure IoT EdgeHub é posterior à versão 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Clonar o repositório e instalar o Gerenciador de biblioteca de Vcpkg e, em seguida, instale o **pacote do azure-iot-sdk-c** para Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Você pode usar um registro do Docker local, em vez de um registro em nuvem, para fins de protótipo e teste.

- Para testar o módulo em um dispositivo, é necessário um hub IoT ativo com pelo menos um dispositivo do IoT Edge. Para usar seu computador como um dispositivo do IoT Edge, siga as etapas no guia de início rápido para [Linux](quickstart-linux.md) ou [Windows](quickstart.md). Se estiver executando o daemon do IoT Edge no computador de desenvolvimento, talvez seja necessário interromper o EdgeHub e o EdgeAgent antes de iniciar o desenvolvimento no Visual Studio.

### <a name="check-your-tools-version"></a>Verificar a versão de ferramentas

1. No menu **Ferramentas**, selecione **Extensões e atualizações**. Expanda **Instalado > Ferramentas** e você poderá encontrar as **Ferramentas do Azure IoT Edge** e o **Cloud Explorer para Visual Studio**.

1. Observe a versão instalada. Você pode comparar esta versão com a versão mais recente no Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools))

1. Se a sua versão for mais antiga do que a disponível no Visual Studio Marketplace, atualize suas ferramentas no Visual Studio conforme mostrado na seção a seguir.

### <a name="update-your-tools"></a>Atualize suas ferramentas

1. Na caixa de diálogo **Extensões e Atualizações**, expanda **Atualizações > Visual Studio Marketplace**, escolha **Ferramentas do Azure IoT Edge** ou **Cloud Explorer para Visual Studio** e selecione **Atualizar**.

1. Depois de fazer o download da atualização das ferramentas, feche o Visual Studio para disparar a atualização das ferramentas usando o instalador VSIX.

1. No instalador, selecione **OK** para iniciar e depois **Modificar** para atualizar as ferramentas.

1. Depois que a atualização for concluída, selecione **Fechar** e reinicie o Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Criar um projeto do Azure IoT Edge

O modelo de projeto do Azure IoT Edge no Visual Studio cria um projeto que pode ser implantado em dispositivos do Azure IoT Edge no Hub IoT do Azure. Primeiro crie uma solução do Azure IoT Edge e, em seguida, gerar o primeiro módulo na solução. Cada solução do IoT Edge pode conter mais de um módulo.

1. No Visual Studio, selecione **Novo** > **Projeto** no menu **Arquivo**.

1. No **novo projeto** caixa de diálogo, selecione **instalado**, selecione **IoT do Azure**, selecione **Azure IoT Edge**, insira um nome para seu projeto e Especifique o local e, em seguida, selecione **Okey**. O nome do projeto padrão é **AzureIoTEdgeApp1**.

   ![Novo Projeto](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

1. No **Adicionar aplicativo do IoT Edge e o módulo** janela, selecione **Linux Amd64**, **Windows Amd64**, ou ambos como a plataforma de aplicativos. Se você selecionar ambos, você cria uma solução com dois projetos que referenciam o módulo de código padrão.

   > [!TIP]
   > A extensão do Azure IoT Edge para Visual Studio, no momento não dá suporte a criar projetos para a plataforma ARM. Consulte este [entrada de blog do desenvolvedor de IoT](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) para obter um exemplo de como usar o Visual Studio Code para desenvolver uma solução para ARM32v7/armhf.

1. Selecione a  **C# módulo** ou **módulo C** e, em seguida, especifique o nome do módulo e o repositório de imagens de módulo. O Visual Studio preenche automaticamente o nome do módulo com **localhost:5000/<your module name\>**. Substitua-o pelas informações de seu registro. Se você usa um registro local do Docker para testes, **localhost** é uma opção adequada. Se usar o Registro de Contêiner do Azure, utilize o servidor de início de sessão nas configurações do registro. O servidor de início de seção é semelhante a **\<nome do registro\>.azurecr.io**. Apenas substitua a parte da cadeia de caracteres **localhost:5000** para que o resultado final se pareça com **\<* nome do registro*\>.azurecr.io/* \<nome do seu módulo\>* * *. O nome do módulo padrão é **IoTEdgeModule1**

1. Selecione **Okey** para criar a solução do Azure IoT Edge com um módulo que usa C# ou C.

Agora você tem um **AzureIoTEdgeApp1.Linux.Amd64** projeto ou um **AzureIoTEdgeApp1.Windows.Amd64** projeto, ou ambos e também uma **IoTEdgeModule1** project no seu solução. Cada **AzureIoTEdgeApp1** projeto tem um `deployment.template.json` arquivo, que define os módulos que você deseja compilar e implantar sua solução IoT Edge e também define as rotas entre módulos. A solução padrão tem um módulo **tempSensor** e um módulo **IoTEdgeModule1**. O módulo **tempSensor** gera dados simulados para o módulo **IoTEdgeModule1**, enquanto o código padrão no módulo **IoTEdgeModule1** é um módulo que recebe mensagens recebidas para o Hub IoT.

O projeto **IoTEdgeModule1** é um aplicativo de console do .NET Core 2.1. Ele contém os arquivos do Docker que você precisa para seu dispositivo IoT Edge em execução com o contêiner do Windows ou do Linux. O `module.json` arquivo descreve os metadados de um módulo. O código do módulo real, que usa o SDK do dispositivo de IoT do Azure como uma dependência, é encontrado na `Program.cs` ou `main.c` arquivo.

## <a name="develop-your-module"></a>Desenvolver seu módulo

O código do módulo padrão que vem com a solução está localizado em **IoTEdgeModule1** > **Program.cs** (para C#) ou **Main. c** (C). O módulo e o `deployment.template.json` arquivo são configurados para que você pode compilar a solução, envie por push ao registro de contêiner e implantá-lo em um dispositivo para iniciar o teste sem tocar em nenhum código. O módulo é criado para apenas receber entradas de uma fonte (nesse caso, o módulo **tempSensor** que simula dados) e redirecioná-las ao Hub IoT.

Quando você estiver pronto para personalizar o modelo de módulo com seu próprio código, use o [SDKs do Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md) crie módulos que abordam a chave precisa para soluções de IoT, como, confiabilidade, segurança e gerenciamento de dispositivo.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inicialize o iotedgehubdev com a cadeia de conexão do dispositivo IoT Edge

1. Copie a cadeia de conexão de qualquer dispositivo do IoT Edge da **Cadeia de conexão primária** no Gerenciador de nuvem do Visual Studio. Não copie a sequência de conexão do dispositivo não-Edge, pois o ícone d e um dispositivo IoT Edge é diferente daquele do dispositivo não-Edge.

   ![Copiar Cadeia de Conexão de Dispositivo de Borda](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Clique com o botão direito do mouse no projeto **AzureIoTEdgeApp1** e depois clique em **Configurar a Edge Device Connection String** para exibir a janela de configuração do Azure IoT Edge.

   ![Abra a janela de cadeia de caracteres de Conexão de borda definida](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Insira a cadeia de caracteres de conexão da primeira etapa e depois selecione **Ok**.

> [!NOTE]
> Siga estas etapas apenas uma vez no computador de desenvolvimento e os resultados serão automaticamente aplicados a todas as soluções do Azure IoT Edge subsequentes. Esse procedimento pode ser seguido novamente se for necessário alterar para uma cadeia de caracteres de conexão diferente.

## <a name="build-and-debug-single-module"></a>Compilar e depurar módulo único

Normalmente, queremos testar/depurar cada módulo antes de colocá-lo em execução dentro de uma solução com vários módulos.

1. Clique com botão direito em **IoTEdgeModule1** e selecione **Definir como projeto de inicialização** no menu de contexto.

   ![Definir o projeto de inicialização](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Pressione **F5** ou clique no botão abaixo para executar o módulo. Pode levar entre 10&ndash;20 segundos na primeira vez.

   ![Executar módulo](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Você deverá ver um aplicativo de console .NET Core iniciado se o módulo tiver sido inicializado com êxito.

   ![Módulo em execução](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Se desenvolvendo no C#, defina um ponto de interrupção no `PipeMessage()` funcionam no **Program.cs**; se usando o C, defina um ponto de interrupção `InputQueue1Callback()` funcionam no **Main. c**. Você pode testá-lo, em seguida, enviando uma mensagem, executando o seguinte comando em um **Git Bash** ou **WSL Bash** shell. (Não é possível executar o comando `curl` no PowerShell ou no Prompt de Comando.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Depurar módulo único](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    O ponto de interrupção deve ser disparado. Você pode assistir as variáveis na janela **Locais** do Visual Studio.

   > [!TIP]
   > Também é possível usar [PostMan](https://www.getpostman.com/) ou outras ferramentas de API para enviar mensagens em vez de `curl`.

1. Pressione **Ctrl + F5** ou clique no botão Parar para parar a depuração.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Compilar e depurar a solução do IoT Edge com vários módulos

Após concluir o desenvolvimento de módulo único, pode-se executar e depurar a solução inteira com vários módulos.

1. Adicione um segundo módulo para a solução clicando **AzureIoTEdgeApp1** e selecionando **Add** > **novo módulo do IoT Edge**. O nome padrão do segundo módulo é **IoTEdgeModule2** e ele irá agir como um outro módulo de pipe.

1. Abra o arquivo e `deployment.template.json`veja que o **IoTEdgeModule2** foi adicionado na seção **módulos**. Substitua a seção **Rotas** pelo seguinte. Se você tiver personalizado seus nomes de módulo, certifique-se de atualizar esses nomes correspondentes.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. Clique com o botão direito em **AzureIoTEdgeApp1** e selecione **Definir como projeto de inicialização** no menu de contexto.

1. Defina os pontos de interrupção e pressione **F5** para executar e depurar vários módulos simultaneamente. Você deve ver várias janelas de aplicativo de console .NET Core, que cada janela que representa um módulo diferente.

   ![Depurar módulos múltiplos](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Pressione **Ctrl + F5** ou clique no botão Parar para parar a depuração.

## <a name="build-and-push-images"></a>Compilar e efetuar push de imagens

1. Certifique-se de que **AzureIoTEdgeApp1** é o seu projeto de inicialização. Selecione a configuração **Depurar** ou **Versão** para suas imagens de módulo serem construídas.

    > [!NOTE]
    > Ao escolher **Depurar**, o Visual Studio usará `Dockerfile.(amd64|windows-amd64).debug` para compilar imagens do Docker. Isso inclui o VSDBG, depurador de linha de comando do .NET Core, na imagem de contêiner durante a criação. É recomendável que você use a configuração **Versão** que usa o `Dockerfile.(amd64|windows-amd64)` sem o VSDBG para os módulos do IoT Edge prontos para produção.

1. Se estiver usando um registro privado, como o Registro de Contêiner do Azure, use o seguinte comando do Docker para se conectar a ele. Se estiver usando o registro local, você poderá [executar um registro local](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Se você estiver usando um registro privado como o Registro de Contêiner do Azure, é necessário adicionar informações de login do registro para as configurações de tempo de execução encontradas no arquivo `deployment.template.json`. Substitua os espaços reservados com o nome real de nome de usuário do administrador ACR, senha e registro.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. Clique com o botão direito do mouse em **AzureIoTEdgeApp1** e escolha **Compilar e efetuar push da solução de Edge** para compilar e efetuar push da imagem do Docker para cada módulo.

   ![Compilar e efetuar push de imagens](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Implantar a solução

O artigo de início rápido que você usou para configurar seu dispositivo do IoT Edge, você implantou um módulo usando o portal do Azure. Você também pode implantar módulos usando o Cloud Explorer para o Visual Studio. Um manifesto de implantação já está preparado para o seu cenário, o `deployment.json` arquivo e tudo o que precisa fazer é selecionar um dispositivo para receber a implantação.

1. Abra **Cloud Explorer** clicando em **Exibir** > **Cloud Explorer**. Verifique se está logado no Visual Studio 2017.

1. No **Cloud Explorer**, expanda sua assinatura, localize o Hub IoT do Azure e o dispositivo Azure IoT Edge que você deseja implantar.

1. Clique com o botão direito do mouse no dispositivo do IoT Edge para criar uma implantação para ele. Você precisa escolher o arquivo de manifesto de implantação sob o `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Você não deverá marcar `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Clique no botão atualizar para ver os novos módulos sendo executados junto com o módulo **TempSensor** em **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Exibir os dados gerados

1. Para monitorar a mensagem D2C para um dispositivo específico, clique no dispositivo na lista e selecione **Iniciar monitoramento de mensagens D2C** na janela **Ação**.

1. Para interromper o monitoramento de dados, clique no dispositivo na lista e selecione **Parar monitoramento de mensagens de D2C** na janela **Ação**.

## <a name="next-steps"></a>Próximas etapas

Para desenvolver módulos padronizados para seus dispositivos do IoT Edge, consulte [Entender e usar os SDKs de Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).
