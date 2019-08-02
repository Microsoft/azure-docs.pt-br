---
title: Gerenciamento de registros
description: Este tópico explica como registrar dispositivos em hubs de notificação para receber notificações por push.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.author: jowargo
ms.date: 04/08/2019
ms.openlocfilehash: fffa6784702f239e0af0e9e88a4b9937d20b86ed
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67488641"
---
# <a name="registration-management"></a>Gerenciamento de registros

## <a name="overview"></a>Visão geral

Este tópico explica como registrar dispositivos em hubs de notificação para receber notificações por push. O tópico descreve em alto nível os registros e apresenta os dois padrões principais para registro de dispositivos: registro diretamente do dispositivo para o hub de notificação e registro por meio de um back-end de aplicativo.

## <a name="what-is-device-registration"></a>O que é o registro de dispositivos

O registro de dispositivos com um Hub de Notificação é realizado usando um **Registro** ou uma **Instalação**.

### <a name="registrations"></a>Registros

Um registro associa o identificador PNS (Serviço de Notificação de Plataforma) de um dispositivo com marcas e, possivelmente, um modelo. O identificador PNS pode ser um ChannelURI, um token do dispositivo ou uma ID de registro de FCM. As marcas são usadas para direcionar notificações para o conjunto correto de identificadores de dispositivos. Para saber mais, veja [Expressões de marca e de roteamento](notification-hubs-tags-segment-push-message.md). Os modelos são usados para implementar transformações por registro. Para saber mais, veja [Modelos](notification-hubs-templates-cross-platform-push-messages.md).

> [!NOTE]
> Os Hubs de notificação do Azure dá suporte a um máximo de 60 marcas por dispositivo.

### <a name="installations"></a>Instalações

