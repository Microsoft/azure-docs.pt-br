---
title: Enviar notificações por push com os Hubs de Notificação do Azure e o Bing Spatial Data | Microsoft Docs
description: Neste tutorial, você aprenderá a entregar notificações por push baseadas na localização com os Hubs de Notificação do Azure e o Bing Spatial Data.
services: notification-hubs
documentationcenter: windows
keywords: push notifications,push notifications
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: f41beea1-0d62-4418-9ffc-c9d70607a1b7
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 51ad23e67a77c28d0ad8a147168a0094f5de1796
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91577995"
---
# <a name="tutorial-send-location-based-push-notifications-with-notification-hubs-and-bing-spatial-data"></a>Tutorial: Enviar notificações por push baseadas em localização com os Hubs de Notificação e o Bing Spatial Data

Neste tutorial, você aprenderá a entregar notificações por push baseadas na localização com os Hubs de Notificação do Azure e o Bing Spatial Data.

Neste tutorial, você deve executar as seguintes etapas:

> [!div class="checklist"]
> * Configurar a fonte de dados
> * Configurar o aplicativo UWP
> * Configurar o back-end
> * Enviar notificações por push de teste no aplicativo da UWP (Plataforma Universal do Windows)

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**. Caso você não tenha uma assinatura do Azure, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* [Visual Studio 2015 Atualização 1](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) ou posterior ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409)).
* Versão mais recente do [SDK do Azure](https://azure.microsoft.com/downloads/).
* [Conta do Centro de Desenvolvimento do Bing Mapas](https://www.bingmapsportal.com/) (você pode criá-la gratuitamente e associá-la à sua conta da Microsoft).

## <a name="set-up-the-data-source"></a>Configurar a fonte de dados

1. Faça logon no [Centro de Desenvolvimento do Bing Mapas](https://www.bingmapsportal.com/).
2. Na barra de navegação superior, selecione **Fontes de dados** e selecione **Gerenciar Fontes de Dados**.

    ![Captura de tela do Centro de Desenvolvimento do Bing Mapas, na página gerenciar fontes de dados, com a opção Carregar dados como uma fonte de dados delineada em vermelho.](./media/notification-hubs-geofence/bing-maps-manage-data.png)
3. Se você não tiver uma fonte de dados, você verá um link para criar uma fonte de dados. Selecione **Carregar os dados como uma fonte de dados**. Você também pode usar o menu **Fontes de dados** > **Carregar dados**.

    ![Captura de tela da caixa de diálogo Carregar uma fonte de dados.](./media/notification-hubs-geofence/bing-maps-create-data.png)
4. Crie um arquivo `NotificationHubsGeofence.pipe` no disco rígido com o seguinte conteúdo: Neste tutorial, use um arquivo de exemplo baseado em pipe que enquadra uma área da orla marítima de São Francisco:

    ```text
    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    ```

    O arquivo de pipe representa esta entidade:

    ![Captura de tela de um mapa da orla marítima de São Francisco (EUA) com um polígono vermelho delineando uma área dos píeres.](./media/notification-hubs-geofence/bing-maps-geofence.png)
5. Na página **Carregar uma fonte de dados**, execute as seguintes ações:
   1. Selecione **pipe** para **Formato de dados**.
   2. Procure e selecione o arquivo `NotificationHubGeofence.pipe` que você criou na etapa anterior.
   3. Selecione o botão **Carregar**.

      > [!NOTE]
      > Poderá ser solicitada uma nova chave para a **Chave Mestra**, que é diferente da **Chave de Consulta**. Basta criar uma nova chave por meio do painel e atualizar a página de carregamento da fonte de dados.
6. Depois que você carregar o arquivo de dados, você deve certificar-se de que publicou a fonte de dados. Selecione **Fontes de dados** -> **Gerenciar Fontes de Dados** como você fez antes.
7. Selecione a fonte de dados na lista e escolha **Publicar** na coluna **Ações**.

    ![Captura de tela do Centro de Desenvolvimento do Bing Mapas, na página gerenciar fontes de dados, com a guia Dados com Codificação Geográfica selecionada e a opção Publicar delineada em vermelho.](./media/notification-hubs-geofence/publish-button.png)
8. Altere para a guia **Fontes de Dados Publicadas** e confirme se você vê sua fonte de dados na lista.

    ![Captura de tela do Centro de Desenvolvimento do Bing Mapas na página Gerenciar Fontes de Dados com a guia Fontes de Dados Publicadas selecionada.](./media/notification-hubs-geofence/bing-maps-published-data.png)
9. Selecione **Editar**. Você vê (em um relance) locais que você introduziu nos dados.

    ![Captura de tela da página Editar dados de entidade, mostrando um mapa da parte ocidental dos Estados Unidos e um ponto magenta sobre a orla marítima de São Francisco.](./media/notification-hubs-geofence/bing-maps-data-details.png)

    Neste ponto, o portal não mostrará os limites geográficos que criamos. Tudo o que você precisa é de uma confirmação de que o local especificado está na vizinhança certa.
10. Agora você tem todos os requisitos da fonte de dados. Para obter os detalhes na URL de solicitação para a chamada à API, no Centro de Desenvolvimento do Bing Mapas, escolha **Fontes de dados** e selecione **Informações da Fonte de Dados**.

    ![Captura de tela do Centro de Desenvolvimento do Bing Mapas na página de Informações da fonte de dados.](./media/notification-hubs-geofence/bing-maps-data-info.png)

    A **URL de consulta** é o ponto de extremidade no qual você pode executar consultas para verificar se o dispositivo ainda está dentro dos limites de um local ou não. Para realizar essa verificação, simplesmente execute uma chamada GET na URL de consulta, com os seguintes parâmetros anexados:

    ```text
    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY
    ```

    O Bing Mapas executa automaticamente os cálculos para ver se o dispositivo está dentro do geofence. Depois de executar a solicitação por meio de um navegador (ou cURL), você obterá uma resposta JSON padrão:

    ![Captura de tela da resposta JSON padrão.](./media/notification-hubs-geofence/bing-maps-json.png)

    Essa resposta só acontece quando o ponto está realmente dentro dos limites designados. Caso contrário, você obterá um bucket vazio de **resultados**:

    ![Captura de tela de uma resposta JSON com um bucket de resultados vazio.](./media/notification-hubs-geofence/bing-maps-nores.png)

## <a name="set-up-the-uwp-application"></a>Configurar o aplicativo UWP

1. No Visual Studio, inicie um novo projeto do tipo **Aplicativo em Branco (Universal do Windows)** .

    ![Captura de tela de uma caixa de diálogo de Novo Projeto do Visual Studio, com a opção de Aplicativo em Branco (Universal do Windows) para Visual C# realçada.](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

    Uma vez concluída a criação do projeto, você deverá ter o agente para o próprio aplicativo. Agora vamos configurar tudo para a infraestrutura de isolamento geográfico. Como você vai usar os serviços do Bing para essa solução, há um ponto de extremidade de API REST público que permite consultar quadros de local específico:

    ```text
    http://spatial.virtualearth.net/REST/v1/data/
    ```
    Especifique os parâmetros a seguir para fazer isso funcionar:

   * **ID da Fonte de Dados** e **Nome da Fonte de Dados** – na API do Bing Mapas, as fontes de dados contêm diversos metadados classificados, como locais e horas comerciais de operação.  
   * **Nome da Entidade** – a entidade que você deseja usar como um ponto de referência para a notificação.
   * **Chave de API do Bing Mapas** – a chave que você obteve anteriormente quando criou a conta do Centro de Desenvolvimento do Bing.

     Agora que a fonte de dados está pronta, você pode começar a trabalhar no aplicativo UWP.
2. Habilite serviços de localização para o seu aplicativo. Abra o arquivo `Package.appxmanifest` no **Gerenciador de Soluções**.

    ![Captura de tela de Gerenciador de Soluções, com o arquivo Package.appxmanifest realçado.](./media/notification-hubs-geofence/vs-package-manifest.png)
3. Na guia de propriedades do pacote que acabou de abrir, alterne para a guia **Recursos** e selecione **Local**.

    ![Captura de tela da caixa de diálogo Propriedades do Pacote, mostrando a guia Funcionalidades com a opção Localização realçada.](./media/notification-hubs-geofence/vs-package-location.png)
4. Crie uma nova pasta na solução denominada `Core` e adicione um novo arquivo a ela, chamado `LocationHelper.cs`:

    ![Captura de tela do Gerenciador de Soluções, com a nova pasta Núcleo realçada.](./media/notification-hubs-geofence/vs-location-helper.png)

    A classe `LocationHelper` tem um código para obter o local do usuário por meio da API do sistema:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }
    ```

    Para saber mais sobre como obter o local do usuário em aplicativos UWP, confira[Obter a localização do usuário](/windows/uwp/maps-and-location/get-location).
5. Para verificar se a aquisição da localização está funcionando, abra o lado do código da página principal (`MainPage.xaml.cs`). Criar um novo manipulador de eventos para o evento `Loaded` no construtor `MainPage`.

    ```csharp
    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }
    ```

    A implementação do manipulador de eventos é a seguinte:

    ```csharp
    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }
    ```
6. Executar o aplicativo e permitir que ele acesse seu local.

    ![Captura de tela da caixa de diálogo Permitir que os Hubs de Notificação Limitem Geograficamente o acesso à sua localização.](./media/notification-hubs-geofence/notification-hubs-location-access.png)
7. Assim que o aplicativo for iniciado, você deverá ser capaz de ver as coordenadas na janela **Saída** :

    ![Captura de tela da Janela de Saída exibindo as coordenadas.](./media/notification-hubs-geofence/notification-hubs-location-output.png)

    Agora você sabe que a aquisição de localização funciona, você pode remover o manipulador de eventos carregado se desejar, porque você não o usará mais.
8. A próxima etapa é capturar as alterações de localização. Na classe `LocationHelper`, adicione o manipulador de eventos para `PositionChanged`:

    ```csharp
    geolocator.PositionChanged += Geolocator_PositionChanged;
    ```

    A implementação mostra as coordenadas de localização na janela **Saída**:

    ```csharp
    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }
    ```

## <a name="set-up-the-backend"></a>Configurar o back-end

1. Baixe o [exemplo de back-end .NET do GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).
2. Quando o download for concluído, abra a pasta `NotifyUsers` e depois o arquivo `NotifyUsers.sln` no Visual Studio.
3. Defina o projeto `AppBackend` como o **Projeto de Inicialização** e inicie-o.

    ![Captura de tela do menu de clique com o botão direito do mouse da solução com a opção Definir como Projeto de Inicialização realçada.](./media/notification-hubs-geofence/vs-startup-project.png)

    O projeto já está configurado para enviar notificações por push para dispositivos de destino e, portanto, você precisa fazer somente duas coisas – especificar a cadeia de conexão correta para o hub de notificação e adicionar identificação de limite para enviar a notificação somente quando o usuário estiver dentro do limite geográfico.

4. Para configurar a cadeia de conexão, abra `Notifications.cs` na pasta `Models`. A função `NotificationHubClient.CreateClientFromConnectionString` deve conter as informações sobre o hub de notificação que você pode obter no [portal do Azure](https://portal.azure.com) (examine a página **Políticas de Acesso** em **Configurações**). Salve o arquivo de configuração atualizado.
5. Crie um modelo para o resultado da API do Bing Mapas. A maneira mais fácil de fazer isso é abrir a pasta `Models` e escolher **Adicionar** > **Classe**. Nomeie-o `GeofenceBoundary.cs`. Depois, copie o JSON da resposta de API que você obteve na primeira seção. No Visual Studio, use **Editar** > **Colar especial** > **Colar JSON como Classes**.

    Dessa forma, você garante que o objeto será desserializado exatamente como foi pretendido. O conjunto de classes resultante deve ter a seguinte aparência:

    ```csharp
    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }
    ```
6. Em seguida, abra `Controllers` > `NotificationsController.cs`. Atualize a chamada Post para levar em consideração a latitude e a longitude do destino. Para isso, adicione duas cadeias de caracteres à assinatura da função – `latitude` e `longitude`.

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)
    ```
