---
title: Tutorial – Tutorial do módulo Java personalizado usando o Azure IoT Edge
description: Este tutorial mostra como criar um módulo do IoT Edge com código em Java e implantá-lo em um dispositivo de borda.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom:
- mvc
- mqtt
- devx-track-java
ms.openlocfilehash: f5f931ce1aa6c06f151241b6136470d494dbcd48
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218746"
---
# <a name="tutorial-develop-a-java-iot-edge-module-using-linux-containers"></a>Tutorial: desenvolver um módulo do IoT Edge em Java usando contêineres Linux

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Use os módulos do Azure IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o orienta através da criação e implantação de um módulo IoT Edge que filtra os dados do sensor. Você usará o dispositivo do IoT Edge simulado que foi criado em Implantar Azure IoT Edge em um dispositivo simulado nos artigos de início rápido. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Use o Visual Studio Code para criar um módulo do IoT Edge Java com base no pacote de modelo maven do Azure IoT Edge e SDK do dispositivo Java do Azure IoT.
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no registro.
> * Implantar o módulo no dispositivo IoT Edge.
> * Exibir os dados gerados.

O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ele somente envia mensagens upstream se a temperatura estiver acima de um limite especificado. Esse tipo de análise na borda é útil para reduzir a quantidade de dados que é comunicada e armazenada na nuvem.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial demonstra como desenvolver um módulo em **Java** usando o **Visual Studio Code** e como implantá-lo em um dispositivo do IoT Edge. O IoT Edge não dá suporte a módulos Java criados como contêineres do Windows.

Use a tabela a seguir para entender suas opções para desenvolver e implantar módulos do Java:

| Java | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Use o VS Code para módulos do Java no Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Use o VS Code para módulos do Java no Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

