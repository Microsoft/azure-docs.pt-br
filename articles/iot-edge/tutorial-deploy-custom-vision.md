---
title: Tutorial – Implantar o classificador de Visão Personalizada em um dispositivo usando o Azure IoT Edge
description: Neste tutorial, aprenda a fazer um modelo de pesquisa visual computacional ser executado como um contêiner usando o IoT Edge e a Visão Personalizada.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: c867add4d4214dc847c39ee66b7177daaafdfc8a
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221680"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>Tutorial: Realizar a classificação de imagem na borda com o Serviço de Visão Personalizada

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

O Azure IoT Edge pode tornar sua solução de IoT mais eficiente movendo cargas de trabalho da nuvem para a borda. Esse recurso funciona bem para serviços que processam muitos dados, como modelos visuais computacionais. O [Serviço de Visão Personalizada](../cognitive-services/custom-vision-service/overview.md) permite criar classificadores de imagem personalizados e implantá-los em dispositivos como contêineres. Juntos, esses dois serviços permitem a você encontrar insights em imagens ou fluxos de vídeo sem a necessidade de primeiro transferir todos os dados do local. A Visão Personalizada fornece um classificador que compara uma imagem com um modelo treinado para gerar insights.

Por exemplo, a Visão Personalizada em um dispositivo do IoT Edge conseguiu determinar se uma estrada está com tráfego acima ou abaixo do normal, ou se uma garagem de estacionamento tem vagas disponíveis em sequência. Essas informações podem ser compartilhadas com outro serviço que atuará em relação a elas.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Criar um classificador de imagem com a Visão Personalizada.
> * Desenvolver um módulo do IoT Edge que consulta o servidor Web da Visão Personalizada em seu dispositivo.
> * Enviar os resultados do classificador de imagem para o Hub IoT.

<center>

