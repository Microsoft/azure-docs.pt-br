---
title: Tutorial para desenvolver um módulo do Node.js para Linux – Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código em Node.js e implantá-lo em um dispositivo de borda
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 77e7edfba94c7b428e59dadfd9f0824608941d7a
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223244"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-using-linux-containers"></a>Tutorial: Desenvolver e implantar um módulo do IoT Edge em Node.js usando contêineres Linux

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Use o Visual Studio Code para desenvolver código em Node.js e implantá-lo em um dispositivo que executa o Azure IoT Edge.

Use os módulos do IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o orienta através da criação e implantação de um módulo IoT Edge que filtra os dados do sensor. Você usa o dispositivo IoT Edge que criou nos inícios rápidos. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Use o Visual Studio Code para criar um módulo Node.js do IoT Edge
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no registro
> * Implantar o módulo no dispositivo IoT Edge
> * Exibir os dados gerados

O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ele somente envia mensagens upstream se a temperatura estiver acima de um limite especificado. Este tipo de análise na borda é útil para reduzir a quantidade de dados comunicados e armazenados na nuvem.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial demonstra como desenvolver um módulo em **Node.js** usando o **Visual Studio Code** e como implantá-lo em um dispositivo IoT Edge.

O IoT Edge não dá suporte a módulos do Node.js usando contêineres Windows.

Use a tabela a seguir para entender suas opções para desenvolver e implantar módulos do Node.js:

| Node.js | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Use o VS Code para módulos do Node.js no Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Use o VS Code para módulos do Node.js no Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

