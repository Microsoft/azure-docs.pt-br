---
title: Visualização de dados em tempo real dos dados do hub IoT em um aplicativo Web
description: Use o aplicativo Web para visualizar dados de temperatura e umidade que são coletados de um sensor e enviados ao hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 53b5add7526b0c20487e8fe3adb0b8ebe207a2ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200045"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualizar dados de sensor em tempo real de seu hub IoT do Azure em um aplicativo Web

![Diagrama de ponta a ponta](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que você aprenderá

Neste tutorial, você aprenderá como visualizar dados de sensor em tempo real que o seu hub IoT recebe com a execução de um aplicativo Web node.js em seu computador local. Depois de executar o aplicativo Web localmente, você poderá, opcionalmente, seguir as etapas para hospedar o aplicativo Web no Serviço de Aplicativo do Azure. Se você quiser tentar visualizar os dados em seu Hub IoT usando o Power BI, consulte [Usar o Power BI para visualizar dados de sensor em tempo real do Hub IoT do Azure](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>O que fazer

* Adicionar um grupo de consumidores ao hub IoT que o aplicativo Web usará para ler os dados do sensor
* Baixar o código do aplicativo Web do GitHub
* Examinar o código do aplicativo Web
* Configurar variáveis de ambiente para manter os artefatos do Hub IoT necessários para seu aplicativo Web
* Executar o aplicativo Web em seu computador de desenvolvimento
* Abrir a página da Web para ver os dados de temperatura e umidade em tempo real de seu Hub IoT
* (Opcional) Usar a CLI do Azure para hospedar seu aplicativo Web no Serviço de Aplicativo do Azure

## <a name="what-you-need"></a>O que você precisa

* Conclua o tutorial do [simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com Node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Eles cobrem os seguintes requisitos:

  * Uma assinatura ativa do Azure
  * Um Hub IoT em sua assinatura
  * Um aplicativo cliente que envia mensagens para o seu Hub IoT

* [Baixar Git](https://www.git-scm.com/downloads)

* As etapas neste artigo pressupõem um computador de desenvolvimento do Windows. No entanto, você pode facilmente executar essas etapas em um sistema Linux em seu shell preferencial.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao hub IoT

Os [grupos de consumidores](../event-hubs/event-hubs-features.md#event-consumers) fornecem exibições independentes no fluxo de eventos que permitem que aplicativos e serviços do Azure consumam dados de modo independente do mesmo ponto de extremidade do Hub de Eventos. Nesta seção, você adiciona um grupo de consumidores ao ponto de extremidade interno do hub IoT que o aplicativo Web usará para ler dados.

Execute o seguinte comando para adicionar um grupo de consumidores ao ponto de extremidade interno do hub IoT:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Anote o nome que você escolher, você precisará dele mais tarde neste tutorial.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Obter uma cadeia de conexão de serviço para seu hub IoT

Os hubs IoT são criados com várias políticas de acesso padrão. Uma dessas políticas é a política de **serviço**, que fornece permissões suficientes para um serviço ler e gravar os pontos de extremidade do hub IoT. Execute o seguinte comando para obter uma cadeia de conexão para o hub IoT que segue a política de serviço:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

A cadeia de conexão deve ser semelhante à seguinte:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Anote a cadeia de conexão de serviço, você precisará dela mais tarde neste tutorial.

## <a name="download-the-web-app-from-github"></a>Baixar o aplicativo Web do GitHub

Abra uma janela Comando e insira os seguintes comandos para baixar o exemplo do GitHub e alterar para o diretório de exemplo:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Examinar o código do aplicativo Web

No diretório web-apps-node-iot-hub-data-visualization, abra o aplicativo Web em seu editor favorito. Abaixo é possível ver a estrutura de arquivos exibida no VS Code:

![Estrutura de arquivos do aplicativo Web](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Reserve um tempo para examinar os seguintes arquivos:

* O **Server.js** é um script de serviço que inicializa o WebSocket e a classe wrapper do Hub de Eventos. Ele fornece um retorno de chamada para a classe wrapper do Hub de Eventos que a classe usa para difundir mensagens de entrada para o WebSocket.

* **Event-hub-reader.js** é um script do lado do serviço que se conecta ao ponto de extremidade interno do hub IoT usando a cadeia de conexão e o grupo de consumidores especificados. Ele extrai o DeviceId e o EnqueuedTimeUtc de metadados em mensagens de entrada e, em seguida, retransmite a mensagem usando o método de retorno de chamada registrado por server.js.

* O **Chart-device-data.js** é um script do cliente que escuta no WebSocket, controla cada DeviceId e armazena os últimos 50 pontos de dados de entrada para cada dispositivo. Em seguida, ele associa os dados do dispositivo selecionado ao objeto de gráfico.

* **Index.html** manipula o layout da interface do usuário para a página da Web e faz referência aos scripts necessários para a lógica no lado do cliente.

## <a name="configure-environment-variables-for-the-web-app"></a>Configurar variáveis de ambiente para o aplicativo Web

Para ler dados de seu hub IoT, o aplicativo Web precisa da cadeia de conexão do seu hub IoT e o nome do grupo de consumidores que ele deve ler. Ele obtém essas cadeias de caracteres do ambiente de processo nas seguintes linhas no server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Defina as variáveis de ambiente na janela Comando com os comandos a seguir. Substitua os valores de espaço reservado pela cadeia de conexão de serviço para o hub IoT e o nome do grupo de consumidores criado anteriormente. Não coloque as cadeias de caracteres entre aspas.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Executar o aplicativo Web

1. Verifique se o dispositivo está executando e enviando dados.

2. Na janela Comando, execute as seguintes linhas para baixar e instalar pacotes referenciados e iniciar o site:

   ```cmd
   npm install
   npm start
   ```

3. Você deve ver a saída no console, que indica que o aplicativo Web foi conectado com êxito ao hub IoT e está escutando na porta 3000:

   ![Aplicativo Web iniciado no console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Abrir uma página da Web para ver os dados do hub IoT

Abra um navegador para `http://localhost:3000`.

Na lista **Selecionar um dispositivo**, selecione o dispositivo para ver um gráfico em execução dos últimos 50 pontos de dados de temperatura e umidade enviados pelo dispositivo para o hub IoT.

![Página de aplicativo Web mostrando a umidade e a temperatura em tempo real](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Você também deve ver a saída no console, que mostra as mensagens que seu aplicativo Web está difundindo para o cliente de navegador:  

![Saída de difusão do aplicativo Web no console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Hospedar o aplicativo Web no Serviço de Aplicativo

O [recurso Aplicativos Web do Serviço de Aplicativo do Azure](../app-service/overview.md) fornece uma PAAS (plataforma como serviço) para hospedar aplicativos Web. Os aplicativos Web hospedados no Serviço de Aplicativo do Azure podem se beneficiar de recursos avançados do Azure, como segurança adicional, balanceamento de carga e escalabilidade, bem como soluções de DevOps do Azure e de parceiros, como implantação contínua, gerenciamento de pacotes e assim por diante. O Serviço de Aplicativo do Azure é compatível com aplicativos Web desenvolvidos em muitas linguagens populares e implantados na infraestrutura do Windows ou do Linux.

Nesta seção, você provisiona um aplicativo Web no Serviço de Aplicativo e implanta seu código nele usando os comandos da CLI do Azure. Você pode encontrar detalhes dos comandos usados na documentação [az webapp](/cli/azure/webapp). Antes de começar, verifique se você concluiu as etapas para [adicionar um grupo de recursos ao seu hub IoT](#add-a-consumer-group-to-your-iot-hub), [obter uma cadeia de conexão de serviço para seu hub IoT](#get-a-service-connection-string-for-your-iot-hub) e [baixar o aplicativo Web do GitHub](#download-the-web-app-from-github).

1. Um [plano de Serviço de Aplicativo](../app-service/overview-hosting-plans.md) define um conjunto de recursos de computação para um aplicativo hospedado no Serviço de Aplicativo ser executado. Neste tutorial, usamos a Camada de desenvolvedor/gratuita para hospedar o aplicativo Web. Com a Camada gratuita, seu aplicativo Web é executado em recursos compartilhados do Windows com outros aplicativos do Serviço de Aplicativo, incluindo aplicativos de outros clientes. O Azure também oferece Planos do Serviço de Aplicativo para implantar aplicativos Web em recursos de computação do Linux. Caso já tenha um Plano do Serviço de Aplicativo que deseje usar, ignore esta etapa.

   Para criar um Plano do Serviço de Aplicativo usando a camada gratuita do Windows, execute o comando a seguir. Use o mesmo grupo de recursos no qual seu hub IoT está. O nome do plano do serviço pode conter letras maiúsculas e minúsculas, números e hifens.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Agora, provisione um aplicativo Web em seu Plano do Serviço de Aplicativo. O parâmetro `--deployment-local-git` permite que o código do aplicativo Web seja carregado e implantado de um repositório Git em seu computador local. O nome do aplicativo Web deve ser globalmente exclusivo e pode conter letras maiúsculas e minúsculas, números e hifens. Especifique o Node versão 10.6 ou posterior para o parâmetro `--runtime`, dependendo da versão do runtime do Node.js que você está usando. Você pode usar o comando `az webapp list-runtimes` para obter uma lista de runtimes com suporte.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Agora, adicione configurações de aplicativo para as variáveis de ambiente que especificam a cadeia de conexão do hub IoT e o grupo de consumidores do hub de eventos. As configurações individuais são delimitadas por espaço no parâmetro `-settings`. Use a cadeia de conexão de serviço para o hub IoT e o grupo de consumidores criado anteriormente neste tutorial. Não coloque os valores entre aspas.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. Habilite o protocolo WebSocket para o aplicativo Web e defina o aplicativo Web para receber somente solicitações HTTPS (as solicitações HTTP são redirecionadas para HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Para implantar o código no Serviço de Aplicativo, você usará suas [credenciais de implantação no nível de usuário](../app-service/deploy-configure-credentials.md). Suas credenciais de implantação no nível do usuário são diferentes das suas credenciais do Azure e são usadas para implantações locais e FTP do Git em um aplicativo Web. Uma vez definidas, elas são válidas em todos os aplicativos do Serviço de Aplicativo em todas as assinaturas em sua conta do Azure. Se você tiver definido anteriormente as credenciais de implantação no nível do usuário, poderá usá-las.

   Se você não definiu anteriormente as credenciais de implantação no nível do usuário ou não se lembra de sua senha, execute o comando a seguir. Seu nome de usuário de implantação deve ser exclusivo no Azure e não deve conter o \@ símbolo ' ' para Pushes git locais. Quando for solicitado, insira e confirme sua nova senha. A senha deve ter pelo menos oito caracteres, com dois destes três elementos: letras, números, símbolos.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Obtenha a URL do Git a ser usada para enviar por push seu código para o Serviço de Aplicativo.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Adicione um remoto ao clone que referencie o repositório Git para o aplicativo Web no Serviço de Aplicativo. Para \<Git clone URL\> , use a URL retornada na etapa anterior. Execute o seguinte comando na janela Comando.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Para implantar o código no Serviço de Aplicativo, insira o comando a seguir na janela Comando. Se você for solicitado a fornecer credenciais, insira as credenciais de implantação no nível do usuário que você criou na etapa 5. Certifique-se de enviar por push para a ramificação principal do serviço de aplicativo remoto.

    ```cmd
    git push webapp main:main
    ```

9. O progresso da implantação será atualizado na janela Comando. Uma implantação bem-sucedida terminará com linhas semelhantes à seguinte saída:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  main -> main
    ```

10. Execute o seguinte comando para consultar o estado do seu aplicativo Web e verificar se ele está em execução:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Navegue até `https://<your web app name>.azurewebsites.net` em um navegador. Uma página da Web semelhante à que você viu quando executou o aplicativo Web localmente é exibida. Supondo que o dispositivo esteja executando e enviando dados, você deverá ver um gráfico em execução das 50 leituras mais recentes de temperatura e umidade enviadas pelo dispositivo.

## <a name="troubleshooting"></a>Solução de problemas

Se você tiver problemas com este exemplo, tente as etapas nas seções a seguir. Se você ainda tiver problemas, envie-nos comentários na parte inferior deste tópico.

### <a name="client-issues"></a>Problemas com o cliente

* Se um dispositivo não aparecer na lista ou se nenhum grafo estiver sendo desenhado, verifique se o código do dispositivo está em execução no dispositivo.

* No navegador, abra as ferramentas para desenvolvedores (em muitos navegadores, a tecla F12 pode ser usada para isso) e localize o console. Procure mensagens de avisos ou erros nele.

* Você pode depurar o script do lado do cliente no /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problemas do site local

* Assista à saída na janela em que você iniciou o nó para a saída do console.

* Depure o código do servidor, especificamente server.js e /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Problemas do Serviço de Aplicativo do Azure

* No portal do Azure, vá para seu aplicativo Web. Em **Monitoramento** no painel esquerdo, selecione **Logs do Serviço de Aplicativo**. Ative o **Log de Aplicativo (Sistema de Arquivos)** , defina **Nível** como Erro e, em seguida, selecione **Salvar**. Em seguida, abra **Fluxo de log** (em **Monitoramento**).

* Em seu aplicativo Web no portal do Azure, em **Ferramentas de Desenvolvimento**, selecione **Console** e valide as versões de nó e npm com `node -v` e `npm -v`.

* Se você vir um erro sobre a não localização de um pacote, as etapas poderão ter sido executadas fora de ordem. Quando o site é implantado (com `git push`), o serviço de aplicativo executa `npm install`, que é executado com base na versão atual do nó que ele configurou. Se isso for alterado na configuração mais tarde, você precisará fazer uma alteração não significativa no código e enviá-lo por push novamente.

## <a name="next-steps"></a>Próximas etapas

Você usou com êxito seu aplicativo Web para visualizar dados do sensor em tempo real do seu Hub IoT.

Para outro modo de visualizar os dados de Hub IoT do Azure, confira [Usar o Power BI para visualizar dados de sensor em tempo real do seu hub IoT](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
