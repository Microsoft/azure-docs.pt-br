---
title: Integração com o Serviço do Azure SignalR
titleSuffix: Azure Digital Twins
description: Veja como transmitir telemetria dos Gêmeos Digitais do Azure para clientes usando o Azure SignalR
author: dejimarquis
ms.author: aymarqui
ms.date: 02/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 89bd77c30ec52a72087598b86f22e85659fa1b0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102203888"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Integrar os Gêmeos Digitais do Azure ao Serviço do Azure SignalR

Neste artigo, você aprenderá a integrar os Gêmeos Digitais do Azure ao [Serviço do Azure SignalR](../azure-signalr/signalr-overview.md).

A solução descrita neste artigo permite enviar dados telemétricos de gêmeo digital para clientes conectados, como uma página da Web ou um aplicativo móvel. Como resultado, os clientes são atualizados com métricas em tempo real e status de dispositivos IoT, sem a necessidade de sondar o servidor ou enviar novas solicitações HTTP para atualizações.

## <a name="prerequisites"></a>Pré-requisitos

Aqui estão os pré-requisitos que você deve concluir antes de continuar:

* Antes de integrar sua solução com o Serviço do Azure SignalR nesse artigo, você deve concluir o [_**Tutorial: Conectar uma solução de ponta a ponta**_](tutorial-end-to-end.md) dos Gêmeos Digitais do Azure, porque este artigo de instruções se baseia nele. O tutorial orienta você pela configuração de uma instância dos Gêmeos Digitais do Azure que funciona com um dispositivo IoT virtual para disparar atualizações do gêmeo digital. Este artigo de instruções conectará essas atualizações a um aplicativo Web de exemplo usando o Serviço do Azure SignalR.

* Você precisará dos seguintes valores do tutorial:
  - Tópico da grade de eventos
  - Resource group
  - Serviço de aplicativo/nome do aplicativo de funções
    
