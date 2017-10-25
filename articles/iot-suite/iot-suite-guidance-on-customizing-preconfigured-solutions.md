---
title: "Personalizando soluções pré-configuradas | Microsoft Docs"
description: "Fornece orientação sobre como personalizar as soluções pré-configuradas do Pacote IoT do Azure."
services: 
suite: iot-suite
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4653ae53-4110-4a10-bd6c-7dc034c293a8
ms.service: iot-suite
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: corywink
ms.openlocfilehash: 110085f8a4ef0b22b1b4982615eabc29487f9413
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="customize-a-preconfigured-solution"></a>Personalizar uma solução pré-configurada

As soluções pré-configuradas fornecidas com o Pacote IoT do Azure demonstram os serviços no pacote trabalhando juntos para fornecer uma solução de ponta a ponta. Do ponto de partida, há diversos lugares nos quais você pode estender e personalizar a solução para cenários específicos. As seções a seguir descrevem esses pontos comuns de personalização.

## <a name="find-the-source-code"></a>Localizar o código-fonte

O código-fonte para a solução pré-configurada está disponível no GitHub nos seguintes repositórios:

* Monitoramento remoto: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* Manutenção preditiva: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)
* Fábrica conectada: [https://github.com/Azure/azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory)

O código-fonte para as soluções pré-configuradas é fornecido para demonstrar os padrões e as práticas usadas para implementar a funcionalidade de ponta a ponta de uma solução IoT usando o Azure IoT Suite. Você pode encontrar mais informações sobre como compilar e implantar as soluções em repositórios GitHub.

## <a name="change-the-preconfigured-rules"></a>Alterar as regras pré-configuradas

A solução de monitoramento remoto inclui três trabalhos do [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/) para lidar com informações do dispositivo, telemetria e lógica de regras na solução.

Os três trabalhos de Stream Analytics e sua sintaxe são descritos em detalhes na [Passo a passo da solução pré-configurada de monitoramento remoto](iot-suite-remote-monitoring-sample-walkthrough.md). 

Você pode editar esses trabalhos diretamente para alterar a lógica ou adicionar lógica específica para seu cenário. Você pode encontrar os trabalhos do Stream Analytics da seguinte maneira:

1. Vá para o [portal do Azure](https://portal.azure.com).
2. Navegue até o grupo de recursos com o mesmo nome da sua solução IoT. 
3. Selecione o trabalho de Stream Analytics do Azure que você deseja modificar. 
4. Interrompa o trabalho selecionando **Parar**no conjunto de comandos. 
5. Edite as entradas, consulta e saídas.
   
    Uma modificação simples é alterar a consulta para o trabalho **Regras** para usar um **"<"**, em vez de um **">"**. O portal de solução ainda mostra **">"** quando a regra for editada, mas percebe como o comportamento é invertido devido à alteração no trabalho subjacente.
6. Iniciar o trabalho

> [!NOTE]
> O painel de monitoramento remoto depende de dados específicos, por isso, alterar os trabalhos pode fazer com que o painel falhe.

## <a name="add-your-own-rules"></a>Adicionar suas próprias regras

Além de alterar os trabalhos de Stream Analytics do Azure, você pode usar o portal do Azure para adicionar novos trabalhos ou adicionar novas consultas para trabalhos existentes.

## <a name="customize-devices"></a>Personalizar dispositivos

Uma das atividades mais comuns de extensão é o trabalho com dispositivos específicos ao seu cenário. Há vários métodos para trabalhar com dispositivos. Esses métodos incluem alterar um dispositivo simulado de acordo com seu cenário ou usar o [SDK de Dispositivo IoT][IoT Device SDK] para conectar seu dispositivo físico à solução.

Para obter um guia passo a passo para adicionar dispositivos, consulte o artigo [Dispositivos de Conexão do Iot Suite](iot-suite-connecting-devices.md) e o [Exemplo de SDK para o monitoramento remoto em C](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring). Este exemplo é projetado para trabalhar com a solução pré-configurada de monitoramento remoto.

### <a name="create-your-own-simulated-device"></a>Criar seu próprio dispositivo simulado

Incluído no [código-fonte da solução de monitoramento remota](https://github.com/Azure/azure-iot-remote-monitoring), é um simulador de .NET. Esse simulador é provisionado como parte da solução e pode ser alterado para enviar metadados diferentes, telemetria, e responder a comandos e métodos diferentes.

O simulador pré-configurado na solução pré-configurada de monitoramento remoto simula um dispositivo mais frio que emite a temperatura e a telemetria da umidade. Você pode modificar o simulador no projeto [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) quando tiver dividido o repositório GitHub.

### <a name="available-locations-for-simulated-devices"></a>Locais disponíveis para dispositivos simulados

O conjunto padrão de locais fica em Seattle/Redmond, Washington, Estados Unidos. É possível alterar esses locais em [SampleDeviceFactory.cs][lnk-sample-device-factory].

### <a name="add-a-desired-property-update-handler-to-the-simulator"></a>Adicionar um manipulador de atualização da propriedade desejada ao simulador

Você pode definir um valor para uma propriedade desejada para um dispositivo no portal de solução. É responsabilidade do dispositivo lidar com a solicitação de alteração da propriedade quando o dispositivo recupera o valor da propriedade desejada. Para adicionar suporte para uma alteração de valor da propriedade por meio de uma propriedade desejada, você precisa adicionar um manipulador ao simulador.

O simulador contém manipuladores para as propriedades **SetPointTemp** e **TelemetryInterval** que você pode atualizar definindo os valores desejados no portal da solução.

O exemplo a seguir mostra o manipulador para a propriedade desejada **SetPointTemp** na classe **CoolerDevice**:

```csharp
protected async Task OnSetPointTempUpdate(object value)
{
    var telemetry = _telemetryController as ITelemetryWithSetPointTemperature;
    telemetry.SetPointTemperature = Convert.ToDouble(value);

    await SetReportedPropertyAsync(SetPointTempPropertyName, telemetry.SetPointTemperature);
}
```

Esse método atualiza a temperatura do ponto de telemetria, em seguida, informa a alteração ao Hub IoT definindo uma propriedade relatada.

Você pode adicionar seus próprios manipuladores para suas próprias propriedades seguindo o padrão no exemplo anterior.

Você também deve associar a propriedade desejada ao manipulador como mostrado no exemplo a seguir do construtor **CoolerDevice**:

```csharp
_desiredPropertyUpdateHandlers.Add(SetPointTempPropertyName, OnSetPointTempUpdate);
```

Observe que **SetPointTempPropertyName** é uma constante definida como "Config.SetPointTemp".

### <a name="add-support-for-a-new-method-to-the-simulator"></a>Adicionar o suporte de um novo método ao simulador

Você pode personalizar o simulador para adicionar o suporte de um novo [método (método direto)][lnk-direct-methods]. Há duas etapas principais necessárias:

- O simulador deve notificar o Hub IoT na solução pré-configurada com os detalhes do método.
- O simulador deve incluir o código para lidar com a chamada do método quando você o chama no painel **Detalhes do dispositivo** no Gerenciador de Soluções ou por meio de um trabalho.

A solução pré-configurada de monitoramento remoto usa as *propriedades relatadas* para enviar os detalhes dos métodos com suporte para o Hub IoT. O back-end de solução mantém uma lista de todos os métodos suportados por cada dispositivo junto com um histórico de chamadas do método. Você pode exibir essas informações sobre os dispositivos e chamar os métodos no portal de solução.

Para notificar o Hub IoT que um dispositivo oferece suporte a um método, o dispositivo deve adicionar os detalhes do método ao nó **SupportedMethods** nas propriedades relatadas:

```json
"SupportedMethods": {
  "<method signature>": "<method description>",
  "<method signature>": "<method description>"
}
```

A assinatura do método tem o seguinte formato: `<method name>--<parameter #0 name>-<parameter #1 type>-...-<parameter #n name>-<parameter #n type>`. Por exemplo, para especificar o método **InitiateFirmwareUpdate** que espera um parâmetro da cadeia de caracteres denominado **FwPackageURI**, use a assinatura do método a seguir:

```json
InitiateFirmwareUpate--FwPackageURI-string: "description of method"
```

Para obter uma lista dos tipos de parâmetro com suporte, consulte a classe **CommandTypes** no projeto de infraestrutura.

Para excluir um método, defina a assinatura do método para `null` nas propriedades relatadas.

> [!NOTE]
> O back-end de solução só atualiza as informações sobre os métodos com suporte quando ele recebe uma mensagem de *informações do dispositivo* a partir do dispositivo.

O exemplo de código a seguir da classe **SampleDeviceFactory** no projeto Common mostra como adicionar um método à lista de **SupportedMethods** nas propriedades relatadas enviadas pelo dispositivo:

```csharp
device.Commands.Add(new Command(
    "InitiateFirmwareUpdate",
    DeliveryType.Method,
    "Updates device Firmware. Use parameter 'FwPackageUri' to specifiy the URI of the firmware file, e.g. https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin",
    new[] { new Parameter("FwPackageUri", "string") }
));
```

Esse trecho de código adiciona os detalhes do método **InitiateFirmwareUpdate**, incluindo o texto a exibir no portal de solução e os detalhes dos parâmetros do método necessários.

O simulador envia as propriedades relatadas, incluindo a lista de métodos com suporte, ao Hub IoT quando o simulador inicia.

Adicione um manipulador ao código do simulador para cada método com suporte. Você pode ver os manipuladores existentes na classe **CoolerDevice** no projeto Simulator.WebJob. O exemplo a seguir mostra o manipulador do método **InitiateFirmwareUpdate**:

```csharp
public async Task<MethodResponse> OnInitiateFirmwareUpdate(MethodRequest methodRequest, object userContext)
{
    if (_deviceManagementTask != null && !_deviceManagementTask.IsCompleted)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "Device is busy"
        }, 409));
    }

    try
    {
        var operation = new FirmwareUpdate(methodRequest);
        _deviceManagementTask = operation.Run(Transport).ContinueWith(async task =>
        {
            // after firmware completed, we reset telemetry
            var telemetry = _telemetryController as ITelemetryWithTemperatureMeanValue;
            if (telemetry != null)
            {
                telemetry.TemperatureMeanValue = 34.5;
            }

            await UpdateReportedTemperatureMeanValue();
        });

        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "FirmwareUpdate accepted",
            Uri = operation.Uri
        }));
    }
    catch (Exception ex)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = ex.Message
        }, 400));
    }
}
```

Os nomes do manipulador do método devem começar com `On` seguido do nome do método. O parâmetro **methodRequest** contém todos os parâmetros passados com a chamada do método a partir do back-end da solução. O valor de retorno deve ser do tipo **Task&lt;MethodResponse&gt;**. O método utilitário **BuildMethodResponse** ajuda a criar o valor de retorno.

No manipulador do método, você pode:

- iniciar uma tarefa assíncrona.
- recuperar as propriedades desejadas no *dispositivo gêmeo* no Hub IoT.
- atualizar uma única propriedade relatada usando o método **SetReportedPropertyAsync** na classe **CoolerDevice**.
- atualizar várias propriedades relatadas criando uma instância **TwinCollection** e chamar o método **Transport.UpdateReportedPropertiesAsync**.

O exemplo anterior de atualização do firmware executa as seguintes etapas:

- verifica se o dispositivo é capaz de aceitar a solicitação de atualização do firmware.
- inicia de forma assíncrona a operação de atualização do firmware e redefine a telemetria quando a operação é concluída.
- retorna imediatamente a mensagem "FirmwareUpdate aceito" para indicar que a solicitação foi aceita pelo dispositivo.

### <a name="build-and-use-your-own-physical-device"></a>Compilar e usar seu próprio dispositivo (físico)

Os [SDKs do Azure IoT](https://github.com/Azure/azure-iot-sdks) fornecem bibliotecas para conectar a vários tipos de dispositivo (linguagens e sistemas operacionais) em soluções de IoT.

## <a name="modify-dashboard-limits"></a>Modificar os limites do painel

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Número de dispositivos exibida na lista suspensa do painel

O padrão é 200. É possível alterar esse número em [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Número de marcações a serem exibidas no controle do Mapa do Bing

O padrão é 200. É possível alterar esse número em [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Período do gráfico de telemetria

O padrão é 10 minutos. É possível alterar esse valor em [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="manually-set-up-application-roles"></a>Configurar manualmente as funções do aplicativo

O procedimento a seguir descreve como adicionar as funções de aplicativo **Admin** e **ReadOnly** a uma solução pré-configurada. Observe que as soluções pré-configuradas provisionadas no site azureiotsuite.com já incluem as funções **Admin** e **ReadOnly**.

Membros da função **ReadOnly** podem ver o painel e a lista de dispositivos, mas não têm permissão para adicionar dispositivos, alterar os atributos do dispositivo ou enviar comandos.  Membros da função **Admin** têm acesso total a todos os recursos da solução.

1. Vá para o [Portal Clássico do Azure][lnk-classic-portal].
2. Selecione **Active Directory**.
3. Clique no nome do locatário AAD que você usou ao provisionar a solução.
4. Clique em **Aplicativos**.
5. Clique no nome do aplicativo que corresponda ao nome da solução pré-configurada. Se você não vir seu aplicativo na lista, selecione **Aplicativos que minha empresa** possui na lista suspensa **Mostrar** e clique na marca de seleção.
6. Na parte inferior da página, clique em **Gerenciar Manifesto** e em **Baixar Manifesto**.
7. Este procedimento baixa um arquivo .json para seu computador local. Abra esse arquivo para edição em um editor de texto de sua escolha.
8. Na terceira linha do arquivo .json, você pode ver:

   ```json
   "appRoles" : [],
   ```
   Substitua essa linha pelo código a seguir:

   ```json
   "appRoles": [
   {
   "allowedMemberTypes": [
   "User"
   ],
   "description": "Administrator access to the application",
   "displayName": "Admin",
   "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
   "isEnabled": true,
   "value": "Admin"
   },
   {
   "allowedMemberTypes": [
   "User"
   ],
   "description": "Read only access to device information",
   "displayName": "Read Only",
   "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
   "isEnabled": true,
   "value": "ReadOnly"
   } ],
   ```

9. Salve o arquivo .json atualizado (você pode substituir o arquivo existente).
10. No Portal Clássico do Azure, na parte inferior da página, selecione **Gerenciar Manifesto** e, em seguida, **Carregar Manifesto** para carregar o arquivo .json salvo na etapa anterior.
11. Agora você adicionou as funções **Admin** e **ReadOnly** ao seu aplicativo.
12. Para atribuir uma dessas funções a um usuário no diretório, consulte [Permissões no site azureiotsuite.com][lnk-permissions].

## <a name="feedback"></a>Comentários

Há alguma personalização que você gostaria que fosse abordada neste documento? Adicione sugestões de recursos ao [User Voice](https://feedback.azure.com/forums/321918-azure-iot)ou faça comentários sobre este artigo. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as opções para personalizar as soluções pré-configuradas, confira:

* [Conectar um Aplicativo lógico à solução pré-configurada de monitoramento remoto do Azure IoT Suite][lnk-logicapp]
* [Usar telemetria dinâmica com a solução pré-configurada de monitoramento remoto][lnk-dynamic]
* [Metadados de informações de dispositivo na solução pré-configurada de monitoramento remoto][lnk-devinfo]
* [Personalizar como a solução de fábrica conectada exibe dados de seus servidores de agente do usuário OPC][lnk-cf-customize]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-cf-customize]: iot-suite-connected-factory-customize.md