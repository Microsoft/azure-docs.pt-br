---
title: Criar um Hub IoT do Azure usando a API REST do provedor de recursos | Microsoft Docs
description: Saiba como usar a API REST do provedor de recursos C# para criar e gerenciar um hub IoT programaticamente.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 2f393701b97be76acab49a627a195b337018fa29
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92144432"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Criar um Hub IoT usando a API REST do provedor de recursos (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Você pode usar a [API REST do provedor de recursos do Hub IoT](/rest/api/iothub/iothubresource) para criar e gerenciar Hubs IoT do Azure de forma programática. Este tutorial mostra como usar a API de REST do Provedor de Recursos de Hub do IoT para criar um hub IoT a partir de um programa C#.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, você precisará do seguinte:

* Visual Studio.

* Uma conta ativa do Azure. Se você não tem uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* [Azure PowerShell 1.0](/powershell/azure/install-Az-ps) ou posterior.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparar seu projeto do Visual Studio

1. No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# usando o modelo de projeto **Aplicativo do Console (.NET Framework)**. Nomeie o projeto **CreateIoTHubREST**.

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e clique em **Gerenciar Pacotes NuGet**.

3. No Gerenciador de Pacotes NuGet, marque **Incluir pré-lançamento** e na página **Navegar** procure **Microsoft.Azure.Management.ResourceManager**. Selecione o pacote, clique em **Instalar**, em **Revisar alterações** clique em **OK** e em **Aceito** para aceitar as licenças.

4. No Gerenciador de Pacotes do NuGet, procure **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Clique em **Instalar**, em **Revisar alterações**, clique em **OK** e em **Aceito** para aceitar a licença.

5. Em Program.cs, substitua as instruções **using** existentes pelo código a seguir:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. Em Program.cs, adicione as variáveis estáticas a seguir, substituindo os valores de espaço reservado. Você fez uma anotação de **ApplicationId**, **SubscriptionId**, **TenantId** e **Password** anteriormente neste tutorial. **Nome do grupo de recursos** é o nome do grupo de recursos que você usa ao criar o Hub IoT. Use um grupo de recursos existente ou novo. **Nome do Hub IoT** é o nome do Hub IoT que você criou, como **MyIoTHub**. O nome do hub IoT deve ser globalmente exclusivo. **Nome da implantação** é um nome para a implantação, como **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
   
    [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Use a API REST do provedor de recursos para criar um Hub IoT

Use a [API REST do provedor de recursos do Hub IoT](/rest/api/iothub/iothubresource) para criar um novo Hub IoT em seu grupo de recursos. Você também pode usar a API REST do provedor de recursos para fazer alterações em um Hub IoT existente.

1. Adicione o seguinte método ao Program.cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Adicione o seguinte código ao método **CreateIoTHub**. Esse código cria um objeto **HttpClient** com o token de autenticação nos cabeçalhos:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Adicione o seguinte código ao método **CreateIoTHub**. Esse código descreve o Hub IoT a ser criado e gera uma representação JSON. Para obter a lista atual de locais que dão suporte ao Hub IoT, consulte [status do Azure](https://azure.microsoft.com/status/):

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Adicione o seguinte código ao método **CreateIoTHub**. Esse código envia a solicitação REST ao Azure. Em seguida, o código verifica a resposta e recupera a URL, que você pode usar para monitorar o estado da tarefa de implantação:

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Adicione o código a seguir ao final do método **CreateIoTHub**. Esse código usa o endereço **asyncStatusUri** recuperado na etapa anterior para aguardar a conclusão da implantação:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Adicione o código a seguir ao final do método **CreateIoTHub**. Este código recupera as chaves do Hub IoT criadas e impressas no console:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Compilar e executar o aplicativo

Agora, você pode concluir o aplicativo chamando o método **CreateIoTHub** antes de compilá-lo e de executá-lo.

1. Adicione o seguinte código ao final do método **Main** :

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Clique em **Criar** e **Compilar Solução**. Corrija todos os erros.

3. Clique em **Depurar** e, em seguida, **Iniciar Depuração** para executar o aplicativo. Pode levar vários minutos para que a implantação seja executada.

4. Para verificar seu aplicativo adicionado ao novo hub IoT, visite o [Portal do Azure](https://portal.azure.com/) e exiba sua lista de recursos. Como alternativa, use o cmdlet **Get-AzResource** do PowerShell.

> [!NOTE]
> Este aplicativo de exemplo adiciona um Hub IoT Standard S1 pelo qual você será cobrado. Quando tiver terminado, você poderá excluir o Hub IoT por meio da [portal do Azure](https://portal.azure.com/) ou usando o cmdlet **Remove-AzResource** do PowerShell quando tiver terminado.

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou um Hub IoT usando a API REST do provedor de recursos, convém explorar ainda mais:

* Leia sobre as funcionalidades da [API REST do provedor de recursos Hub IoT](/rest/api/iothub/iothubresource).

* Leia a [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md) para saber mais sobre os recursos do Azure Resource Manager.

Para saber mais sobre como desenvolver para o Hub IoT, veja os seguintes artigos:

* [Introdução ao SDK C](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/quickstart-linux.md)