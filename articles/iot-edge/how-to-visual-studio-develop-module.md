---
title: Desenvolver e depurar módulos no Visual Studio-Azure IoT Edge
description: Use o Visual Studio com as ferramentas de IoT do Azure para desenvolver um módulo IoT Edge C ou C# e enviá-lo por push do Hub IoT para um dispositivo IoT, conforme configurado por um manifesto de implantação.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 2ae6d46198d979f91de5bf31d389f75961b4ab88
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437160"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Use o Visual Studio 2019 para desenvolver e depurar módulos para Azure IoT Edge

Você pode transformar sua lógica de negócios em módulos do Azure IoT Edge. Este artigo mostra como usar o Visual Studio 2019 como a ferramenta principal para desenvolver e depurar módulos.

As ferramentas do Azure IoT Edge para Visual Studio fornecem os seguintes benefícios:

- Criar, editar, compilar, executar e depurar soluções do Azure IoT Edge e módulos no seu computador de desenvolvimento local.
- Implantar a solução do Azure IoT Edge no dispositivo do Azure IoT Edge por meio do Hub IoT do Azure.
- Codifique seus módulos de IoT do Azure em C ou C# e tenha todos os benefícios do desenvolvimento do Visual Studio.
- Gerenciar dispositivos do Azure IoT Edge e módulos com interface do usuário.

Este artigo mostra como usar as ferramentas de Azure IoT Edge para o Visual Studio 2019 para desenvolver seus módulos de IoT Edge. Você também aprenderá como implantar seu projeto no dispositivo Azure IoT Edge. Atualmente, o Visual Studio 2019 fornece suporte para módulos escritos em C e C#. As arquiteturas de dispositivo com suporte são Windows x64 e Linux x64 ou ARM32. Para obter mais informações sobre sistemas operacionais, linguagens e arquiteturas com suporte, consulte [suporte a linguagens e arquitetura](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Pré-requisitos

