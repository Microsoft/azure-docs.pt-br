---
title: "Usar as propriedades do dispositivo gêmeo do Hub IoT (.NET/Node) do Azure | Microsoft Docs"
description: "Como usar dispositivos gêmeos do Hub IoT do Azure para configurar dispositivos. Use o SDK do dispositivo IoT do Azure para Node.js para implementar um aplicativo de dispositivo simulado e o SDK do serviço IoT do Azure para .NET para implementar um aplicativo de serviço que modifica a configuração de um dispositivo usando um dispositivo gêmeo."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: elioda
ms.openlocfilehash: 8f9626fc47e7d32cb104b960bea9b7de9efa6f3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Usar as propriedades desejadas para configurar os dispositivos
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

No fim deste tutorial, você terá dois aplicativos de console:

* **SimulateDeviceConfiguration.js**, um aplicativo de dispositivo simulado que aguarda uma atualização da configuração desejada e reporta o status de um processo simulado de atualização de configuração.
* **SetDesiredConfigurationAndQuery**, um aplicativo de back-end .NET que define a configuração desejada em um dispositivo e consulta o processo de atualização de configuração.

> [!NOTE]
> O artigo [SDKs do IoT do Azure][lnk-hub-sdks] fornece informações sobre os SDKs do IoT do Azure que você pode usar para compilar dispositivos e aplicativos back-end.
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Node.js versão 4.0.x ou posterior.
* Uma conta ativa do Azure. Se não tiver uma conta, você poderá criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.

Caso tenha seguido o tutorial [Introdução aos dispositivos gêmeos][lnk-twin-tutorial], você já terá um Hub IoT e uma identidade de dispositivo chamada **myDeviceId**. Nesse caso, você pode pular para a seção [Criar o aplicativo do dispositivo simulado][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Criar o aplicativo de dispositivo simulado
Nesta seção, você cria um aplicativo de console do Node.js que se conecta ao seu hub como **myDeviceId**, aguarda uma atualização de configuração desejada e, em seguida, reporta atualizações sobre o processo simulado de atualização de configuração.

1. Crie uma nova pasta vazia denominada **simulatedeviceconfiguration**. Na pasta **simulatedeviceconfiguration**, crie um novo arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões.
   
    ```
    npm init
    ```
1. No prompt de comando, na pasta **simulatedeviceconfiguration**, execute o comando a seguir para instalar os pacotes **azure-iot-device** e **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. Usando um editor de texto, crie um novo arquivo **SimulateDeviceConfiguration.js** na pasta **simulatedeviceconfiguration**.
1. Adicione o seguinte código ao arquivo **SimulateDeviceConfiguration.js** e substitua o espaço reservado **{cadeia de conexão do dispositivo}** pela cadeia de conexão do dispositivo copiada quando você criou a identidade do dispositivo **myDeviceId**:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });
   
    O objeto **Client** expõe todos os métodos necessários para você, do dispositivo, interagir com gêmeos de dispositivo. Esse código inicializa o objeto **Client**, recupera o dispositivo gêmeo para **myDeviceId** e então anexa um manipulador para a atualização em *propriedades desejadas*. O manipulador verifica que há uma solicitação de alteração de configuração real ao comparar as configIds, então invoca um método que inicia a alteração de configuração.
   
    Observe que, para simplificar, esse código usa um padrão embutido em código para a configuração inicial. Um aplicativo real provavelmente carregaria essa configuração de um armazenamento local.
   
   > [!IMPORTANT]
   > Eventos de alteração da propriedade desejada sempre são emitidos uma vez na conexão do dispositivo. Certifique-se de verificar se há uma alteração real nas propriedades desejadas antes de executar qualquer ação.
   > 
   > 
1. Adicione os seguintes métodos antes da invocação de `client.open()`:
   
        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";
   
            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }
   
        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
   
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;
   
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };
   
    O método **initConfigChange** atualiza as propriedades relatadas no objeto do dispositivo gêmeo local com a solicitação de atualização de configuração, define o status para **Pendente** e atualiza o dispositivo gêmeo no serviço. Depois de atualizar o dispositivo gêmeo com êxito, ele simula um processo de execução longa que termina com a execução de **completeConfigChange**. Esse método atualiza as propriedades reportadas locais definindo o status como **Êxito** e removendo o objeto **pendingConfig**. Em seguida, ele atualiza o dispositivo gêmeo no serviço.
   
    Observe que, para economizar largura de banda, as propriedades reportadas são atualizadas especificando-se apenas as propriedades a serem modificadas (chamadas de **patch** no código acima), em vez de substituir o documento inteiro.
   
   > [!NOTE]
   > Este tutorial não simula nenhum comportamento para atualizações de configuração simultâneas. Alguns processos de atualização de configuração podem ser capazes de acomodar as alterações de configuração de destino enquanto a atualização está em execução, outros podem colocá-las em fila e outros poderiam ainda rejeitá-las com uma condição de erro. Certifique-se de considerar o comportamento desejado para o seu processo de configuração específico e adicione a lógica apropriada antes de iniciar a alteração de configuração.
   > 
   > 