Antes de iniciar este tutorial, você deve ter percorrido o tutorial anterior para configurar o ambiente de desenvolvimento para o desenvolvimento de contêiner Linux: [Desenvolver módulos do IoT Edge usando contêineres Linux](tutorial-develop-for-linux.md). Ao concluir esse tutorial, você deve ter os seguintes pré-requisitos implementados:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure.
* Um dispositivo que executa o Azure IoT Edge. Você pode usar os inícios rápidos para configurar um [dispositivo Linux](quickstart-linux.md) ou um [dispositivo Windows](quickstart.md).
* Um registro de contêiner, como o [Registro de Contêiner do Azure](../container-registry/index.yml).
* O [Visual Studio Code](https://code.visualstudio.com/) configurado com o [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* O [Docker CE](https://docs.docker.com/install/) configurado para executar contêineres do Linux.

Para desenvolver um módulo do IoT Edge em Node.js, instale os seguintes pré-requisitos adicionais em seu computador de desenvolvimento:

* [Node.js e npm](https://nodejs.org). O pacote do npm é distribuído com Node.js, o que significa que quando você baixa Node.js, o npm é automaticamente instalado em seu computador.

## <a name="create-a-module-project"></a>Criar um projeto de módulo

As etapas a seguir mostram como criar um módulo Node.js do IoT Edge usando o Visual Studio Code e as Ferramentas de IoT do Azure.

### <a name="create-a-new-project"></a>Criar um novo projeto

Use **npm** para criar um modelo de solução em Node.js para usar de base.

1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado** para abrir o terminal integrado do Visual Studio Code.

2. No terminal integrado, digite o seguinte comando para instalar o **yeoman** e o gerador para o módulo Node.js do Azure IoT Edge:

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Selecione **Exibir** > **Paleta de comandos** para abrir a paleta de comandos do VS Code.

4. Na paleta de comandos, digite e execute o comando **Azure: Entrar** e siga as instruções para entrar na conta do Azure. Se já tiver entrado, pode ignorar esta etapa.

5. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Siga os prompts na paleta de comandos para criar sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha **Módulo Node.js**. |
   | Fornecer um nome de módulo | Nomeie seu módulo **NodeModule**. |
   | Fornecer o repositório de imagem do Docker para o módulo | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner é preenchida previamente o nome fornecido na última etapa. Substitua **localhost:5000** pelo valor do **Servidor de logon** do seu Registro de Contêiner do Azure. Você pode recuperar o servidor de Logon da página Visão Geral do seu registro de contêiner no portal do Azure. <br><br>O repositório de imagens final se parece com \<registry name\>.azurecr.io/nodemodule. |

   ![Fornecer o repositório de imagem do Docker](./media/tutorial-node-module/repository.png)

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O arquivo do ambiente armazena as credenciais para o repositório de contêiner e as compartilha com o runtime do IoT Edge. O runtime precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge.

A extensão do IoT Edge tenta efetuar pull de suas credenciais de Registro de Contêiner do Azure e as popula no arquivo de ambiente. Verifique se suas credenciais já estão incluídas. Caso contrário, adicione-as agora:

1. No explorador do VS Code, abra o arquivo **.env**.
2. Atualize os campos com os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure.
3. Salve o arquivo.

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

No momento, o Visual Studio Code pode desenvolver módulos do Node.js para dispositivos Linux AMD64 e Linux ARM32v7. É necessário selecionar qual arquitetura você deseja usar para cada solução, porque o contêiner é criado e executado de maneira diferente para cada tipo de arquitetura. O padrão é o Linux AMD64.

1. Abra a paleta de comandos e pesquise **Azure IoT Edge: definir a plataforma de destino padrão para a solução Edge** ou selecione o ícone de atalho na barra lateral na parte inferior da janela.

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manteremos o padrão **amd64**.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

Cada modelo é fornecido com código de exemplo incluído, que usa dados de sensor simulados do módulo **SimulatedTemperatureSensor** e os encaminha para o Hub IoT. Nesta seção, adicione código para fazer o NodeModule analisar as mensagens antes de enviá-las.

1. No explorador do VS Code, abra **modules** > **NodeModule** > **app.js**.

2. Adicione uma variável de limite de temperatura abaixo dos módulos de nó obrigatórios. O limite de temperatura define o valor que a temperatura medida deve exceder para que os dados sejam enviados para o Hub IoT.

    ```javascript
    var temperatureThreshold = 25;
    ```

3. Substitua toda a função `PipeMessage` pela função `FilterMessage`.

    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

4. Substitua o nome da função `pipeMessage` por `filterMessage` na função `client.on()`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

5. Copie o snippet de código a seguir para o retorno de chamada da função `client.open()`, após `client.on()` dentro da instrução `else`. Essa função é invocada quando as propriedades desejadas são atualizadas.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

6. Salve o arquivo app.js.

7. No gerenciador do VS Code, abra o arquivo **deployment.template.json** no workspace da solução IoT Edge.

8. Adicione o módulo gêmeo NodeModule no manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção `moduleContent`, após o módulo gêmeo do `$edgeHub`:

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Adicionar gêmeo de módulo ao modelo de implantação](./media/tutorial-node-module/module-twin.png)

9. Salve o arquivo deployment.template.json.

## <a name="build-and-push-your-module"></a>Criar e enviar seu módulo por push

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a NodeModule que filtrará mensagens em que a temperatura relatada do computador estiver dentro dos limites aceitáveis. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner.

1. Abra o terminal integrado do VS Code selecionando **Exibir** > **Terminal**.

2. Entre no Docker inserindo o seguinte comando no terminal. Entre com o nome de usuário, a senha e o servidor de logon do seu Registro de Contêiner do Azure. É possível recuperar esses valores na seção **Chaves de acesso** no registro do portal do Azure.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Talvez você receba um aviso de segurança recomendando usar `--password-stdin`. Embora essa prática seja recomendada para cenários de produção, ela não serve para este tutorial. Para saber mais, confira a referência de [logon do docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

3. No gerenciador do VS Code, clique com o botão direito do mouse no arquivo **deployment.template.json** e selecione **Criar e Efetuar Push da Solução IoT Edge**.

   O comando de criação e de envio por push inicia três operações. Primeiro, é criada uma pasta na solução denominada **config** que contém o manifesto de implantação completo, criado com base nas informações do modelo de implantação e em outros arquivos da solução. Depois, ele executa `docker build` para montar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Por fim, ele executa `docker push` para enviar por push o repositório de imagens para seu registro de contêiner.

   Esse processo pode levar vários minutos na primeira vez, mas será mais rápido na próxima vez em que você executar os comandos.

## <a name="deploy-modules-to-device"></a>Implantar módulos no dispositivo

Use o gerenciador do Visual Studio Code e a extensão Azure IoT Tools para implantar o projeto de módulo em seu dispositivo IoT Edge. Você já tem um manifesto de implantação preparado para o seu cenário, o arquivo **deployment.amd64.json** na pasta config. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

Verifique se seu dispositivo IoT Edge está em funcionamento.

1. No gerenciador do Visual Studio Code, na seção **Hub IoT do Azure**, expanda **Dispositivos** para ver sua lista de dispositivos IoT.

2. Clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **Criar Implantação para Dispositivo Único**.

3. Selecione o arquivo **deployment.amd64.json** na pasta **config** e clique em **Selecionar Manifesto de Implantação do Edge**. Não use o arquivo deployment.template.json.

4. No dispositivo, expanda **Módulos** para ver uma lista de módulos implantados e em execução. Clique no botão Atualizar. Você deve ver o novo **NodeModule** sendo executado junto com o módulo **SimulatedTemperatureSensor** e **$edgeAgent** e **$edgeHub**.

    Pode levar alguns minutos para que os módulos sejam iniciados. O runtime do IoT Edge precisa receber seu novo manifesto de implantação, obter as imagens de módulo do runtime do contêiner e iniciar cada novo módulo.

## <a name="view-the-generated-data"></a>Exibir os dados gerados

Depois que você aplica o manifesto de implantação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo coleta as novas informações de implantação e inicia a execução nele. Todos os módulos em execução no dispositivo que não estão incluídos no manifesto de implantação são interrompidos. Todos os módulos ausentes do dispositivo são iniciados.

Você pode ver o status do seu dispositivo IoT Edge usando a seção **Dispositivos de Hub IoT do Azure** do explorer do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implantados e em execução.

1. No gerenciador do Visual Studio Code, clique com o botão direito do mouse no nome do dispositivo IoT Edge e escolha **Iniciar o monitoramento de pontos de extremidade internos de eventos**.

2. Exiba as mensagens que chegam ao seu Hub IoT. Pode levar algum tempo para que as mensagens cheguem. O dispositivo do IoT Edge precisa receber a nova implantação dele e iniciar todos os módulos. Em seguida, as alterações que fizemos no código NodeModule aguardam até que a temperatura do computador atinja 25 graus antes de enviar mensagens. Também é adicionado o tipo de mensagem **Alerta** às mensagens que atingem esse limite de temperatura.

## <a name="edit-the-module-twin"></a>Editar o módulo gêmeo

Usamos o módulo gêmeo NodeModule no manifesto de implantação para definir o limite de temperatura em 25 graus. É possível usar o módulo gêmeo para alterar a funcionalidade sem precisar atualizar o código do módulo.

1. No Visual Studio Code, expanda os detalhes em seu dispositivo IoT Edge para ver os módulos em execução.

2. Clique com o botão direito do mouse em **NodeModule** e selecione **Editar módulo gêmeo**.

3. Encontre **TemperatureThreshold** nas propriedades desejadas. Altere o valor para uma temperatura 5 a 10 graus maior do que a temperatura relatada mais recente.

4. Salve o arquivo de módulo gêmeo.

5. Clique com o botão direito do mouse em qualquer lugar no painel de edição do módulo gêmeo e selecione **Atualizar módulo gêmeo**.

6. Monitore as mensagens do dispositivo para nuvem recebidas. Você deve ver uma interrupção das mensagens até que o novo limite de temperatura seja atingido.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge que contém código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge.

É possível passar para os próximos tutoriais para saber como o Azure IoT Edge pode ajudar você a implantar os serviços de nuvem do Azure para processar e analisar dados na borda.

> [!div class="nextstepaction"]
> [Funções](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)