Este artigo presume que você esteja usando um computador ou uma máquina virtual que executa Windows como seu computador de desenvolvimento. Em computadores com Windows, você pode desenvolver módulos do Windows ou Linux. Para desenvolver módulos do Windows, use um computador Windows executando a versão 1809/Build 17763 ou mais recente. Para desenvolver módulos do Linux, use um computador Windows que atenda aos [requisitos do Docker desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Como este artigo usa o Visual Studio 2019 como a ferramenta de desenvolvimento principal, instale o Visual Studio. Certifique-se de incluir o desenvolvimento **do Azure** e o **desenvolvimento de desktops com** cargas de trabalho do C++ em sua instalação do Visual Studio 2019. Você pode [Modificar o Visual Studio 2019](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true) para adicionar as cargas de trabalho necessárias.

Depois que o Visual Studio 2019 estiver pronto, você também precisará das seguintes ferramentas e componentes:

- Baixe e instale as [ferramentas de Azure IOT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) do Visual Studio Marketplace para criar um projeto IOT Edge no visual Studio 2019.

> [!TIP]
> Se você estiver usando o Visual Studio 2017, baixe e instale o [Azure IOT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) para vs 2017 no Visual Studio Marketplace

- Fazer o download e instalar o [Docker Community Edition](https://docs.docker.com/install/) no computador de desenvolvimento para compilar e executar suas imagens de módulo. É necessário definir o Docker CE para ser executado no modo de contêiner do Linux ou no modo de contêiner do Windows.

- Para configurar o ambiente de desenvolvimento local para depurar, executar e testar a solução IoT Edge, instale a [Ferramenta de desenvolvimento do Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Instale o [Python (2.7/3.6 +) e o Pip](https://www.python.org/) e, em seguida, instale o pacote **iotedgehubdev** executando o comando a seguir no seu terminal. Certifique-se de que sua versão da ferramenta de desenvolvimento do Azure IoT EdgeHub é posterior à versão 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Clone o repositório e instale o Gerenciador de biblioteca do Vcpkg e, em seguida, instale o **pacote Azure-IOT-SDK-c** para Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Registro de Contêiner do Azure](../container-registry/index.yml) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Você pode usar um registro do Docker local, em vez de um registro em nuvem, para fins de protótipo e teste.

- Para testar o módulo em um dispositivo, é necessário um hub IoT ativo com pelo menos um dispositivo do IoT Edge. Para usar seu computador como um dispositivo do IoT Edge, siga as etapas no guia de início rápido para [Linux](quickstart-linux.md) ou [Windows](quickstart.md). Se estiver executando o daemon do IoT Edge no computador de desenvolvimento, talvez seja necessário interromper o EdgeHub e o EdgeAgent antes de iniciar o desenvolvimento no Visual Studio.

### <a name="check-your-tools-version"></a>Verificar a versão de ferramentas

1. No menu **extensões** , selecione **gerenciar extensões**. Expanda as **ferramentas de > instaladas** e encontre **Azure IOT Edge Tools for Visual Studio** e **Cloud Explorer para Visual Studio**.

1. Observe a versão instalada. Você pode comparar esta versão com a versão mais recente no Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Se a sua versão for mais antiga do que a disponível no Visual Studio Marketplace, atualize suas ferramentas no Visual Studio conforme mostrado na seção a seguir.

### <a name="update-your-tools"></a>Atualize suas ferramentas

1. Na janela **gerenciar extensões** , expanda **atualizações > Visual Studio Marketplace**, selecione **Azure IOT Edge ferramentas** ou **Cloud Explorer para Visual Studio** e selecione **Atualizar**.

1. Depois de fazer o download da atualização das ferramentas, feche o Visual Studio para disparar a atualização das ferramentas usando o instalador VSIX.

1. No instalador, selecione **OK** para iniciar e depois **Modificar** para atualizar as ferramentas.

1. Depois que a atualização for concluída, selecione **Fechar** e reinicie o Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Criar um projeto do Azure IoT Edge

O modelo de projeto do Azure IoT Edge no Visual Studio cria um projeto que pode ser implantado em dispositivos do Azure IoT Edge no Hub IoT do Azure. Primeiro, você cria uma solução de Azure IoT Edge e, em seguida, gera o primeiro módulo nessa solução. Cada solução do IoT Edge pode conter mais de um módulo.

> [!TIP]
> A estrutura do projeto do IoT Edge criada pelo Visual Studio não é a mesma do Visual Studio Code.

1. Na caixa de diálogo novo projeto do Visual Studio, pesquise e selecione **Azure IOT Edge** projeto e clique em **Avançar**. Na janela configuração do projeto, insira um nome para o seu projeto e especifique o local e, em seguida, selecione **criar**. O nome do projeto padrão é **AzureIoTEdgeApp1**.

   ![Criar um novo projeto](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. Na janela **adicionar IOT Edge aplicativo e módulo** , selecione módulo **C#** ou **módulo C** e, em seguida, especifique o nome do módulo e o repositório de imagens do módulo. O Visual Studio preenche automaticamente o nome do módulo com **localhost:5000/<your module name\>**. Substitua-o pelas informações de seu registro. Se você usar um registro do Docker local para teste, o **localhost** será bem. Se usar o Registro de Contêiner do Azure, utilize o servidor de início de sessão nas configurações do registro. O servidor de logon é semelhante a **_\<registry name\>_ . azurecr.Io**. Substitua apenas a parte **localhost: 5000** da cadeia de caracteres para que o resultado final seja semelhante a **\<*registry name*\> . azurecr.Io/ _\<your module name\>_**. O nome do módulo padrão é **IotEdgeModule1**

   ![Adicionar aplicativo e módulo](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Selecione **OK** para criar a solução de Azure IOT Edge com um módulo que usa o C# ou o C.

Agora você tem um projeto **AzureIoTEdgeApp1. Linux. AMD64** ou um projeto **AzureIoTEdgeApp1. Windows. AMD64** e também um projeto **IotEdgeModule1** em sua solução. Cada projeto **AzureIoTEdgeApp1** tem um `deployment.template.json` arquivo, que define os módulos que você deseja compilar e implantar para sua solução de IOT Edge e também define as rotas entre os módulos. A solução padrão tem um módulo **SimulatedTemperatureSensor** e um módulo **IotEdgeModule1** . O módulo **SimulatedTemperatureSensor** gera dados simulados para o módulo **IotEdgeModule1** , enquanto o código padrão no módulo **IotEdgeModule1** canaliza diretamente as mensagens recebidas para o Hub IOT do Azure.

Para ver como o sensor de temperatura simulado funciona, exiba o [código-fonte SimulatedTemperatureSensor. csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

O projeto **IotEdgeModule1** é um aplicativo de console do .net Core 2,1 se for um módulo C#. Ele contém os arquivos do Docker que você precisa para seu dispositivo IoT Edge em execução com o contêiner do Windows ou do Linux. O `module.json` arquivo descreve os metadados de um módulo. O código de módulo real, que usa o SDK do dispositivo IoT do Azure como uma dependência, é encontrado no `Program.cs` `main.c` arquivo ou.

## <a name="develop-your-module"></a>Desenvolver seu módulo

O código de módulo padrão que vem com a solução está localizado em **IotEdgeModule1**  >  **Program.cs** (para C#) ou **Main. c** (c). O módulo e o `deployment.template.json` arquivo são configurados para que você possa criar a solução, enviá-la por push para o registro de contêiner e implantá-la em um dispositivo para iniciar o teste sem tocar em nenhum código. O módulo foi criado para pegar a entrada de uma fonte (nesse caso, o módulo **SimulatedTemperatureSensor** que simula dados) e redirecioná-lo para o Hub IOT do Azure.

Quando você estiver pronto para personalizar o modelo de módulo com seu próprio código, use os [SDKs do Hub IOT do Azure](../iot-hub/iot-hub-devguide-sdks.md) para criar módulos que atendam às principais necessidades de soluções de IOT, como segurança, gerenciamento de dispositivos e confiabilidade.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inicialize o iotedgehubdev com a cadeia de conexão do dispositivo IoT Edge

1. Copie a cadeia de conexão de qualquer dispositivo do IoT Edge da **Cadeia de conexão primária** no Gerenciador de nuvem do Visual Studio. Não copie a sequência de conexão do dispositivo não-Edge, pois o ícone d e um dispositivo IoT Edge é diferente daquele do dispositivo não-Edge.

   ![Copiar Cadeia de Conexão de Dispositivo de Borda](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. No menu **ferramentas** , selecione **ferramentas de Azure IOT Edge**  >  **instalação IOT Edge Simulator**, Cole a cadeia de conexão e clique em **OK**.

   ![Abra a janela de cadeia de caracteres de Conexão de borda definida](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Insira a cadeia de caracteres de conexão da primeira etapa e depois selecione **Ok**.

> [!NOTE]
> Siga estas etapas apenas uma vez no computador de desenvolvimento e os resultados serão automaticamente aplicados a todas as soluções do Azure IoT Edge subsequentes. Esse procedimento pode ser seguido novamente se for necessário alterar para uma cadeia de caracteres de conexão diferente.

## <a name="build-and-debug-single-module"></a>Compilar e depurar um único módulo

Normalmente, queremos testar/depurar cada módulo antes de colocá-lo em execução dentro de uma solução com vários módulos.

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse em **IotEdgeModule1** e selecione **definir como projeto de inicialização** no menu de contexto.

   ![Definir o projeto de inicialização](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Pressione **F5** ou clique no botão abaixo para executar o módulo. Pode levar entre 10&ndash;20 segundos na primeira vez.

   ![Executar módulo](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Você deverá ver um aplicativo de console .NET Core iniciado se o módulo tiver sido inicializado com êxito.

   ![Módulo em execução](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Se estiver desenvolvendo em C#, defina um ponto de interrupção na `PipeMessage()` função em **Program.cs**; se estiver usando C, defina um ponto de interrupção na `InputQueue1Callback()` função em **Main. C**. Em seguida, você pode testá-lo enviando uma mensagem executando o comando a seguir em um shell do **git bash** ou **WSL bash** . (Não é possível executar o comando `curl` no PowerShell ou no Prompt de Comando.)

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

1. Em **Gerenciador de soluções**, adicione um segundo módulo à solução clicando com o botão direito do mouse em **AzureIoTEdgeApp1** e selecionando **Adicionar**  >  **novo módulo IOT Edge**. O nome padrão do segundo módulo é **IotEdgeModule2** e atuará como outro módulo de pipe.

1. Abra o arquivo `deployment.template.json` e você verá **IotEdgeModule2** foi adicionado na seção **módulos** . Substitua a seção **Rotas** pelo seguinte. Se você tiver personalizado seus nomes de módulo, certifique-se de atualizar esses nomes correspondentes.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Clique com o botão direito em **AzureIoTEdgeApp1** e selecione **Definir como projeto de inicialização** no menu de contexto.

1. Defina os pontos de interrupção e pressione **F5** para executar e depurar vários módulos simultaneamente. Você deve ver várias janelas do aplicativo de console do .NET Core, que cada janela representa um módulo diferente.

   ![Depurar módulos múltiplos](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Pressione **Ctrl + F5** ou clique no botão Parar para parar a depuração.

## <a name="build-and-push-images"></a>Compilar e efetuar push de imagens

1. Certifique-se de que **AzureIoTEdgeApp1** é o seu projeto de inicialização. Selecione a configuração **Depurar** ou **Versão** para suas imagens de módulo serem construídas.

    > [!NOTE]
    > Ao escolher **Depurar**, o Visual Studio usará `Dockerfile.(amd64|windows-amd64).debug` para compilar imagens do Docker. Isso inclui o VSDBG, depurador de linha de comando do .NET Core, na imagem de contêiner durante a criação. É recomendável que você use a configuração **Versão** que usa o `Dockerfile.(amd64|windows-amd64)` sem o VSDBG para os módulos do IoT Edge prontos para produção.

1. Se você estiver usando um registro privado como o ACR (registro de contêiner do Azure), use o comando do Docker a seguir para entrar nele.  Você pode obter o nome de usuário e a senha na página **chaves de acesso** do registro no portal do Azure. Se estiver usando o registro local, você poderá [executar um registro local](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Se você estiver usando um registro privado como o Registro de Contêiner do Azure, é necessário adicionar informações de login do registro para as configurações de runtime encontradas no arquivo `deployment.template.json`. Substitua os espaços reservados com o nome real de nome de usuário do administrador ACR, senha e registro.

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

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse em **AzureIoTEdgeApp1** e selecione **compilar e enviar por push IOT Edge módulos** para compilar e enviar por push a imagem do Docker para cada módulo.

## <a name="deploy-the-solution"></a>Implantar a solução

O artigo de início rápido que você usou para configurar seu dispositivo do IoT Edge, você implantou um módulo usando o portal do Azure. Você também pode implantar módulos usando o Cloud Explorer para o Visual Studio. Um manifesto de implantação já está preparado para o seu cenário, o `deployment.json` arquivo e tudo o que precisa fazer é selecionar um dispositivo para receber a implantação.

1. Abra **Cloud Explorer** clicando em **Exibir** > **Cloud Explorer**. Verifique se você fez logon no Visual Studio 2019.

1. No **Cloud Explorer**, expanda sua assinatura, localize o Hub IoT do Azure e o dispositivo Azure IoT Edge que você deseja implantar.

1. Clique com o botão direito do mouse no dispositivo IoT Edge para criar uma implantação para ele. Navegue até o manifesto de implantação configurado para sua plataforma localizada na pasta **config** na sua solução do Visual Studio, como `deployment.arm32v7.json` .

1. Clique no botão atualizar para ver os novos módulos em execução junto com o módulo **SimulatedTemperatureSensor** e **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Exibir os dados gerados

1. Para monitorar a mensagem D2C para um dispositivo IoT-Edge específico, selecione-o no Hub IoT no **Cloud Explorer** e clique em **Iniciar Monitoramento de ponto de extremidade de evento interno** na janela **ação** .

1. Para interromper o monitoramento de dados, selecione **parar monitoramento de ponto de extremidade de evento interno** na janela **ação** .

## <a name="next-steps"></a>Próximas etapas

Para desenvolver módulos padronizados para seus dispositivos do IoT Edge, consulte [Entender e usar os SDKs de Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).