![Diagrama – Arquitetura do tutorial, classificador de preparação e implantação](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

>[!TIP]
>Este tutorial é uma versão simplificada do projeto de exemplo [Visão Personalizada e Azure IoT Edge em um Raspberry Pi 3](https://github.com/Azure-Samples/custom-vision-service-iot-edge-raspberry-pi). Este tutorial foi criado para ser executado em uma VM de nuvem e usa imagens estáticas para treinar e testar o classificador de imagens, o que é útil para alguém que está começando a avaliar a Visão Personalizada no IoT Edge. O projeto de exemplo usa o hardware físico e configura um feed de câmera ao vivo para treinar e testar o classificador de imagens, o que é útil para alguém que deseja experimentar um cenário mais detalhado de vida real.

Antes de iniciar este tutorial, você deve ter percorrido o tutorial anterior para configurar o ambiente para o desenvolvimento de contêiner Linux: [Desenvolver módulos do IoT Edge usando contêineres Linux](tutorial-develop-for-linux.md). Ao concluir esse tutorial, você deve ter os seguintes pré-requisitos implementados:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure.
* Um dispositivo que executa o Azure IoT Edge com contêineres Linux. Você pode usar os inícios rápidos para configurar um [dispositivo Linux](quickstart-linux.md) ou um [dispositivo Windows](quickstart.md).
* Um registro de contêiner, como o [Registro de Contêiner do Azure](../container-registry/index.yml).
* O [Visual Studio Code](https://code.visualstudio.com/) configurado com o [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* O [Docker CE](https://docs.docker.com/install/) configurado para executar contêineres do Linux.

Para desenvolver um módulo do IoT Edge com o Serviço de Visão Personalizada, instale os seguintes pré-requisitos adicionais em seu computador de desenvolvimento:

* [Python](https://www.python.org/downloads/)
* [Git](https://git-scm.com/downloads)
* [Extensão do Python para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

## <a name="build-an-image-classifier-with-custom-vision"></a>Criar um classificador de imagem com a Visão Personalizada

Para criar um classificador de imagem, você precisa criar um projeto de Visão Personalizada e fornecer imagens de treinamento. Para obter mais informações sobre as etapas que você seguirá nesta seção, consulte [Como criar um classificador com a Visão Personalizada](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md).

Depois que seu classificador de imagem for criado e treinado, você poderá exportá-lo como um contêiner do Docker e implantá-lo em um dispositivo do IoT Edge.

### <a name="create-a-new-project"></a>Criar um novo projeto

1. No navegador da Web, navegue até a [página da Web da Visão Personalizada](https://customvision.ai/).

2. Selecione **Entrar** e entre com a mesma conta usada para acessar recursos do Azure.

3. Selecione **Novo projeto**.

4. Crie seu projeto com os seguintes valores:

   | Campo | Valor |
   | ----- | ----- |
   | Nome | Forneça um nome para o projeto, como **EdgeTreeClassifier**. |
   | Descrição | Descrição do projeto opcional. |
   | Recurso | Selecione um de seus grupos de recursos do Azure que inclua um recurso do Serviço de Visão Personalizada ou **crie um novo** se você ainda não tiver adicionado um. |
   | Tipos de projeto | **Classificação** |
   | Tipos de classificação | **Multiclasses (uma marca por imagem)** |
   | Domínios | **Geral (compacto)** |
   | Exportar funcionalidades | **Plataformas básicas (Tensorflow, CoreML, ONNX,...)** |

5. Selecione **Criar projeto**.

### <a name="upload-images-and-train-your-classifier"></a>Carregar imagens e treinar o classificador

A criação de um classificador de imagem requer um conjunto de imagens de treinamento e imagens de teste.

1. Clone ou baixe as imagens de exemplo do repositório [CustomVision-Windows-cognitivas](https://github.com/Microsoft/Cognitive-CustomVision-Windows) em seu computador de desenvolvimento local.

   ```cmd/sh
   git clone https://github.com/Microsoft/Cognitive-CustomVision-Windows.git
   ```

2. Retorne ao projeto da Visão Personalizada e selecione **Adicionar imagens**.

3. Navegue até o repositório git que você clonou localmente e navegue até a primeira pasta de imagens, **Cognitive-CustomVision-Windows/Samples/Images/Hemlock**. Selecione todas as dez imagens na pasta e selecione **Abrir**.

4. Adicione a marca **hemlock** a esse grupo de imagens e pressione **Enter** para aplicar a marca.

5. Selecione **Carregar 10 arquivos**.

   ![Carregar arquivos de hemlock marcados para Visão Personalizada](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. Quando as imagens forem carregadas com êxito, selecione **Concluído**.

7. Selecione **Adicionar imagens** novamente.

8. Navegue até a segunda pasta de imagens, **Cognitive-CustomVision-Windows/Samples/Images/Japanese Cherry**. Selecione todas as dez imagens na pasta e selecione **Abrir**.

9. Adicione a marca **japanese cherry** a esse grupo de imagens e pressione **Enter** para aplicar a marca.

10. Selecione **Carregar 10 arquivos**. Quando as imagens forem carregadas com êxito, selecione **Concluído**.

11. Quando ambos os conjuntos de imagens estiverem marcados e carregados, selecione **Treinar** para treinar o classificador.

### <a name="export-your-classifier"></a>Exportar o classificador

1. Depois de treinar o classificador, selecione **Exportar** na página Desempenho do classificador.

   ![Exportar seu classificador de imagens treinado](./media/tutorial-deploy-custom-vision/export.png)

2. Selecione **DockerFile** como a plataforma. 

3. Selecione **Linux** como a versão.  

4. Selecione **Exportar**. 

   ![Exportar como DockerFile com contêineres do Linux](./media/tutorial-deploy-custom-vision/export-2.png)

5. Quando a exportação estiver concluída, selecione **Baixar** e salve o pacote .zip localmente em seu computador. Extraia todos os arquivos do pacote. Você usará esses arquivos para criar um módulo do IoT Edge que contém o servidor de classificação de imagem. 

Quando você chegar a este ponto, terá concluído a criação e o treinamento de seu projeto da Visão Personalizada. Você usará os arquivos exportados na próxima seção, mas terminou de usar a página da Web da Visão Personalizada. 

## <a name="create-an-iot-edge-solution"></a>Criar uma solução IoT Edge

Agora você tem os arquivos de uma versão de contêiner de seu classificador de imagem no computador de desenvolvimento. Nesta seção, você configura o contêiner de classificador de imagem para ser executado como um módulo do IoT Edge. Você também pode criar um segundo módulo que será implantado juntamente com o classificador de imagem. O segundo módulo posta solicitações para o classificador e envia os resultados como mensagens ao Hub IoT. 

### <a name="create-a-new-solution"></a>Criar uma nova solução

Uma solução é uma maneira lógica de desenvolver e organizar vários módulos para uma mesma implantação do IoT Edge. Uma solução contém código para um ou mais módulos, bem como o manifesto de implantação que declara como configurá-los em um dispositivo do IoT Edge. 

1. No Visual Studio Code, selecione **Exibir** > **Paleta de comandos** para abrir a paleta de comandos do VS Code. 

1. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Na paleta de comandos, forneça as seguintes informações para criar sua solução: 

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução, como **CustomVisionSolution**, ou aceite o padrão. |
   | Selecionar modelo do módulo | Escolha **Módulo de Python**. |
   | Fornecer um nome de módulo | Nomeie seu módulo **classifier**.<br><br>É importante que esse nome de módulo esteja em letras minúsculas. O IoT Edge diferencia maiúsculas de minúsculas ao fazer referência a módulos, e essa solução usa uma biblioteca que formata todas as solicitações em letras minúsculas. |
   | Fornecer o repositório de imagem do Docker para o módulo | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner foi preenchida automaticamente na última etapa. Substitua **localhost:5000** pelo valor do **Servidor de logon** do seu Registro de Contêiner do Azure. Você pode recuperar o servidor de Logon da página Visão Geral do seu registro de contêiner no portal do Azure.<br><br>A cadeia de caracteres final se parece com **\<registry name\>.azurecr.io/classifier**. |
 
   ![Fornecer o repositório de imagem do Docker](./media/tutorial-deploy-custom-vision/repository.png)

A janela do Visual Studio Code carregará seu workspace da solução IoT Edge.

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O arquivo do ambiente armazena as credenciais para o registro de contêiner e as compartilha com o runtime do IoT Edge. O runtime precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge.

A extensão do IoT Edge tenta efetuar pull de suas credenciais de Registro de Contêiner do Azure e as popula no arquivo de ambiente. Verifique se suas credenciais já estão incluídas. Caso contrário, adicione-as agora:

1. No explorador do VS Code, abra o arquivo .env.
2. Atualize os campos com os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure.
3. Salve o arquivo.

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

No momento, o Visual Studio Code pode desenvolver módulos para os dispositivos Linux AMD64 e Linux ARM32v7. É necessário selecionar qual arquitetura você deseja usar para cada solução, porque o contêiner é criado e executado de maneira diferente para cada tipo de arquitetura. O padrão é o Linux AMD64, que será usado para este tutorial. 

1. Abra a paleta de comandos e pesquise **Azure IoT Edge: definir a plataforma de destino padrão para a solução Edge** ou selecione o ícone de atalho na barra lateral na parte inferior da janela. 

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manteremos o padrão **amd64**. 

### <a name="add-your-image-classifier"></a>Adicionar o classificador de imagem

O modelo de módulo de Python no Visual Studio Code contém um código de exemplo que você pode executar para testar o IoT Edge. Você não usará esse código neste cenário. Use as etapas nesta seção para substituir o código de exemplo pelo contêiner de classificador de imagem que você exportou anteriormente. 

1. No explorador de arquivos, navegue até o pacote da Visão Personalizada que você baixou e extraiu. Copie todo o conteúdo do pacote extraído. Ele deve incluir duas pastas, **app** e **azureml**, e dois arquivos, **Dockerfile** e **README**. 

2. No explorador de arquivos, navegue até o diretório onde o Visual Studio Code foi instruído a criar sua solução IoT Edge. 

3. Abra a pasta do módulo classificador. Se você usou os nomes sugeridos na seção anterior, a estrutura de pasta se parecerá com **CustomVisionSolution/modules/classifier**. 

4. Cole os arquivos na pasta **classifier**. 

5. Retorne para a janela do Visual Studio Code. O workspace da solução agora deve mostrar os arquivos do classificador de imagem na pasta do módulo. 

   ![Workspace da solução com arquivos do classificador de imagem](./media/tutorial-deploy-custom-vision/workspace.png)

6. Abra o arquivo **module.json** na pasta do classificador. 

7. Atualize o parâmetro **plataformas** para apontar para o novo Dockerfile que você adicionou e remova todas as opções além de AMD64, que é a única arquitetura que estamos usando para este tutorial. 

   ```json
   "platforms": {
       "amd64": "./Dockerfile"
   }
   ```

8. Salve suas alterações. 

### <a name="create-a-simulated-camera-module"></a>Criar um módulo de câmera simulada

Em uma implantação real da Visão Personalizada, você teria uma câmera para fornecer imagens ao vivo ou fluxos de vídeo. Neste cenário, você pode simular a câmera criando um módulo que envia uma imagem de teste para o classificador de imagem. 

#### <a name="add-and-configure-a-new-module"></a>Adicionar e configurar um novo módulo

Nesta seção, você adiciona um novo módulo ao mesmo CustomVisionSolution e fornece o código para criar a câmera simulada. 

1. Na janela do Visual Studio Code, use a paleta de comandos para executar **Azure IoT Edge: Adicionar módulo do IoT Edge**. Na paleta de comandos, forneça as seguintes informações para o novo módulo: 

   | Prompt | Valor | 
   | ------ | ----- |
   | Selecionar arquivo de modelo de implantação | Selecione o arquivo **deployment.template.json** na pasta CustomVisionSolution. |
   | Selecionar modelo do módulo | Selecione **Módulo de Python** |
   | Fornecer um nome de módulo | Nomeie seu módulo **cameraCapture** |
   | Fornecer o repositório de imagem do Docker para o módulo | Substitua **localhost:5000** pelo valor do **Servidor de logon** de seu registro de contêiner do Azure.<br><br>A cadeia de caracteres final se parece com **\<registryname\>.azurecr.io/cameracapture**. |

   A janela do VS Code carrega seu novo módulo no workspace da solução e atualiza o arquivo deployment.template.json. Agora você verá duas pastas de módulo: classifier e cameraCapture. 

2. Abra o arquivo **main.py** na pasta **modules** / **cameraCapture**. 

3. Substitua o aquivo inteiro pelo código a seguir. Esse código de exemplo envia solicitações POST para o serviço de processamento de imagens em execução no módulo do classificador. Nós fornecemos a esse contêiner de módulo uma imagem de exemplo para usar nas solicitações. Em seguida, ele empacota a resposta como uma mensagem do Hub IoT e a envia para uma fila de saída.  

    ```python
    # Copyright (c) Microsoft. All rights reserved.
    # Licensed under the MIT license. See LICENSE file in the project root for
    # full license information.

    import time
    import sys
    import os
    import requests
    import json
    from azure.iot.device import IoTHubModuleClient, Message

    # global counters
    SENT_IMAGES = 0

    # global client
    CLIENT = None

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = Message(bytearray(strMessage, 'utf8'))
        CLIENT.send_message_to_output(message, "output1")
        global SENT_IMAGES
        SENT_IMAGES += 1
        print( "Total images sent: {}".format(SENT_IMAGES) )

    # Send an image to the image classifying server
    # Return the JSON response from the server with the prediction result
    def sendFrameForProcessing(imagePath, imageProcessingEndpoint):
        headers = {'Content-Type': 'application/octet-stream'}

        with open(imagePath, mode="rb") as test_image:
            try:
                response = requests.post(imageProcessingEndpoint, headers = headers, data = test_image)
                print("Response from classification service: (" + str(response.status_code) + ") " + json.dumps(response.json()) + "\n")
            except Exception as e:
                print(e)
                print("No response from classification service")
                return None

        return json.dumps(response.json())

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global CLIENT
                CLIENT = IoTHubModuleClient.create_from_edge_environment()
            except Exception as iothub_error:
                print ( "Unexpected error {} from IoTHub".format(iothub_error) )
                return

            print ( "The sample is now sending images for processing and will indefinitely.")

            while True:
                classification = sendFrameForProcessing(imagePath, imageProcessingEndpoint)
                if classification:
                    send_to_hub(classification)
                time.sleep(10)

        except KeyboardInterrupt:
            print ( "IoT Edge module sample stopped" )

    if __name__ == '__main__':
        try:
            # Retrieve the image location and image classifying server endpoint from container environment
            IMAGE_PATH = os.getenv('IMAGE_PATH', "")
            IMAGE_PROCESSING_ENDPOINT = os.getenv('IMAGE_PROCESSING_ENDPOINT', "")
        except ValueError as error:
            print ( error )
            sys.exit(1)

        if ((IMAGE_PATH and IMAGE_PROCESSING_ENDPOINT) != ""):
            main(IMAGE_PATH, IMAGE_PROCESSING_ENDPOINT)
        else: 
            print ( "Error: Image path or image-processing endpoint missing" )
    ```

4. Salve o arquivo **main.py**. 

5. Abra o arquivo **requrements.txt**. 

6. Adicione uma nova linha para uma biblioteca a ser incluída no contêiner.

   ```Text
   requests
   ```

7. Salve o arquivo **requirements.txt** .


#### <a name="add-a-test-image-to-the-container"></a>Adicionar uma imagem de teste ao contêiner

Em vez de usar uma câmera real para fornecer uma imagem de feed para este cenário, vamos usar uma única imagem de teste. Uma imagem de teste está incluída no repositório GitHub que você baixou para as imagens de treinamento neste tutorial. 

1. Navegue até a imagem de teste, localizada em **Cognitive-CustomVision-Windows** / **Samples** / **Images** / **Test**. 

2. Copie **test_image.jpg** 

3. Navegue até o diretório da solução IoT Edge e cole a imagem de teste na pasta **modules** / **cameraCapture**. A imagem deve estar na mesma pasta do arquivo main.py que você editou na seção anterior. 

4. No Visual Studio Code, abra o arquivo **Dockerfile.amd64** para o módulo cameraCapture.

5. Após a linha que estabelece o diretório de trabalho, `WORKDIR /app`, adicione a seguinte linha de código:

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

6. Salve o Dockerfile.

### <a name="prepare-a-deployment-manifest"></a>Preparar um manifesto de implantação

Até agora, neste tutorial, você treinou um modelo da Visão Personalizada para classificar imagens de árvores e empacotou esse modelo como um módulo do IoT Edge. Em seguida, você criou um segundo módulo que pode consultar o servidor de classificação de imagens e relatar os resultados para o Hub IoT. Agora, você está pronto para criar o manifesto de implantação que informará a um dispositivo IoT Edge como iniciar e executar esses dois módulos juntos. 

A extensão do IoT Edge para Visual Studio Code fornece um modelo em cada solução IoT Edge para ajudá-lo a criar um manifesto de implantação. 

1. Abra o arquivo **deployment.template.json** na pasta da solução. 

2. Localize a seção **modules**, que deve conter três módulos: os dois que você criou, classifier e cameraCapture, e um terceiro que é incluído por padrão, SimulatedTemperatureSensor. 

3. Exclua o módulo **SimulatedTemperatureSensor** com todos os seus parâmetros. Esse módulo é incluído para fornecer dados de exemplo para cenários de teste, mas ele não é necessário nesta implantação. 

4. Se você não nomeou o módulo de classificação de imagem **classifier**, verifique agora se o nome está todo em minúsculas. O módulo cameraCapture chama o módulo classifier usando uma biblioteca de solicitações que formata todas as solicitações em letras minúsculas, e o IoT Edge diferencia maiúsculas de minúsculas. 

5. Atualize o parâmetro **createOptions** para o módulo cameraCapture com o JSON a seguir. Essas informações criam variáveis de ambiente no contêiner do módulo que são recuperadas no processo main.py. Ao incluir essas informações no manifesto de implantação, você poderá alterar a imagem ou ponto de extremidade sem ter que recompilar a imagem do módulo. 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    Se você não nomeou seu módulo de Visão Personalizada *classifier*, atualize o valor do ponto de extremidade de processamento de imagens para corresponder ao nome usado. 

6. Na parte inferior do arquivo, atualize o parâmetro **routes** para o módulo $edgeHub. Faça o roteamento dos resultados da previsão de cameraCapture para o Hub IoT.

    ```json
        "routes": {
          "cameraCaptureToIoTHub": "FROM /messages/modules/cameraCapture/outputs/* INTO $upstream"
        },
    ```

    Se você não nomeou o segundo módulo *cameraCapture*, atualize o valor da rota para corresponder ao nome usado. 

7. Salve o arquivo **deployment.template.json**.

## <a name="build-and-push-your-iot-edge-solution"></a>Compilar sua solução do IoT Edge e efetuar push dela

Com os dois módulos criados e o modelo do manifesto de implantação configurado, você está pronto para compilar as imagens de contêiner e enviá-las ao registro de contêiner.

Depois que as imagens estiverem em seu registro, você poderá implantar a solução em um dispositivo do IoT Edge. Você pode definir os módulos em um dispositivo por meio do Hub IoT, mas também pode acessar o Hub IoT e dispositivos por meio do Visual Studio Code. Nesta seção, você configura o acesso ao seu Hub IoT, depois usa o VS Code para implantar a solução ao seu dispositivo IoT Edge.

Primeiro, crie e efetue push da solução para o registro de contêiner.

1. Abra o terminal integrado do VS Code selecionando **Exibir** > **Terminal**.

2. Entre no Docker inserindo o seguinte comando no terminal. Entre com o nome de usuário, a senha e o servidor de logon do seu Registro de Contêiner do Azure. É possível recuperar esses valores na seção **Chaves de acesso** no registro do portal do Azure.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Talvez você receba um aviso de segurança recomendando usar `--password-stdin`. Embora essa prática seja recomendada para cenários de produção, ela não serve para este tutorial. Para saber mais, confira a referência de [logon do docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

3. No explorador do VS Code, clique com o botão direito do mouse no arquivo **deployment.template.json** e selecione **Compilar e enviar por push solução IoT Edge**.

   O comando de criação e de envio por push inicia três operações. Primeiro, é criada uma pasta na solução denominada **config** que contém o manifesto de implantação completo, criado com base nas informações do modelo de implantação e em outros arquivos da solução. Depois, ele executa `docker build` para montar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Por fim, ele executa `docker push` para enviar por push o repositório de imagens para seu registro de contêiner.

   Esse processo pode levar vários minutos na primeira vez, mas será mais rápido na próxima vez em que você executar os comandos.

## <a name="deploy-modules-to-device"></a>Implantar módulos no dispositivo

Use o gerenciador do Visual Studio Code e a extensão Azure IoT Tools para implantar o projeto de módulo em seu dispositivo IoT Edge. Você já tem um manifesto de implantação preparado para o seu cenário, o arquivo **deployment.amd64.json** na pasta config. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

Verifique se seu dispositivo IoT Edge está em funcionamento.

1. No gerenciador do Visual Studio Code, na seção **Hub IoT do Azure**, expanda **Dispositivos** para ver sua lista de dispositivos IoT.

2. Clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **Criar Implantação para Dispositivo Único**.

3. Selecione o arquivo **deployment.amd64.json** na pasta **config** e clique em **Selecionar Manifesto de Implantação do Edge**. Não use o arquivo deployment.template.json.

4. No dispositivo, expanda **Módulos** para ver uma lista de módulos implantados e em execução. Clique no botão Atualizar. Você deve ver os novos módulos **classifier** e **cameraCapture** sendo executados em conjunto com **$edgeAgent** e **$edgeHub**.  

Você também pode verificar se todos os módulos estão em execução no próprio dispositivo. No seu dispositivo IoT Edge, execute o comando a seguir para ver o status dos módulos.

   ```bash
   iotedge list
   ```

Pode levar alguns minutos para que os módulos sejam iniciados. O runtime do IoT Edge precisa receber seu novo manifesto de implantação, obter as imagens de módulo do runtime do contêiner e iniciar cada novo módulo.

## <a name="view-classification-results"></a>Exibir resultados de classificação

Há duas maneiras de exibir os resultados de seus módulos, seja no próprio dispositivo, conforme as mensagens são geradas e enviadas, ou no Visual Studio Code, conforme as mensagens vão chegando ao Hub IoT. 

Em seu dispositivo, exiba os logs do módulo cameraCapture para ver as mensagens sendo enviadas e a confirmação de que elas foram recebidas pelo Hub IoT. 

   ```bash
   iotedge logs cameraCapture
   ```

No Visual Studio Code, clique com o botão direito no nome do dispositivo do IoT Edge e selecione **Iniciar o Monitoramento de Ponto de Extremidade Interno**. 

> [!NOTE]
> Inicialmente, talvez você veja erros de conexão na saída do módulo cameraCapture. Isso ocorre devido ao atraso entre a implantação e a inicialização dos módulos.
>
> O módulo cameraCapture tenta estabelecer a conexão automaticamente mais uma vez até obter êxito. Após uma conexão bem-sucedida, você verá as mensagens de classificação de imagem esperadas descritas abaixo.

Os resultados do módulo da Visão Personalizada, que são enviados como mensagens do módulo cameraCapture, incluem a probabilidade de a imagem ser uma cicuta (hemlock) ou cerejeira (cherry tree). Como a imagem é uma cicuta, você deve ver a probabilidade como 1.0.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, é possível excluir as configurações locais e os recursos do Azure usados neste artigo para evitar encargos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você treinou um modelo da Visão Personalizada e o implantou como um módulo em um dispositivo do IoT Edge. Em seguida, você criou um módulo que pode consultar o serviço de classificação de imagem e relatar os resultados para o Hub IoT.

Siga para os próximos tutoriais para saber mais sobre outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Armazenar dados na borda com os bancos de dados do SQL Server](tutorial-store-data-sql-server.md)