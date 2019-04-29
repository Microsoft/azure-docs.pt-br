---
title: Tutorial para segurança do X.509 no Hub IoT do Azure | Microsoft Docs
description: Introdução à segurança com base em X.509 em seu Hub IoT do Azure em um ambiente simulado.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.openlocfilehash: 5795cde35d53a64620c4fdb6c3af99a7f56b12d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440636"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Configurar a segurança de X.509 em seu Hub IoT do Azure

Este tutorial simula as etapas necessárias para proteger seu Hub IoT do Azure usando a *Autenticação de certificado X.509*. Para fins ilustrativos, mostraremos como usar a ferramenta de software livre OpenSSL para criar certificados localmente no seu computador Windows. É recomendável que você use este tutorial apenas para fins de teste. Para o ambiente de produção, você deve adquirir os certificados de uma *autoridade de certificado raiz (AC)*.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial exige que você tenha os seguintes recursos prontos:

* Você criou um Hub IoT com sua assinatura do Azure. Consulte [Criar um Hub IoT por meio do portal](iot-hub-create-through-portal.md) para obter etapas detalhadas.

* Você tem [Visual Studio 2017 ou Visual Studio de 2019](https://www.visualstudio.com/vs/) instalado em seu computador.

## <a name="get-x509-ca-certificates"></a>Obter certificados de AC X.509

A segurança baseada em certificado X.509 no Hub IoT exige que você comece com uma [cadeia de certificados X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), que inclui o certificado raiz, bem como os certificados intermediários até o certificado de folha.

Você pode escolher uma das maneiras a seguir para obter os certificados:

* Adquira certificados X.509 de uma *autoridade de certificado (AC) raiz*. Isso é recomendado para ambientes de produção.

* Crie seus próprios certificados X.509 usando uma ferramenta de terceiros, como [OpenSSL](https://www.openssl.org/). Isso não terá problema para fins de teste e desenvolvimento. Veja [Gerenciar certificados de Autoridade de Certificação de teste para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) para obter informações sobre como gerar certificados de autoridade de certificação de teste usando o PowerShell ou Bash. O restante deste tutorial usa certificados de autoridade de certificação de teste gerados seguindo as instruções em [Gerenciar certificados de Autoridade de Certificação de teste para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Registrar certificados de AC X.509 para o Hub IoT

Estas etapas mostram como adicionar uma nova Autoridade de certificação ao Hub IoT por meio do portal.

1. No portal do Azure, navegue até seu Hub IoT e abra o menu **CONFIGURAÇÕES** > **Certificados**.

2. Clique em **Adicionar** para adicionar um novo certificado.

3. Insira um nome de exibição fácil para o certificado. Selecione o arquivo de certificado raiz chamado *RootCA.cer* criado na seção anterior, em seu computador. Clique em **Carregar**.

4. Após receber uma notificação de que o certificado foi carregado com êxito, clique em **Salvar**.

    ![Carregar um certificado](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   Isso exibirá o certificado na lista **Gerenciador de Certificados**. Observe que o **STATUS** desse certificado é *Não verificado*.

5. Clique no certificado que você adicionou na etapa anterior.

6. Na folha **Detalhes do Certificado**, clique em **Gerar Código de Verificação**.

7. Isso cria um **Código de verificação** para validar a propriedade de certificado. Copie o código para a área de transferência.

   ![Verificar o certificado](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. Agora, você precisa assinar esse *Código de verificação* com a chave privada associada ao certificado de autoridade de certificação X.509, que gera uma assinatura. Há ferramentas disponíveis para executar esse processo de assinatura, por exemplo, o OpenSSL. Isso é conhecido como [Prova de posse](https://tools.ietf.org/html/rfc5280#section-3.1). A etapa 3 em [Gerenciar certificados de Autoridade de Certificação de teste para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) gera um código de verificação.

9. Carregue a assinatura resultante da etapa 8 acima em seu Hub IoT no portal. Na folha **Detalhes do Certificado** no portal do Azure, vá até o **arquivo .pem ou .cer do Certificado de verificação** e selecione a assinatura, por exemplo, *VerifyCert4.cer* criada pelo comando do PowerShell de exemplo usando o ícone de _Gerenciador de arquivos_ ao lado dela.

10. Depois que o certificado for carregado com êxito, clique em **Verificar**. O **STATUS** de seu certificado muda para **_Verificado_** na folha **Certificados**. Clique em **Atualizar** se ele não for atualizado automaticamente.

    ![Carregar verificação de certificado](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  

## <a name="create-an-x509-device-for-your-iot-hub"></a>Criar um dispositivo X.509 para o Hub IoT

1. No portal do Azure, navegue até a página **Explorers > Dispositivos IoT** do seu hub IoT.

2. Clique em **+ Adicionar** para adicionar um novo dispositivo.

3. Dê um nome de exibição fácil para a **ID do dispositivo** e selecione **_Assinado pela AC X.509_** como o **Tipo de autenticação**. Clique em **Salvar**.

   ![Criar dispositivo X.509 no portal](./media/iot-hub-security-x509-get-started/create-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Autenticar o dispositivo X.509 com os certificados X.509

Para autenticar o dispositivo X.509, é necessário assinar primeiro o dispositivo com o certificado de autoridade de certificação. A assinatura de dispositivos de folha normalmente é feita na fábrica, na qual as ferramentas de fabricação foram habilitadas adequadamente. À medida que o dispositivo passa de um fabricante para outro, a ação de assinatura de cada fabricante é capturada como um certificado intermediário na cadeia. O resultado final é uma cadeia de certificados do Certificado de Autoridade de Certificação para o certificado de folha do dispositivo. A etapa 4 em [Gerenciar certificados de Autoridade de Certificação de teste para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) gera um certificado de dispositivo.

Em seguida, mostraremos como criar um aplicativo C# para simular o dispositivo X.509 registrado para o Hub IoT. Enviaremos valores de temperatura e umidade do dispositivo simulado para o hub. Observe que, neste tutorial, vamos criar apenas o aplicativo do dispositivo. Isso é tido como um exercício para os leitores criarem o aplicativo de serviço do Hub IoT que enviará a resposta para os eventos enviados por esse dispositivo simulado. O aplicativo C# pressupõe que você seguiu as etapas em [Gerenciar certificados de Autoridade de Certificação de teste para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. No Visual Studio, crie um novo projeto da Área de Trabalho Clássica do Windows do Visual C# usando o modelo de projeto de Aplicativo de Console. Chame o projeto de **SimulateX509Device**.

   ![Criar projeto de dispositivo X.509 no Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SimulateX509Device** e clique em **Gerenciar Pacotes NuGet...**. Na janela Gerenciador de Pacotes NuGet, selecione **Procurar** e pesquise por **microsoft.azure.devices.client**. Selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices.Client** e aceite os termos de uso. Esse procedimento baixa, instala e adiciona uma referência ao pacote NuGet do SDK do dispositivo IoT do Azure e suas dependências.

   ![Adicionar o pacote do NuGet do SDK de dispositivo no Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

3. Adicione as seguintes linhas de código na parte superior do arquivo *Program.cs*:

    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

4. Adicione as seguintes linhas de código dentro da classe **Program**:

    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

     Use o nome de dispositivo fácil usado na seção anterior em vez do espaço reservado _<your_device_id >_.

5. Adicione a função a seguir para criar números aleatórios de temperatura e umidade e envie esses valores para o hub:

    ```CSharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

6. Finalmente, adicione as seguintes linhas de código à função **principal**, substituindo espaços reservados _device-id_, _your-iot-hub-name_ e _absolute-path-to-your-device-pfx-file_ conforme solicitado pelo seu programa de instalação.

    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

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
    ```

   Esse código se conecta ao seu Hub IoT ao criar a cadeia de caracteres de conexão para seu dispositivo X.509. Uma vez conectado com êxito, ele, em seguida, envia os eventos de temperatura e umidade para o hub e aguarda sua resposta. 
7. Já que este aplicativo acessa um arquivo *.pfx*, você pode precisar executar isso no modo *Admin*. Compile a solução do Visual Studio. Abra uma nova janela de comando como um **Administrador**e navegue até a pasta que contém essa solução. Navegue até o caminho *bin/Debug* dentro da pasta de solução. Execute o aplicativo **SimulateX509Device.exe** da janela de comando _Administrador_. Você verá o dispositivo se conectar ao hub e enviar os eventos com êxito. 

   ![Execute o aplicativo de dispositivo](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como proteger sua solução IoT, confira:

* [Práticas recomendadas de segurança de IoT](../iot-fundamentals/iot-security-best-practices.md)

* [Arquitetura de segurança IoT](../iot-fundamentals/iot-security-architecture.md)

* [Proteger sua implantação de IoT](../iot-fundamentals/iot-security-deployment.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
