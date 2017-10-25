---
title: Processar mensagens do dispositivo para nuvem do Hub IoT do Azure usando rotas (.Net) | Microsoft Docs
description: "Como processar mensagens do dispositivo para nuvem do Hub IoT usando regras de direcionamento e pontos de extremidade personalizados para enviar mensagens para outros serviços de back-end."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 5177bac9-722f-47ef-8a14-b201142ba4bc
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 1d2b52ea005ab520bf294efa603512c00a92ee63
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="process-iot-hub-device-to-cloud-messages-using-routes-net"></a>Processar mensagens do dispositivo para nuvem do Hub IoT usando rotas (.NET)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Este tutorial baseia-se no tutorial [Introdução ao Hub IoT] . O tutorial:

* Mostra como usar regras de roteamento do para enviar mensagens de dispositivo à nuvem de maneira fácil, com base em configuração.
* Ilustra como isolar mensagens interativas que exigem ação imediata no back-end da solução para processamento adicional. Por exemplo, um dispositivo pode enviar uma mensagem de alarme que dispara e insere um tíquete em um sistema CRM. Por outro lado, as mensagens de ponto de dados, como telemetria de temperatura, são enviadas ao mecanismo de análise.

No final deste tutorial, você executará três aplicativos de console .NET:

* **SimulatedDevice**, uma versão modificada do aplicativo criado no tutorial [Introdução ao Hub IoT], que envia mensagens de ponto de dados do dispositivo para a nuvem a cada segundo e mensagens interativas do dispositivo para a nuvem a cada 10 segundos.
* **ReadDeviceToCloudMessages**, que exibe a telemetria não crítica enviada pelo aplicativo de dispositivo.
* **ReadCriticalQueue** retira da fila as mensagens críticas enviadas pelo seu aplicativo de dispositivo de uma fila do Barramento de Serviço. Essa fila é conectada ao Hub IoT.

> [!NOTE]
> O Hub IoT tem suporte de SDK para várias plataformas de dispositivo e linguagens, incluindo C, Java e JavaScript. Para saber como substituir o dispositivo simulado neste tutorial por um dispositivo físico, confira o [Centro de desenvolvedores do Azure IoT].

Para concluir este tutorial, você precisará do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. <br/>Se você não tem uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

Você também precisa ter um conhecimento básico do [Armazenamento do Azure] e do [Barramento de Serviço do Azure].

## <a name="send-interactive-messages"></a>Enviar mensagens interativas

Modifique o aplicativo de dispositivo criado no tutorial [Introdução ao Hub IoT] para enviar ocasionalmente mensagens interativas.

No Visual Studio, no projeto **SimulatedDevice**, substitua o método `SendDeviceToCloudMessagesAsync` pelo código a seguir:

```csharp
private static async void SendDeviceToCloudMessagesAsync()
{
    double minTemperature = 20;
    double minHumidity = 60;
    Random rand = new Random();

    while (true)
    {
        double currentTemperature = minTemperature + rand.NextDouble() * 15;
        double currentHumidity = minHumidity + rand.NextDouble() * 20;

        var telemetryDataPoint = new
        {
            deviceId = "myFirstDevice",
            temperature = currentTemperature,
            humidity = currentHumidity
        };
        var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
        string levelValue;

        if (rand.NextDouble() > 0.7)
        {
            messageString = "This is a critical message";
            levelValue = "critical";
        }
        else
        {
            levelValue = "normal";
        }
        
        var message = new Message(Encoding.ASCII.GetBytes(messageString));
        message.Properties.Add("level", levelValue);
        
        await deviceClient.SendEventAsync(message);
        Console.WriteLine("{0} > Sent message: {1}", DateTime.Now, messageString);

        await Task.Delay(1000);
    }
}
```

Isso adiciona aleatoriamente a propriedade `"level": "critical"` às mensagens enviadas pelo dispositivo, que simula uma mensagem que exige ação imediata do back-end da solução. O aplicativo do dispositivo passa essas informações nas propriedades da mensagem, e não no corpo da mensagem, de modo que o Hub IoT pode rotear a mensagem para o destino apropriado.

> [!NOTE]
> Você pode usar as propriedades a fim de direcionar as mensagens para vários cenários, incluindo processamento de ampliação, além do exemplo de afunilamento mostrado aqui.

> [!NOTE]
> Para simplificar, esse tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição, como uma retirada exponencial, como sugerido no artigo [Tratamento de falhas transitórias]do MSDN.

## <a name="route-messages-to-a-queue-in-your-iot-hub"></a>Rotear mensagens para uma fila em seu Hub IoT

Nesta seção, você:

* Criará uma fila do Barramento de Serviço.
* A conectará ao seu Hub IoT.
* Configurará seu Hub IoT para enviar mensagens para a fila com base na presença de uma propriedade na mensagem.

Para saber mais sobre como processar mensagens das filas do Barramento de Serviço, confira [Introdução às filas][Service Bus queue].

