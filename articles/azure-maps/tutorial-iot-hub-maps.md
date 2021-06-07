---
title: 'Tutorial: Implementar análise espacial de IoT | Microsoft Azure Mapas'
description: Tutorial sobre como integrar o Hub IoT às APIs de serviço do Microsoft Azure Mapas
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9ebc6e266c93e55bc250e8450356f8b695dd9080
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714984"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>Tutorial: implementar análise espacial de IoT usando os Azure Mapas

Em um cenário de IoT, é comum capturar e acompanhar eventos relevantes que ocorrem no espaço e no tempo. Os exemplos incluem aplicativos de gerenciamento de frota, acompanhamento de ativos, mobilidade e cidade inteligente. Este tutorial guia você por uma solução que, usando as APIs dos Azure Mapas, acompanha a movimentação de aluguel de carros usados.

Neste tutorial, você vai:

> [!div class="checklist"]
> * Criar uma conta de armazenamento do Azure para registrar os dados de acompanhamento de carros.
> * Carregar uma cerca geográfica no serviço Dados dos Azure Mapas (versão prévia) usando a API de Upload de Dados.
> * Criar um hub no Hub IoT do Azure e registrar um dispositivo.
> * Crie uma função no Azure Functions implementando a lógica de negócios com base na análise espacial do Azure Mapas.
> * Assinar eventos de telemetria de dispositivo de IoT usando a função do Azure por meio da Grade de Eventos do Azure.
> * Filtrar os eventos de telemetria usando o roteamento de mensagens do Hub IoT.

## <a name="prerequisites"></a>Pré-requisitos

