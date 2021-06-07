---
title: Carregar arquivos de dispositivos para o Hub IoT do Azure com .NET | Microsoft Docs
description: Como carregar arquivos de um dispositivo para a nuvem usando o SDK do dispositivo IoT do Azure para .NET. Os arquivos carregados são armazenados em um contêiner de Azure Storage Blob.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 43cafb8c5efe0581fe7c4136aa41980b3d817be2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99981401"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Fazer upload de arquivos do seu dispositivo na nuvem com o Hub IoT (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial se baseia no código do tutorial [Enviar mensagens de nuvem para dispositivo com o Hub IoT](iot-hub-csharp-csharp-c2d.md) para mostrar como usar os recursos de carregamento de arquivo do Hub IoT. Ele mostra como:

* Fornecer com segurança um URI de blob do Azure a um dispositivo para carregamento de um arquivo.

* Usar as notificações de carregamento de arquivo do Hub IoT para disparar o processamento do arquivo no back-end do aplicativo.

O início rápido [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md) e o tutorial [Enviar mensagens de nuvem para dispositivo com o Hub IoT](iot-hub-csharp-csharp-c2d.md) mostram a funcionalidade básica do sistema de mensagens de dispositivo para nuvem e de nuvem para dispositivo do Hub IoT. O tutorial [Configurar o roteamento de mensagens com o Hub IoT](tutorial-routing.md) descreve uma forma de armazenar mensagens de dispositivo para nuvem de maneira confiável no Armazenamento de blobs do Microsoft Azure. No entanto, em alguns cenários não é possível mapear facilmente os dados que seus dispositivos enviam em mensagens relativamente pequenas de dispositivo para nuvem que o Hub IoT aceita. Por exemplo:

* Arquivos grandes que contêm imagens

* vídeos

* Dados de vibração amostrados a alta frequência

* Alguma forma de dados pré-processados

Esses arquivos normalmente são processados em lote na nuvem usando ferramentas como o [Azure Data Factory](../data-factory/introduction.md) ou a pilha do [Hadoop](../hdinsight/index.yml). Quando você precisar carregar arquivos de um dispositivo, ainda poderá usar a segurança e a confiabilidade do Hub IoT.

No final deste tutorial, você executará dois aplicativos do console .NET:

* **SimulatedDevice**. Esse aplicativo carrega um arquivo no armazenamento usando um URI SAS fornecido pelo seu Hub IoT. É uma versão modificada do aplicativo criado no tutorial [Enviar mensagens de nuvem para dispositivo com o Hub IoT](iot-hub-csharp-csharp-c2d.md).

* **ReadFileUploadNotification**. Este aplicativo recebe notificações de upload de arquivo do hub IoT.

