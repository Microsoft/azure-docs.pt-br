---
title: Desenvolver módulo para dispositivos Windows – Azure IoT Edge | Microsoft Docs
description: Este tutorial explica como configurar os recursos de nuvem e de computador de desenvolvimento para desenvolver módulos do IoT Edge usando contêineres do Windows para dispositivos Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 94a287cd996bd18b757620254540f8dc0df499e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051853"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Tutorial: Desenvolver módulos do IoT Edge para dispositivos Windows

Use o Visual Studio para desenvolver e implantar código em dispositivos Windows que executam o IoT Edge.

No início rápido, você criou um dispositivo IoT Edge usando uma máquina virtual do Windows e implantou um módulo predefinido com base no Azure Marketplace. Este tutorial explica o que é necessário para desenvolver e implantar seu próprio código em um dispositivo IoT Edge. Este tutorial é um pré-requisito útil para todos os outros tutoriais, que se aprofundarão nas linguagens de programação específicas ou nos serviços do Azure. 

Este tutorial usa o exemplo de como implantar um **módulo do C# em um dispositivo Windows**. Este exemplo foi escolhido porque é o cenário de desenvolvimento mais comum. Se você está interessado em desenvolver em um idioma diferente ou se planeja implantar os serviços do Azure como módulos, este tutorial ainda será útil para saber mais sobre as ferramentas de desenvolvimento. Após entender os conceitos de desenvolvimento, será possível escolher a linguagem de sua preferência ou o serviço do Azure nos quais se aprofundar. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar seu computador de desenvolvimento.
> * Usar as ferramentas do IoT Edge para o Visual Studio para criar um projeto.
> * Criar seu projeto como um contêiner e armazená-lo em um Registro de Contêiner do Azure.
> * Implantar seu código em um dispositivo IoT Edge. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Principais conceitos

Este tutorial explica o desenvolvimento de um módulo do IoT Edge. Um *módulo do IoT Edge* ou, às vezes, apenas *módulo* de forma abreviada, é um contêiner que contém o código executável. É possível implantar um ou mais módulos em um dispositivo IoT Edge. Módulos executam tarefas específicas, como ingerir dados de sensores, executar análise de dados ou operações de limpeza de dados ou enviar mensagens a um hub IoT. Para saber mais, confira [Understand Azure IoT Edge modules](iot-edge-modules.md) (Noções básicas sobre módulos do Azure IoT Edge).

Ao desenvolver módulos do IoT Edge, é importante entender as diferenças entre o computador de desenvolvimento e o dispositivo IoT Edge de destino em que o módulo será implantado eventualmente. O contêiner que você criar para armazenar seu código de módulo deverá corresponder ao SO (sistema operacional) do *dispositivo de destino*. Para o desenvolvimento de contêiner do Windows, esse conceito é mais simples porque os contêineres do Windows são executados somente em sistemas de operacionais do Windows. Entretanto, você poderia, por exemplo, usar seu computador de desenvolvimento do Windows para criar módulos para dispositivos Linux IoT Edge. Nesse cenário, você precisa garantir que seu computador de desenvolvimento estava executando contêineres do Linux. Ao percorrer este tutorial, lembre-se da diferença entre o *sistema operacional do computador de desenvolvimento* e o *sistema operacional do contêiner*.

Este tutorial direciona dispositivos Windows que executam o IoT Edge. Os dispositivos Windows IoT Edge usam contêineres do Windows. É recomendável usar o Visual Studio para desenvolvimento para dispositivos Windows, portanto, é isso que este tutorial usará. É possível usar o Visual Studio Code também, embora haja diferenças no suporte entre as duas ferramentas.