7. Crie uma nova classe no projeto chamado `ApiHelper.cs` – você a usa para a conexão com o Bing para criarmos pontos de verificação em interseções de limite. Implemente uma função `IsPointWithinBounds` conforme mostrado no código a seguir:

    ```csharp
    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }
    ```

    > [!IMPORTANT]
    > Substitua o ponto de extremidade de API pela URL de consulta obtida anteriormente do Centro de Desenvolvimento do Bing (o mesmo se aplica à chave de API).

    Se houver resultados para a consulta, isso significa que o ponto especificado está nos limites da delimitação geográfica e, portanto, a função retorna `true`. Se não houver nenhum resultado, o Bing está dizendo que o ponto está fora do quadro de pesquisa e, portanto, a função retorna `false`.
8. Em `NotificationsController.cs`, crie uma verificação logo antes da instrução switch:

    ```csharp
    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }
    ```

## <a name="test-push-notifications-in-the-uwp-app"></a>Teste de notificações por push no aplicativo UWP

1. No aplicativo UWP, agora você deve conseguir testar as notificações. Na classe `LocationHelper`, crie uma nova função – `SendLocationToBackend`:

    ```csharp
    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }
    ```

    > [!NOTE]
    > Defina o `POST_URL` para o local do seu aplicativo web implantado. Por enquanto, é possível executá-lo localmente, mas à medida que você trabalhar na implantação de uma versão pública, precisará hospedá-lo em um provedor externo.