1. Execute o aplicativo do dispositivo:
   
        node SimulateDeviceConfiguration.js
   
    Você deve ver a mensagem `retrieved device twin`. Mantenha o aplicativo em execução.

## <a name="create-the-service-app"></a>Criar o aplicativo do serviço
Nesta seção, você criará um aplicativo de console .NET que atualiza as *propriedades desejadas* no dispositivo gêmeo associado à **myDeviceId** com um novo objeto de configuração de telemetria. Ele então consulta os dispositivos gêmeos armazenados no Hub IoT e mostra a diferença entre as configurações desejadas e reportadas do dispositivo.

1. No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# à solução atual usando o modelo de projeto **Aplicativo do Console** . Nomeie o projeto **SetDesiredConfigurationAndQuery**.
   
    ![Novo projeto da Área de Trabalho Clássica do Windows no Visual C#][img-createapp]
1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SetDesiredConfigurationAndQuery** e clique em **Gerenciar Pacotes NuGet...**.
1. Na janela **Gerenciador de Pacotes Nuget**, selecione **Procurar**, procure **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de uso. O procedimento baixa, instala e adiciona uma referência ao [pacote Nuget do SDK do Dispositivo IoT do Azure][lnk-nuget-service-sdk] e suas dependências.
   
    ![Janela do Gerenciador de Pacotes NuGet][img-servicenuget]
1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. Adicione os seguintes campos à classe **Program** . Substitua o valor do espaço reservado pela cadeia de conexão do Hub IoT criado na seção anterior.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Adicione o seguinte método à classe **Programa** :
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            try
            {
                while (true)
                {
                    var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                    var results = await query.GetNextAsTwinAsync();
                    foreach (var result in results)
                    {
                        Console.WriteLine("Config report for: {0}", result.DeviceId);
                        Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine();
                    }
                    Thread.Sleep(10000);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Exception: {ex.Message}");
            }
        }
   
    O objeto **Registry** expõe todos os métodos necessários para interagir com gêmeos de dispositivo do serviço. Esse código inicializa o objeto **Registry**, recupera o dispositivo gêmeo para **myDeviceId** e então atualiza as propriedades desejadas com um novo objeto de configuração de telemetria.
    Depois disso, a cada 10 segundos, ele consulta os dispositivos gêmeos armazenados no Hub IoT e imprime as configurações de telemetria desejadas e relatadas. Consulte a [Linguagem de consulta de Hub IoT][lnk-query] para saber como gerar relatórios em todos os seus dispositivos.
   
   > [!IMPORTANT]
   > Esse aplicativo consulta o Hub IoT a cada 10 segundos para fins ilustrativos. Use consultas para gerar relatórios voltados para o usuário em vários dispositivos, não para detectar alterações. Se sua solução exigir notificações em tempo real de eventos de dispositivo, use [notificações gêmeas][lnk-twin-notifications].
   > 
   > 
1. Por fim, adicione as seguintes linhas ao método **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery().Wait();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. No Gerenciador de Soluções, abra **Definir projetos de StartUp...** e certifique-se de que a **Ação** para o projeto **SetDesiredConfigurationAndQuery** é **Iniciar**. Compilar a solução.
1. Com **SimulateDeviceConfiguration.js** em execução, execute o aplicativo .NET no Visual Studio usando **F5** e você deverá ver a configuração reportada mudar de **Êxito** para **Pendente** e para **Êxito** novamente com a nova frequência de envio ativo de cinco minutos em vez de 24 horas.

 ![Dispositivo configurado com êxito][img-deviceconfigured]
   
   > [!IMPORTANT]
   > Há um atraso de até um minuto entre a operação de relatório de dispositivo e o resultado da consulta. Isso é para habilitar a infraestrutura de consulta a trabalhar em escala muito alta. Para recuperar os modos de exibição consistentes de um único dispositivo gêmeo, use o método **getDeviceTwin** na classe **Registry**.
   > 
   > 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você definiu uma configuração desejada como *propriedades desejadas* no back-end da solução e criou um aplicativo de dispositivo para detectar essa alteração e simular um processo de atualização de várias etapas, relatando seu status por meio das propriedades relatadas.

Veja os recursos a seguir para saber como:

* Enviar telemetria de dispositivos com o tutorial [Introdução ao Hub IoT][lnk-iothub-getstarted].
* Agendar ou executar operações em grandes conjuntos de dispositivos. Veja o tutorial [Schedule and broadcast jobs][lnk-schedule-jobs] (Agendar e transmitir trabalhos).
* Controlar dispositivos interativamente (como ativar uma ventoinha de um aplicativo controlado pelo usuário), com o tutorial [Uso de métodos diretos][lnk-methods-tutorial].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-node-twin-how-to-configure/deviceconfigured.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-csharp-node-twin-how-to-configure.md#create-the-simulated-device-app
