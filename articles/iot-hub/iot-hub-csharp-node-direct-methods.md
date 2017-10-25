---
title: "Usar métodos diretos do Hub IoT do Azure (.NET/Node) | Microsoft Docs"
description: "Como usar os métodos diretos do Hub IoT do Azure. Use o SDK do dispositivo IoT do Azure para Node.js para implementar um aplicativo de dispositivo simulado que inclui um método direto e o SDK do serviço do Azure IoT para .NET para implementar um aplicativo de serviço que invoca o método direto."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: nberdy
ms.openlocfilehash: 76f1d32b4afeacae1488b4cf28be6c8cf7f4ea37
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-netnode"></a>Usar métodos diretos para (.NET/Node)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

Neste tutorial, vamos desenvolver um .NET e um aplicativo de console do Node.js:

* **CallMethodOnDevice.sln**, um aplicativo de back-end .NET que chama um método no aplicativo do dispositivo simulado e exibe a resposta.
* **SimulatedDevice.js**, um aplicativo Node.js que simula um dispositivo que se conecta ao seu Hub IoT com a identidade do dispositivo criada anteriormente e responde ao método chamado pela nuvem.

> [!NOTE]
> O artigo [SDKs de IoT do Azure][lnk-hub-sdks] fornece informações sobre os SDKs de IoT do Azure que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução.
> 
> 

Para concluir este tutorial, você precisará:

* Visual Studio 2015 ou Visual Studio 2017.
* Node.js versão 4.0.x ou posterior.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado
Nesta seção, você cria um aplicativo de console do Node.js que responde a um método chamado pelo back-end da solução.

1. Crie uma nova pasta vazia denominada **simulateddevice**. Na pasta **simulateddevice** , crie um arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:
   
    ```
    npm init
    ```
2. No prompt de comando, na pasta **simulateddevice**, execute o seguinte comando para instalar o pacote **azure-iot-device** e pacotes **azure-iot-device-mqtt**:
   
    ```
        npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Usando um editor de texto, crie um arquivo na pasta **simulateddevice** e coloque o nome **SimulatedDevice.js**.
4. Adicione as seguintes instruções `require` no início do arquivo **SimulatedDevice.js** :
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. Adicione uma variável **connectionString** e use-a para criar uma instância **DeviceClient**. Substitua **{cadeia de conexão do dispositivo}** pela cadeia de conexão de dispositivo gerada na seção *Criar uma identidade de dispositivo*:
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. Adicione a seguinte função para implementar o método direto no dispositivo:
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. Abra a conexão com o Hub IoT e inicialize o ouvinte do método:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. Salve e feche o arquivo **SimulatedDevice.js** .

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (tais como tentar conexão novamente), conforme sugerido no artigo do MSDN [Tratamento de Falhas Transitórias][lnk-transient-faults].
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>Chama um método direto em um dispositivo
Nesta seção, você cria um aplicativo de console do .NET que chama um método no dispositivo simulado e, em seguida, exibe a resposta.

1. No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# à solução atual usando o modelo de projeto **Aplicativo do Console** . Verifique se a versão do .NET Framework é 4.5.1 ou posterior. Nomeie o projeto como **CallMethodOnDevice**.
   
    ![Novo projeto da Área de Trabalho Clássica do Windows no Visual C#][10]
2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **CallMethodOnDevice** e clique em **Gerenciar Pacotes NuGet...**.
3. Na janela **Gerenciador de Pacotes Nuget**, selecione **Procurar**, procure **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de uso. O procedimento baixa, instala e adiciona uma referência ao [pacote Nuget do SDK do Dispositivo IoT do Azure][lnk-nuget-service-sdk] e suas dependências.
   
    ![Janela do Gerenciador de Pacotes NuGet][11]

4. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :
   
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices;
5. Adicione os seguintes campos à classe **Program** . Substitua o valor do espaço reservado pela cadeia de conexão do Hub IoT criado na seção anterior.
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Adicione o seguinte método à classe **Programa** :
   
        private static async Task InvokeMethod()
        {
            var methodInvocation = new CloudToDeviceMethod("writeLine") { ResponseTimeout = TimeSpan.FromSeconds(30) };
            methodInvocation.SetPayloadJson("'a line to be written'");

            var response = await serviceClient.InvokeDeviceMethodAsync("myDeviceId", methodInvocation);

            Console.WriteLine("Response status: {0}, payload:", response.Status);
            Console.WriteLine(response.GetPayloadAsJson());
        }
   
    Este método invoca um método direto com o nome `writeLine` no dispositivo `myDeviceId`. Em seguida, ele grava a resposta fornecida pelo dispositivo no console. Observe como é possível especificar um valor de tempo limite para a resposta do dispositivo.
7. Por fim, adicione as seguintes linhas ao método **Main** :
   
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        InvokeMethod().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

## <a name="run-the-applications"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. No Visual Studio, no Gerenciador de Soluções, clique com o botão direito na solução e clique em **Definir Projetos de Inicialização...**. Selecione **Único projeto de inicialização**e, em seguida, selecione o projeto **CallMethodOnDevice** no menu suspenso.

2. Em um prompt de comando, na pasta **simulateddevice**, execute o seguinte comando para iniciar a escuta de chamadas de método de seu Hub IoT:
   
    ```
    node SimulatedDevice.js
    ```
   Aguarde a abertura do dispositivo simulado: ![][7]
3. Agora que o dispositivo está conectado e aguardando chamadas de método, execute o aplicativo **CallMethodOnDevice** .NET para chamar o método no aplicativo do dispositivo simulado. Você deve ver a resposta do dispositivo escrita no console.
   
    ![][8]
4. O dispositivo reage ao método imprimindo esta mensagem:
   
    ![][9]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você usou essa identidade do dispositivo para habilitar o aplicativo do dispositivo simulado para reagir aos métodos invocados pela nuvem. Você também criou um aplicativo que invoca métodos no dispositivo e exibe a resposta do dispositivo. 

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Introdução ao Hub IoT]
* [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Para saber como estender sua solução de IoT e agendar chamadas de método em vários dispositivos, confira o tutorial [Agendar e difundir trabalhos][lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-csharp-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-csharp-node-direct-methods/netserviceapp.png
[9]: ./media/iot-hub-csharp-node-direct-methods/methods-output.png

[10]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp1.png
[11]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp2.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Introdução ao Hub IoT]: iot-hub-node-node-getstarted.md
