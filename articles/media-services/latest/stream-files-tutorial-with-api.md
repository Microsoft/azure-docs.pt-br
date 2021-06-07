---
Título: Fazer upload, codificar e transmitir com os Serviços de Mídia v3: Descrição dos Serviços de Mídia do Azure: Tutorial mostrando como fazer upload de um arquivo, codificar vídeo e transmitir conteúdo com os Serviços de Mídia do Azure v3.
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: tutorial ms.custom: mvc ms.date: 17/03/2021 ms.author: inhenkel
---

# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>Tutorial: Fazer upload, codificar e transmitir vídeos com os Serviços de Mídia v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Embora este tutorial use exemplos do [SDK do .NET](/dotnet/api/microsoft.azure.management.media.models.liveevent), as etapas gerais são as mesmas para [API REST](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event) ou outros [SDKs](media-services-apis-overview.md#sdks) com suporte.

Os Serviços de Mídia do Azure permitem codificar seus arquivos de mídia em formatos que podem ser reproduzidos em uma ampla variedade de navegadores e dispositivos. Por exemplo, talvez você queira transmitir por streaming o conteúdo nos formatos MPEG-DASH ou HLS da Apple. Antes do streaming, será necessário codificar o arquivo de mídia digital de alta qualidade. Para obter ajuda com a codificação, confira [Conceito de codificação](encode-concept.md). Este tutorial carrega um arquivo de vídeo local e codifica o arquivo carregado. Também é possível codificar o conteúdo disponibilizado por meio de uma URL HTTPS. Para mais informações, consulte [Criar uma entrada de trabalho de uma URL HTTP(s)](job-input-from-http-how-to.md).

![Reproduzir um vídeo com o Player de Mídia do Azure](./media/stream-files-tutorial-with-api/final-video.png)

Este tutorial mostra como:

> [!div class="checklist"]
> * Baixar o aplicativo de exemplo descrito no tópico.
> * Examinar o código que carrega, codifica e transmite.
> * Execute o aplicativo.
> * Testar a URL de streaming.
> * Limpe os recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se o Visual Studio não estiver instalado, você poderá obter o [Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Crie uma conta de Serviços de Mídia](./account-create-how-to.md).<br/>Lembre-se dos valores que você usou para o nome do grupo de recursos e o nome da conta de Serviços de Mídia.
- Siga as etapas em [Acessar API de Serviços de Mídia com a CLI do Azure](./access-api-howto.md) e salve as credenciais. Você precisará usá-las para acessar a API.

## <a name="download-and-set-up-the-sample"></a>Baixar e configurar o exemplo

Clone um repositório do GitHub que tem o exemplo de streaming de .NET em seu computador usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

O exemplo está localizado na pasta [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles).

Abra [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) no projeto que você baixou. Substitua os valores pelas credenciais que você obteve de [acesso às APIs](./access-api-howto.md).

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Examine o código que carrega, codifica e transmite

Esta seção examina funções definidas no arquivo [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) do projeto *UploadEncodeAndStreamFiles*.

O exemplo executa as ações a seguir:

1. Cria uma nova **Transformação** (primeiro, verifica se a transformação especificada existe).
2. Cria um **Ativo** de saída que é usado como a saída do **Trabalho** de codificação.
3. Cria um **Ativo** de entrada e carrega o arquivo de vídeo local especificado nele. O ativo é usado como entrada do trabalho.
4. Envia o trabalho de codificação usando a entrada e saída criadas.
5. Verifica o status do trabalho.
6. Cria um **Localizador de Streaming**.
7. Compila as URLs de streaming.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a usar as APIs de Serviços de Mídia com a SDK .NET

Para começar a usar a APIs de Serviços de Mídia do Azure com o .NET, é necessário criar um objeto **AzureMediaServicesClient**. Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Azure AD. No código que você clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no arquivo de configuração local.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Criar um ativo de entrada e carregar um arquivo local para ele

A função **CreateInputAsset** cria um novo [Ativo](/rest/api/media/assets) de entrada e carrega o arquivo de vídeo local especificado para ele. Este **Ativo** é usado como entrada para o trabalho de codificação. Nos Serviços de Mídia v3, a entrada para um **Trabalho** pode ser um **Ativo** ou o conteúdo que você disponibiliza a sua conta do Serviços de Mídia por meio de URLs de HTTPS. Para saber como codificar com base em uma URL HTTPS, confira [este](job-input-from-http-how-to.md) artigo.

Em Serviços de Mídia v3, você usará as APIs de Armazenamento do Microsoft Azure para carregar os arquivos. O snippet .NET a seguir mostra como.

A função a seguir realiza essas ações:

