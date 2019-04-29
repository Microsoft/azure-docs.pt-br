---
title: Introdução ao fornecimento de conteúdo sob demanda usando o .NET | Microsoft Docs
description: Este tutorial orienta você pelas etapas de implementação de um aplicativo de fornecimento de conteúdo sob demanda com os Serviços de Mídia do Azure usando .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 2d55af3e9ed3ad64f9ba7726799b31acb6b48580
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465009"
---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Introdução ao fornecimento de conteúdo sob demanda usando o SDK do .NET  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

Este tutorial o orienta ao longo das etapas de implementação de um serviço básico de fornecimento de conteúdo de VoD (Vídeo sob Demanda) com o aplicativo AMS (Serviços de Mídia do Azure) usando o SDK .NET dos Serviços de Mídia do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir o tutorial:

* Uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta dos Serviços de Mídia. Para criar uma conta de Serviços de Mídia, consulte [Como criar uma conta de Serviços de Mídia](media-services-portal-create-account.md).
* .NET Framework 4.0 ou posterior.
* Visual Studio.

Este tutorial inclui as seguintes tarefas:

1. Iniciar pontos de extremidade de streaming (usando o portal do Azure).
2. Criar e configurar um projeto do Visual Studio.
3. Conectar-se à conta dos Serviços de Mídia.
2. Carregar um arquivo de vídeo.
3. Codificar o arquivo de origem em um conjunto de arquivos MP4 com taxa de bits adaptável.
4. Publicar o ativo e obter URLs de download progressivo e streaming.  
5. Reproduzir o conteúdo.

## <a name="overview"></a>Visão geral
Este tutorial orienta você pelas etapas de implementação de um aplicativo de entrega de conteúdo de vídeo sob demanda (VoD) com os Serviços de Mídia do Azure (AMS) para .NET.

O tutorial apresenta o fluxo de trabalho básico dos Serviços de Mídia e os objetos e as tarefas de programação mais comuns necessárias para o desenvolvimento dos Serviços de Mídia do Microsoft Azure. No final do tutorial, você poderá transmitir ou baixar progressivamente um arquivo de mídia de exemplo que você carregou, codificou e baixou.

### <a name="ams-model"></a>Modelo do AMS

A imagem a seguir mostra alguns dos objetos mais usados ao desenvolver aplicativos VoD em relação ao modelo de OData de Serviços de Mídia.

Clique na imagem para exibi-la em tamanho normal.  

<a href="./media/media-services-dotnet-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-dotnet-get-started/media-services-overview-object-model-small.png"></a> 