* Você precisará ter o [**Node.js**](https://nodejs.org/) instalado em seu computador.

Você também deve entrar no [portal do Azure](https://portal.azure.com/) com sua conta do Azure.

## <a name="solution-architecture"></a>Arquitetura da solução

Você anexará o Serviço do Azure SignalR para Gêmeos Digitais do Azure por meio do caminho abaixo. As seções A, B e C no diagrama são extraídas do diagrama da arquitetura do [pré-requisito do tutorial de ponta a ponta](tutorial-end-to-end.md). Nesse artigo de instruções, você criará a seção D na arquitetura existente.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Uma exibição dos serviços do Azure em um cenário de ponta a ponta. Retrata dados que fluem de um dispositivo para o Hub IoT, por meio de uma função do Azure (seta B) para uma instância dos Gêmeos Digitais do Azure (seção A) e, depois, pela Grade de Eventos para outra função do Azure para processamento (seta C). A seção D mostra os dados que fluem da mesma Grade de Eventos na seta C para uma função do Azure rotulada 'broadcast'. A função 'broadcast' se comunica com outra função do Azure rotulada 'negotiate', e 'broadcast' e 'negotiate' se comunicam com dispositivos de computador." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>Baixar os aplicativos de exemplo

Primeiro, baixe os aplicativos de exemplo necessários. Você precisará do seguinte:
* [**Exemplos de ponta a ponta dos Gêmeos Digitais do Azure**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/): este exemplo contém um *AdtSampleApp* que contém duas funções do Azure para mover dados em uma instância dos Gêmeos Digitais do Azure (você pode aprender sobre esse cenário mais detalhadamente no [*Tutorial: Conectar uma solução de ponta a ponta*](tutorial-end-to-end.md)). Ele também contém um aplicativo de exemplo *DeviceSimulator* que simula um dispositivo IoT, gerando um novo valor de temperatura a cada segundo.
    - Se você ainda não baixou o exemplo como parte do tutorial em [*Pré-requisitos*](#prerequisites), navegue até o [link](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) de exemplo e selecione o botão *Procurar código* abaixo do título. Isso levará você a um repositório GitHub para obter exemplos que poderão ser baixados como um arquivo *.zip* clicando no botão *Código*, depois em *Baixar arquivo .zip*.

        :::image type="content" source="media/includes/download-repo-zip.png" alt-text="Uma exibição do repositório de gêmeos digitais de exemplo no GitHub. Após clicar no botão Código, uma pequena caixa de diálogo será exibida, na qual o botão Baixar arquivo .zip estará realçado." lightbox="media/includes/download-repo-zip.png":::

    Isso baixará uma cópia do mesmo repositório de exemplo no seu computador, como **digital-twins-samples-master.zip**. Descompacte a pasta.
* [**Exemplo de aplicativo Web de integração do SignalR**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/): este é um aplicativo Web React de exemplo que consumirá dados de telemetria dos Gêmeos Digitais do Azure de um Serviço do Azure SignalR.
    -  Navegue até o link de exemplo e use o mesmo processo de download para baixar uma cópia do exemplo em seu computador, como _**digitaltwins-signalr-webapp-sample-main.zip**_. Descompacte a pasta.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Deixe a janela do navegador aberta no portal do Azure, pois você a usará novamente na próxima seção.

## <a name="publish-and-configure-the-azure-functions-app"></a>Publicar e configurar o aplicativo do Azure Functions

Nesta seção, você configurará duas funções do Azure:
* **negotiate** – uma função de gatilho HTTP. Usa a associação de entrada *SignalRConnectionInfo* para gerar e retornar informações de conexão válidas.
* **broadcast** – uma função de gatilho da [Grade de Eventos](../event-grid/overview.md). Ele recebe dados de telemetria dos Gêmeos Digitais do Azure por meio da grade de eventos e usa a associação de saída da instância do *SignalR* criada na etapa anterior para transmitir a mensagem a todos os aplicativos cliente conectados.

Inicie o Visual Studio (ou outro editor de código de sua escolha) e abra a solução de código na pasta *digital-twins-samples-master > ADTSampleApp*. Em seguida, execute as seguintes etapas para criar as funções:

1. No projeto *SampleFunctionsApp*, crie uma classe C# chamada **SignalRFunctions.cs**.

1. Substitua o conteúdo do arquivo da classe pelo seguinte código:
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. Na janela do *Console do Gerenciador de Pacotes* do Visual Studio ou em qualquer janela Comando em seu computador, navegue até a pasta *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp* e execute o seguinte comando para instalar o pacote NuGet `SignalRService` no projeto:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Isso deve resolver os problemas de dependência na classe.

1. Publique sua função no Azure, usando as etapas descritas na [seção *Publicar o aplicativo*](tutorial-end-to-end.md#publish-the-app) do tutorial *Conectar uma solução de ponta a ponta*. Você pode publicá-la no mesmo serviço de aplicativo/aplicativo de funções que usou no [Pré-requisito](#prerequisites) do tutorial de ponta a ponta ou criar um, mas talvez queira usar o mesmo para minimizar a duplicação. 

Em seguida, configure as funções para se comunicar com sua instância do Azure SignalR. Você começará coletando a **cadeia de conexão** da instância do SignalR e a adicionará às configurações do aplicativo de funções.

1. Vá para o [portal do Azure](https://portal.azure.com/) e pesquise o nome da sua instância do SignalR na barra de pesquisa na parte superior do portal. Selecione a instância para abri-la.
1. Selecione **Chaves** no menu da instância para exibir as cadeias de conexão da instância do Serviço do SignalR.
1. Selecione o ícone *Copiar* para copiar a cadeia de conexão primária.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Captura de tela do portal do Azure que mostra a página Chaves para a instância do SignalR. O ícone 'Copiar para a área de transferência' ao lado da CADEIA DE CONEXÃO principal está realçado." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

1. Por fim, adicione a **cadeia de conexão** do SignalR do Azure às configurações do aplicativo de funções, usando o comando da CLI do Azure a seguir. Além disso, substitua os espaços reservados pelo seu grupo de recursos e pelo nome do serviço de aplicativo/aplicativo de funções do [pré-requisito do tutorial](how-to-integrate-azure-signalr.md#prerequisites). O comando pode ser executado no [Azure Cloud Shell](https://shell.azure.com) ou localmente se você tiver a CLI do Azure [instalada em seu computador](/cli/azure/install-azure-cli):
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    A saída desse comando imprime todas as configurações de aplicativo configuradas para sua função do Azure. Procure `AzureSignalRConnectionString` na parte inferior da lista para verificar se ela foi adicionada.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Trecho da saída em uma janela Comando, mostrando um item de lista chamado 'AzureSignalRConnectionString'":::

#### <a name="connect-the-function-to-event-grid"></a>Conectar a função à Grade de Eventos

Em seguida, assine a função do Azure *broadcast* para o **tópico da grade de eventos** criado durante o [pré-requisito do tutorial](how-to-integrate-azure-signalr.md#prerequisites). Isso permitirá que os dados de telemetria fluam do gêmeo thermostat67 pelo tópico da grade de eventos e para a função. Daqui, a função pode difundir os dados para todos os clientes.

Para fazer isso, você criará uma **Assinatura do evento** do tópico da grade de eventos para sua função do Azure *broadcast* como um ponto de extremidade.

No [portal do Azure](https://portal.azure.com/), navegue até o tópico da grade de eventos pesquisando pelo nome dele na barra de pesquisa superior. Selecione *+ Assinatura de Evento*.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Portal do Azure: assinatura de evento da Grade de Eventos":::

Na página *Criar Assinatura de Evento*, preencha os campos da seguinte maneira (os campos preenchidos por padrão não são mencionados):
* *DETALHES DA ASSINATURA DE EVENTO* > **Nome**: dê um nome à assinatura de evento.
* *DETALHES DO PONTO DE EXTREMIDADE* > **Tipo de Ponto de Extremidade**: selecione *Função do Azure* nas opções de menu.
* *DETALHES DO PONTO DE EXTREMIDADE* > **Ponto de Extremidade**: clique no link *Selecionar um ponto de extremidade*. Isso abrirá uma janela *Selecionar Função do Azure*:
    - Preencha os valores para **Assinatura**, **Grupo de recursos**, **Aplicativo de funções** e **Função** (*broadcast*). Alguns deles poderão ser preenchidos automaticamente após a seleção da assinatura.
    - Clique em **Confirmar seleção**.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Exibição do portal do Azure da criação de uma assinatura de evento. Os campos acima estão preenchidos e os botões 'Confirmar Seleção' e 'Criar' estão realçados.":::

Na página *Criar Assinatura de Evento*, selecione **Criar**.

Neste ponto, você deve ver duas assinaturas de evento na página *Tópicos da grade de eventos*.

:::image type="content" source="media/how-to-integrate-azure-signalr/view-event-subscriptions.png" alt-text="Exibição do portal do Azure de duas assinaturas de evento na página Tópicos da grade de eventos" lightbox="media/how-to-integrate-azure-signalr/view-event-subscriptions.png":::.

## <a name="configure-and-run-the-web-app"></a>Configurar e executar o aplicativo Web

Nesta seção, você verá o resultado em ação. Primeiro, configure o **aplicativo Web do cliente de exemplo** para se conectar ao fluxo do Azure SignalR que você configurou. Em seguida, você iniciará o **aplicativo de exemplo de dispositivo simulado** que envia dados de telemetria por meio da instância dos Gêmeos Digitais do Azure. Depois disso, você exibirá o aplicativo Web de exemplo para ver os dados do dispositivo simulado atualizando o aplicativo Web de exemplo em tempo real.

### <a name="configure-the-sample-client-web-app"></a>Configurar o aplicativo Web do cliente de exemplo

Em seguida, configure o aplicativo Web do cliente de exemplo. Comece coletando a **URL do ponto de extremidade HTTP** da função *negotiate* e use-a para configurar o código do aplicativo em seu computador.

1. Vá para a página [Aplicativos de funções](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) do portal do Azure e selecione seu aplicativo de funções na lista. No menu do aplicativo, selecione *Funções* e escolha a função *negotiate*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Exibição do portal do Azure do aplicativo de funções, com 'Funções' realçado no menu. A lista de funções é mostrada na página e a função 'negotiate' também está realçada.":::

1. Clique em *Obter URL da função* e copie o valor **até _/api_ (não inclua o último _/negotiate?_ )** . Você usará isso na próxima etapa.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Exibição do portal do Azure da função 'negotiate'. O botão 'Obter URL da função' está realçado e a parte da URL do início até '/api'":::

1. Usando o Visual Studio ou qualquer editor de código de sua escolha, abra a pasta descompactada _**digitaltwins-signalr-webapp-sample-main**_ que você baixou na seção [*Baixar os aplicativos de exemplo*](#download-the-sample-applications).

1. Abra o arquivo *src/App.js* e substitua a URL da função em `HubConnectionBuilder` pela URL do ponto de extremidade HTTP da função **negotiate** que você salvou na etapa anterior:

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<Function URL>')
            .build();
    ```
1. No *Prompt de Comando do Desenvolvedor* do Visual Studio ou em qualquer janela Comando em seu computador, navegue até a pasta *digitaltwins-signalr-webapp-sample-main\src*. Execute o seguinte comando para instalar os pacotes de nó dependente:

    ```cmd
    npm install
    ```

Em seguida, defina as permissões em seu aplicativo de funções no portal do Azure:
1. Na página de [Aplicativos de funções](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) do portal do Azure, selecione sua instância de aplicativo de funções.

1. Role para baixo no menu de instância e selecione *CORS*. Na página CORS, adicione `http://localhost:3000` como uma origem permitida inserindo-a na caixa vazia. Marque a caixa *Habilitar Acesso-Controle-Permitir-Credenciais* e clique em *Salvar*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Configuração de CORS no Azure Functions":::

### <a name="run-the-device-simulator"></a>Executar o simulador do dispositivo

Durante o pré-requisito do tutorial de ponta a ponta, você [configurou o simulador de dispositivo](tutorial-end-to-end.md#configure-and-run-the-simulation) para enviar dados por um Hub IoT e para sua instância dos Gêmeos Digitais do Azure.

Agora, tudo o que você precisa fazer é iniciar o projeto do simulador, localizado em *digital-twins-samples-master > DeviceSimulator > DeviceSimulator.sln*. Se você estiver usando o Visual Studio, poderá abrir o projeto e executá-lo com esse botão na barra de ferramentas:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="O botão de início do Visual Studio (projeto DeviceSimulator)":::

Uma janela do console será aberta e exibirá mensagens da telemetria de temperatura simulada. Elas estão sendo enviadas por meio da sua instância dos Gêmeos Digitais do Azure, em que elas são coletadas pelas funções do Azure e SignalR.

Você não precisa fazer mais nada nesse console, mas deixe-o em execução enquanto conclui a próxima etapa.

### <a name="see-the-results"></a>Confira os resultados

Para ver os resultados em ação, inicie o **exemplo de aplicativo Web de integração do SignalR**. Você pode fazer isso em qualquer janela de console na localização *digitaltwins-signalr-webapp-sample-main\src*, executando este comando:

```cmd
npm start
```

Isso abrirá uma janela do navegador executando o aplicativo de exemplo, que exibe um medidor de temperatura visual. Depois que o aplicativo estiver em execução, você deverá começar a ver os valores de telemetria de temperatura do simulador de dispositivo que se propagam pelos Gêmeos Digitais do Azure sendo refletidos pelo aplicativo Web em tempo real.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Trecho do aplicativo Web do cliente de exemplo, mostrando um medidor de temperatura visual. A temperatura refletida é 67,52":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais dos recursos criados nesse artigo, siga estas etapas para excluí-los. 

Usando o Azure Cloud Shell ou a CLI do Azure local, você pode excluir todos os recursos do Azure em um grupo de recursos com o comando [az group delete](/cli/azure/group#az-group-delete). Remover o grupo de recursos também removerá...
* a instância dos Gêmeos Digitais do Azure (do tutorial de ponta a ponta)
* o Hub IoT e o registro do dispositivo de Hub (do tutorial de ponta a ponta)
* o tópico da grade de eventos e as assinaturas associadas
* o aplicativo Azure Functions, incluindo todas as três funções e os recursos associados, como armazenamento
* a instância do SignalR do Azure

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Por fim, exclua as pastas de exemplo do projeto que você baixou para o computador local (*digital-twins-samples-master.zip*, *digitaltwins-signalr-webapp-sample-main.zip* e suas contrapartes descompactadas).

## <a name="next-steps"></a>Próximas etapas

Nesse artigo, você configura funções do Azure com o SignalR para difundir eventos de telemetria dos Gêmeos Digitais do Azure para um aplicativo cliente de exemplo.

Em seguida, saiba mais sobre o Serviço do Azure SignalR:
* [*O que é o Serviço do Azure SignalR?*](../azure-signalr/signalr-overview.md)

Ou leia mais sobre a Autenticação do Serviço do Azure SignalR com o Azure Functions:
* [*Autenticação do Serviço Azure SignalR*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)