* Cria um **Ativo**.
* Obtém uma [URL SAS](../../storage/common/storage-sas-overview.md) gravável para o [contêiner no armazenamento](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container) do ativo.

    Se estiver usando a função [ListContainerSas](/rest/api/media/assets/listcontainersas) do ativo para obter URLs SAS, observe que a função retorna várias URLs SAS, pois há duas chaves de conta de armazenamento para cada conta de armazenamento. Uma conta de armazenamento tem duas chaves porque permite a rotação contínua das chaves da conta de armazenamento (por exemplo, alterar a primeira enquanto se usa a outra e, em seguida, começar a usar a nova primeira chave e realizar a rotação da segunda). A primeira URL SAS representa a chave de armazenamento 1 e a segunda representa a chave de armazenamento 2.
* Carrega o arquivo para o contêiner no armazenamento usando a URL de SAS.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Criar um ativo de saída para armazenar o resultado de um trabalho

O [Ativo](/rest/api/media/assets) de saída armazena o resultado do trabalho de codificação. O projeto define a função **DownloadResults** que faz o download dos resultados desse ativo de saída para a pasta de "saída", para que você possa ver o que tem.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Criar uma transformação e um trabalho que codifica o arquivo carregado

Ao codificar ou processar o conteúdo nos Serviços de Mídia do Microsoft Azure, é um padrão comum configurar as configurações de codificação como uma receita. Em seguida, você pode enviar um **Trabalho** para aplicar essa fórmula a um vídeo. Ao enviar novos trabalhos para cada novo vídeo, você está aplicando essa fórmula a todos os vídeos na biblioteca. Uma receita nos Serviços de Mídia do Microsoft Azure é chamada de **Transformação**. Para obter mais informações, consulte [Transformações e Trabalhos](./transform-jobs-concept.md). O exemplo descrito neste tutorial define uma receita que codifica o vídeo para transmitir para uma variedade de dispositivos de iOS e Android.

#### <a name="transform"></a>Transformar

Ao criar um novo exemplo de [Transformação](/rest/api/media/transforms), você precisa especificar o que deseja produzir como uma saída. O parâmetro necessário é um objeto **TransformOutput**, como mostrado no código a seguir. Cada **TransformOutput** contém um **Predefinição**. **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que serão usadas para gerar o **TransformOutput** desejado. O exemplo descrito neste artigo usa uma predefinição chamada **AdaptiveStreaming**. A predefinição codifica o vídeo de entrada em uma escada de taxa de bits gerada automaticamente (pares de resolução de taxa de bits) com base na taxa de bits e resolução de entrada e produz arquivos ISO MP4 com vídeo H. 264 e correspondente a cada par de resolução de taxa de bits de áudio AAC. Para obter informações sobre essa predefinição, consulte [escada de bitrate de geração automática](encode-autogen-bitrate-ladder.md).

Você pode usar um EncoderNamedPreset interno ou usar as predefinições personalizadas. Para obter mais informações, consulte [Como personalizar predefinições do codificador](transform-custom-presets-how-to.md).