Uma instalação é um registro aprimorado que inclui um conjunto de propriedades relacionadas ao envio por push. Essa é a abordagem mais recente e adequada para registrar seus dispositivos. No entanto, não há suporte pelo SDK do .NET do lado do cliente ([SDK do Hub de Notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) até o momento.  Isso significa que, se você estiver registrando no próprio dispositivo cliente, precisará usar a abordagem da [API REST de Hubs de Notificação](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) para dar suporte às instalações. Se você estiver usando um serviço de back-end, deverá ser capaz de usar o [SDK do Hub de Notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

A seguir, algumas vantagens importantes do uso de instalações:

- A criação ou atualização de uma instalação é totalmente idempotente. Portanto, você pode repetir sem as preocupações com registros duplicados.
- O modelo de instalação dá suporte a um formato de marca especial (`$InstallationId:{INSTALLATION_ID}`) que permite enviar uma notificação diretamente para o dispositivo específico. Por exemplo, se o código do aplicativo define uma ID de instalação do `joe93developer` para esse dispositivo em particular, um desenvolvedor pode direcionar a este dispositivo ao enviar uma notificação para o `$InstallationId:{joe93developer}` marca. Isso permite que você direcione um dispositivo específico sem precisar fazer nenhuma codificação adicional.
- O uso de instalações também permite que você faça atualizações parciais no registro. A atualização parcial de uma instalação é solicitada com um método PATCH usando o [padrão JSON-Patch](https://tools.ietf.org/html/rfc6902). Isso é útil quando você deseja atualizar marcas no registro. Não é necessário obter todo o registro e reenviar todas as marcas anteriores novamente.

Uma instalação pode conter as seguintes propriedades. Para obter uma listagem completa das propriedades da instalação, consulte [Criar ou substituir uma instalação com API REST](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) ou [Propriedades da Instalação](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation).

```json
// Example installation format to show some supported properties
{
    installationId: "",
    expirationTime: "",
    tags: [],
    platform: "",
    pushChannel: "",
    ………
    templates: {
        "templateName1" : {
            body: "",
            tags: [] },
        "templateName2" : {
            body: "",
            // Headers are for Windows Store only
            headers: {
                "X-WNS-Type": "wns/tile" }
            tags: [] }
    },
    secondaryTiles: {
        "tileId1": {
            pushChannel: "",
            tags: [],
            templates: {
                "otherTemplate": {
                    bodyTemplate: "",
                    headers: {
                        ... }
                    tags: [] }
            }
        }
    }
}
```

> [!NOTE]
> Por padrão, os registros e as instalações não expiram.

Os registros e instalações devem conter um identificador PNS válido para cada dispositivo/canal. Como os identificadores PNS só podem ser obtidos em um aplicativo cliente no dispositivo, um padrão é registrar diretamente no dispositivo com o aplicativo cliente. Por outro lado, as considerações de segurança e a lógica de negócios relacionadas às marcas podem exigir o gerenciamento do registro do dispositivo no back-end do aplicativo.

> [!NOTE]
> A API de instalações não oferece suporte ao serviço do Baidu (embora a API de registros faz). 

### <a name="templates"></a>Modelos

Se você quiser usar [Modelos](notification-hubs-templates-cross-platform-push-messages.md), a instalação do dispositivo também armazenará todos os modelos associados a esse dispositivo em um formato JSON (veja o exemplo acima). Os nomes de modelo ajudam a direcionar a modelos diferentes do mesmo dispositivo.

Observe que cada nome de modelo é mapeado para um corpo de modelo e um conjunto opcional de marcas. Além disso, cada plataforma pode ter propriedades adicionais de modelo. Para a Windows Store (usando WNS) e o Windows Phone 8 (usando MPNS), um conjunto adicional de cabeçalhos pode fazer parte do modelo. No caso dos APNs, você pode definir uma propriedade de expiração para uma constante ou para uma expressão de modelo. Para obter uma lista completa de propriedades de instalação, confira o tópico [Criar ou substituir uma instalação com REST](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) .

### <a name="secondary-tiles-for-windows-store-apps"></a>Blocos secundários para aplicativos da Windows Store

Para aplicativos cliente da Windows Store, o envio de notificações para blocos secundários é igual ao envio delas ao bloco primário. Isso também tem suporte nas instalações. Os blocos secundários têm um ChannelUri diferente, o qual o SDK no aplicativo cliente trata de forma transparente.

O dicionário SecondaryTiles usa o mesmo TileId usado para criar o objeto SecondaryTiles em seu aplicativo da Windows Store. Assim como acontece com o ChannelUri primário, os ChannelUris de blocos secundários podem ser alterados a qualquer momento. Para manter as instalações no hub de notificação atualizadas, o dispositivo deve atualizá-las com os ChannelUris atuais dos blocos secundários.

## <a name="registration-management-from-the-device"></a>Gerenciamento de registro do dispositivo

Ao gerenciar o registro de dispositivos de aplicativos cliente, o back-end é responsável apenas pelo envio de notificações. Os aplicativos cliente mantêm os identificadores PNS atualizados e registram marcas. A figura a seguir ilustra esse padrão.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Primeiro, o dispositivo recupera o identificador PNS do PNS e registra diretamente com o hub de notificação. Após a conclusão do registro, o back-end do aplicativo pode enviar uma notificação visando esse registro. Para saber mais sobre como enviar notificações, veja [Expressões de marca e de roteamento](notification-hubs-tags-segment-push-message.md).

Nesse caso, você usará apenas direitos de Escuta para acessar os hubs de notificação do dispositivo. Para saber mais, consulte [Segurança](notification-hubs-push-notification-security.md).

O registro do dispositivo é o método mais simples, mas tem algumas desvantagens:

- um aplicativo cliente só pode atualizar suas marcas quando o aplicativo está ativo. Por exemplo, se um usuário tiver dois dispositivos que registram marcas relacionadas a equipes esportivas, quando o primeiro dispositivo registrar-se para uma marca adicional (por exemplo, Seahawks), o segundo dispositivo não receberá notificações sobre o Seahawks até que o aplicativo no segundo dispositivo seja executado uma segunda vez. Normalmente, quando as marcas são afetadas por vários dispositivos, o gerenciamento das marcas do back-end é uma opção desejável.
- Como os aplicativos podem ser violados, a proteção do registro para marcas específicas exige um cuidado extra, conforme explicado na seção “Segurança no nível da marca”.

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Exemplo de código para registrar com um hub de notificação de um dispositivo usando uma instalação

Neste momento, isso tem suporte somente com o uso da [API REST dos Hubs de Notificação](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation).

Você também pode usar o método PATCH com o [padrão JSON-Patch](https://tools.ietf.org/html/rfc6902) para atualizar a instalação.

```
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }
}

private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
        string hubName, string listenConnectionString)
{
    if (deviceInstallation.installationId == null)
        return HttpStatusCode.BadRequest;

    // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
    ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
    string hubResource = "installations/" + deviceInstallation.installationId + "?";
    string apiVersion = "api-version=2015-04";

    // Determine the targetUri that we will sign
    string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

    //=== Generate SaS Security Token for Authorization header ===
    // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
    string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

    using (var httpClient = new HttpClient())
    {
        string json = JsonConvert.SerializeObject(deviceInstallation);

        httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

        var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
        return response.StatusCode;
    }
}

var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

string installationId = null;
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored an installation id in application data, create and store as application data.
if (!settings.ContainsKey("__NHInstallationId"))
{
    installationId = Guid.NewGuid().ToString();
    settings.Add("__NHInstallationId", installationId);
}

installationId = (string)settings["__NHInstallationId"];

var deviceInstallation = new DeviceInstallation
{
    installationId = installationId,
    platform = "wns",
    pushChannel = channel.Uri,
    //tags = tags.ToArray<string>()
};

var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                    "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

if (statusCode != HttpStatusCode.Accepted)
{
    var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
else
{
    var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Exemplo de código para registrar com um hub de notificação de um dispositivo usando um registro

Esses métodos criam ou atualizam um registro para o dispositivo no qual são chamados. Isso significa que, para atualizar o indicador ou as marcas, você deve substituir todo o registro. Lembre-se de que os registros são temporários e, portanto, você sempre deverá ter um armazenamento confiável com as marcas atuais exigidas por um dispositivo específico.

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device id from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration id in device
// storage. Then when the app starts, you can check if a registration id already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration id in application data, store in application data.
if (!settings.ContainsKey("__NHRegistrationId"))
{
    // make sure there are no existing registrations for this push handle (used for iOS and Android)    
    string newRegistrationId = null;
    var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
    foreach (RegistrationDescription registration in registrations)
    {
        if (newRegistrationId == null)
        {
            newRegistrationId = registration.RegistrationId;
        }
        else
        {
            await hub.DeleteRegistrationAsync(registration);
        }
    }

    newRegistrationId = await hub.CreateRegistrationIdAsync();

    settings.Add("__NHRegistrationId", newRegistrationId);
}

string regId = (string)settings["__NHRegistrationId"];

RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
registration.RegistrationId = regId;
registration.Tags = new HashSet<string>(YourTags);

try
{
    await hub.CreateOrUpdateRegistrationAsync(registration);
}
catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
{
    settings.Remove("__NHRegistrationId");
}
```

## <a name="registration-management-from-a-backend"></a>Gerenciamento de registros de um back-end

O gerenciamento de registros do back-end exige a produção adicional de código. O aplicativo do dispositivo deve fornecer o indicador PNS atualizado para o back-end sempre que o aplicativo for iniciado (junto com marcas e modelos) e o back-end deve atualizar esse identificador no hub de notificação. A figura a seguir ilustra esse design.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

As vantagens do gerenciamento de registros a partir do back-end incluem a capacidade de modificar marcas para os registros, mesmo quando o aplicativo correspondente no dispositivo estiver inativo, e a capacidade de autenticar o aplicativo cliente antes de adicionar uma marca a seu registro.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Exemplo de código para registrar com um hub de notificação de um back-end usando uma instalação

O dispositivo do cliente ainda obtém seu identificador PNS e as propriedades de instalação relevantes como fazia antes, e chama uma API personalizada no back-end que pode executar o registro e autorizar marcas etc. O back-end pode aproveitar o [SDK do Hub de Notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Você também pode usar o método PATCH com o [padrão JSON-Patch](https://tools.ietf.org/html/rfc6902) para atualizar a instalação.

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// Custom API on the backend
public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
{

    Installation installation = new Installation();
    installation.InstallationId = deviceUpdate.InstallationId;
    installation.PushChannel = deviceUpdate.Handle;
    installation.Tags = deviceUpdate.Tags;

    switch (deviceUpdate.Platform)
    {
        case "mpns":
            installation.Platform = NotificationPlatform.Mpns;
            break;
        case "wns":
            installation.Platform = NotificationPlatform.Wns;
            break;
        case "apns":
            installation.Platform = NotificationPlatform.Apns;
            break;
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }


    // In the backend we can control if a user is allowed to add tags
    //installation.Tags = new List<string>(deviceUpdate.Tags);
    //installation.Tags.Add("username:" + username);

    await hub.CreateOrUpdateInstallationAsync(installation);

    return Request.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Exemplo de código para registrar com um hub de notificação de um dispositivo usando uma ID de registro

No back-end do aplicativo, você pode executar operações básicas de CRUDS nos registros. Por exemplo:

```
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by id
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

O back-end deve manipular a simultaneidade entre as atualizações do registro. O Barramento de Serviço oferece um controle de simultaneidade otimista para gerenciamento de registro. No nível HTTP, isso é implementado com o uso de ETag nas operações de gerenciamento de registro. Esse recurso é usado de forma transparente pelos SDKs da Microsoft, que lançam uma exceção se uma atualização for rejeitada por motivos de simultaneidade. O back-end é responsável por manipular essas exceções e tentar atualizar novamente, se isso for necessário.