1. Entre no [portal do Azure](https://portal.azure.com).

2. [Crie uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura. Para saber mais, confira [Gerenciar a autenticação nos Azure Mapas](how-to-manage-authentication.md).

4. [Crie um grupo de recursos](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). Neste tutorial, nomeamos nosso grupo de recursos *ContosoRental*, mas você pode escolher qualquer nome que desejar.

5. Baixe o [projeto rentalCarSimulation em C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

Este tutorial usa o aplicativo [Postman](https://www.postman.com/), mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="use-case-rental-car-tracking"></a>Caso de uso: acompanhamento de carros de aluguel

Digamos que uma empresa de aluguel de carros deseja registrar informações de localização, distância viajada e estado de funcionamento dos próprios carros de aluguel. A empresa também deseja armazenar essas informações sempre que um carro deixa a região geográfica autorizada correta.

Os carros de aluguel são equipados com dispositivos IoT que enviam regularmente dados de telemetria ao Hub IoT. A telemetria inclui a localização atual e indica se o motor do carro está ligado. O esquema de localização do dispositivo adere ao [esquema de Plug and Play para dados geoespaciais](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md) de IoT. O esquema de telemetria do dispositivo do carro alugado é semelhante ao seguinte código JSON:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

Neste tutorial, apenas um veículo será rastreado. Depois de configurar os serviços do Azure, você precisará baixar o [projeto rentalCarSimulation em C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) para executar o simulador de veículo. O processo inteiro, desde o evento até a execução da função, é resumido nas seguintes etapas:

1. O dispositivo no veículo envia dados telemétricos ao Hub IoT.

2. Se o motor do carro estiver funcionando, o hub publicará os dados de telemetria na Grade de Eventos.

3. Uma função do Azure é disparada devido à respectiva assinatura de evento para eventos de telemetria do dispositivo.

4. A função registra as coordenadas de localização do dispositivo do veículo, a hora do evento e a identificação do dispositivo. Em seguida, ela usa a [API de Obtenção de Cerca Geográfica Espacial](/rest/api/maps/spatial/getgeofence) para determinar se o carro foi dirigido para fora da cerca geográfica. Se ele estiver fora dos limites da cerca geográfica, a função armazenará os dados de localização recebidos do evento em um contêiner de blob. A função também consultará a [Reversão do Endereço de Pesquisa](/rest/api/maps/search/getsearchaddressreverse) para converter a localização da coordenada em um endereço de rua, que ela armazenará com o restante dos dados de localização do dispositivo.

O diagrama a seguir mostra uma visão geral de alto nível do sistema.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Diagrama de visão geral do sistema.":::

A figura a seguir mostra a área de cerca geográfica realçada em azul. A rota do carro de aluguel é indicada por uma linha verde.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Figura mostrando a rota de cerca geográfica.":::

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

Para armazenar dados de acompanhamento de violação de carros, crie uma [conta de armazenamento para uso geral v2](../storage/common/storage-account-overview.md) em seu grupo de recursos. Se você não tiver criado um grupo de recursos, siga as instruções em [Criar um grupo de recursos](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). Neste tutorial, você nomeará seu grupo de recursos como *AluguelContoso*.

Para criar uma conta de armazenamento, siga as instruções em [Criar uma conta de armazenamento](../storage/common/storage-account-create.md?tabs=azure-portal). Neste tutorial, você nomeará a conta de armazenamento como *ArmazenamentoAluguelContoso*, mas você pode nomeá-la como quiser.

Ao criar com sucesso sua conta de armazenamento, você precisa criar um contêiner para armazenar os dados de registro.

1. Abra sua conta de armazenamento recém-criada. Na seção **Informações Gerais**, selecione o link de **Contêineres**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Captura de tela de contêineres de armazenamento de blob.":::

2. No canto superior esquerdo, selecione **+ Container**. Um painel é exibido no lado direito do navegador. Nomeie seu contêiner como *contoso-aluguel-registros* e selecione **Criar**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Captura de tela da criação de um contêiner de blob.":::

3. Acesse o painel de **Chaves de acesso** em sua conta de armazenamento e copie o **Nome da conta de armazenamento** e o valor da **Chave** na seção **key1**. Você precisa de ambos os valores na seção "Criar uma Função do Azure e adicionar uma assinatura de Grade de Eventos".

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Captura de tela do nome e da chave da conta de armazenamento de cópias.":::

## <a name="upload-a-geofence"></a>Carregar uma cerca geográfica

Em seguida, use o [aplicativo Postman](https://www.getpostman.com) para [carregar a cerca geográfica](./geofence-geojson.md) dos Azure Mapas. A cerca geográfica define a área geográfica autorizada para nosso veículo de aluguel. Você usará a cerca geográfica na função do Azure para determinar se um carro se deslocou para fora da área da cerca geográfica.

Siga estas etapas para carregar a cerca geográfica usando a API de Upload de Dados dos Azure Mapas: 

1. Abra o aplicativo Postman e selecione **Nova**. Na janela **Criar**, selecione **Coleção**. Nomeie a coleção e selecione **Criar**.

2. Para criar a solicitação, selecione **Novo** outra vez. Na janela **Criar**, selecione **Solicitação** e nomeie a solicitação. Selecione a coleção que você criou na etapa anterior e clique em **Salvar**.

3. Selecione o método HTTP **POST** na guia do construtor e insira a URL a seguir para carregar a cerca geográfica na API de Upload de Dados. Lembre-se de substituir `{subscription-key}` pela chave da assinatura primária.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    No caminho da URL, o valor `geojson` no parâmetro `dataFormat` representa o formato dos dados sendo carregados.

4. Selecione **Corpo** > **bruto** para o formato de entrada e escolha **JSON** na lista suspensa. [Abra o arquivo de dados JSON](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4) e copie o JSON para a seção do corpo. Selecione **Enviar**.

5. Selecione **Enviar** e aguarde a solicitação ser processada. Depois que a solicitação for concluída, vá para a guia **Cabeçalhos** da resposta. Copie o valor da chave **Local**, que é `status URL`.

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Para verificar o status da chamada à API, crie uma solicitação HTTP **GET** em `status URL`. Você precisará acrescentar sua chave de assinatura primária à URL para autenticação. A solicitação **GET** deve ser semelhante à seguinte URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}
   ```
   
7. Quando a solicitação HTTP **GET** for concluída com êxito, ela retornará um `resourceLocation`. O `resourceLocation` contém o `udid` exclusivo para o conteúdo carregado. Copie essa `udid` para uso posterior neste tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>Criar um hub IoT

O Hub IoT permite comunicação bidirecional segura e confiável entre um aplicativo IoT e os dispositivos gerenciados por ele. Para este tutorial, convém obter informações do seu dispositivo no veículo para determinar a localização do carro alugado. Nesta seção, você criará um hub IoT dentro do grupo de recursos *AluguelContoso*. Esse hub será responsável por publicar nossos eventos de telemetria do dispositivo.

> [!NOTE]
> A capacidade de publicar eventos de telemetria de dispositivo na Grade de Eventos está atualmente em versão prévia. Esse recurso está disponível em todas as regiões, exceto nas seguintes: Leste dos EUA, Oeste dos EUA, Oeste da Europa, Azure Governamental, Azure China 21Vianet e Azure Alemanha.

Para criar um hub IoT no grupo de recursos *AluguelContoso*, siga as etapas em [Criar um hub IoT](../iot-hub/quickstart-send-telemetry-dotnet.md#create-an-iot-hub).

## <a name="register-a-device-in-your-iot-hub"></a>Registrar um dispositivo em seu hub IoT

Os dispositivos não podem se conectar ao hub IoT, a menos que estejam registrados no registro de identidade do hub IoT. Aqui, você criará um único dispositivo com o nome *InVehicleDevice*. Para criar e registrar o dispositivo no hub IoT, siga as etapas em [Registrar um novo dispositivo no hub IoT](../iot-hub/iot-hub-create-through-portal.md#register-a-new-device-in-the-iot-hub). Copie a cadeia de conexão primária do seu dispositivo. Você precisará dela mais tarde.

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>Crie uma função e adicione uma assinatura de Grade de Eventos

O Azure Functions é um serviço de computação sem servidor que permite executar pequenos trechos de código, também chamados de "funções", sem a necessidade de provisionar explicitamente nem gerenciar a infraestrutura de computação. Para saber mais, confira [Azure Functions](../azure-functions/functions-overview.md).

Uma função é disparada por determinado evento. Aqui, você criará uma função que é disparada por um gatilho da Grade de Eventos. Crie a relação entre gatilho e função criando uma assinatura de evento para eventos de telemetria de dispositivo do Hub IoT. Quando ocorre um evento de telemetria de dispositivo, sua função é chamada como um ponto de extremidade e recebe os dados relevantes para o dispositivo que você registrou anteriormente no Hub IoT.

Este é o [código de script C# que sua função conterá](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Agora, configure sua função do Azure.

1. No painel do portal do Azure, selecione **Criar um recurso**. Digite **Aplicativo de Funções** na caixa de texto de pesquisa. Selecione **Aplicativo de Funções** > **Criar**.

1. Na página de criação do **Aplicativo de Funções**, nomeie o aplicativo de funções. Em **Grupo de Recursos**, selecione **ContosoRental** na lista suspensa. Selecione **.NET Core** como a **Pilha de Runtime**. Na parte inferior da página, selecione **A seguir: Hospedagem >** .

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Captura de tela da criação de um aplicativo de funções.":::

1. Para **Conta de armazenamento**, selecione a conta de armazenamento que você criou em [Criar uma conta de armazenamento do Azure](#create-an-azure-storage-account). Selecione **Examinar + criar**.

1. Examine os detalhes do aplicativo de funções e selecione **Criar**.

1. Depois que o aplicativo é criado, você adiciona uma função a ele. Vá para o aplicativo de funções. Selecione o painel **Funções**. Na parte superior da página, selecione **+ Adicionar**. O painel do modelo de função é exibido. Role o painel até encontrar **Gatilho da Grade de Eventos do Azure** e o selecione.

     >[!IMPORTANT]
    > O **Gatilho da Hub de Eventos do Azure** e o **Gatilho da Grade de Eventos do Azure** têm ícones semelhantes. Selecione o modelo de **Gatilho da Grade de Eventos do Azure**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Captura de tela da criação de uma função.":::

1. Dê um nome à função. Neste tutorial, você usará o nome *GetGeoFunction*, mas em geral pode usar o nome que desejar. Selecione **Criar função**.

1. No menu à esquerda, selecione o painel **Código + Teste**. Copie e cole o [script C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) na janela de código.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Cópia/Captura de tela do código colado na janela de funções.":::

1. No código C#, substitua os seguintes parâmetros:
    * Substitua **SUBSCRIPTION_KEY** pela chave de assinatura primária da conta do Azure Mapas.
    * Substitua **UDID** pelo `udid` da cerca geográfica que você carregou em [Carregar uma cerca geográfica](#upload-a-geofence).
    * A função `CreateBlobAsync` no script cria um blob por evento na conta de armazenamento de dados. Substitua **ACCESS_KEY**, **ACCOUNT_NAME** e **STORAGE_CONTAINER_NAME** pela chave de acesso da sua conta de armazenamento, pelo nome da conta e pelo contêiner de armazenamento de dados. Esses valores foram gerados quando você criou sua conta de armazenamento em [Criar uma conta de Armazenamento do Azure](#create-an-azure-storage-account).

1. No menu esquerdo, selecione o painel **Integração**. Selecione **Gatilho da Grade de Eventos** no diagrama. Dê um nome para o gatilho, *eventGridEvent*, e selecione **Criar assinatura de Grade de Eventos**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Captura de tela da adição de assinatura de evento.":::

1. Preencha os detalhes da assinatura. Nomeie a assinatura do evento. Para **Esquema de Evento**, selecione **Esquema de Grade de Eventos**. Para **Tipos de Tópico**, selecione **Contas do Hub IoT do Azure**. Para **Grupo de Recursos**, selecione o grupo de recursos criado no início deste tutorial. Em **Recurso**, selecione o hub IoT criado em "Criar um hub IoT do Azure". Para **Filtrar por Tipos de Evento**, selecione **Telemetria do Dispositivo**.

   Depois de escolher essas opções, você verá o **Tipo de Tópico** se alterar para **Hub IoT**. Para **Nome do Tópico do Sistema**, é possível usar o mesmo nome que o recurso. Por fim, na seção **Detalhes do ponto de extremidade**, clique em **Selecionar um ponto de extremidade**. Aceite todas as configurações e selecione **Confirmar Seleção**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Captura de tela da criação da assinatura de evento.":::

1. Examine suas configurações. Lembre-se de que o ponto de extremidade especifica a função que você criou no início desta seção. Selecione **Criar**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Captura de tela da confirmação de criação da assinatura de evento.":::

1. Agora você está de volta no painel **Editar Gatilho**. Clique em **Salvar**.

## <a name="filter-events-by-using-iot-hub-message-routing"></a>Filtrar eventos usando o roteamento de mensagens do Hub IoT

Quando você adiciona uma assinatura de Grade de Eventos à função do Azure, uma rota de mensagens é criada automaticamente no hub IoT especificado. O roteamento de mensagens permite que você roteie diferentes tipos de dados para vários pontos de extremidade. Por exemplo, você pode rotear mensagens de telemetria do dispositivo, eventos do ciclo de vida do dispositivo e eventos de alteração de dispositivo gêmeo. Para saber mais, confira [Usar o roteamento de mensagens do Hub IoT](../iot-hub/iot-hub-devguide-messages-d2c.md).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Captura de tela do roteamento de mensagens no hub IoT.":::

Em seu cenário de exemplo, você quer receber mensagens apenas quando o carro alugado estiver em movimento. Crie uma consulta de roteamento para filtrar os eventos em que a propriedade `Engine` é igual a **"ON"** . Para criar uma consulta de roteamento, selecione a rota **RouteToEventGrid** e substitua a **Consulta de roteamento** por **"Engine = 'ON'"** . Em seguida, selecione **Salvar**. Agora, o hub IoT apenas publicará a telemetria de dispositivos em que o motor estiver ligado.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Captura de tela da filtragem do roteamento de mensagens.":::

>[!TIP]
>Existem várias maneiras de consultar mensagens IoT de dispositivo para nuvem. Para saber mais sobre a sintaxe de roteamento de mensagens, confira [Roteamento de mensagens do Hub IoT](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

## <a name="send-telemetry-data-to-iot-hub"></a>Enviar dados de telemetria ao Hub IoT

Quando a função do Azure estiver em execução, você poderá enviar dados telemétricos para o hub IoT, que os encaminhará para a Grade de Eventos. Use um aplicativo C# para de simular dados de localização de um dispositivo em um carro de aluguel. Para executar o aplicativo, você precisa do SDK do .NET Core 2.1.0 ou posterior em seu computador de desenvolvimento. Siga estas etapas para enviar dados telemétricos simulados ao hub IoT:

1. Se você ainda não fez isso, baixe o projeto [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) em C#.

2. Abra o arquivo `simulatedCar.cs` em um editor de texto de sua escolha e substitua o valor da `connectionString` pelo que você salvou quando registrou o dispositivo. Salve as alterações no arquivo.

3. Verifique se você tem o .NET Core instalado no computador. Na janela do terminal local, abra a pasta raiz do projeto em C# e execute o seguinte comando a fim de instalar os pacotes necessários para o aplicativo de dispositivo simulado:

    ```cmd/sh
    dotnet restore
    ```

4. No mesmo terminal, execute o seguinte comando para compilar e executar o aplicativo de simulação de carros alugados:

    ```cmd/sh
    dotnet run
    ```


  Seu terminal local deve ser semelhante ao mostrado abaixo.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Captura de tela da saída do terminal.":::

Se você abrir o contêiner de Armazenamento de Blobs agora, verá quatro blobs para locais em que o veículo estava fora da cerca geográfica.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Captura de tela da visualização dos blobs dentro do contêiner.":::

O mapa a seguir mostra quatro pontos de localização de veículos fora da cerca geográfica. Cada localização foi registrada em intervalos de tempo regulares.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Captura de tela do mapa de violação.":::

## <a name="explore-azure-maps-and-iot"></a>Explorar o Azure Mapas e o IoT do Azure

Para explorar as APIs dos Azure Mapas usadas neste tutorial, confira:

* [Obter Reversão do Endereço de Pesquisa](/rest/api/maps/search/getsearchaddressreverse)
* [Obter Cerca Geográfica](/rest/api/maps/spatial/getgeofence)

Para obter uma lista completa das APIs REST do Azure Mapas, confira:

* [APIs REST do Azure Mapas](/rest/api/maps/spatial/getgeofence)

* [IoT Plug and Play](../iot-pnp/index.yml)

Para obter uma lista de dispositivos certificados pelo Azure para IoT, visite:

* [Dispositivos certificados pelo Azure](https://devicecatalog.azure.com/)

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos que exijam limpeza.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como enviar a telemetria do dispositivo para a nuvem e vice-versa, confira:


> [!div class="nextstepaction"]
> [Enviar telemetria por meio de um dispositivo](../iot-hub/quickstart-send-telemetry-dotnet.md)