Ao criar uma [Transformação](/rest/api/media/transforms), você deverá verificar primeiro se já existe uma usando o método **Get**, conforme mostrado no código a seguir. Em Serviços de Mídia v3, os métodos **Get** em entidades retornam **nulo** se a entidade não existe (uma verificação de maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Trabalho

Conforme mecionado acima, o objeto de [Transformação](/rest/api/media/transforms) é a receita e um [Trabalho](/rest/api/media/jobs) é a solicitação real para os Serviços de Mídia para aplicar a **Transformação** a um determinado conteúdo de vídeo ou áudio de entrada. O **Trabalho** especifica informações como o local da entrada vídeo e o local da saída.

Neste exemplo, o vídeo de entrada foi carregado a partir de sua máquina local. Se quiser saber como codificar usando uma URL HTTPS, confira [este](job-input-from-http-how-to.md) artigo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Aguarde a conclusão do trabalho

Este trabalho levará algum tempo para concluir e quando você desejar ser notificado. O exemplo de código a seguir mostra como sondar o serviço para o status do [Trabalho](/rest/api/media/jobs). Sondagem não é uma prática recomendada para aplicativos de produção devido à latência potencial. A sondagem pode ser acelerada, se houver uso excessivo em uma conta. Os desenvolvedores devem usar a Grade de Eventos.

A Grade de Eventos foi projetada para alta disponibilidade, desempenho consistente e dimensionamento dinâmico. Com a Grade de Eventos, seus aplicativos podem escutar e reagir a eventos de praticamente todos os serviços do Azure, bem como de origens personalizadas. A manipulação de eventos simples e reativa baseada em HTTP ajuda você a criar soluções eficientes por meio da filtragem e do roteamento de eventos.  Consulte [Rotear eventos para um ponto de extremidade personalizado de web](monitoring/job-state-events-cli-how-to.md).

O **Trabalho** normalmente passa pelos seguintes estados: **Agendado**, **Enfileirado**, **Processando**, **Concluído** (o estado final). Se o trabalho encontrou um erro, você receberá o estado do **Erro**. Se o trabalho estiver para ser cancelado, você obterá **Cancelando** e **Cancelado** quando estiver pronto.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Códigos de erro do trabalho

Confira [Códigos de erro](/rest/api/media/jobs/get#joberrorcode).

### <a name="get-a-streaming-locator"></a>Obter um Localizador de Streaming

Depois que a codificação for concluída, a próxima etapa é tornar no vídeo no ativo de saída disponível para os clientes para reprodução. Você pode disponibilizá-lo em duas etapas: primeiro, crie um [Localizador de Streaming](/rest/api/media/streaminglocators) e, em seguida, crie as URLs de streaming que os clientes podem usar.

O processo de criação de um **Localizador de Streaming** é chamado de publicação. Por padrão, o **Localizador de Streaming** é válido imediatamente após você fazer as chamadas à API e dura até ser excluído, a menos que você configure os horários de início e término opcionais.

Ao criar um [StreamingLocator](/rest/api/media/streaminglocators), você precisará especificar o **StreamingPolicyName** desejado. Neste exemplo, você fará o streaming limpo (ou de conteúdo não criptografado), de modo que a política de streaming transparente predefinida (**PredefinedStreamingPolicy.ClearStreamingOnly**) é utilizada.

> [!IMPORTANT]
> Ao usar uma [Política de Streaming](/rest/api/media/streamingpolicies) personalizada, você deve criar um conjunto limitado de políticas para a conta de Serviço de Mídia e reutilizá-los para os Localizadores de Streaming sempre que as mesmas opções e protocolos de criptografia forem necessários. A conta de Serviço de Mídia tem uma cota para o número de entradas de Política de Streaming. Você não deve criar uma nova Política de Streaming para cada Localizador de Streaming.

O código a seguir pressupõe que você esteja chamando a função com um locatorName exclusivo.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Ao passo que o exemplo deste tópico discute streaming, você pode usar a mesma chamada para criar um Localizador de Streaming para fornecimento de vídeo por meio de download progressivo.

### <a name="get-streaming-urls"></a>Obter URLs de streaming

Agora que o [Localizador de Streaming](/rest/api/media/streaminglocators) foi criado, você pode obter as URLs de streaming, conforme mostrado em **GetStreamingURLs**. Para criar uma URL, você precisa concatenar o nome do host do [Ponto de Extremidade de Streaming](/rest/api/media/streamingendpoints) com o caminho do **Localizador de Streaming**. Neste exemplo, o *padrão* do **Ponto de Extremidade de Streaming** é usado. Ao criar uma conta de Serviço de Mídia, este *padrão* **Ponto de Extremidade de Streaming** estará em um estado parado, portanto você precisa chamar **Iniciar**.

> [!NOTE]
> Neste método, é necessário o locatorName que foi usado durante a criação do **Localizador de Streaming** para o Ativo gerado.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos em sua conta de Serviços de Mídia

Em geral, você deve limpar tudo, exceto os objetos que planeja reutilizar (normalmente, você reutilizará transformações e persistirá StreamingLocators etc.). Se desejar que sua conta seja limpa após fazer experimentos, exclua os recursos que não planeja reutilizar. Por exemplo, o seguinte código exclui o trabalho, os ativos criados e a política de chave de conteúdo:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

1. Pressione Ctrl + F5 para executar o aplicativo *EncodeAndStreamFiles*.
2. Copie uma das URLs de streaming do console.

Este exemplo exibe as URLs que podem ser usadas para reproduzir o vídeo usando protocolos diferentes:

![Exemplo de saída mostrando URLs para o vídeo de streaming dos Serviços de Mídia](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Testar a URL de streaming

Para testar o streaming, este artigo usa o Player de Mídia do Azure.

> [!NOTE]
> Se um player estiver hospedado em um site https, atualize a URL para "https".

1. Abra um navegador da Web e navegue até [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Na caixa **URL:** , cole um dos valores de URL de streaming que você obteve ao executar o aplicativo.
3. Selecione **Atualizar Player**.

O Player de Mídia do Azure pode ser usado para testes, mas não deve ser usado em um ambiente de produção.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que você criou neste tutorial, exclua o grupo de recursos criados anteriormente.

Execute este comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Os SDKs dos Serviços de Mídia do Azure v3 não são thread-safe. Ao desenvolver um aplicativo multithread, você deve gerar e usar um novo objeto AzureMediaServicesClient por thread.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como carregar, codificar e transmitir seu vídeo, consulte o seguinte artigo: 

> [!div class="nextstepaction"]
> [Analisar vídeos](analyze-videos-tutorial.md)