A tabela a seguir lista os cenários de desenvolvimento compatíveis com os **contêineres do Windows** no Visual Studio Code e no Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Serviços do Azure** | Funções do Azure <br> Stream Analytics do Azure |   |
| **Idiomas** | C# (depuração não compatível) | C <br> C# |
| **Mais informações** | [Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Ferramentas do Azure IoT Edge para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Ferramentas do Azure IoT Edge para Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Pré-requisitos

Um computador de desenvolvimento:

* Windows 10 com a atualização 1809 ou mais recente.
* É possível usar seu próprio computador ou uma máquina virtual, dependendo de suas preferências de desenvolvimento.
* Instale o [Git](https://git-scm.com/). 

Um dispositivo Azure IoT Edge no Windows:

* Recomendamos que você não execute o IoT Edge no computador de desenvolvimento, mas use, em vez disso, um dispositivo separado. Essa distinção entre computador de desenvolvimento e dispositivo IoT Edge mais precisamente reflete um cenário de implantação verdadeiro e ajuda a manter os diversos conceitos claros.
* Se não tiver um segundo dispositivo disponível, use o artigo de início rápido para criar um dispositivo IoT Edge no Azure com uma [máquina virtual Windows](quickstart.md).

Recursos de nuvem:

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou de camada padrão no Azure. 

## <a name="install-container-engine"></a>Instalar o mecanismo de contêiner

Os módulos do IoT Edge são empacotados como contêineres, portanto, você precisa de um mecanismo de contêiner no computador de desenvolvimento para criar e gerenciar os contêineres. É recomendável usar o Docker Desktop para o desenvolvimento por causa dos seus muitos recursos e de sua popularidade como um mecanismo de contêiner. Com o Docker Desktop em um computador Windows, é possível alternar entre contêineres do Linux e do Windows para poder desenvolver módulos facilmente para tipos diferentes de dispositivos IoT Edge. 

Use a documentação do Docker para instalar em seu computador de desenvolvimento: 

* [Instalar o Docker Desktop para Windows](https://docs.docker.com/docker-for-windows/install/)

  * Quando instala o Docker Desktop para Windows, você é solicitado se deseja usar os contêineres do Linux ou do Windows. Neste tutorial, use os **contêineres do Windows**. Para saber mais, confira [Switch between Windows and Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) (Alternar entre contêineres do Windows e do Linux).


## <a name="set-up-visual-studio-and-tools"></a>Configurar o Visual Studio e as ferramentas

As extensões da IoT para o Visual Studio ajudam a desenvolver módulos do IoT Edge. Essas extensões oferecem modelos de projeto, automatizam a criação do manifesto de implantação e permitem que você monitore e gerencie dispositivos IoT Edge. Nesta seção, você instalará o Visual Studio e a extensão IoT Edge. Em seguida, configurará sua conta do Azure para gerenciar recursos do Hub IoT no Visual Studio. 

Este tutorial ensina as etapas de desenvolvimento para o Visual Studio 2019. Se você estiver usando o Visual Studio 2017 (versão 15.7 ou superior), as etapas serão muito semelhantes. Se preferir usar o Visual Studio Code, veja as instruções em [Usar o Visual Studio Code para desenvolver e depurar módulos para o Azure IoT Edge](how-to-vs-code-develop-module.md). 

1. Prepare o Visual Studio 2019 no seu computador de desenvolvimento. 

   * Se você ainda não tiver o Visual Studio em seu computador de desenvolvimento, [instale o Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) com as cargas de trabalho a seguir: 

      * Desenvolvimento do Azure
      * Desenvolvimento para desktop com C++
      * Desenvolvimento multiplataforma com o .NET Core

   * Se você já tiver o Visual Studio de 2019 no computador de desenvolvimento, siga as etapas em [Modificar o Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) para adicionar as cargas de trabalho necessárias.

2. Baixe e instale a extensão [Ferramentas do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) para Visual Studio 2019. 

   Se estiver usando o Visual Studio 2017 (versão 15.7 ou superior), baixe e instale as [Ferramentas do Azure IoT Edge para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. Quando suas instalações estiverem concluídas, abra o Visual Studio 2019 e selecione **Continuar sem código**.

4. Selecione **Exibição** > **Cloud Explorer**. 

5. Selecione o ícone de perfil no Cloud Explorer e entre em sua conta do Azure se ainda não tiver entrado. 

6. Depois de entrar, suas assinaturas do Azure estarão listadas. Expanda a assinatura que tem seu Hub IoT. 

7. Na sua assinatura, expanda os **Hubs IoT** e, depois, o hub IoT. Você deverá ver uma lista de seus dispositivos IoT e poderá usar esse gerenciador para gerenciá-los. 

   ![Acessar recursos do Hub IoT no Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Criar um projeto de módulo

A extensão Ferramentas do Azure IoT Edge oferece modelos de projeto para todas as linguagens de módulo do IoT Edge compatíveis com o Visual Studio. Todos esses modelos têm os arquivos e o código nos quais você precisa implantar um módulo de trabalho para testar o IoT Edge ou oferecem a você um ponto de partida para personalizar o modelo com sua própria lógica de negócios. 

1. Selecione **Arquivo** > **Novo** > **Projeto...**

2. Na janela de novo projeto, pesquise **IoT Edge** e escolha o projeto **Azure IoT Edge (Windows amd64)** . Clique em **Próximo**. 

   ![Criar um projeto do Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

3. Na janela Configurar o novo projeto, renomeie o projeto e a solução para algo descritivo como **CSharpTutorialApp**. Clique em **Criar** para criar o projeto.

   ![Configurar um novo projeto do Azure IoT Edge](./media/tutorial-develop-for-windows/configure-project.png)
 

4. No aplicativo IoT Edge e na janela do módulo, configure seu projeto com os seguintes valores: 

   | Campo | Valor |
   | ----- | ----- |
   | Selecione um modelo | Selecione **Módulo em C#** . | 
   | Nome do projeto de módulo | Aceite o **IoTEdgeModule1** padrão. | 
   | Repositório de imagens do Docker | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner é populada previamente com base no valor de nome do projeto de módulo. Substitua **localhost:5000** pelo valor do servidor de logon do seu registro de contêiner do Azure. Você pode recuperar o servidor de logon da página Visão Geral do seu registro de contêiner no portal do Azure. <br><br> O repositório de imagem final se parece com \<nome do registro\>.azurecr.io/iotedgemodule1. |

   ![Configure seu projeto para o dispositivo de destino, tipo de módulo e registro de contêiner](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Selecione **Sim** para aplicar suas alterações. 

Depois que o novo projeto for carregado na janela do Visual Studio, aguarde um pouco para se familiarizar com os arquivos criados: 

* Um projeto de IoT Edge chamado **CSharpTutorialApp**.
    * A pasta **módulos** contém ponteiros para os módulos inclusos no projeto. Nesse caso, ela deve ser apenas IoTEdgeModule1. 
    * O arquivo **deployment.template.json** é o modelo para ajudar você a criar um manifesto de implantação. Um *manifesto de implantação* é um arquivo que define exatamente quais módulos você deseja implantar em um dispositivo, como eles devem ser configurados e como eles podem se comunicar entre si e com a nuvem. 
* Um projeto de módulo do IoT Edge chamado **IoTEdgeModule1**.
    * O arquivo **program.cs** contém o código do módulo C# padrão que vem com o modelo de projeto. O módulo padrão obtém a entrada de uma fonte e a passa para o Hub IoT. 
    * O arquivo **module.json** apresenta detalhes sobre o módulo, incluindo o repositório de imagem completo, a versão da imagem e qual Dockerfile usar para cada plataforma compatível.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Forneça suas credenciais de Registro para o agente do IoT Edge

O tempo de execução do IoT Edge precisa das credenciais de registro para efetuar pull de suas imagens de contêineres para o dispositivo IoT Edge. Adicione essas credenciais ao modelo de implantação. 

1. Abra o arquivo **deployment.template.json**.

2. Localize a propriedade **registryCredentials** nas propriedades desejadas do $edgeAgent. 

3. Atualize a propriedade com suas credenciais, seguindo este formato: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Salve o arquivo deployment.template.json. 

### <a name="review-the-sample-code"></a>Revisar o código de exemplo

O modelo de solução criado inclui um exemplo de código para um módulo do IoT Edge. Esse módulo de exemplo simplesmente recebe mensagens e as passa adiante. A funcionalidade de pipeline demonstra um conceito importante no IoT Edge, que é como os módulos comunicam-se uns com os outros.

Cada módulo pode ter várias filas de *entrada* e de *saída* declaradas em seu código. O hub do IoT Edge em execução no dispositivo roteia mensagens da saída de um módulo para a entrada de um ou mais módulos. A linguagem específica para declarar entradas e saídas varia entre linguagens, mas o conceito é o mesmo em todos os módulos. Para saber mais sobre o roteamento entre módulos, confira [Declarar rotas](module-composition.md#declare-routes).

O código de exemplo C# que vem com o modelo de projeto usa a [classe ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) do SDK do Hub IoT para .NET. 

1. No arquivo **program.cs**, localize o método **SetInputMessageHandlerAsync**.

2. O método [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) configura uma fila de entrada para receber mensagens de entrada. Examine essa função e veja como inicializa uma fila de entrada chamada **input1**. 

   ![Localizar o nome de entrada no construtor SetInputMessageHandlserAsync](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Em seguida, localize o método **SendEventAsync**.

4. O método [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) processa as mensagens recebidas e configura uma fila de saída para passá-las a diante. Examine esse método e veja que inicia uma fila de saída chamada **output1**. 

   ![Localizar o nome da saída no construtor SendEventAsync](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Abra o arquivo **deployment.template.json**.

6. Localize a propriedade **modules** das propriedades desejadas do $edgeAgent. 

   Deve haver dois módulos listados aqui. O primeiro é **tempSensor**, que está incluído em todos os modelos por padrão para fornecer dados de temperatura simulada que você pode usar para testar seus módulos. O segundo é o módulo **IotEdgeModule1** que você criou como parte desse projeto.

   A propriedade desse módulo declara quais módulos devem ser incluídos na implantação nos dispositivos. 

7. Localize a propriedade **routes** das propriedades desejadas de $edgeHub. 

   Uma das funções do módulo do hub do IoT Edge é rotear as mensagens entre todos os módulos em uma implantação. Examine os valores na propriedade routes. A primeira rota, **IotEdgeModule1ToIoTHub**, usa um caractere curinga ( **\*** ) para incluir qualquer mensagem recebida de qualquer fila de saída no módulo IoTEdgeModule1. Essas mensagens entram em *$upstream*, que é um nome reservado que indica o Hub IoT. A segunda rota, **sensorToIotEdgeModule1**, usa mensagens provenientes do módulo tempSensor e roteia-as para a fila de entrada *input1* do módulo IotEdgeModule1. 

   ![Examinar rotas em deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)


## <a name="build-and-push-your-solution"></a>Compilar e enviar por push sua solução

Você examinou o código do módulo e o modelo de implantação para entender alguns conceitos importantes de implantação. Agora você está pronto para criar a imagem de contêiner IotEdgeModule1 e efetuar push dela para o registro de contêiner. Com a extensão de ferramentas de IoT para o Visual Studio, esta etapa também gera o manifesto de implantação com base nas informações no arquivo de modelo e nas informações do módulo dos arquivos de solução. 

### <a name="sign-in-to-docker"></a>Entrar no Docker

Forneça suas credenciais do registro de contêiner para o Docker no computador de desenvolvimento, de modo que ele possa efetuar push da imagem de contêiner para que ela seja armazenada no registro. 

1. Abra o PowerShell ou um prompt de comando.

2. Entre no Docker com as credenciais do Registro de Contêiner do Azure que você salvou após criar o Registro. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Talvez você receba um aviso de segurança que recomende o uso de `--password-stdin`. Embora essa prática seja recomendada para cenários de produção, ela não serve para este tutorial. Para saber mais, confira a referência do [logon do docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Compilar e efetuar push

Agora, o computador de desenvolvimento tem acesso ao registro de contêiner e seus dispositivos IoT Edge também terão. É hora de transformar o código do projeto em uma imagem de contêiner. 

1. Clique com o botão direito do mouse na pasta de projeto **CSharpTutorialApp** e selecione **Criar e enviar módulos do IoT Edge por push**. 

   ![Criar e efetuar push de módulos do IoT Edge](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   O comando de criação e de envio por push inicia três operações. Primeiro, é criada uma pasta na solução denominada **config** que contém o manifesto de implantação completo, criado com base nas informações do modelo de implantação e em outros arquivos da solução. Depois, ele executa `docker build` para montar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Em seguida, ele executa `docker push` para enviar o repositório de imagens por push para seu registro de contêiner. 

   Esse processo pode levar vários minutos na primeira vez, mas será mais rápido na próxima vez em que você executar os comandos. 

2. Abra o arquivo **deployment.windows-amd64.json** na pasta de configuração recém-criada. (A pasta de configuração pode não aparecer no Gerenciador de Soluções no Visual Studio. Se esse for o caso, selecione o ícone **Mostrar todos os arquivos** na barra de tarefas do Gerenciador de Soluções.)

3. Localize o parâmetro **imagem** da seção IotEdgeModule1. Observe que a imagem contém o repositório de imagens completo com o nome, a versão e a marca de arquitetura do arquivo module.json.

4. Abra o arquivo **module.json** na pasta IotEdgeModule1. 

5. Altere o número de versão da imagem de módulo. (A versão, não $schema-version.) Por exemplo, incremente o número de versão de patch para **0.0.2** como se tivéssemos feito uma pequena correção no código do módulo. 

   >[!TIP]
   >As versões de módulo permitem o controle de versão e que você teste alterações em um pequeno conjunto de dispositivos antes de implantar atualizações em produção. Se você não incrementar a versão de módulo antes de criar e efetuar push, então você substituirá o repositório em seu registro de contêiner. 

6. Salve suas alterações no arquivo module.json.

7. Clique com o botão direito do mouse na pasta de projeto **CSharpTutorialApp** novamente e selecione **Criar e enviar módulos do IoT Edge por push** mais uma vez. 

8. Salve o arquivo **deployment.windows-amd64.json** de novo. Observe que um arquivo não foi criado quando você executou o comando de criação e de envio por push novamente. Em vez disso, o mesmo arquivo foi atualizado para refletir as alterações. Agora, a imagem IotEdgeModule1 aponta para a versão 0.0.2 do contêiner. Essa alteração no manifesto de implantação é como você informa ao dispositivo IoT Edge que há uma nova versão de um módulo para efetuar pull. 

9. Para verificar ainda mais o que o comando de criação e de envio por push fez, acesse o [portal do Azure](https://portal.azure.com) e navegue até o registro de contêiner. 

10. No registro de contêiner, selecione **Repositórios** e, sem seguida, **iotedgemodule1**. Verifique se as duas versões da imagem foram enviadas por push para o Registro.

    ![Exiba as duas versões da imagem no registro de contêiner](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Solucionar problemas

Se encontrar erros ao criar e enviar sua imagem de módulo por push, isso geralmente estará relacionado à configuração do Docker em seu computador de desenvolvimento. Use as seguintes verificações para examinar sua configuração: 

* Você executou o comando `docker login` usando as credenciais que copiou do seu registro de contêiner? Essas credenciais são diferentes daquelas que você usa para entrar no Azure. 
* Seu repositório de contêiner está correto? Ele tem seu nome de registro de contêiner correto e seu nome de módulo correto? Abra o arquivo **module.json** na pasta IotEdgeModule1 para verificar. O valor do repositório deve ter a seguinte aparência: **\<nome do registro\>.azurecr.io/iotedgemodule1**. 
* Se você usou um nome diferente de **IotEdgeModule1** para o seu módulo, esse nome está consistente em toda a solução?
* Seu computador está executando o mesmo tipo de contêineres que você está criando? Este tutorial é para dispositivos Windows IoT Edge, portanto, os arquivos do Visual Studio devem ter a extensão **windows-amd64** e o Docker Desktop deve estar executando contêineres do Windows. 

## <a name="deploy-modules-to-device"></a>Implantar módulos no dispositivo

Você verificou que as imagens de contêineres criadas estão armazenadas em seu registro de contêiner, portanto, chegou a hora de implantá-las em um dispositivo. Verifique se seu dispositivo IoT Edge está em funcionamento. 

1. Abra o Cloud Explorer no Visual Studio e expanda os detalhes para o hub IoT. 

2. Selecione o nome do dispositivo que você deseja implantar. Na lista **Ações**, selecione **Criar Implantação**.

   ![Criar implantação para dispositivo único](./media/tutorial-develop-for-windows/create-deployment.png)


3. No explorador de arquivos, selecione o arquivo de configuração do seu projeto e selecione o arquivo **deployment.windows-amd64.json**. Esse arquivo geralmente está localizado em `C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Não use o arquivo deployment.template.json, que não tem os valores de imagem de módulo completo nele. 

4. Expanda os detalhes de seu dispositivo IoT Edge no Cloud Explorer para ver os módulos no seu dispositivo.

5. Use o botão **Atualizar** para atualizar o status do dispositivo para ver que os módulos tempSensor e IotEdgeModule1 estão implantados em seu dispositivo. 


   ![Exibir módulos em execução em seu dispositivo IoT Edge](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Exibir mensagens do dispositivo

O código IotEdgeModule1 recebe mensagens por meio de sua fila de entrada e as passa adiante por meio de sua fila de saída. O manifesto de implantação declarou rotas que passavam mensagens de tempSensor para IotEdgeModule1 e, em seguida, encaminhava mensagens de IotEdgeModule1 para o Hub IoT. As ferramentas do Azure IoT Edge para Visual Studio permitem que você veja mensagens à medida que elas chegam ao Hub IoT dos seus dispositivos individuais. 

1. No Cloud Explorer do Visual Studio, selecione o nome do dispositivo IoT Edge que você implantou. 

2. No menu **Ações**, selecione **Iniciar o Monitoramento do Ponto de Extremidade de Evento Interno**.

3. Observe a janela de **Saída** no Visual Studio para ver as mensagens chegando ao seu hub IoT. 

   Pode levar alguns minutos para que os dois módulos sejam iniciados. O tempo de execução do IoT Edge precisa receber seu novo manifesto de implantação, obter as imagens de módulo do tempo de execução do contêiner e iniciar cada novo módulo. Se você 

   ![Exibir o dispositivo de entrada para mensagens de nuvem](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Exibir alterações no dispositivo

Se desejar ver o que está acontecendo no seu próprio dispositivo, use os comandos nesta seção para inspecionar o tempo de execução do IoT Edge e os módulos em execução em seu dispositivo. 

Os comandos nesta seção são para seu dispositivo IoT Edge, não para seu computador de desenvolvimento. Se estiver usando uma máquina virtual para seu dispositivo IoT Edge, conecte-se a ela agora. No Azure, acesse a página de visão geral da máquina virtual e selecione **Conectar** para acessar a conexão de área de trabalho remota. No dispositivo, abra um comando ou janela do PowerShell para executar os comandos `iotedge`.

* Exiba todos os módulos implantados em seu dispositivo e verifique seu status:

   ```cmd
   iotedge list
   ```

   Você deverá ver quatro módulos: os dois módulos de tempo de execução do IoT Edge, o tempSensor e o IotEdgeModule1. Todos os quatro devem estar listados como em execução.

* Inspecione os logs para ver se há um módulo específico:

   ```cmd
   iotedge logs <module name>
   ```

   Os módulos do IoT Edge diferenciam maiúsculas de minúsculas. 

   Os logs de tempSensor e IotEdgeModule1 devem mostrar as mensagens que estão processando. O módulo edgeAgent é responsável por iniciar os outros módulos, portanto seus logs terão informações sobre como implementar o manifesto de implantação. Se algum módulo não estiver listado ou não estiver em execução, os logs de edgeAgent provavelmente terão os erros. O módulo edgeHub é responsável pelas comunicações entre os módulos e o Hub IoT. Se os módulos estiverem em funcionamento, mas as mensagens não estiverem chegando ao seu hub IoT, os logs de edgeHub provavelmente terão os erros. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou o Visual Studio 2019 em seu computador de desenvolvimento e implantou seu primeiro módulo do IoT Edge dele. Agora que você conhece os conceitos básicos, experimente adicionar uma funcionalidade a um módulo para que ele possa analisar os dados que são passados por ele. Escolha a linguagem de sua preferência: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