Antes de iniciar este tutorial, você deve ter passado pelo tutorial anterior para configurar seu ambiente de desenvolvimento para o desenvolvimento de contêiner do Linux: [Desenvolver módulos do IoT Edge para dispositivos Linux](tutorial-develop-for-linux.md). Ao concluir qualquer um desses tutoriais, você deve ter os seguintes pré-requisitos implementados:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure.
* Um dispositivo que executa o Azure IoT Edge com contêineres Linux. Você pode usar os inícios rápidos para configurar um [dispositivo Linux](quickstart-linux.md) ou um [dispositivo Windows](quickstart.md).
* Um registro de contêiner, como o [Registro de Contêiner do Azure](../container-registry/index.yml).
* O [Visual Studio Code](https://code.visualstudio.com/) configurado com o [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* O [Docker CE](https://docs.docker.com/install/) configurado para executar contêineres do Linux.

Para desenvolver um módulo do IoT Edge em Java, instale os seguintes pré-requisitos adicionais em seu computador de desenvolvimento:

* [Pacote de extensão Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) para Visual Studio Code.
* [Java SE Development Kit 11](/azure/developer/java/fundamentals/java-jdk-long-term-support) e [defina a `JAVA_HOME`variável de ambiente](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) para apontar para a sua instalação do JDK.
* [Maven](https://maven.apache.org/)

   >[!TIP]
   >Os processos de instalação do Java e do Maven adicionam variáveis de ambiente ao seu sistema. Reinicie as instâncias abertas do terminal do Visual Studio Code, do PowerShell ou do prompt de comando depois de concluir a instalação. Essa etapa garante que esses utilitários possam reconhecer os comandos do Java e do Maven no futuro.

## <a name="create-a-module-project"></a>Criar um projeto de módulo

As seguintes etapas criam um projeto de módulo do IoT Edge que se baseia no pacote de modelo do maven do Azure IoT Edge e no SDK do dispositivo Java do Azure IoT. Você criará o projeto usando o Visual Studio Code e as Ferramentas de IoT do Azure.

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução em Java que possa ser personalizado com seu próprio código.

1. No Visual Studio Code, selecione **Exibir** > **Paleta de comandos** para abrir a paleta de comandos do VS Code.

2. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Siga os prompts na paleta de comandos para criar sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha o **Módulo Java**. |
   | Fornecer um nome de módulo | Nomeie o módulo como **JavaModule**. |
   | Fornecer o repositório de imagem do Docker para o módulo | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner é preenchida previamente o nome fornecido na última etapa. Substitua **localhost:5000** pelo valor do **Servidor de logon** do seu Registro de Contêiner do Azure. Você pode recuperar o servidor de Logon da página Visão Geral do seu registro de contêiner no portal do Azure. <br><br>O repositório de imagens final se parece com \<registry name\>.azurecr.io/javamodule. |
   | Fornecer o valor para a ID do grupo | Insira um valor para a ID do grupo ou aceite o padrão **com.edgemodule**. |

   ![Fornecer o repositório de imagem do Docker](./media/tutorial-java-module/repository.png)

Se for sua primeira vez para criar o módulo do Java, poderão ser necessários vários minutos para baixar os pacotes do maven. Quando a solução está pronta, a janela do VS Code carrega seu workspace da solução do IoT Edge. O workspace da solução contém cinco componentes de nível superior:

* A pasta **módulos** contém o código Java do seu módulo e os arquivos do Docker para a criar seu módulo como uma imagem de contêiner.
* O arquivo **\.env** armazena suas credenciais de registro de contêiner.
* O arquivo **deployment.template.json** contém as informações que o runtime do IoT Edge usa para implantar módulos em um dispositivo.
* O arquivo **deployment.debug.template.json** contém a versão de depuração de módulos.
* Você não editará a pasta **\.vscode** ou o arquivo **\.gitignore** neste tutorial.

Se você não especifica um registro de contêiner durante a criação de sua solução, mas aceita o valor de localhost:5000, você não terá um arquivo \.env.

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O arquivo do ambiente armazena as credenciais para o registro de contêiner e as compartilha com o runtime do IoT Edge. O runtime precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge.

A extensão do IoT Edge tenta efetuar pull de suas credenciais de Registro de Contêiner do Azure e as popula no arquivo de ambiente. Verifique se suas credenciais já estão incluídas. Caso contrário, adicione-as agora:

1. No explorador do VS Code, abra o arquivo .env.
2. Atualize os campos com os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure.
3. Salve o arquivo.

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

No momento, o Visual Studio Code pode desenvolver módulos em Java para os dispositivos Linux AMD64 e Linux ARM32v7. É necessário selecionar qual arquitetura você deseja usar para cada solução, porque o contêiner é criado e executado de maneira diferente para cada tipo de arquitetura. O padrão é o Linux AMD64.

1. Abra a paleta de comandos e pesquise **Azure IoT Edge: definir a plataforma de destino padrão para a solução Edge** ou selecione o ícone de atalho na barra lateral na parte inferior da janela.

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manteremos o padrão **amd64**.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

1. No explorador de VS Code, abra **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

2. Adicione o seguinte código na parte superior do arquivo para importar novas classes referenciadas.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;

    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;

    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

3. Adicione a seguinte definição à classe **Aplicativo**. Essa variável define um limite de temperatura. A temperatura medida do computador não será relatada para o Hub IoT até que ultrapasse esse valor.

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. Substitua o método execute de **MessageCallbackMqtt** pelo código a seguir. Esse método é chamado sempre que o módulo recebe uma mensagem MQTT do hub do IoT Edge. Ele filtra as mensagens que reportam temperaturas abaixo do limite de temperatura definido através do módulo gêmeo.

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
        private int counter = 0;
        @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;

            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    }
    ```

5. Adicione as duas seguintes classes internas estáticas à classe **Aplicativo**. Essas classes atualizam a variável de tempThreshold quando a propriedade desejada do gêmeo do módulo muda. Todos os módulos possuem seu próprio módulo gêmeo, o que permite configurar o código em execução dentro de um módulo diretamente da nuvem.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }

    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

6. Adicione as seguintes linhas ao método **main** após **client.open()** para assinar as atualizações duplas de módulo.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

7. Salve o arquivo App.java.

8. No gerenciador do VS Code, abra o arquivo **deployment.template.json** no workspace da solução IoT Edge.

9. Adicione o módulo duplo **JavaModule** ao manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção **moduleContent**, após o módulo gêmeo do **$edgeHub**:

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Adicionar gêmeo de módulo ao modelo de implantação](./media/tutorial-java-module/module-twin.png)

10. Salve o arquivo deployment.template.json.

## <a name="build-and-push-your-module"></a>Criar e enviar seu módulo por push

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a **JavaModule** para filtrar mensagens em que a temperatura relatada do computador estiver abaixo do limite aceitável. Agora, crie a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner.

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

4. No dispositivo, expanda **Módulos** para ver uma lista de módulos implantados e em execução. Clique no botão Atualizar. Você deve ver o novo **JavaModule** sendo executado junto com o módulo **SimulatedTemperatureSensor** e **$edgeAgent** e **$edgeHub**.  

    Pode levar alguns minutos para que os módulos sejam iniciados. O runtime do IoT Edge precisa receber seu novo manifesto de implantação, obter as imagens de módulo do runtime do contêiner e iniciar cada novo módulo.

## <a name="view-the-generated-data"></a>Exibir os dados gerados

Depois que você aplica o manifesto de implantação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo coleta as novas informações de implantação e inicia a execução nele. Todos os módulos em execução no dispositivo que não estão incluídos no manifesto de implantação são interrompidos. Todos os módulos ausentes do dispositivo são iniciados.

1. No gerenciador do Visual Studio Code, clique com o botão direito do mouse no nome do dispositivo IoT Edge e escolha **Iniciar o monitoramento de pontos de extremidade internos de eventos**.

2. Exiba as mensagens que chegam ao seu Hub IoT. Pode levar algum tempo para que as mensagens cheguem. O dispositivo do IoT Edge precisa receber a nova implantação dele e iniciar todos os módulos. Em seguida, as alterações que fizemos no código JavaModule aguardam até que a temperatura do computador atinja 25 graus antes de enviar as mensagens. Também é adicionado o tipo de mensagem **Alerta** às mensagens que atingem esse limite de temperatura.

## <a name="edit-the-module-twin"></a>Editar o módulo gêmeo

Usamos o módulo gêmeo JavaModule no manifesto de implantação para definir o limite de temperatura em 25 graus. É possível usar o módulo gêmeo para alterar a funcionalidade sem precisar atualizar o código do módulo.

1. No Visual Studio Code, expanda os detalhes em seu dispositivo IoT Edge para ver os módulos em execução.

2. Clique com o botão direito do mouse em **JavaModule** e selecione **Editar Módulo Gêmeo**.

3. Encontre **TemperatureThreshold** nas propriedades desejadas. Altere o valor para uma temperatura 5 a 10 graus maior do que a temperatura relatada mais recente.

4. Salve o arquivo de módulo gêmeo.

5. Clique com o botão direito do mouse em qualquer lugar no painel de edição do módulo gêmeo e selecione **Atualizar módulo gêmeo**.

6. Monitore as mensagens do dispositivo para nuvem recebidas. Você deve ver uma interrupção das mensagens até que o novo limite de temperatura seja atingido.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge que filtra dados brutos gerados pelo seu dispositivo do IoT Edge.

Passe para os próximos tutoriais para saber como o Azure IoT Edge ajuda a implantar os Serviços de Nuvem do Azure para processar e analisar dados na borda.

> [!div class="nextstepaction"]
> [Funções](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)
