---
title: 'Tutorial: Conectar uma solução de ponta a ponta'
titleSuffix: Azure Digital Twins
description: Tutorial para criar uma solução de ponta a ponta dos Gêmeos Digitais do Azure controlada por dados do dispositivo.
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: f1653158f7a181ad2d61bc726ba7765eab934341
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107458"
---
# <a name="tutorial-build-out-an-end-to-end-solution"></a>Tutorial: Criar uma solução de ponta a ponta

Para configurar uma solução de ponta a ponta completa baseada em dados dinâmicos de seu ambiente, você pode conectar sua instância dos Gêmeos Digitais do Azure a outros serviços do Azure para o gerenciamento de dispositivos e de dados.

Neste tutorial, você vai...
> [!div class="checklist"]
> * Configurar uma instância dos Gêmeos Digitais do Azure
> * Aprender sobre o cenário de criação de exemplo e criar instâncias dos componentes pré-gravados
> * Usar um aplicativo do [Azure Functions](../azure-functions/functions-overview.md) para rotear a telemetria simulada de um dispositivo do [Hub IoT](../iot-hub/about-iot-hub.md) para propriedades dos gêmeos digitais
> * Propagar alterações por meio do **gráfico gêmeo**, processando notificações dos gêmeos digitais com o Azure Functions, pontos de extremidade e rotas

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Configurar uma sessão do Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>Introdução ao cenário de criação

O projeto de exemplo usado neste tutorial representa um **cenário de construção** do mundo real, que contém um andar, uma sala e um dispositivo de termostato. Esses componentes serão representados digitalmente em uma instância dos Gêmeos Digitais do Azure, que será conectada ao [Hub IoT](../iot-hub/about-iot-hub.md), à [Grade de Eventos](../event-grid/overview.md) e a duas [funções do Azure](../azure-functions/functions-overview.md) para facilitar a movimentação de dados.

Abaixo, temos um diagrama que representa o cenário completo. 