2. Registre o aplicativo UWP para notificações por push. No Visual Studio, escolha **Projeto** > **Loja** > **Associar aplicativo à loja**.

    ![Captura de tela do menu de clique com o botão direito do mouse da solução com as opções Loja e Associar o Aplicativo à Loja realçadas.](./media/notification-hubs-geofence/vs-associate-with-store.png)
3. Depois que você entrar em sua conta de desenvolvedor, selecione um aplicativo existente ou crie um novo e associe o pacote a ele.
4. Vá para o Centro de Desenvolvimento e abra o aplicativo que você criou. Escolha **Serviços** > **Notificações por Push** > **Site do Live Services**.

    ![Captura de tela do Centro de Desenvolvimento do Windows exibindo a página Notificações por push com o site do Live Services realçado.](./media/notification-hubs-geofence/ms-live-services.png)
5. No site, anote o **Segredo do Aplicativo** e o **SID do Pacote**. Você precisa deles no portal do Azure – abra seu hub de notificação, escolha **Configurações** > **Serviços de Notificação** > **Windows (WNS)** e insira as informações nos campos obrigatórios.

    ![Captura de tela mostrando a página de Configurações com as opções WNS (Notification Services e Windows) realçadas e os valores de Chave de Segurança e SID do Pacote preenchidos.](./media/notification-hubs-geofence/notification-hubs-wns.png)
