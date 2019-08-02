---
title: Desenvolver e depurar módulos para o Azure IoT Edge | Microsoft Docs
description: Use o código do Visual Studio Code para desenvolver, criar e depurar um módulo para o Azure IoT Edge usando C#, Python, Node.js, Java ou C
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/23/2019
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: a9c38d82c4b460564077690d3cddd731ec6c2b89
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414511"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Use o código do Visual Studio Code para desenvolver e depurar módulos para o Azure IoT Edge

Você pode transformar sua lógica de negócios em módulos do Azure IoT Edge. Este artigo mostra como usar o código do Visual Studio Code como a principal ferramenta para desenvolver e depurar módulos.

Para módulos escritos em C#, Node. js ou Java, há duas maneiras para depurar seu módulo no Visual Studio Code: Você pode anexar um processo em um contêiner de módulo ou inicializar o código do módulo no modo de depuração. Para os módulos escritos em Python ou C, eles só podem ser depurados, anexando a um processo em contêineres do Linux amd64.

Se você não estiver familiarizado com os recursos de depuração do Visual Studio Code, leia sobre [Depuração](https://code.visualstudio.com/Docs/editor/debugging).

Este artigo fornece instruções para o desenvolvimento e a depuração de módulos em vários idiomas para várias arquiteturas. Atualmente, Visual Studio Code fornece suporte para módulos escritos em C#, C, Python, Node. js e Java. As arquiteturas de dispositivo com suporte são x64 e ARM32. Para obter mais informações sobre sistemas operacionais, linguagens e arquiteturas com suporte, consulte [suporte a linguagens e arquitetura](module-development.md#language-and-architecture-support).

>[!NOTE]
>O desenvolvimento e a depuração de suporte para dispositivos Linux ARM64 estão em [Visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter mais informações, consulte [desenvolver e depurar módulos de IOT Edge do ARM64 no Visual Studio Code (versão prévia)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Pré-requisitos

É possível usar um computador ou uma máquina virtual que execute Windows, macOS ou Linux como o computador de desenvolvimento. Um dispositivo do IoT Edge pode ser outro dispositivo físico.

Instale primeiramente o [Visual Studio Code](https://code.visualstudio.com/) e, em seguida, adicione as extensões a seguir:

- [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Extensão do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Extensões do Visual Studio específicas da linguagem que você está desenvolvendo:
  - C#, incluindo o Azure Functions: [Extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  - Python: [Extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Pacote de extensão Java para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [Extensão C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Você também precisará instalar algumas ferramentas adicionais, específicas da linguagem para desenvolver seu módulo:

- C#, incluindo o Azure Functions: [SDK do .NET Core 2.1](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) e [Pip](https://pip.pypa.io/en/stable/installing/#installation) para instalar os pacotes do Python (normalmente incluídos com a instalação do Python).

- Node.js: [Node.js](https://nodejs.org). Você também desejará instalar [Yeoman](https://www.npmjs.com/package/yo) e o [Gerador de Módulo de Node. js do Azure IoT Edge](https://www.npmjs.com/package/generator-azure-iot-edge-module).

- Java: [Java SE Development Kit 10](https://aka.ms/azure-jdks) e [Maven](https://maven.apache.org/). Você precisará [definir o `JAVA_HOME` ambiente variável](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) para apontar para a sua instalação JDK.

Para criar e implantar a sua imagem do módulo, você precisará do Docker para criar a imagem do módulo e um registro de contêiner para manter a imagem do módulo:

- [Docker Community Edition](https://docs.docker.com/install/) no computador de desenvolvimento.

- [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Você pode usar um registro do Docker local, em vez de um registro em nuvem, para fins de protótipo e teste.

A menos que você esteja desenvolvendo seu módulo em C, você também precisará da [Ferramenta de Desenvolvimento Azure IoT Edge](https://pypi.org/project/iotedgehubdev/) baseado em Python para configurar seu ambiente de desenvolvimento local para depurar, executar e testar sua solução IoT Edge. Se você ainda não fez isso, instale o [Python (2.7/3.6) e o Pip](https://www.python.org/) e, em seguida, instale **iotedgehubdev** executando este comando no terminal.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
> [!NOTE]
> Se você tiver vários Python, incluindo o Python 2,7 pré-instalado (por exemplo, no Ubuntu ou no MacOS), certifique-se de estar usando `pip` o `pip3` correto ou para instalar o **iotedgehubdev**

Para testar o módulo em um dispositivo, é necessário um hub IoT ativo com pelo menos um dispositivo do IoT Edge. Para usar seu computador como um dispositivo do IoT Edge, siga as etapas no guia de início rápido para [Linux](quickstart-linux.md) ou [Windows](quickstart.md). Se você estiver executando o daemon do IoT Edge no computador de desenvolvimento, talvez seja necessário interromper o EdgeHub e o EdgeAgent antes de passar para a próxima etapa.

## <a name="create-a-new-solution-template"></a>Crie um novo modelo de solução

As etapas a seguir mostram como criar um módulo IoT Edge em seu idioma de desenvolvimento preferido (incluindo o Azure Functions, gravado em C#) usando o Visual Studio Code e as Ferramentas do Azure IoT. Você começa criando uma solução e, em seguida, gerando o primeiro módulo nessa solução. Cada solução pode conter vários módulos.

1. Selecione **Exibir** > **Paleta de Comandos**.

1. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**.

   ![Executar a nova solução do IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

1. Navegue até a pasta em que você deseja criar a nova solução e selecione **Selecionar pasta**.

1. Digite um nome para a solução.

1. Selecione um modelo de módulo para sua linguagem de desenvolvimento preferida para ser o primeiro módulo na solução.

1. Digite um nome para o módulo. Escolha um nome exclusivo no registro de contêiner.

1. Forneça o nome do repositório de imagens do módulo. O Visual Studio Code preenche automaticamente o nome do módulo com **localhost:5000/<nome do seu módulo\>** . Substitua-o pelas informações de seu registro. Se você usa um registro local do Docker para testes, **localhost** é uma opção adequada. Se usar o Registro de Contêiner do Azure, utilize o servidor de início de sessão nas configurações do registro. O servidor de início de seção é semelhante a **\<nome do registro\>.azurecr.io**. Apenas substitua a parte da cadeia de caracteres **localhost:5000** para que o resultado final se pareça com * *\<* nome do registro *\>.azurecr.io/* \<nome do seu módulo\>* * *.

   ![Fornecer o repositório de imagem do Docker](./media/how-to-develop-csharp-module/repository.png)

O Visual Studio Code usa as informações fornecidas, cria uma solução do IoT Edge e, em seguida, a carrega em uma nova janela.

Há quatro itens na solução:

- Uma pasta **.vscode** que contém configurações de depuração.

- Uma pasta **módulos** com subpastas para cada módulo.  Dentro da pasta para cada módulo há um arquivo, **Module. JSON**, que controla como os módulos são criados e implantados.  Esse arquivo precisaria ser modificado para alterar o contêiner de implantação do módulo Regristry do localhost para um registro remoto. Neste ponto, você tem apenas um módulo.  No entanto, é possível adicionar mais na paleta de comandos usando o comando **Azure IoT Edge: Adicionar módulo do IoT Edge**.

- Um arquivo **.env** lista as variáveis de ambiente. Se o Registro de Contêiner do Azure for seu registro, você terá um nome de usuário e uma senha do Registro de Contêiner do Azure nele.

  > [!NOTE]
  > O arquivo de ambiente será criado somente se você fornecer um repositório de imagens para o módulo. Se você aceitou os padrões do localhost para testar e depurar localmente, não será necessário declarar variáveis de ambiente.

- Um arquivo **deployment.template.json** lista o novo módulo em conjunto com um módulo **tempSensor** de exemplo que simula dados que podem ser usados para teste. Para obter mais informações sobre o funcionamento dos manifestos de implantação, consulte [Saiba como usar manifestos de implantação para implantar módulos e estabelecer rotas](module-composition.md).

## <a name="add-additional-modules"></a>Adicionar módulos adicionais

Para adicionar módulos adicionais à sua solução, execute o comando **Azure IoT Edge: Adicione módulo do IoT Edge** na paleta de comandos. Você também pode clicar com o botão direito na pasta **módulos** ou no arquivo `deployment.template.json` no modo de exibição do Gerenciador do Visual Studio Code e, em seguida, selecionar **Adicionar Módulo do IoT Edge**.

## <a name="develop-your-module"></a>Desenvolver seu módulo

O código padrão do módulo que vem com a solução está localizado em:

- Função do Azure (C#): **módulos > *&lt;nome do módulo&gt;*  >  *&lt;nome de módulo&gt;* . cs**
- C#: **módulos > *&lt;nome do módulo&gt;* > Program.cs**
- Python: **módulos > *&lt;nome do módulo&gt;* > main.py**
- Node.js: **módulos > *&lt;nome do módulo&gt;* > app.js**
- Java: **módulos > *&lt;o nome do módulo&gt;* > src > principal > java > com > edgemodulemodules > App. Java**
- C: **módulos > *&lt;nome do módulo&gt;* > main. c**

O módulo e o arquivo deployment.template.json são configurados de forma que você possa compilar a solução, enviá-la por push ao registro de contêiner e implantá-la em um dispositivo para iniciar os testes sem lidar com nenhum código. O módulo é criado para apenas receber entradas de uma fonte (nesse caso, o módulo tempSensor que simula dados) e redirecioná-las ao Hub IoT.

Quando você estiver pronto para personalizar o modelo com seu próprio código, use os [SDKs do Hub IoT](../iot-hub/iot-hub-devguide-sdks.md) para criar módulos que atendam às principais necessidades de soluções de IoT, como segurança, gerenciamento de dispositivos e confiabilidade.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Depurar um módulo sem um contêiner (C#, Node. js, Java)

Se você estiver desenvolvendo em C#, Node. js ou Java, o módulo requer o uso de um objeto **ModuleClient** no código do módulo padrão para que possa iniciar, executar e rotear mensagens. Você também usará o canal de entrada padrão **Entrada1** para agir quando o módulo recebe mensagens.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Configurar o simulador do IoT Edge para solução do IoT Edge

No computador de desenvolvimento, é possível iniciar o simulador do IoT Edge em vez de instalar o daemon de segurança do IoT Edge para que você possa executar a solução do IoT Edge.

1. No Device Explorer, no lado esquerdo, clique com o botão direito do mouse na ID do dispositivo do IoT Edge, e em seguida selecione **Configurar simulador do IoT Edge** para iniciar o simulador com a cadeia de conexão do dispositivo.
1. Você pode ver que o simulador do IoT Edge foi configurado com êxito lendo o detalhe de progresso no terminal integrado.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Configurar simulador do IoT Edge para aplicativo de módulo único

Para configurar e iniciar o simulador, execute o comando **Azure IoT Edge: Iniciar o simulador de Hub do IoT Edge para um único módulo** da paleta de comando do Visual Studio Code. Quando solicitado, use o valor **Entrada1** do código do módulo padrão (ou o valor equivalente a partir do seu código) como o nome de entrada para seu aplicativo. O comando disparará a CLI **iotedgehubdev** e iniciará o simulador IoT Edge e um contêiner de módulo de utilitário de teste. Será possível ver as saídas abaixo no terminal integrado, se o simulador foi iniciado no modo de módulo único com êxito. Você também poderá ver um comando `curl` para ajudar a enviar mensagens. Você o usará mais tarde.

   ![Configurar simulador do IoT Edge para aplicativo de módulo único](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Você pode usar o modo de exibição do Explorador do Docker no Visual Studio Code para ver o status de execução do módulo.

   ![Status do módulo do simulador](media/how-to-develop-csharp-module/simulator-status.png)

   O contêiner **edgeHubDev** é o núcleo do simulador de IoT Edge local. Ele pode ser executado no computador de desenvolvimento sem o daemon de segurança do IoT Edge e fornecer configurações de ambiente ao aplicativo de módulo nativo ou contêineres de módulo. O contêiner de **entrada** expôs as APIs de REST para ajudar a conectar as mensagens ao canal de entrada de destino no módulo.

### <a name="debug-module-in-launch-mode"></a>Depurar o módulo Java no modo de inicialização

1. Prepare o ambiente para depuração de acordo com os requisitos da sua linguagem de desenvolvimento, defina um ponto de interrupção em seu módulo e selecione a configuração de depuração para usar:
   - **C#**
     - No terminal integrado Visual Studio Code, altere o diretório para a  ***&lt;pasta&gt; nome do módulo*** e, em seguida, execute o comando a seguir para compilar o aplicativo .NET Core.

       ```cmd
       dotnet build
       ```

     - Abra o arquivo `Program.cs` e adicione um ponto de interrupção.

     - Navegue até a exibição de Depuração do Visual Studio Code, selecionando **Exibir > Depurar**. Selecione a configuração de depuração no ***&lt;seu nome de módulo&gt;* Depuração Local (.NET Core)** no menu suspenso.

        > [!NOTE]
        > Se o seu .NET `TargetFramework` Core não estiver consistente com o caminho do `launch.json`programa no, você precisará atualizar manualmente o caminho do `launch.json` programa no para `TargetFramework` que ele corresponda ao em seu arquivo. csproj para que Visual Studio Code possa iniciar com êxito Program.

   - **Node.js**
     - No terminal integrado do Visual Studio Code, altere o diretório para ***&lt;sua pasta do nome do módulo&gt;*** e execute o seguinte comando para instalar os pacotes de nó

       ```cmd
       npm install
       ```

     - Abra o arquivo `app.js` e adicione um ponto de interrupção.

     - Navegue até a exibição de Depuração do Visual Studio Code, selecionando **Exibir > Depurar**. Selecione a configuração de depuração no ***&lt;seu nome de módulo&gt;* Depuração Local (Node.js)** no menu suspenso.
   - **Java**
     - Abra o arquivo `App.java` e adicione um ponto de interrupção.

     - Navegue até a exibição de Depuração do Visual Studio Code, selecionando **Exibir > Depurar**. Selecione a configuração de depuração no ***&lt;seu nome de módulo&gt;* Depuração Local (Java)** no menu suspenso.

1. Clique em **Iniciar Depuração** ou pressione **F5** para iniciar a sessão de depuração.

1. No terminal integrado do Visual Studio Code, execute o seguinte comando para enviar uma mensagem **Olá, Mundo** para o módulo. Este é o comando mostrado nas etapas anteriores ao configurar o simulador do IoT Edge.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Se você estiver usando o Windows, certificando-se de que o shell do terminal integrado do Visual Studio Code é **Git Bash** ou **WSL Bash**. Não é possível executar o comando `curl` no PowerShell ou no Prompt de Comando.
   > [!TIP]
   > Também é possível usar [PostMan](https://www.getpostman.com/) ou outras ferramentas de API para enviar mensagens por meio de `curl`.

1. Na exibição de Depuração do Visual Studio Code, você verá as variáveis no painel esquerdo.

1. Para parar a sessão de depuração, selecione o botão Parar ou pressione **Shift + F5**e, em seguida, execute **Azure IoT Edge: Parar o simulador do IoT Edge** na paleta de comandos para interromper o simulador e limpar.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Depuração no modo anexo com o simulador do IoT Edge (C#, Node. js, Java, Azure Functions)

A solução padrão contém dois módulos, um é um módulo de sensor de temperatura simulado e o outro é o módulo de pipe. O sensor de temperatura simulado envia mensagens para o módulo de pipe e, em seguida, as mensagens são redirecionadas para Hub IoT. Na pasta do módulo que você criou, há vários arquivos do Docker para diferentes tipos de contêineres. Use qualquer um dos arquivos que terminam com a extensão **.debug** para compilar o módulo para teste.

Atualmente, depuração no modo de anexo é compatível somente conforme a seguir:

- Módulos C#, incluindo aqueles para o Azure Functions dá suporte à depuração em contêineres do Linux amd64
- Módulos Node.js dão suporte à depuração em contêineres Linux amd64 e arm32v7 e contêineres Windows amd64
- Os módulos Java são compatíveis com depuração em contêineres Linux amd64 e arm32v7

> [!TIP]
> Você pode alternar entre as opções para a plataforma padrão para sua solução IoT Edge clicando no item na barra de status do Visual Studio Code.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Configurar o simulador do IoT Edge para solução do IoT Edge

No computador de desenvolvimento, é possível iniciar o simulador do IoT Edge, em vez de instalar o daemon de segurança do IoT Edge para que você possa executar a solução do IoT Edge.

1. No Device Explorer, no lado esquerdo, clique com o botão direito do mouse na ID do dispositivo do IoT Edge, e em seguida selecione **Configurar simulador do IoT Edge** para iniciar o simulador com a cadeia de conexão do dispositivo.

1. Você pode ver que o simulador do IoT Edge foi configurado com êxito lendo o detalhe de progresso no terminal integrado.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Compilar e executar contêiner para depuração e depuração em modo de anexação

1. Abra o arquivo de módulo (`Program.cs`, `app.js`, `App.java`, ou `<your module name>.cs`) e adicione um ponto de interrupção.

1. Na exibição do Visual Studio Code Explorer, clique com botão direito no `deployment.debug.template.json` arquivo para sua solução e, em seguida, selecione **solução de compilação e execução do IoT Edge no simulador**. É possível observar todos os logs de contêiner de módulo na mesma janela. Você também pode navegar para a exibição do Docker para observar o status do contêiner.

   ![Observar variáveis](media/how-to-develop-csharp-module/view-log.png)

1. Navegue até a exibição de Depuração do Visual Studio Code e selecione o arquivo de configuração de depuração para o seu módulo. O nome da opção de depuração deve ser semelhante a ***&lt;seu nome de módulo&gt;* Depuração Remota**

1. Clique em **Iniciar depuração** ou pressione **F5**. Selecione o processo ao qual ele deverá ser anexado.

1. Na exibição de Depuração do Visual Studio Code, você verá as variáveis no painel esquerdo.

1. Para parar a sessão de depuração, primeiro selecione o botão Parar ou pressione **Shift + F5**e, em seguida, execute **Azure IoT Edge: Para o Simulador IoT Edge** na paleta de comandos.

> [!NOTE]
> O exemplo anterior mostra como depurar os módulos IoT Edge nos contêineres. Ele adicionou portas expostas às configurações `createOptions` de contêiner do módulo. Após concluir a depuração dos módulos, é recomendável remover essas portas expostas de módulos do IoT Edge prontos para produção.
>
> Para módulos gravados em C#, incluindo o Azure Functions, ele é baseado na versão de depuração do `Dockerfile.amd64.debug`, que inclui o depurador de linha de comando do .NET Core (VSDBG) na imagem de contêiner durante a criação. Depois de depurar seus módulos C#, recomendamos que você use diretamente o Dockerfile sem VSDBG para módulos IoT Edge prontos para produção.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Depurar um módulo com o tempo de execução do IoT Edge

Em cada pasta de módulo, há vários arquivos do Docker para diferentes tipos de contêiner. Use qualquer um dos arquivos que terminam com a extensão **.debug** para compilar o módulo para teste.

Durante a depuração de módulos usando esse método, seus módulos são executados sobre o tempo de execução do IoT Edge. O dispositivo IoT Edge e o Visual Studio Code podem estar no mesmo computador ou, mais comumente, o Visual Studio Code está no computador de desenvolvimento e o tempo de execução do IoT Edge e os módulos estão em execução em outro computador físico. Para depurar no Visual Studio Code, você deve:

- Configurar seu dispositivo IoT Edge, compilar módulos do IoT Edge com o Dockerfile **.debug** e, em seguida, implantar o dispositivo do IoT Edge.
- Expor o IP e a porta do módulo para que o depurador possa ser anexado.
- Atualize o `launch.json` para que o Visual Studio Code possa se anexar ao processo no contêiner no computador remoto. Esse arquivo está localizado na pasta `.vscode` no seu workspace e é atualizado sempre que você adiciona um novo módulo que dá suporte à depuração.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Compilar e implantar o módulo no dispositivo do IoT Edge

1. No Visual Studio Code, abra o `deployment.debug.template.json` arquivo que contém a versão de depuração de suas imagens de módulo com os devidos conjuntos de valores `createOptions`.

1. Se você estiver desenvolvendo seu módulo de Python, siga estas etapas antes de continuar:
   - Abra o arquivo `main.py` e adicione esse código após a seção de importação:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Adicione a seguinte linha única de código para o retorno de chamada que você deseja depurar:

      ```python
      ptvsd.break_into_debugger()
      ```

     Por exemplo, se você deseja depurar o `receive_message_callback` método, você poderia inserir essa linha de código, conforme mostrado abaixo:

      ```python
      def receive_message_callback(message, hubManager):
          ptvsd.break_into_debugger()
          global RECEIVE_CALLBACKS
          message_buffer = message.get_bytearray()
          size = len(message_buffer)
          print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode ('utf-8'), size) )
          map_properties = message.properties()
          key_value_pair = map_properties.get_internals()
          print ( "    Properties: %s" % key_value_pair )
          RECEIVE_CALLBACKS += 1
          print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
          hubManager.forward_event_to_output("output1", message, 0)
          return IoTHubMessageDispositionResult.ACCEPTED
      ```

1. Na paleta de comandos do Visual Studio Code:
   1. Execute o comando **Azure IoT Edge: Compilar e enviar por push a solução IoT Edge**.

   1. Selecione o `deployment.debug.template.json` arquivo para sua solução.

1. Na seção **Dispositivos de Hub IoT do Azure** do modo de exibição do Gerenciador do Visual Studio Code:
   1. Clique com o botão direito do mouse na ID de dispositivo do IoT Edge e, em seguida, selecione **Criar implantação para um único dispositivo**.

      > [!TIP]
      > Para confirmar que o dispositivo que você escolheu é um dispositivo IoT Edge, selecione-o para expandir a lista de módulos e verificar a presença de **$edgeHub** e **$edgeAgent**. Todos os dispositivos IoT Edge incluem esses dois módulos.

   1. Navegue até a pasta da sua solução **config**, selecione o arquivo `deployment.debug.amd64.json` e, em seguida, selecione **Selecionar o manifesto de implantação de borda**.

Você verá a implantação criada com êxito, com uma ID de implantação em um terminal integrado.

Você pode verificar o status do contêiner executando o comando `docker ps` no terminal. Se o tempo de execução do IoT Edge e Visual Studio Code estiver em execução no mesmo computador, você também poderá verificar o status na exibição do Docker do Visual Studio Code.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Expor o IP e a porta do módulo para o depurador

Você poderá ignorar esta seção se os módulos estiverem em execução no mesmo computador que o Visual Studio Code, pois está usando localhost para anexar ao contêiner e já tem as configurações de porta corretas no Dockerfile **.debug**, nas configurações `createOptions` do contêiner do módulo e no arquivo `launch.json`. Se os módulos e o Visual Studio Code estiverem em execução em computadores separados, siga as etapas para a linguagem de desenvolvimento.

- **C#, incluindo o Azure Functions**

  [Configure o canal SSH no seu computador de desenvolvimento e o dispositivo do IoT Edge](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) e, depois, edite o arquivo `launch.json` a ser anexado.

- **Node.js**

  - Verifique se o módulo no computador a ser depurado está em execução e pronto para anexar depuradores e se a porta 9229 está acessível externamente. Para verificar isso, abra `http://<target-machine-IP>:9229/json` no computador do depurador. Essa URL deve mostrar informações sobre o módulo Node.js a ser depurado.
  
  - No computador de desenvolvimento, abra o Visual Studio Code e edite `launch.json` para que o valor de endereço do ***&lt;nome do seu módulo&gt;* com perfil Remote Debug (Node.js)** (ou ***&lt;nome do seu módulo&gt;* com perfil Remote Debug (Node.js em Contêiner do Windows)** se o módulo estiver em execução como um contêiner do Windows) seja o IP do computador que está sendo depurado.

- **Java**

  - Compile um túnel SSH para o computador que será depurado executando `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N`.
  
  - No computador de desenvolvimento, abra o Visual Studio Code e edite o ***&lt;nome do seu módulo&gt;* com perfil Remote Debug (Java)** perfil em `launch.json` para que você possa anexar ao computador de destino. Para saber mais sobre edição `launch.json` e depuração de Java com o Visual Studio Code, confira a seção sobre [configurar o depurador](https://code.visualstudio.com/docs/java/java-debugging#_configuration).

- **Python**

  - Certifique-se de que a porta 5678 no computador a ser depurado esteja aberta e acessível.

  - No código `ptvsd.enable_attach(('0.0.0.0', 5678))` inserido anteriormente em `main.py`, altere **0.0.0.0** para o endereço IP do computador a ser depurado. Compile, efetue push e implante seu módulo do IoT Edge novamente.

  - No computador de desenvolvimento, abra o Visual Studio Code e, em seguida, edite `launch.json` para que o `host` valor do ***&lt;nome do seu módulo&gt;* com perfil Remote Debug (Python)** use o endereço IP do computador de destino em vez de `localhost`.

### <a name="debug-your-module"></a>Depurar seu aplicativo

1. Na exibição Depuração do Visual Studio Code e selecione o arquivo de configuração de depuração para o seu módulo. O nome da opção de depuração deve ser semelhante a ***&lt;seu nome de módulo&gt;* Depuração Remota**

1. Abra o arquivo de módulo para sua linguagem de desenvolvimento e adicione um ponto de interrupção:

   - **Função do Azure (C#)** : adicione seu ponto de interrupção ao arquivo `<your module name>.cs`.
   - **C#** : adicione seu ponto de interrupção ao arquivo `Program.cs`.
   - **Node.js**: adicione seu ponto de interrupção ao arquivo `app.js`.
   - **Java**: adicione seu ponto de interrupção ao arquivo `App.java`.
   - **Python**: adicione seu ponto de interrupção ao arquivo `main.py`no método de retorno de chamada em que você adicionou a linha `ptvsd.break_into_debugger()`.
   - **C**: adicione seu ponto de interrupção ao arquivo `main.c`.

1. Selecione **Iniciar Depuração** ou selecione **F5**. Selecione o processo ao qual ele deverá ser anexado.

1. Na exibição de Depuração do Visual Studio Code, você verá as variáveis no painel esquerdo.

> [!NOTE]
> O exemplo anterior mostra como depurar os módulos IoT Edge nos contêineres. Ele adicionou portas expostas às configurações `createOptions` de contêiner do módulo. Após concluir a depuração dos módulos, é recomendável remover essas portas expostas de módulos do IoT Edge prontos para produção.

## <a name="build-and-debug-a-module-remotely"></a>Compilar e depurar um módulo remotamente

Com as alterações recentes nos mecanismos do Docker e do Moby para oferecer suporte a conexões SSH e uma nova configuração nas Ferramentas IoT do Azure que permite a injeção de configurações de ambiente nas paletas de comandos do Visual Studio Code e nos terminais do Azure IoT Edge, agora você pode compilar e depurar módulos em dispositivos remotos.

Consulte esta [entrada do blog do desenvolvedor de IoT](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) para saber mais e obter instruções passo a passo.

## <a name="next-steps"></a>Próximas etapas

Após compilar o módulo, saiba como [Implantar módulos do Azure IoT Edge do Visual Studio Code](how-to-deploy-modules-vscode.md).

Para desenvolver módulos para seus dispositivos do IoT Edge, consulte [Entender e usar os SDKs de Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).
