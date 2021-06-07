---
title: Tutorial – Testar a capacidade dos certificados X.509 de autenticar dispositivos em um Hub IoT do Azure | Microsoft Docs
description: Tutorial – Testar os certificados X.509 para autenticação no Hub IoT do Azure
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 7d1900782fce6b84ed79014e985393f3626d171b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379427"
---
# <a name="tutorial-testing-certificate-authentication"></a>Tutorial: Como testar a autenticação de certificado

Use o exemplo de código C# a seguir para testar se o certificado pode autenticar seu dispositivo no Hub IoT. Observe que você precisará fazer o seguinte antes de executar o código de teste:

* Criar um certificado de AC raiz ou de AC subordinada.
* Carregar o Certificado de Autoridade de Certificação no Hub IoT.
* Provar que você é o proprietário do Certificado de Autoridade de Certificação.
* Adicionar um dispositivo ao Hub IoT.
* Criar um certificado de dispositivo com a mesma identificação do seu dispositivo.

## <a name="code-example"></a>Exemplo de código

O exemplo de código a seguir mostra como criar um aplicativo C# para simular o dispositivo X.509 registrado no hub IoT. O exemplo envia valores de temperatura e umidade do dispositivo simulado para o hub. Neste tutorial, criaremos apenas o aplicativo do dispositivo. Isso é deixado como um exercício para os leitores criarem o aplicativo de serviço do Hub IoT que enviará a resposta para os eventos enviados por esse dispositivo simulado.

1. Abra o Visual Studio, selecione **Criar um projeto** e escolha o modelo de projeto **Aplicativo de Console (.NET Framework)** . Selecione **Avançar**.

1. Em **Configurar seu novo projeto**, nomeie o projeto *SimulateX509Device* e selecione **Criar**.

   ![Criar um projeto de dispositivo X.509 no Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SimulateX509Device** e selecione **Gerenciar Pacotes NuGet**.

1. No **Gerenciador de Pacotes NuGet**, selecione **Procurar** e pesquise e escolha **Microsoft.Azure.Devices.Client**. Selecione **Instalar**.

   ![Adicionar o pacote NuGet do SDK de dispositivo no Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Esta etapa baixa, instala e adiciona uma referência ao pacote NuGet do SDK do dispositivo IoT do Azure e as dependências dele.

    Insira e execute o seguinte código:

```csharp
using System;
using Microsoft.Azure.Devices.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;
using System.Text;

namespace SimulateX509Device
{
    class Program
    {
        private static int MESSAGE_COUNT = 5;

        // Temperature and humidity variables.
        private const int TEMPERATURE_THRESHOLD = 30;
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();

        // Set the device ID to the name (device identifier) of your device.
        private static String deviceId = "{your-device-id}";

        static async Task SendEvent(DeviceClient deviceClient)
        {
            string dataBuffer;
            Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

            // Iterate MESSAGE_COUNT times to set randomm termperature and humidity values.
            for (int count = 0; count < MESSAGE_COUNT; count++)
            {
                // Set random values for temperature and humidity.
                temperature = rnd.Next(20, 35);
                humidity = rnd.Next(60, 80);
                dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
                Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
                eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
                Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

                // Send to IoT Hub.
                await deviceClient.SendEventAsync(eventMessage);
            }
        }
        static void Main(string[] args)
        {
            try
            {
                // Create an X.509 certificate object.
                var cert = new X509Certificate2(@"{full path to pfx certificate.pfx}", "{your certificate password}");

                // Create an authentication object using your X.509 certificate. 
                var auth = new DeviceAuthenticationWithX509Certificate("{your-device-id}", cert);

                // Create the device client.
                var deviceClient = DeviceClient.Create("{your-IoT-Hub-name}.azure-devices.net", auth, TransportType.Mqtt);

                if (deviceClient == null)
                {
                    Console.WriteLine("Failed to create DeviceClient!");
                }
                else
                {
                    Console.WriteLine("Successfully created DeviceClient!");
                    SendEvent(deviceClient).Wait();
                }

                Console.WriteLine("Exiting...\n");
            }
            catch (Exception ex)
            {
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
         }
    }
}
```