Você pode exibir todo o modelo [aqui](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Iniciar pontos de extremidade de streaming usando o portal do Azure

Ao trabalhar com os Serviços de Mídia do Azure, um dos cenários mais comuns o fornecimento de vídeo via streaming de taxa de bits adaptável. Os Serviços de Mídia fornecem um empacotamento dinâmico que permite a você enviar o conteúdo codificado para MP4 da taxa de bits adaptável nos formatos de transmissão suportados pelos Serviços de Mídia (MPEG DASH, HLS, Smooth Streaming) just-in-time, sem ter que armazenar as versões recolocadas de cada um dos formatos de transmissão.

>[!NOTE]
>Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado **Executando**.

Para iniciar o ponto de extremidade de streaming, faça o seguinte:

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Na janela Configurações, clique em Pontos de extremidade de streaming.
3. Clique no ponto de extremidade de streaming padrão.

    A janela DETALHES DO PONTO DE EXTREMIDADE DE STREAMING PADRÃO é exibida.

4. Clique no ícone Iniciar.
5. Clique no botão Salvar para salvar as alterações.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

1. Configure seu ambiente de desenvolvimento e preencha o arquivo de configuração app.config com as informações de conexão, conforme descrito em [Desenvolvimento de Serviços de Mídia com o .NET](media-services-dotnet-how-to-use.md). 
2. Crie uma nova pasta (a pasta pode estar em qualquer lugar na unidade local) e copie um arquivo .mp4 que você deseja codificar e transmitir ou baixar progressivamente. Este exemplo usa o caminho "C:\VideoFiles".

## <a name="connect-to-the-media-services-account"></a>Conectar-se à conta dos Serviços de Mídia

Ao usar os serviços de mídia com o .NET, você deve usar a classe **CloudMediaContext** para a maioria das tarefas de programação dos Serviços de Mídia: conectar-se à conta de Serviços de Mídia; criar, atualizar, acessar e excluir os seguintes objetos: ativos, arquivos de ativos, trabalhos, políticas de acesso, localizadores, etc.

Substitua a classe Program padrão pelo código a seguir: O código demonstra como ler os valores de conexão por meio do arquivo App.config e como criar o objeto **CloudMediaContext** para poder se conectar aos Serviços de Mídia. Para saber mais, consulte [Conectar-se à API dos Serviços de Mídia](media-services-use-aad-auth-to-access-ams-api.md).

Atualize o nome do arquivo e o caminho onde está o arquivo de mídia.

A função **Main** chama métodos que serão definidos posteriormente nesta seção.

> [!NOTE]
> Você receberá erros de compilação até que adicione definições a todas as funções que são definidas posteriormente neste artigo.

```csharp
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Add calls to methods defined in this section.
            // Make sure to update the file name and path to where you have your media file.
            IAsset inputAsset =
            UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

            IAsset encodedAsset =
            EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

            PublishAssetGetURLs(encodedAsset);
        }
        catch (Exception exception)
        {
            // Parse the XML error message in the Media Services response and create a new
            // exception with its content.
            exception = MediaServicesExceptionParser.Parse(exception);

            Console.Error.WriteLine(exception.Message);
        }
        finally
        {
            Console.ReadLine();
        }
        }
```

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Criar um novo ativo e carregar um arquivo de vídeo

No Serviços de Mídia, você carrega (ou ingere) seus arquivos digitais em um ativo. A entidade **Asset** pode conter vídeo, áudio, imagens, coleções de miniaturas, sequências de texto e arquivos de legendas (e os metadados sobre esses arquivos).  Depois que os arquivos são carregados, o conteúdo é armazenado com segurança na nuvem para processamento adicional e transmissão. Os arquivos no ativo são chamados **Arquivos de Ativo**.

O método **UploadFile** definido abaixo chama **CreateFromFile** (definido em extensões do SDK .NET). **CreateFromFile** cria um novo ativo no qual o arquivo de origem especificado é carregado.

O método **CreateFromFile** contém **AssetCreationOptions**, o que permite especificar uma das seguintes opções de criação de ativos:

* **None** - nenhuma criptografia é usada. Esse é o valor padrão. Observe que, ao usar essa opção, seu conteúdo não será protegido quando estiver em trânsito ou em repouso no armazenamento.
  Se você pretende enviar um MP4 usando o download progressivo, use essa opção.
* **StorageEncrypted** – use essa opção para criptografar seu conteúdo limpo localmente usando a criptografia AES de 256 bits e, em seguida, carregá-lo para o armazenamento do Azure, onde ele é armazenado, criptografado em repouso. Ativos protegidos pela criptografia de armazenamento são descriptografados automaticamente e posicionados em um sistema de arquivos criptografado antes da codificação, então opcionalmente criptografados novamente antes do carregamento como um novo ativo de saída. O caso de uso primário para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte em repouso no disco.
* **CommonEncryptionProtected** — use esta opção se você estiver carregando conteúdo que já foi criptografado e protegido com criptografia comum ou DRM PlayReady (por exemplo, Smooth Streaming protegido com DRM PlayReady).
* **EnvelopeEncryptionProtected** – use esta opção se você estiver carregando HLS criptografado com AES. Observe que os arquivos devem ter sido codificados e criptografados pelo Gerenciador de Transformação.

O método **CreateFromFile** também permite especificar um retorno de chamada para relatar o progresso do upload do arquivo.

No exemplo a seguir, podemos especificar **Nenhum** para as opções de ativo.

Adicionar o método a seguir à classe do programa.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Codificar o arquivo de origem em um conjunto de arquivos MP4 com taxa de bits adaptável
Após a inserção de Ativos nos Serviços de Mídia, a mídia poderá ser codificada, transmultiplexada, marcada com marca d'água e assim por diante antes que seja entregue aos clientes. Essas atividades são agendadas e executadas em contraste com várias instâncias de função de plano de fundo para garantir a disponibilidade e desempenho elevados. Essas atividades são chamadas de Trabalhos, e cada Trabalho é composto por Tarefas atômicas, que fazem o trabalho real no arquivo do Ativo.

Como mencionado anteriormente, ao trabalhar com os Serviços de Mídia do Azure, um dos cenários mais comuns é fornecer streaming com uma taxa de bits adaptável aos clientes. Os Serviços de Mídia podem empacotar dinamicamente um conjunto de arquivos MP4 com taxa de bits adaptável em um dos seguintes formatos: HTTP Live Streaming (HLS), Smooth Streaming e MPEG DASH.

Para tirar proveito do empacotamento dinâmico, você precisa codificar ou transcodificar seu arquivo mezanino (fonte) em um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos Smooth Streaming de taxa de bits adaptável.  

O código a seguir mostra como enviar um trabalho de codificação. O trabalho contém uma tarefa que determina a transcodificação do arquivo de mezanino em um conjunto de MP4s de taxa de bits adaptável usando o **Codificador de Mídia Standard**. O código envia o trabalho e aguarda até que ele seja concluído.

Depois que o trabalho for concluído, você poderá transmitir seu ativo ou baixar progressivamente arquivos MP4 criados como resultado de transcodificação.

Adicionar o método a seguir à classe do programa.

```csharp
    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "Adaptive Streaming",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }
```

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publicar o ativo e obter URLs para streaming e download progressivo

Para transmitir ou baixar um ativo, primeiro você precisa "publicá-lo" criando um localizador. Os localizadores fornecem acesso aos arquivos contidos no ativo. Os Serviços de Mídia oferecem suporte a dois tipos de localizadores: OnDemandOrigin, usados para transmitir mídia por streaming (por exemplo, MPEG DASH, HLS ou Smooth Streaming) e SAS (Assinatura de Acesso), usados para baixar arquivos de mídia.

### <a name="some-details-about-url-formats"></a>Alguns detalhes sobre os formatos de URL

Depois de criar os localizadores, você pode criar as URLs que seriam usadas para transmitir ou baixar os arquivos. O exemplo neste tutorial produz URLs que podem ser coladas em navegadores apropriados. Esta seção fornece apenas breves exemplos da aparência de formatos diferentes.

#### <a name="a-streaming-url-for-mpeg-dash-has-the-following-format"></a>Uma URL de streaming para MPEG DASH tem o seguinte formato:

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest **(format=mpd-time-csf)**

#### <a name="a-streaming-url-for-hls-has-the-following-format"></a>Uma URL de streaming para HLS tem o seguinte formato:

{nome do ponto de extremidade de streaming - nome de conta dos serviços de mídia}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest **(format=m3u8-aapl)**

#### <a name="a-streaming-url-for-smooth-streaming-has-the-following-format"></a>Uma URL de streaming para Smooth Streaming tem o seguinte formato:

{nome do ponto de extremidade de streaming - nome de conta do dos serviços de mídia}.streaming.mediaservices.windows.net/{ID do localizador}/{nome do arqui}.ism/Manifest


#### <a name="a-sas-url-used-to-download-files-has-the-following-format"></a>Uma URL SAS usada para baixar arquivos tem o seguinte formato:

{nome do contêiner de blob}/{nome do ativo}/{nome do arquivo}/{assinatura SAS}

Extensões do SDK do .NET dos Serviços de Mídia fornecem métodos auxiliares práticos, que retornam URLs formatadas para o ativo publicado.

O código a seguir usa extensões do SDK .NET para criar os localizadores e obter streaming e URLs de download progressivo. O código também mostra como baixar os arquivos em uma pasta local.

Adicionar o método a seguir à classe do programa.

```csharp
    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }
```

## <a name="test-by-playing-your-content"></a>Testar ao reproduzir o conteúdo

Depois que você executar o programa definido na seção anterior, as URLs semelhantes à seguinte serão exibidas na janela do console.

URLs de streaming adaptáveis:

Smooth Streaming

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URLs de download progressivo (áudio e vídeo).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Para transmitir o vídeo, cole a URL na caixa de texto de URL no [Azure Media Services Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html).

Para testar o download progressivo, cole uma URL em um navegador (por exemplo, Internet Explorer, Chrome ou Safari).

Para saber mais, consulte os tópicos a seguir:

- [Reprodução de seu conteúdo com players existentes](media-services-playback-content-with-existing-players.md)
- [Desenvolver aplicativos de player de vídeo](media-services-develop-video-players.md)
- [Inserindo um vídeo de streaming adaptável MPEG-DASH em um aplicativo HTML5 com DASH.js](media-services-embed-mpeg-dash-in-html5.md)

## <a name="download-sample"></a>Baixar exemplo
O exemplo de código a seguir contém o código que você criou neste tutorial: [exemplo](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]



<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: https://go.microsoft.com/fwlink/?linkid=255386
[Portal]: https://portal.azure.com/