Primeiro, você criará a instância dos Gêmeos Digitais do Azure (**seção A** no diagrama), depois, vai configurar o fluxo de dados de telemetria nos gêmeos digitais (**seta B**) e, em seguida, vai configurar a propagação de dados por meio do gráfico gêmeo (**seta C**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Gráfico do cenário de construção completo. Descreve dados que fluem de um dispositivo para o Hub IoT, por meio de uma função do Azure (seta B) para uma instância dos Gêmeos Digitais do Azure (seção A) e, em seguida, pela Grade de Eventos para outra função do Azure para processamento (seta C)":::

Para trabalhar com o cenário, você vai interagir com os componentes do aplicativo de exemplo pré-escrito que baixou anteriormente.

Estes são os componentes implementados pelo aplicativo de exemplo *AdtSampleApp* do cenário de construção:
* Autenticação de dispositivo 
* Exemplos de uso do [SDK do .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client) (encontrados em *CommandLoop.cs*)
* Interface de console para chamar a API dos Gêmeos Digitais do Azure
* *SampleClientApp* – uma solução de exemplo dos Gêmeos Digitais do Azure
* *SampleFunctionsApp* – um aplicativo do Azure Functions que atualiza seu gráfico dos Gêmeos Digitais do Azure como resultado da telemetria do Hub IoT e de eventos dos Gêmeos Digitais do Azure

### <a name="instantiate-the-pre-created-twin-graph"></a>Criar uma instância do gráfico gêmeo pré-criado

Primeiro, você usará a solução *AdtSampleApp* do projeto de exemplo para criar a parte referente aos Gêmeos Digitais do Azure do cenário de ponta a ponta (**seção A**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="Um trecho do gráfico de cenário de construção completo realçando a seção A, a instância dos Gêmeos Digitais do Azure":::

Na janela do Visual Studio em que o projeto _**AdtE2ESample**_ está aberto, execute o projeto com este botão na barra de ferramentas:

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="O botão de início do Visual Studio (projeto SampleClientApp)":::

Uma janela de console será aberta, executará a autenticação e aguardará um comando. Neste console, execute o comando seguinte para criar uma instância da solução de exemplo dos Gêmeos Digitais do Azure.

> [!IMPORTANT]
> Se você já tiver gêmeos digitais e relações em sua instância dos Gêmeos Digitais do Azure, a execução desse comando os excluirá e os substituirá pelos gêmeos e pelas relações do cenário de exemplo.

```cmd/sh
SetupBuildingScenario
```

A saída desse comando é uma série de mensagens de confirmação, conforme três [**gêmeos digitais**](concepts-twins-graph.md) são criados e conectados em sua instância dos Gêmeos Digitais do Azure: um andar chamado *floor1*, uma sala chamada *room21* e um sensor de temperatura chamado *thermostat67*. Esses gêmeos digitais representam as entidades que existiriam em um ambiente do mundo real.

Elas são conectadas pelas relações no seguinte [**gráfico gêmeo**](concepts-twins-graph.md). O gráfico gêmeo representa o ambiente como um todo, incluindo como as entidades interagem e se relacionam entre si.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="Um gráfico que mostra que floor1 contém room21 e que room21 contém thermostat67" border="false":::

Você pode verificar que os gêmeos foram criados executando o comando a seguir, que consulta a instância conectada dos Gêmeos Digitais do Azure para todos os gêmeos digitais que ela contém:

```cmd/sh
Query
```

>[!TIP]
> Esse método simplificado é fornecido como parte do projeto _**AdtE2ESample**_. Fora do contexto deste exemplo de código, você pode consultar todos os gêmeos na instância a qualquer momento usando as [APIs de Consulta](/rest/api/digital-twins/dataplane/query) ou os [comandos da CLI](how-to-use-cli.md).
>
> Este é o corpo da consulta completa para obter todas as gêmeos digitais na instância:
> 
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

Depois disso, você pode parar de executar o projeto. No entanto, mantenha a solução aberta no Visual Studio, pois ela será usada em todo o tutorial.

## <a name="set-up-the-sample-function-app"></a>Configurar o aplicativo de funções de exemplo

A etapa seguinte é configurar um [aplicativo do Azure Functions](../azure-functions/functions-overview.md) que será usado em todo este tutorial para processar dados. O aplicativo de funções, *SampleFunctionsApp*, contém duas funções:
* *ProcessHubToDTEvents*: processa dados de entrada do Hub IoT e atualiza os Gêmeos Digitais do Azure adequadamente
* *ProcessDTRoutedData*: processa dados dos gêmeos digitais e atualiza o gêmeos pai nos Gêmeos Digitais do Azure adequadamente

Nesta seção, você publicará o aplicativo de funções pré-escrito e garantirá que ele possa acessar os Gêmeos Digitais do Azure atribuindo a ele uma identidade do Azure AD (Azure Active Directory). A conclusão dessas etapas permitirá que o restante do tutorial use as funções dentro do aplicativo de funções. 

De volta à janela do Visual Studio em que o projeto _**AdtE2ESample**_ está aberto, o aplicativo de funções está localizado no arquivo de projeto _**SampleFunctionsApp**_. Você pode exibi-lo no painel *Gerenciador de Soluções*.

### <a name="update-dependencies"></a>Atualizar dependências

Antes de publicar o aplicativo, é uma boa ideia verificar se suas dependências estão atualizadas garantindo que você tenha a versão mais recente de todos os pacotes incluídos.

No painel *Gerenciador de Soluções*, expanda _**SampleFunctionsApp** > Dependências_. Selecione com o botão direito do mouse *Pacotes* e escolha *Gerenciar Pacotes do NuGet...* .

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="Visual Studio: Gerenciar pacotes do NuGet para o projeto SampleFunctionsApp" border="false":::

Isso abrirá o Gerenciador de Pacotes do NuGet. Selecione a guia *Atualizações* e, se houver pacotes a serem atualizados, marque a caixa para *Selecionar todos os pacotes*. Em seguida, clique em *Atualizar*.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="Visual Studio: Como selecionar para atualizar todos os pacotes no Gerenciador de Pacotes do NuGet":::

### <a name="publish-the-app"></a>Publicar o aplicativo

De volta à janela do Visual Studio em que o projeto _**AdtE2ESample**_ está aberto, localize o projeto _**SampleFunctionsApp**_ no painel *Gerenciador de Soluções*.

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

Para que o seu aplicativo de funções possa acessar os Gêmeos Digitais do Azure, será necessário ter permissões para acessar a sua instância dos Gêmeos Digitais do Azure e o nome do host da instância. Você vai configurá-los em seguida.

### <a name="configure-permissions-for-the-function-app"></a>Configurar permissões para o aplicativo de funções

Há duas configurações que precisam ser definidas para que o aplicativo de funções acesse a instância dos Gêmeos Digitais do Azure. Elas podem ser realizadas por meio de comandos no [Azure Cloud Shell](https://shell.azure.com). 

#### <a name="assign-access-role"></a>Atribuir função de acesso

A primeira configuração fornece a função de **proprietário de dados dos Gêmeos Digitais do Azure** ao aplicativo de funções na instância dos Gêmeos Digitais do Azure. Essa função é necessária para qualquer usuário ou função que queira executar muitas atividades de plano de dados na instância. Você pode ler mais sobre atribuições de função e segurança em [*Conceitos: segurança para soluções dos Gêmeos Digitais do Azure*](concepts-security.md). 

1. Use o comando a seguir para ver os detalhes da identidade gerenciada pelo sistema para a função. Anote o campo **principalId** na saída.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Se o resultado estiver vazio em vez de mostrar detalhes de uma identidade, crie uma identidade gerenciada pelo sistema para a função usando este comando:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > A saída exibirá detalhes da identidade, incluindo o valor **principalId** necessário para a próxima etapa. 

1. Use o valor de **principalId** no comando a seguir para atribuir a identidade do aplicativo de funções à função de **Proprietário de Dados dos Gêmeos Digitais do Azure** para sua instância dos Gêmeos Digitais do Azure.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

O resultado desse comando são informações de saída sobre a atribuição de função que você criou. Agora, o aplicativo de funções tem permissões para acessar dados em sua instância dos Gêmeos Digitais do Azure.

#### <a name="configure-application-settings"></a>Definir as configurações do aplicativo

A segunda configuração cria uma **variável de ambiente** para a função com a URL da instância dos Gêmeos Digitais do Azure. O código de função usará isso para fazer referência à sua instância. Para obter mais informações sobre variáveis de ambiente, confira [*Gerenciar o seu aplicativo de funções*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

Execute o comando a seguir, preenchendo os espaços reservados com os detalhes dos recursos.

```azurecli-interactive
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```

A saída é a lista de configurações para a Função do Azure, que agora deve conter uma entrada chamada **ADT_SERVICE_URL**.


## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>Processar telemetria simulada de um dispositivo do Hub IoT

Um gráfico dos Gêmeos Digitais do Azure deve ser controlado pela telemetria de dispositivos reais. 

Nesta etapa, você conectará um dispositivo de termostato simulado registrado no [Hub IoT](../iot-hub/about-iot-hub.md) ao gêmeo digital que o representa nos Gêmeos Digitais do Azure. Conforme o dispositivo simulado emite telemetria, os dados serão direcionados pela função d o Azure *ProcessHubToDTEvents*, que dispara uma atualização correspondente no gêmeo digital. Dessa forma, o gêmeo digital se mantém atualizado com os dados do dispositivo real. Nos Gêmeos Digitais do Azure, o processo de direcionamento de dados de eventos de um local para outro é chamado de [**roteamento de eventos**](concepts-route-events.md).

Ele acontece nesta parte do cenário de ponta a ponta (**seta B**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="Um trecho do gráfico do cenário de construção completo realçando a seta B, os elementos antes dos Gêmeos Digitais do Azure: o dispositivo, o Hub IoT e a primeira função do Azure":::

Estas são as ações que você realizará para configurar esta conexão do dispositivo:
1. Criar um hub IoT que gerenciará o dispositivo simulado
2. Conectar o hub IoT à função do Azure apropriada configurando uma assinatura de evento
3. Registrar o dispositivo simulado no Hub IoT
4. Executar o dispositivo simulado e gerar telemetria
5. Consultar os Gêmeos Digitais do Azure para ver os resultados ao vivo

### <a name="create-an-iot-hub-instance"></a>Criar uma instância do Hub IoT

Os Gêmeos Digitais do Azure foram criados para trabalhar com o [Hub IoT](../iot-hub/about-iot-hub.md), um serviço do Azure para gerenciar dispositivos e seus dados. Nesta etapa, você vai configurar um hub IoT que gerenciará o dispositivo de exemplo neste tutorial.

No Azure Cloud Shell, use este comando para criar um hub IoT:

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

A saída desse comando são informações sobre o Hub IoT que foi criado.

Salve o **nome** que você deu ao hub IoT. Você o usará mais tarde.

### <a name="connect-the-iot-hub-to-the-azure-function"></a>Conectar o hub IoT à função do Azure

Em seguida, conecte o Hub IoT à função do Azure *ProcessHubToDTEvents* no aplicativo de funções publicado anteriormente, de modo que os dados possam fluir do dispositivo no Hub IoT para a função, que atualiza os Gêmeos Digitais do Azure.

Para fazer isso, você criará uma **Assinatura de Evento** no Hub IoT, com a função do Azure como um ponto de extremidade. Isso "assina" a função para eventos que ocorrem no Hub IoT.

No [portal do Azure](https://portal.azure.com/), navegue até o hub IoT recém-criado pesquisando pelo nome dele na barra de pesquisa superior. Selecione *Eventos* no menu do hub e selecione *+ Assinatura de Evento*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Portal do Azure: assinatura de evento do Hub IoT":::

Isso abrirá a página *Criar Assinatura de Evento*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Portal do Azure: criar assinatura de evento":::

Preencha os campos da seguinte maneira (os campos preenchidos por padrão não são mencionados):
* *DETALHES DA ASSINATURA DE EVENTO* > **Nome**: dê um nome à assinatura de evento.
* *DETALHES DO TÓPICO* > **Nome do Tópico do Sistema**: especifique um nome a ser usado para o tópico do sistema. 
* *TIPOS DE EVENTO* > **Filtro para Tipos de Evento**: selecione *Telemetria de Dispositivo* nas opções de menu.
* *DETALHES DO PONTO DE EXTREMIDADE* > **Tipo de Ponto de Extremidade**: selecione *Função do Azure* nas opções de menu.
* *DETALHES DO PONTO DE EXTREMIDADE* > **Ponto de Extremidade**: clique no link *Selecionar um ponto de extremidade*. Isso abrirá uma janela *Selecionar Função do Azure*: :::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Assinatura de evento do portal do Azure: selecionar Função do Azure" border="false":::
    - Preencha os valores para **Assinatura**, **Grupo de recursos**, **Aplicativo de funções** e **Função** (*ProcessHubToDTEvents*). Alguns deles poderão ser preenchidos automaticamente após a seleção da assinatura.
    - Clique em **Confirmar seleção**.

Na página *Criar Assinatura de Evento*, selecione **Criar**.

### <a name="register-the-simulated-device-with-iot-hub"></a>Registrar o dispositivo simulado no Hub IoT 

Esta seção cria uma representação do dispositivo no Hub IoT com a ID *thermostat67*. O dispositivo simulado se conectará a ela, e é assim que os eventos de telemetria passarão do dispositivo para o Hub IoT, onde a função do Azure assinada na etapa anterior está escutando, pronta para detectar os eventos e continuar o processamento.

No Azure Cloud Shell, crie um dispositivo no Hub IoT com o seguinte comando:

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

A saída são informações sobre o dispositivo criado.

### <a name="configure-and-run-the-simulation"></a>Configurar e executar a simulação

Em seguida, configure o simulador do dispositivo para enviar dados para a instância do Hub IoT.

Comece obtendo a *cadeia de conexão do Hub IoT* com este comando:

```azurecli-interactive
az iot hub connection-string show -n <your-IoT-hub-name>
```

Em seguida, obtenha a *cadeia de conexão do dispositivo* com este comando:

```azurecli-interactive
az iot hub device-identity connection-string show --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

Você usará esses valores no código do simulador de dispositivo no projeto local para conectar o simulador ao hub IoT e ao dispositivo do hub IoT.

Em uma nova janela do Visual Studio, abra (na pasta da solução baixada) _Simulador de Dispositivo > **DeviceSimulator.sln**_.

>[!NOTE]
> Agora, você deve ter duas janelas do Visual Studio, uma com _**DeviceSimulator.sln**_ e uma de antes, com _**AdtE2ESample.sln**_.

No painel do *Gerenciador de Soluções* nessa nova janela do Visual Studio, selecione _DeviceSimulator/**AzureIoTHub.cs**_ para abri-lo na janela de edição. Altere os seguintes valores de cadeia de conexão para os valores que você coletou acima:

```csharp
iotHubConnectionString = <your-hub-connection-string>
deviceConnectionString = <your-device-connection-string>
```

Salve o arquivo.

Agora, para ver os resultados da simulação de dados configurada, execute o projeto do **DeviceSimulator** usando esse botão da barra de ferramentas:

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="O botão de início do Visual Studio (projeto DeviceSimulator)":::

Uma janela do console será aberta e exibirá mensagens da telemetria de temperatura simulada. Elas estão sendo enviadas ao hub IoT, onde são detectadas e processadas pela função do Azure.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Saída do console do simulador de dispositivo mostrando a telemetria de temperatura sendo enviada":::

Você não precisa fazer mais nada neste console, mas deixe-o em execução enquanto conclui as próximas etapas.

### <a name="see-the-results-in-azure-digital-twins"></a>Ver os resultados nos Gêmeos Digitais do Azure

A função *ProcessHubToDTEvents* que você publicou anteriormente escuta os dados do Hub IoT e chama uma API dos Gêmeos Digitais do Azure para atualizar a propriedade *Temperatura* no gêmeo *thermostat67*.

Para ver os dados do lado dos Gêmeos Digitais do Azure, vá para a janela do Visual Studio em que o projeto _**AdtE2ESample**_ está aberto e execute-o.

Na janela do console do projeto que é aberta, execute o seguinte comando para obter as temperaturas que estão sendo relatadas pelo gêmeo digital *thermostat67*:

```cmd
ObserveProperties thermostat67 Temperature
```

Você deve ver as temperaturas atualizadas de maneira dinâmica *de sua instância dos Gêmeos Digitais do Azure* sendo registradas no console a cada dois segundos.

>[!NOTE]
> Pode levar alguns segundos para que os dados do dispositivo se propaguem até o gêmeo. As primeiras leituras de temperatura podem aparecer como 0 antes que os dados comecem a chegar.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="Saída do console mostrando o log de mensagens de temperatura do gêmeo digital thermostat67":::

Depois de verificar se está tudo funcionando corretamente, você pode parar de executar os dois projetos. Mantenha as janelas do Visual Studio abertas, pois elas serão usadas no restante do tutorial.

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Propagar eventos dos Gêmeos Digitais do Azure pelo grafo

Até agora neste tutorial, você viu como os Gêmeos Digitais do Azure podem ser atualizados usando dados de um dispositivo externo. A seguir, você verá como as alterações feitas em um gêmeo digital podem ser propagadas pelo grafo dos Gêmeos Digitais do Azure – em outras palavras, como atualizar gêmeos usando dados internos do serviço.

Para fazer isso, você usará a função do Azure *ProcessDTRoutedData* para atualizar um gêmeo da *Sala* quando o gêmeo do *Termostato* conectado for atualizado. Isso acontece nesta parte do cenário de ponta a ponta (**seta C**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="Um trecho do gráfico do cenário de construção completo realçando a seta C, os elementos depois dos Gêmeos Digitais do Azure: a Grade de Eventos e a segunda função do Azure":::

Estas são as ações que você realizará para configurar este fluxo de dados:
1. Criar um ponto de extremidade da Grade de Eventos nos Gêmeos Digitais do Azure que conecta a instância à Grade de Eventos
2. Configurar uma rota nos Gêmeos Digitais do Azure para enviar eventos de alteração de propriedade do gêmeo para o ponto de extremidade
3. Implantar um aplicativo do Azure Functions que escuta (por meio da [Grade de Eventos](../event-grid/overview.md)) no ponto de extremidade e atualiza outros gêmeos adequadamente
4. Executar o dispositivo simulado e consultar os Gêmeos Digitais do Azure para ver os resultados de maneira dinâmica

### <a name="set-up-endpoint"></a>Configurar o ponto de extremidade

A [Grade de Eventos](../event-grid/overview.md) é um serviço do Azure que ajuda você a rotear e entregar eventos provenientes de Serviços do Azure para outros locais no Azure. Você pode criar um [tópico da grade de eventos](../event-grid/concepts.md) para coletar determinados eventos de uma fonte e, então, os assinantes podem escutar o tópico para receber os eventos à medida que eles chegam.

Nesta seção, você cria um tópico da grade de eventos e, em seguida, cria um ponto de extremidade nos Gêmeos Digitais do Azure que aponta (envia eventos) para esse tópico. 

No Azure Cloud Shell, execute o comando a seguir para criar um tópico da grade de eventos:

```azurecli-interactive
az eventgrid topic create -g <your-resource-group> --name <name-for-your-event-grid-topic> -l <region>
```

> [!TIP]
> Para gerar uma lista de nomes de regiões do Azure que podem ser passados para comandos na CLI do Azure, execute este comando:
> ```azurecli-interactive
> az account list-locations -o table
> ```

A saída desse comando são informações sobre o tópico da grade de eventos que você criou.

Em seguida, crie um ponto de extremidade da Grade de Eventos nos Gêmeos Digitais do Azure, que conectará sua instância ao tópico da Grade de Eventos. Use o comando a seguir, preenchendo os campos de espaço reservado conforme necessário:

```azurecli-interactive
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

A saída desse comando são informações sobre o ponto de extremidade que você criou.

Você também pode verificar se a criação do ponto de extremidade foi bem-sucedida executando o seguinte comando para consultar sua instância dos Gêmeos Digitais do Azure para este ponto de extremidade:

```azurecli-interactive
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

Procure o campo `provisioningState` na saída e verifique se o valor é "Êxito". Ele também pode indicar "provisionamento", o que significa que o ponto de extremidade ainda está sendo criado. Nesse caso, aguarde alguns segundos e execute o comando novamente para verificar se ele foi concluído com êxito.

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="Resultado da consulta do ponto de extremidade, mostrando o ponto de extremidade com o provisioningState igual a Êxito":::

Salve os nomes que você deu ao **tópico** e ao **ponto de extremidade** da Grade de Eventos nos Gêmeos Digitais do Azure. Você os usará mais tarde.

### <a name="set-up-route"></a>Configurar rota

Em seguida, crie uma rota dos Gêmeos Digitais do Azure que envia eventos para o ponto de extremidade recém-criado da Grade de Eventos.

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

A saída desse comando são algumas informações sobre a rota que você criou.

>[!NOTE]
>Os pontos de extremidade (da etapa anterior) devem ter o provisionamento concluído para configurar uma rota de eventos que os utilize. Se a criação da rota falhar porque os pontos de extremidade não estão prontos, aguarde alguns minutos e tente novamente.

#### <a name="connect-the-function-to-event-grid"></a>Conectar a função à Grade de Eventos

Em seguida, assine a função do Azure *ProcessDTRoutedData* para o tópico da grade de eventos que você criou anteriormente, para que os dados de telemetria possam fluir do gêmeo *thermostat67*, pelo tópico da grade de eventos, até a função, que volta aos Gêmeos Digitais do Azure e atualiza *room21* de modo adequado.

Para fazer isso, você criará uma **assinatura da Grade de Eventos** que enviará dados do **tópico da Grade de Eventos** que você criou anteriormente para a sua função do Azure *ProcessDTRoutedData*.

No [portal do Azure](https://portal.azure.com/), navegue até o tópico da grade de eventos pesquisando pelo nome dele na barra de pesquisa superior. Selecione *+ Assinatura de Evento*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Portal do Azure: assinatura de evento da Grade de Eventos":::

As etapas para criar essa assinatura de evento são semelhantes a quando você assinou a primeira função do Azure para o Hub IoT anteriormente neste tutorial. Desta vez, você não precisa especificar *Telemetria do Dispositivo* como o tipo de evento a ser escutado e você se conectará a outra função do Azure.

Na página *Criar Assinatura de Evento*, preencha os campos da seguinte maneira (os campos preenchidos por padrão não são mencionados):
* *DETALHES DA ASSINATURA DE EVENTO* > **Nome**: dê um nome à assinatura de evento.
* *DETALHES DO PONTO DE EXTREMIDADE* > **Tipo de Ponto de Extremidade**: selecione *Função do Azure* nas opções de menu.
* *DETALHES DO PONTO DE EXTREMIDADE* > **Ponto de Extremidade**: clique no link *Selecionar um ponto de extremidade*. Isso abrirá uma janela *Selecionar Função do Azure*:
    - Preencha os valores para **Assinatura**, **Grupo de recursos**, **Aplicativo de funções** e **Função** (*ProcessDTRoutedData*). Alguns deles poderão ser preenchidos automaticamente após a seleção da assinatura.
    - Clique em **Confirmar seleção**.

Na página *Criar Assinatura de Evento*, selecione **Criar**.

### <a name="run-the-simulation-and-see-the-results"></a>Executar a simulação e ver os resultados

Agora, você pode executar o simulador do dispositivo para iniciar o novo fluxo de eventos configurado. Vá para a janela do Visual Studio em que o projeto _**DeviceSimulator**_ está aberto e execute o projeto.

Como ocorreu quando você executou o simulador de dispositivo anteriormente, uma janela do console será aberta e exibirá mensagens da telemetria de temperatura simulada. Esses eventos estão passando pelo fluxo configurado anteriormente para atualizar o gêmeo *thermostat67* e, em seguida, percorrendo o fluxo configurado para atualizar o gêmeo *room21* de maneira correspondente.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Saída do console do simulador de dispositivo mostrando a telemetria de temperatura sendo enviada":::

Você não precisa fazer mais nada neste console, mas deixe-o em execução enquanto conclui as próximas etapas.

Para ver os dados do lado dos Gêmeos Digitais do Azure, vá para a janela do Visual Studio em que o projeto _**AdtE2ESample**_ está aberto e execute-o.

Na janela do console do projeto que é aberta, execute o seguinte comando para obter as temperaturas que estão sendo relatadas para **ambos**: o gêmeo digital *thermostat67* e o gêmeo digital *room21*.

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

Você deve ver as temperaturas atualizadas de maneira dinâmica *de sua instância dos Gêmeos Digitais do Azure* sendo registradas no console a cada dois segundos. Observe que a temperatura de *room21* está sendo atualizada de maneira a corresponder às atualizações de *thermostat67*.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="Saída do console mostrando o log de mensagens de temperatura de um termostato e de uma sala":::

Depois de verificar se está tudo funcionando corretamente, você pode parar de executar os dois projetos. Você também pode fechar as janelas do Visual Studio, uma vez que o tutorial está concluído.

## <a name="review"></a>Revisão

Aqui, temos uma revisão do cenário que você criou neste tutorial.

1. Uma instância dos Gêmeos Digitais do Azure representa digitalmente um andar, uma sala e um termostato (representado pela **seção A** no diagrama abaixo)
2. A telemetria do dispositivo simulado é enviada ao Hub IoT, onde a função do Azure *ProcessHubToDTEvents* está escutando eventos de telemetria. A função do Azure *ProcessHubToDTEvents* usa as informações nesses eventos para definir a propriedade de *Temperatura* no *thermostat67* (**seta B** no diagrama).
3. Os eventos de alteração de propriedade nos Gêmeos Digitais do Azure são roteados para um tópico da grade de eventos, em que a função do Azure *ProcessDTRoutedData* está escutando eventos. A função do Azure *ProcessDTRoutedData* usa as informações nesses eventos para definir a propriedade de *Temperatura* no *room21* (**seta B** no diagrama).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Gráfico do cenário de construção completo. Descreve dados que fluem de um dispositivo para o Hub IoT, por meio de uma função do Azure (seta B) para uma instância dos Gêmeos Digitais do Azure (seção A) e, em seguida, pela Grade de Eventos para outra função do Azure para processamento (seta C)":::

## <a name="clean-up-resources"></a>Limpar os recursos

Após concluir este tutorial, você poderá escolher quais recursos gostaria de remover, dependendo do que você gostaria de fazer em seguida.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

* **Se você quiser continuar usando a instância dos Gêmeos Digitais do Azure configurada neste artigo, mas limpar alguns ou todos os modelos, gêmeos e relações dela**, poderá usar os comandos da CLI [az dt](/cli/azure/dt) em uma janela do [Azure Cloud Shell](https://shell.azure.com) para excluir os elementos que deseja remover.

    Essa opção não removerá nenhum dos outros recursos do Azure criados neste tutorial (Hub IoT, aplicativo do Azure Functions etc.). Você pode exclui-los individualmente usando os [comandos dt](/cli/azure/reference-index) apropriados para cada tipo de recurso.

Talvez seja interessante excluir a pasta do projeto do computador local.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um cenário de ponta a ponta que mostra os Gêmeos Digitais do Azure controlados por dados dinâmicos do dispositivo.

Em seguida, comece a examinar a documentação de conceito para saber mais sobre os elementos com os quais você trabalhou no tutorial:

> [!div class="nextstepaction"]
> [*Conceitos: modelos personalizados*](concepts-models.md)