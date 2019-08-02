---
title: Desenvolver módulo para dispositivos Linux – Azure IoT Edge | Microsoft Docs
description: Este tutorial explica como configurar os recursos de nuvem e de computador de desenvolvimento para desenvolver módulos do IoT Edge usando contêineres do Linux para dispositivos Linux
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/10/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: e5499afebf29df2942e74148b33797844fa9c880
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051943"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Tutorial: Desenvolver módulos do IoT Edge para dispositivos Linux

Use o Visual Studio Code para desenvolver e implantar código em dispositivos Linux que executam o IoT Edge. 

Nos artigos de início rápido, você criou um dispositivo IoT Edge usando uma máquina virtual Linux e implantou um módulo predefinido com base no Azure Marketplace. Este tutorial explica o que é necessário para desenvolver e implantar seu próprio código em um dispositivo IoT Edge. Este tutorial é um pré-requisito útil para todos os outros tutoriais, que se aprofundarão nas linguagens de programação específicas ou nos serviços do Azure. 

Este tutorial usa o exemplo de como implantar um **módulo do C# em um dispositivo Linux**. Este exemplo foi escolhido porque é o cenário mais comum do desenvolvedor em soluções do IoT Edge. Mesmo se você planeja usar um idioma diferente ou implantar um serviço do Azure, o tutorial ainda será útil para saber mais sobre os conceitos e as ferramentas de desenvolvimento. Depois de concluir esta introdução ao processo de desenvolvimento, você pode escolher o idioma ou o serviço do Azure para se aprofundar nos detalhes. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar seu computador de desenvolvimento.
> * Usar as ferramentas do IoT Edge para o Visual Studio Code para criar um projeto.
> * Criar seu projeto como um contêiner e armazená-lo em um Registro de Contêiner do Azure.
> * Implantar seu código em um dispositivo IoT Edge. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Principais conceitos

Este tutorial explica o desenvolvimento de um módulo do IoT Edge. Um *módulo do IoT Edge* ou, às vezes, apenas *módulo* de forma abreviada, é um contêiner que contém o código executável. É possível implantar um ou mais módulos em um dispositivo IoT Edge. Módulos executam tarefas específicas, como ingerir dados de sensores, executar análise de dados ou operações de limpeza de dados ou enviar mensagens a um hub IoT. Para saber mais, confira [Understand Azure IoT Edge modules](iot-edge-modules.md) (Noções básicas sobre módulos do Azure IoT Edge).

Ao desenvolver módulos do IoT Edge, é importante entender as diferenças entre o computador de desenvolvimento e o dispositivo IoT Edge de destino em que o módulo será implantado eventualmente. O contêiner que você criar para armazenar seu código de módulo deverá corresponder ao SO (sistema operacional) do *dispositivo de destino*. Por exemplo, o cenário mais comum é alguém desenvolver um módulo em um computador Windows com a intenção de direcionar um dispositivo Linux que executa o IoT Edge. Nesse caso, o sistema operacional do contêiner seria Linux. Ao percorrer este tutorial, lembre-se a diferença entre o *sistema operacional do computador de desenvolvimento* e o *sistema operacional do contêiner*.

Este tutorial direciona dispositivos Linux que executam o IoT Edge. É possível usar o sistema operacional do computador de desenvolvimento de sua preferência, contanto que ele possa executar contêineres do Linux. É recomendável usar o Visual Studio Code para desenvolver para dispositivos Linux, portanto, é isso que este tutorial usará. É possível usar o Visual Studio também, embora haja diferenças no suporte entre as duas ferramentas.