> [!NOTE]
> O Hub IoT é compatível com muitas plataformas de dispositivo e linguagens incluindo C, Java, Python e Javascript por meio dos SDKs do dispositivo IoT do Azure. Confira a [Central de Desenvolvedores do IoT do Azure](https://azure.microsoft.com/develop/iot) para obter instruções passo a passo sobre como conectar seu dispositivo ao Hub IoT do Azure.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio

* Uma conta ativa do Azure. Se você não tem uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Verifique se a porta 8883 está aberta no firewall. O exemplo de dispositivo deste artigo usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Carregar um arquivo de um aplicativo de dispositivo

Nesta seção, você modifica o aplicativo de dispositivo criado em [Enviar mensagens de nuvem para dispositivo com o Hub IoT](iot-hub-csharp-csharp-c2d.md) para receber mensagens de nuvem para dispositivo do hub IoT.

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto **SimulatedDevice** e selecione **Adicionar** > **Item Existente**. Encontre um arquivo de imagem e inclua-o no projeto. Este tutorial pressupõe que a imagem é denominada `image.jpg`.

1. Clique com o botão direito do mouse na imagem e selecione **Propriedades**. Verifique se **Copiar para o Diretório de Saída** está definido como **Copiar sempre**.

    ![Mostrar onde atualizar a propriedade de imagem para copiar para o diretório de saída](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. No arquivo **Program.cs** , adicione as seguintes instruções na parte superior do arquivo:

    ```csharp
    using System.IO;
    ```

1. Adicione o seguinte método à classe **Programa** :

    ```csharp
    private static async Task SendToBlobAsync(string fileName)
    {
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        await deviceClient.GetFileUploadSasUriAsync(new FileUploadSasUriRequest { BlobName = fileName });
        var blob = new CloudBlockBlob(sas.GetBlobUri());
        await blob.UploadFromFileAsync(fileName);
        await deviceClient.CompleteFileUploadAsync(new FileUploadCompletionNotification { CorrelationId = sas.CorrelationId, IsSuccess = true });

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    O método `UploadToBlobAsync` usa o nome de arquivo e a fonte de transmissão do arquivo a ser carregado e manipula o upload para o armazenamento. O aplicativo de console exibe o tempo necessário para carregar o arquivo.

1. Adicione a linha a seguir no método **Main**, logo antes de `Console.ReadLine()`:

    ```csharp
    await SendToBlobAsync("image.jpg");
    ```

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição, como uma retirada exponencial, conforme sugerido em [Tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do hub IoT

Neste artigo, você cria um serviço de back-end para receber mensagens de notificação de upload de arquivo do hub IoT criado em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md). Para receber mensagens de notificação de upload de arquivo, seu serviço precisa da permissão **conexão de serviço**. Por padrão, todo Hub IoT é criado com uma política de acesso compartilhado chamada **serviço** que concede essa permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Receber uma notificação de upload de arquivo

Nesta seção, você escreverá um aplicativo de console do .NET que recebe mensagens de notificação de upload de arquivo do Hub IoT.

1. Na solução atual do Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**. Em **Criar um projeto**, selecione **Aplicativo de Console (.NET Framework)** e, em seguida, selecione **Próximo**.

1. Nomeie o projeto *ReadFileUploadNotification*. Em **Solução**, selecione **Adicionar à solução**. Selecione **Criar** para criar o cluster.

    ![Configurar o projeto ReadFileUploadNotification no Visual Studio](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ReadFileUploadNotification** e selecione **Gerenciar Pacotes NuGet**.

1. No **Gerenciador de Pacotes NuGet**, selecione **Procurar**. Pesquise e selecione **Microsoft.Azure.Devices** e, em seguida, selecione **Instalar**.

    Esta etapa baixa, instala e adiciona uma referência ao [pacote NuGet do SDK do serviço IoT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) no projeto **ReadFileUploadNotification**.

1. No arquivo **Program.cs** deste projeto, adicione a seguinte instrução na parte superior do arquivo:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. Adicione os seguintes campos à classe **Program** . Substitua o valor do espaço reservado `{iot hub connection string}` pela cadeia de conexão do hub IoT que você copiou anteriormente em [Obter a cadeia de conexão do hub IoT](#get-the-iot-hub-connection-string):

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Adicione o seguinte método à classe **Programa** :

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    É importante lembrar que o padrão de recebimento é o mesmo usado para receber mensagens da nuvem para o dispositivo do aplicativo do dispositivo.

1. Por fim, adicione as seguintes linhas ao método **Principal** :

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e selecione **Definir Projetos de Inicialização**.

1. Em **Propriedades Comuns** > **Projeto de Inicialização**, selecione **Vários projetos de inicialização**, em seguida, selecione a ação **Iniciar** para **ReadFileUploadNotification** e **SimulatedDevice**. Selecione **OK** para salvar suas alterações.

1. Pressione **F5**. Ambos os aplicativos devem ser iniciados. Você deve ver o carregamento concluído em um aplicativo de console e a mensagem de notificação do carregamento sendo recebida pelo outro aplicativo de console. Você pode usar o [Portal do Azure](https://portal.azure.com/) ou o Gerenciador de Servidores do Visual Studio para verificar a presença do arquivo carregado em sua conta de Armazenamento do Azure.

    ![Captura de tela mostrando a tela de saída](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar os recursos de carregamento de arquivo do Hub IoT para simplificar os carregamentos de arquivos de dispositivos. Você pode continuar explorando os recursos e cenários do Hub IoT com os seguintes artigos:

* [Criar um Hub IoT de modo programático](iot-hub-rm-template-powershell.md)

* [Introdução ao SDK C](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/quickstart-linux.md)