1. Crie uma fila do Barramento de Serviço conforme descrito em [Introdução às filas][Service Bus queue]. A fila deve estar na mesma assinatura e região que o seu Hub IoT. Anote o namespace e o nome da fila.

    > [!NOTE]
    > As filas do Barramento de Serviço e os tópicos utilizados como pontos de extremidade do Hub IoT não devem ter **Sessões** nem **Detecção Duplicada** habilitadas. Se qualquer uma dessas opções estiver habilitada, o ponto de extremidade aparecerá como **Inacessível** no Portal do Azure.

2. No Portal do Azure, abra o Hub IoT e clique em **Pontos de extremidade**.
    
    ![Pontos de extremidade no Hub IoT][30]

3. Na folha **Pontos de extremidade**, clique em **Adicionar** na parte superior para adicionar a fila ao Hub IoT. Chame o ponto de extremidade de **CriticalQueue** e use o menu suspenso para selecionar **Fila do Barramento de Serviço**, o namespace do Barramento de Serviço no qual suas filas estão e o nome da sua fila. Quando terminar, clique em **Salvar** na parte inferior.
    
    ![Adicionando um ponto de extremidade][31]
    
4. Agora clique em **Rotas** no Hub IoT. Clique em **Adicionar** na parte superior da folha para criar uma regra que encaminhe mensagens para a fila que você acabou de adicionar. Selecione **DeviceTelemetry** como a fonte dos dados. Insira `level="critical"` como a condição e escolha a fila que acabou de adicionar como um ponto de extremidade personalizado como o ponto de extremidade de regra do direcionamento. Quando terminar, clique em **Salvar** na parte inferior.
    
    ![Adicionando uma rota][32]
    
    Verifique se a rota de fallback está definida como **ATIVADA**. Essa é a configuração padrão para um Hub IoT.
    
    ![Rota de fallback][33]

## <a name="read-from-the-queue-endpoint"></a>Ler no ponto de extremidade da fila

Nesta seção, você lê as mensagens no ponto de extremidade da fila.

1. No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# à solução atual usando o modelo de projeto **Aplicativo do Console (.NET Framework)**. Dê ao projeto o nome de **ReadCriticalQueue**.

2. No Gerenciador de Soluções, clique com o botão direito no projeto **ReadCriticalQueue** e clique em **Gerenciar Pacotes NuGet**. Esta operação faz com que a janela **Gerenciador de Pacotes NuGet** seja exibida.

3. Pesquise por **WindowsAzure.ServiceBus**, clique em **Instalar** e aceite os termos de uso. Essa operação baixa, instala e adiciona uma referência ao Barramento de Serviço do Azure, com todas as suas dependências.

4. Adicione a seguinte instrução **using** à parte superior do arquivo **Program.cs**:
   
    ```csharp
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Por fim, adicione as seguintes linhas ao método **Main** . Substitua a cadeia de conexão por permissões **Escutar** para a fila:
   
    ```csharp
    Console.WriteLine("Receive critical messages. Ctrl-C to exit.\n");
    var connectionString = "{service bus listen string}";
    var queueName = "{queue name}";
    
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);

    client.OnMessage(message =>
        {
            Stream stream = message.GetBody<Stream>();
            StreamReader reader = new StreamReader(stream, Encoding.ASCII);
            string s = reader.ReadToEnd();
            Console.WriteLine(String.Format("Message body: {0}", s));
        });
        
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. No Visual Studio, no Gerenciador de Soluções, clique com o botão direito do mouse na solução e selecione **Definir Projetos de Inicialização**. Selecione **Vários projetos de inicialização** e selecione **Iniciar** como a ação para os projetos **ReadDeviceToCloudMessages**, **SimulatedDevice** e **ReadCriticalQueue**.
2. Pressione **F5** para iniciar os três aplicativos de console. O aplicativo **ReadDeviceToCloudMessages** tem somente mensagens não críticas enviadas do aplicativo **SimulatedDevice** e o aplicativo **ReadCriticalQueue** tem somente mensagens críticas.
   
   ![Três aplicativos de console][50]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a expedir confiavelmente mensagens do dispositivo para nuvem usando a funcionalidade de roteamento de mensagens do Hub IoT.

O tutorial [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT][lnk-c2d] mostra como enviar mensagens da solução de back-end para seus dispositivos.

Para ver exemplos de soluções completas que usam o Hub IoT, veja [Azure IoT Suite][lnk-suite].

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT].

Para saber mais sobre o roteamento de mensagens no Hub IoT, confira [Enviar e receber mensagens com o Hub IoT][lnk-devguide-messaging].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[30]: ./media/iot-hub-csharp-csharp-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/route-creation.png
[33]: ./media/iot-hub-csharp-csharp-process-d2c/fallback-route.png

<!-- Links -->
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[Armazenamento do Azure]: https://azure.microsoft.com/documentation/services/storage/
[Barramento de Serviço do Azure]: https://azure.microsoft.com/documentation/services/service-bus/
[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[Introdução ao Hub IoT]: iot-hub-csharp-csharp-getstarted.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Centro de desenvolvedores do Azure IoT]: https://azure.microsoft.com/develop/iot
[Tratamento de falhas transitórias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