A tabela a seguir lista os cenários de desenvolvimento compatíveis para **contêineres do Linux** no Visual Studio Code e no Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Arquitetura de dispositivo do Linux** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Serviços do Azure** | Funções do Azure <br> Stream Analytics do Azure <br> Azure Machine Learning |   |
| **Idiomas** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Mais informações** | [Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Ferramentas do Azure IoT Edge para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Ferramentas do Azure IoT Edge para Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

Este tutorial ensina as etapas de desenvolvimento para o Visual Studio Code. Se preferir usar o Visual Studio, veja as instruções em [Usar o Visual Studio 2019 para desenvolver e depurar módulos para o Azure IoT Edge](how-to-visual-studio-develop-module.md).

## <a name="prerequisites"></a>Pré-requisitos

Um computador de desenvolvimento:

* É possível usar seu próprio computador ou uma máquina virtual, dependendo de suas preferências de desenvolvimento.
* A maioria dos sistemas operacionais que pode executar um mecanismo de contêiner pode ser usada para desenvolver módulos do IoT Edge para dispositivos Linux. Este tutorial usa um computador Windows, mas indica diferenças conhecidas no MacOS ou no Linux. 
* Instale o [Git](https://git-scm.com/) para extrair pacotes de modelo de módulo posteriormente neste tutorial.  
* [C# para extensão do Visual Studio Code (com OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [SDK do .NET Core 2.1](https://www.microsoft.com/net/download).

Um dispositivo do Azure IoT Edge no Linux:

* Recomendamos que você não execute o IoT Edge no computador de desenvolvimento, mas use, em vez disso, um dispositivo separado. Essa distinção entre computador de desenvolvimento e dispositivo IoT Edge mais precisamente reflete um cenário de implantação verdadeiro e ajuda a manter os diversos conceitos claros.
* Se não tiver um segundo dispositivo disponível, use o artigo de início rápido para criar um dispositivo IoT Edge no Azure com uma [máquina virtual Linux](quickstart-linux.md).

Recursos de nuvem:

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou de camada padrão no Azure. 

## <a name="install-container-engine"></a>Instalar o mecanismo de contêiner

Os módulos do IoT Edge são empacotados como contêineres, portanto, você precisa de um mecanismo de contêiner no computador de desenvolvimento para criar e gerenciar os contêineres. É recomendável usar o Docker Desktop para o desenvolvimento por causa dos seus muitos recursos e de sua popularidade como um mecanismo de contêiner. Com o Docker Desktop em um dispositivo Windows, é possível alternar entre contêineres do Linux e do Windows para poder desenvolver módulos facilmente para tipos diferentes de dispositivos IoT Edge. 

Use a documentação do Docker para instalar em seu computador de desenvolvimento: 

* [Instalar o Docker Desktop para Windows](https://docs.docker.com/docker-for-windows/install/)

  * Quando instala o Docker Desktop para Windows, você é solicitado se deseja usar os contêineres do Linux ou do Windows. Essa decisão pode ser alterada a qualquer momento usando uma comutação fácil. Para este tutorial, usaremos contêineres do Linux, porque nossos módulos estão direcionando dispositivos Linux. Para saber mais, confira [Switch between Windows and Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) (Alternar entre contêineres do Windows e do Linux).

* [Instalar o Docker Desktop para Mac](https://docs.docker.com/docker-for-mac/install/)

* Leia [Sobre o Docker CE](https://docs.docker.com/install/) para obter informações sobre a instalação em várias plataformas do Linux.

## <a name="set-up-vs-code-and-tools"></a>Configurar o VS Code e ferramentas

Use as extensões da IoT para o Visual Studio Code desenvolver módulos do IoT Edge. Essas extensões oferecem modelos de projeto, automatizam a criação do manifesto de implantação e permitem que você monitore e gerencie dispositivos IoT Edge. Nesta seção, você instalará o Visual Studio Code e a extensão IoT; em seguida, configurará sua conta do Azure para gerenciar recursos do Hub IoT de dentro do Visual Studio Code. 

1. Instale o [Visual Studio Code](https://code.visualstudio.com/) em seu computador de desenvolvimento. 

2. Após a conclusão da instalação, selecione **Exibir** > **Extensões**. 

3. Pesquise **Azure IoT Tools**, que, na verdade, é uma coleção de extensões que ajudam você a interagir com o Hub IoT e os dispositivos IoT, além de desenvolver módulos do IoT Edge. 

4. Selecione **Instalar**. Cada extensão incluída é instalada individualmente. 

5. Quando a instalação das extensões é concluída, abra a paleta de comandos selecionando **Exibir** > **Paleta de Comandos**. 

6. Na paleta de comandos, pesquise e selecione **Azure: conectar**. Siga os prompts para entrar na sua conta do Azure. 

7. Na paleta de comandos novamente, pesquise e selecione **Hub IoT do Azure: selecionar Hub IoT**. Siga os prompts para selecionar sua assinatura e o hub IoT do Azure. 

7. Abra a seção do gerenciador do Visual Studio Code selecionando o ícone na barra de atividades à esquerda ou selecionando **Exibir** > **Explorer**. 

8. Na parte inferior da seção do Explorer, expanda o menu **Dispositivos do Hub IoT do Azure** recolhido. Você deverá ver os dispositivos e os dispositivos IoT Edge associados ao hub IoT selecionando por meio da paleta de comandos. 

   ![Exibir dispositivos em seu hub IoT](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Criar um projeto de módulo

A extensão Azure IoT Tools oferece modelos de projeto para todas as linguagens de módulo do IoT Edge compatíveis com o Visual Studio Code. Todos esses modelos têm os arquivos e o código nos quais você precisa implantar um módulo de trabalho para testar o IoT Edge ou oferecem a você um ponto de partida para personalizar o modelo com sua própria lógica de negócios. 

Para este tutorial, usamos o modelo de módulo C#, pois é o modelo mais comumente usado. 

### <a name="create-a-project-template"></a>Criar um modelo de projeto

Na paleta de comandos do Visual Studio Code, pesquise e selecione **Azure IoT Edge: Nova Solução do IoT Edge**. Siga os prompts e use os seguintes valores para criar sua solução: 

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha **Módulo C#** . |
   | Fornecer um nome de módulo | Aceite o **SampleModule** padrão. |
   | Fornecer o repositório de imagem do Docker para o módulo | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner é preenchida previamente o nome fornecido na última etapa. Substitua **localhost:5000** pelo valor do servidor de logon do seu registro de contêiner do Azure. Você pode recuperar o servidor de logon da página Visão Geral do seu registro de contêiner no portal do Azure. <br><br> O repositório final de imagens tem a aparência de \<nome do Registro\>.azurecr.io/samplemodule. |
 
   ![Fornecer o repositório de imagem do Docker](./media/tutorial-develop-for-linux/image-repository.png)

Depois que a nova solução for carregada na janela do Visual Studio Code, aguarde um pouco para se familiarizar com os arquivos criados: 

* A pasta **.vscode** contém um arquivo chamado **launch.json**, que é usado para depurar módulos.
* A pasta **modules** contém uma pasta para cada módulo da sua solução. Neste momento, só deve ser **SampleModule** ou qualquer nome que você deu ao módulo. A pasta SampleModule contém o código do programa principal, os metadados do módulo e vários arquivos do Docker. 
* O arquivo **.env** armazena as credenciais para seu registro de contêiner. Essas credenciais são compartilhadas com seu dispositivo IoT Edge para que ele tenha acesso para efetuar pull de imagens de contêiner. 
* O arquivo **deployment.debug.template.json** e o arquivo **deployment.template.json** são modelos que ajudam você a criar um manifesto de implantação. Um *manifesto de implantação* é um arquivo que define exatamente quais módulos você deseja implantar em um dispositivo, como eles devem ser configurados e como eles podem se comunicar entre si e com a nuvem. Os arquivos de modelo usam ponteiros para alguns valores. Quando você transforma o modelo em um manifesto de implantação verdadeiro, os ponteiros são substituídos por valores extraídos de outros arquivos de solução. Localize os dois espaços reservados comuns em seu modelo de implantação: 

  * Na seção de credenciais de Registro, o endereço é preenchido automaticamente com as informações fornecidas quando você criou a solução. No entanto, o nome de usuário e a senha referenciam as variáveis armazenadas no arquivo .env. Isso é por questões de segurança, uma vez que o arquivo .env é ignorado pelo git, mas o modelo de implantação não. 
  * Na seção SampleModule, a imagem de contêiner não é preenchida mesmo se você forneceu o repositório de imagens quando criou a solução. Esse espaço reservado aponta para o arquivo **module.json** dentro da pasta SampleModule. Se você acessar esse arquivo, verá que o campo de imagem contém o repositório, mas também um valor de marca composto pela versão e a plataforma do contêiner. É possível iterar a versão manualmente como parte do ciclo de desenvolvimento; selecione a plataforma do contêiner usando um alternador que apresentaremos posteriormente nesta seção. 

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Forneça suas credenciais de Registro para o agente do IoT Edge

O arquivo do ambiente armazena as credenciais para o registro de contêiner e as compartilha com o tempo de execução do IoT Edge. O tempo de execução precisa dessas credenciais para efetuar pull de suas imagens de contêiner para o dispositivo IoT Edge. 

A extensão do IoT Edge tenta efetuar pull de suas credenciais de Registro de Contêiner do Azure e as popula no arquivo de ambiente. Verifique se suas credenciais já estão incluídas. Caso contrário, adicione-as agora:

1. Abra o arquivo **.env** em sua solução de módulo. 
2. Adicione os valores **nome de usuário** e **senha** que você copiou do Registro de Contêiner do Azure.
3. Salve suas alterações no arquivo .env. 

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

No momento, o Visual Studio Code pode desenvolver módulos em C# para os dispositivos Linux AMD64 e ARM32v7. É necessário selecionar qual arquitetura você está direcionando com cada solução, porque isso afeta a maneira como o contêiner é criado e executado. O padrão é Linux AMD64. 

1. Abra a paleta de comandos e pesquise **Azure IoT Edge: definir a Plataforma de Destino Padrão para a Solução do Edge** ou selecione o ícone de atalho na barra lateral na parte inferior da janela. 

   ![Selecionar o ícone da arquitetura na barra lateral](./media/tutorial-develop-for-linux/select-architecture.png)

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manteremos o padrão **amd64**. 

### <a name="review-the-sample-code"></a>Revisar o código de exemplo

O modelo de solução criado inclui um exemplo de código para um módulo do IoT Edge. Esse módulo de exemplo simplesmente recebe mensagens e as passa adiante. A funcionalidade de pipeline demonstra um conceito importante no IoT Edge, que é como os módulos comunicam-se uns com os outros.

Cada módulo pode ter várias filas de *entrada* e de *saída* declaradas em seu código. O hub do IoT Edge em execução no dispositivo roteia mensagens da saída de um módulo para a entrada de um ou mais módulos. A linguagem específica para declarar entradas e saídas varia entre linguagens, mas o conceito é o mesmo em todos os módulos. Para saber mais sobre o roteamento entre módulos, confira [Declarar rotas](module-composition.md#declare-routes).

O código de exemplo C# que vem com o modelo de projeto usa a [classe ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) do SDK do Hub IoT para .NET. 

1. Abra o arquivo **Program.cs**, na pasta **modules/SampleModule/** . 

2. Em program.cs, localize o método **SetInputMessageHandlerAsync**.

2. O método [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) configura uma fila de entrada para receber mensagens de entrada. Examine essa função e veja como inicializa uma fila de entrada chamada **input1**. 

   ![Localizar o nome da entrada no construtor SetInputMessageCallback](./media/tutorial-develop-for-linux/declare-input-queue.png)

3. Em seguida, localize o método **SendEventAsync**.

4. O método [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) processa as mensagens recebidas e configura uma fila de saída para passá-las a diante. Examine ess emétodo e veja que inicializa uma fila de saída chamada **output1**. 

   ![Localizar o nome da saída em SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Abra o arquivo **deployment.template.json**.

7. Localize a propriedade **modules** das propriedades desejadas do $edgeAgent. 

   Deve haver dois módulos listados aqui. O primeiro é **tempSensor**, que está incluído em todos os modelos por padrão para fornecer dados de temperatura simulada que você pode usar para testar seus módulos. O segundo é o módulo **SampleModule** que você criou como parte dessa solução.

7. Na parte inferior do arquivo, localize as propriedades desejadas para o módulo **$edgeHub**. 

   Uma das funções do módulo do hub do IoT Edge é rotear mensagens entre todos os módulos em uma implantação. Examine os valores na propriedade **rotas**. A primeira rota, **SampleModuleToIoTHub**, usa um caractere curinga ( **\*** ) para indicar as mensagens provenientes das filas de saída no módulo SampleModule. Essas mensagens entram em *$upstream*, que é um nome reservado que indica o Hub IoT. A segunda rota, sensorToSampleModule, usa mensagens provenientes do módulo tempSensor e as roteia para a fila de entrada *input1* que você viu inicializada no código SampleModule. 

   ![Examinar rotas em deployment.template.json](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Compilar e enviar por push sua solução

Você examinou o código do módulo e o modelo de implantação para entender alguns conceitos importantes de implantação. Agora você está pronto para criar a imagem de contêiner SampleModule e efetuar push dela para seu registro de contêiner. Com a extensão de ferramentas de IoT para Visual Studio Code, esta etapa também gera o manifesto de implantação com base nas informações no arquivo de modelo e nas informações do módulo dos arquivos de solução. 

### <a name="sign-in-to-docker"></a>Entrar no Docker

Forneça suas credenciais de registro de contêiner para o Docker para que ele possa efetuar push de sua imagem de contêiner para ser armazenada no Registro. 

1. Abra o terminal integrado do Visual Studio Code selecionando **Exibir** > **Terminal**.

2. Entre no Docker com as credenciais do Registro de Contêiner do Azure que você salvou após criar o Registro. 

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Talvez você receba um aviso de segurança que recomende o uso de `--password-stdin`. Embora essa prática seja recomendada para cenários de produção, ela não serve para este tutorial. Para saber mais, confira a referência do [logon do docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Compilar e efetuar push 

Agora o Visual Studio Code tem acesso ao seu registro de contêiner; portanto, chegou a hora de transformar o código da solução em uma imagem de contêiner. 

1. No gerenciador do Visual Studio Code, clique com o botão direito do mouse no arquivo **deployment.template.json** e selecione **Criar e efetuar push da solução IoT Edge**. 

   ![Criar e efetuar push de módulos do IoT Edge](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   O comando de criação e de envio por push inicia três operações. Primeiro, é criada uma pasta na solução denominada **config** que contém o manifesto de implantação completo, criado com base nas informações do modelo de implantação e em outros arquivos da solução. Depois, ele executa `docker build` para montar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Em seguida, ele executa `docker push` para enviar o repositório de imagens por push para seu registro de contêiner. 

   Esse processo pode levar vários minutos na primeira vez, mas será mais rápido na próxima vez em que você executar os comandos. 

2. Abra o arquivo **deployment.amd64.json** na pasta de configuração recém-criada. O nome do arquivo reflete a arquitetura de destino; portanto, ele será diferente se você escolher uma arquitetura diferente.

3. Observe que os dois parâmetros que têm espaços reservados agora são preenchidos com seus valores adequados. A seção **registryCredentials** tem o nome de usuário e a senha do seu Registro extraídos do arquivo .env. O **SampleModule** tem o repositório de imagens completo com o nome, a versão e a marca de arquitetura do arquivo module.json. 

4. Abra o arquivo **module.json** na pasta SampleModule. 

5. Altere o número de versão da imagem de módulo. (A versão, não $schema-version.) Por exemplo, incremente o número de versão de patch para **0.0.2** como se tivéssemos feito uma pequena correção no código do módulo. 

   >[!TIP]
   >As versões de módulo permitem o controle de versão e que você teste alterações em um pequeno conjunto de dispositivos antes de implantar atualizações em produção. Se você não incrementar a versão de módulo antes de criar e efetuar push, então você substituirá o repositório em seu registro de contêiner. 

6. Salve suas alterações no arquivo module.json.

7. Clique com o botão direito do mouse no arquivo **deployment.template.json** e selecione novamente **Criar e efetuar push da solução IoT Edge**.

8. Abra o arquivo **deployment.amd64.json** novamente. Observe que um arquivo não foi criado quando você executou o comando de criação e de envio por push novamente. Em vez disso, o mesmo arquivo foi atualizado para refletir as alterações. Agora a imagem SampleModule aponta para a versão 0.0.2 do contêiner. 

9. Para verificar ainda mais o que o comando de criação e de envio por push fez, acesse o [portal do Azure](https://portal.azure.com) e navegue até o registro de contêiner. 

10. No registro de contêiner, selecione **Repositórios** e, sem seguida, **samplemodule**. Verifique se as duas versões da imagem foram enviadas por push para o Registro.

    ![Exiba as duas versões da imagem no registro de contêiner](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Solucionar problemas

Se encontrar erros ao criar e enviar sua imagem de módulo por push, isso geralmente estará relacionado à configuração do Docker em seu computador de desenvolvimento. Use as seguintes verificações para examinar sua configuração: 

* Você executou o comando `docker login` usando as credenciais que copiou do seu registro de contêiner? Essas credenciais são diferentes daquelas que você usa para entrar no Azure. 
* Seu repositório de contêiner está correto? Ele tem seu nome de registro de contêiner correto e seu nome de módulo correto? Abra o arquivo **module.json** na pasta SampleModule a ser verificado. O valor do repositório deve ter a aparência de **\<nome do Registro\>.azurecr.io/samplemodule**. 
* Se você usou um nome diferente de **SampleModule** para o seu módulo, esse nome está consistente em toda a solução?
* Seu computador está executando o mesmo tipo de contêineres que você está criando? Este tutorial é para dispositivos IoT Edge do Linux; portanto, o Visual Studio Code deve informar **amd64** ou **arm32v7** na barra lateral e o Docker Desktop deve estar executando contêineres do Linux.  

## <a name="deploy-modules-to-device"></a>Implantar módulos no dispositivo

Você verificou que as imagens de contêineres criadas estão armazenadas em seu registro de contêiner, portanto, chegou a hora de implantá-las em um dispositivo. Verifique se seu dispositivo IoT Edge está em funcionamento. 

1. No gerenciador do Visual Studio Code, expanda a seção Dispositivos do Hub IoT do Azure. 

2. Clique com o botão direito do mouse no dispositivo IoT Edge no qual você deseja implantar e selecione **Criar implantação para um único dispositivo**. 

   ![Criar implantação para dispositivo único](./media/tutorial-develop-for-linux/create-deployment.png)

3. No explorador de arquivos, navegue até a pasta **config** e selecione o arquivo **deployment.amd64.json**. 

   Não use o arquivo deployment.template.json, que não tem as credenciais de registro de contêiner ou os valores de imagem de módulo nele. Se você estiver direcionando um dispositivo Linux ARM32, o manifesto de implantação será denominado deployment.arm32v7.json. 

4. Expanda os detalhes de seu dispositivo IoT Edge e, em seguida, expanda a lista **Módulos** do seu dispositivo. 

5. Use o botão de atualização para atualizar a exibição do dispositivo até ver os módulos tempSensor e SampleModule em execução em seu dispositivo. 

   Pode levar alguns minutos para que os dois módulos sejam iniciados. O tempo de execução do IoT Edge precisa receber seu novo manifesto de implantação, obter as imagens de módulo do tempo de execução do contêiner e iniciar cada novo módulo. 

   ![Exibir módulos em execução em seu dispositivo IoT Edge](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Exibir mensagens do dispositivo

O código SampleModule recebe mensagens por meio de sua fila de entrada e as passa adiante por meio de sua fila de saída. O manifesto de implantação declarou rotas que passavam mensagens para SampleModule de tempSensor e, em seguida, encaminhava mensagens de SampleModule para o Hub IoT. O Azure IoT Tools para Visual Studio Code permitem que você veja mensagens à medida que elas chegam no Hub IoT dos seus dispositivos individuais. 

1. No gerenciador do Visual Studio Code, clique com o botão direito do mouse no dispositivo IoT Edge que deseja monitorar e, em seguida, selecione **Iniciar o Monitoramento do Ponto de Extremidade de Evento Interno**. 

2. Observe a janela de Saída no Visual Studio Code para ver as mensagens chegando em seu hub IoT. 

   ![Exibir o dispositivo de entrada para mensagens de nuvem](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Exibir alterações no dispositivo

Se desejar ver o que está acontecendo no seu próprio dispositivo, use os comandos nesta seção para inspecionar o tempo de execução do IoT Edge e os módulos em execução em seu dispositivo. 

Os comandos nesta seção são para seu dispositivo IoT Edge, não para seu computador de desenvolvimento. Se estiver usando uma máquina virtual para seu dispositivo IoT Edge, conecte-se a ela agora. No Azure, acesse a página de visão geral da máquina virtual e selecione **Conectar** para acessar a conexão de shell segura. 

* Exiba todos os módulos implantados em seu dispositivo e verifique seu status:

   ```bash
   iotedge list
   ```

   Você deverá ver quatro módulos: os dois módulos de tempo de execução do IoT Edge, o tempSensor e o SampleModule. Todos os quatro devem estar listados como em execução.

* Inspecione os logs para ver se há um módulo específico:

   ```bash
   iotedge logs <module name>
   ```

   Os módulos do IoT Edge diferenciam maiúsculas de minúsculas. 

   Os logs de SamplModule e de tempSensor devem mostrar as mensagens que estão processando. O módulo edgeAgent é responsável por iniciar os outros módulos, portanto seus logs terão informações sobre como implementar o manifesto de implantação. Se algum módulo não estiver listado ou não estiver em execução, os logs de edgeAgent provavelmente terão os erros. O módulo edgeHub é responsável pelas comunicações entre os módulos e o Hub IoT. Se os módulos estiverem em funcionamento, mas as mensagens não estiverem chegando ao seu hub IoT, os logs de edgeHub provavelmente terão os erros. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou o Visual Studio Code em seu computador de desenvolvimento e implantou seu primeiro módulo do IoT Edge dele. Agora que você conhece os conceitos básicos, experimente adicionar uma funcionalidade a um módulo para que ele possa analisar os dados que são passados por ele. Escolha a linguagem de sua preferência: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)