6. Escolha **Salvar**.
7. Abra **Referências** no **Gerenciador de Soluções** e selecione **Gerenciar Pacotes NuGet**. Adicione uma referência à **biblioteca gerenciada do Barramento de Serviço do Microsoft Azure** – basta procurar `WindowsAzure.Messaging.Managed` e adicioná-lo ao seu projeto.

    ![Captura de tela da caixa de diálogo Gerenciar Pacotes NuGet com o pacote WindowsAzure.Messaging.Managed realçado.](./media/notification-hubs-geofence/vs-nuget.png)
8. Para fins de teste, crie o manipulador de eventos `MainPage_Loaded` novamente e adicionar este snippet de código a ele:

    ```csharp
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }
    ```

    O código registra o aplicativo no hub de notificação. Você está pronto!
9. Em `LocationHelper`, no manipulador `Geolocator_PositionChanged`, você pode adicionar uma parte do código de teste que impõe a colocação da localização dentro do limite geográfico:

    ```csharp
    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");
    ```

10. Como você não está passando as coordenadas reais (que podem não estar nos limites no momento) e como estamos usando valores predefinidos de teste, você verá uma notificação sobre atualização:

    ![Captura de tela de uma área de trabalho do Windows exibindo a mensagem TESTE.](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

## <a name="next-steps"></a>Próximas etapas

Existem algumas etapas que talvez você precise seguir para que a solução esteja pronta para produção.

1. Primeiro, você precisa garantir que os limites geográficos sejam dinâmicos. Isso requer algum trabalho extra com a API do Bing para poder carregar novos limites na fonte de dados existente. Para obter mais informações, consulte [Documentação da API de Serviços de Dados Espaciais do Bing](/bingmaps/spatial-data-services/).
2. Segundo, como você está trabalhando para garantir que a entrega seja feita aos participantes certos, talvez você queira usar a [marcação](notification-hubs-tags-segment-push-message.md)para tê-los como destino.

A solução mostrada neste tutorial descreve um cenário em que você pode ter uma ampla variedade de plataformas de destino e, portanto, não limita a delimitação geográfica a recursos específicos do sistema. Dito isso, a Plataforma Universal do Windows oferece recursos para [detectar delimitações geográficas prontas](/windows/uwp/maps-and-location/set-up-a-geofence).
