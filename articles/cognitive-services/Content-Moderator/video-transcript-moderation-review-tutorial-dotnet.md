---
title: 'Tutorial: moderar vídeos e transcrições no .NET – Content Moderator'
titleSuffix: Azure Cognitive Services
description: Este tutorial ajuda a entender como criar uma solução completa de moderação de vídeo e transcrição com moderação assistida por computador e criação de análise com interação humana.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: e1c3d3d95261b959a7540aab73faf12f92801d4c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564270"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Tutorial: moderação de vídeos e transcrições

Neste tutorial, você aprenderá a criar uma solução completa de moderação de vídeo e transcrição com moderação assistida por computador e criação de análise com interação humana.

Este tutorial mostra como:

> [!div class="checklist"]
> - Compactar o(s) vídeo(s) de entrada para processamento mais rápido
> - Moderar o vídeo para obter instantâneos e quadros com insights
> - Usar os carimbos de data/hora do quadro para criar miniaturas (imagens)
> - Enviar carimbos de data/hora e miniaturas para criar análises de vídeos
> - Converter a conversação de fala em texto (transcrição) do vídeo com a API do Indexador de Mídia
> - Moderar a transcrição com o serviço de moderação de texto
> - Adicionar a transcrição moderada à análise de vídeo

## <a name="prerequisites"></a>Pré-requisitos

- Inscreva-se no site da [ferramenta de Análise do Content Moderator](https://contentmoderator.cognitive.microsoft.com/) e crie marcas personalizadas. Confira [Usar marcas](Review-Tool-User-Guide/tags.md) se precisar de ajuda com esta etapa.

    ![captura de tela das marcas personalizadas da Moderação de vídeo](images/video-tutorial-custom-tags.png)
- Para executar o aplicativo de exemplo, você precisará de uma conta do Azure, um recurso dos Serviços de Mídia do Azure, um recurso do Azure Content Moderator e credenciais do Azure Active Directory. Para obter instruções sobre como obter esses recursos, confira o guia da [API de Moderação de Vídeo](video-moderation-api.md).
- Baixe o [aplicativo de console de Análise de vídeo](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) no GitHub.

## <a name="enter-credentials"></a>Inserir as credenciais

Edite o arquivo `App.config` e adicione o nome do locatário do Active Directory, os pontos de extremidade de serviço e as chaves de assinatura indicados por `#####`. As seguintes informações são necessárias:

|Chave|DESCRIÇÃO|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Ponto de extremidade de API do AMS (Serviços de Mídia do Azure)|
|`ClientSecret`|Chave de assinatura para Serviços de Mídia do Azure|
|`ClientId`|ID do cliente para Serviços de Mídia do Azure|
|`AzureAdTenantName`|Nome do locatário do Active Directory que representa a organização|
|`ContentModeratorReviewApiSubscriptionKey`|Chave de assinatura para a API de análise do Content Moderator|
|`ContentModeratorApiEndpoint`|Ponto de extremidade para a API do Content Moderator|
|`ContentModeratorTeamId`|ID da equipe do Content Moderator|

## <a name="examine-the-main-code"></a>Examinar o código principal

A classe `Program` em `Program.cs` é o principal ponto de entrada do aplicativo de moderação de vídeo.

### <a name="methods-of-program-class"></a>Métodos da classe Program

|Método|DESCRIÇÃO|
|-|-|
|`Main`|Analisa a linha de comando, reúne a entrada do usuário e inicia o processamento.|
|`ProcessVideo`|Comprime, carrega, modera e cria análises de vídeo.|
|`CreateVideoStreamingRequest`|Cria um fluxo para carregar um vídeo|
|`GetUserInputs`|Coleta entrada do usuário: usado quando não há opções de linha de comando presentes|
|`Initialize`|Inicializa objetos necessários para o processo de moderação|

### <a name="the-main-method"></a>O método Principal

`Main()` é onde a execução inicia, então, é o local para começar o reconhecimento do processo de moderação de vídeo.

[!code-csharp[Main](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=20-24,42-52,54-74)]

`Main()` trata dos seguintes argumentos de linha de comando:

- O caminho para um diretório contendo arquivos de vídeo MPEG-4 a serem enviados para moderação. Todos os arquivos `*.mp4` neste diretório e os subdiretórios são submetidos para moderação.
- Opcionalmente, um sinalizador booliano (verdadeiro/falso) indicando se as transcrições de texto devem ser geradas com a finalidade de moderar o áudio.

Se nenhum argumento de linha de comando estiver presente, `Main()` chama `GetUserInputs()`. Esse método solicita que o usuário insira o caminho para um único arquivo de vídeo e especifique se uma transcrição de texto deve ser gerada.

> [!NOTE]
> O aplicativo de console usa a [API do Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) para gerar transcrições da faixa de áudio do vídeo carregado. Os resultados são fornecidos no formato WebVTT. Para obter mais informações sobre esse formato, consulte [Formato de faixas de texto de vídeo na Web](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Métodos Inicializar e Processar Vídeo

Independentemente das opções do programa serem provenientes da linha de comando ou da entrada interativa do usuário, o próximo `Main()` chama `Initialize()` para criar as instâncias a seguir:

|Classe|DESCRIÇÃO|
|-|-|
|`AMSComponent`|Compacta os arquivos de vídeo antes de enviá-los para moderação.|
|`AMSconfigurations`|Realiza interface com os dados de configuração do aplicativo, localizados em `App.config`.|
|`VideoModerator`| Upload, codificação, criptografia e moderação usando SDK do AMS|
|`VideoReviewApi`|Gerencia análises de vídeo no serviço do Content Moderator|

Essas classes (além de `AMSConfigurations`, que são simples) são abordadas com mais detalhes nas próximas seções deste tutorial.

Finalmente, os arquivos de vídeo são processados um de cada vez chamando `ProcessVideo()` para cada um.

[!code-csharp[ProcessVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=76-118)]

O método `ProcessVideo()` é bastante simples. Ele executa as seguintes operações na ordem:

- Compacta o vídeo
- Carrega o vídeo para um ativo dos Serviços de Mídia do Azure
- Cria um trabalho do AMS para moderar o vídeo
- Cria uma análise de vídeo no Content Moderator

As seções a seguir consideram em mais detalhes alguns dos processos individuais invocados por `ProcessVideo()`. 

## <a name="compress-the-video"></a>Compactar o vídeo

Para minimizar o tráfego, o aplicativo converte arquivos de vídeo para o formato H.264 (MPEG-4 AVC) e os dimensiona para uma largura máxima de 640 pixels. O codec H.264 é recomendado devido à alta eficiência (taxa de compressão). A compactação é feita usando a ferramenta de linha de comando `ffmpeg` gratuita, incluída na pasta `Lib` da solução do Visual Studio. Os arquivos de entrada podem ser de qualquer formato com suporte de `ffmpeg`, incluindo codecs e formatos de arquivos de vídeo normalmente mais utilizados.

> [!NOTE]
> Ao iniciar o programa usando as opções de linha de comando, você especifica um diretório contendo os arquivos de vídeo a serem enviados para moderação. Todos os arquivos nesse diretório com a extensão de nome do arquivo `.mp4` são processados. Para processar outras extensões de nome de arquivo, atualize o `Main()` método em `Program.cs` para incluir as extensões desejadas.

O código que compacta um único arquivo de vídeo é a `AmsComponent` classe em `AMSComponent.cs`. O método responsável por essa funcionalidade é `CompressVideo()`, mostrado aqui.

[!code-csharp[CompressVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/AMSComponent.cs?range=31-59)]

O código executa as etapas a seguir:

- Verifica para certificar-se de que a configuração em `App.config` contém todos os dados necessários
- Verifica para certificar-se de que o binário `ffmpeg` está presente
- Compila o nome do arquivo de saída acrescentando `_c.mp4` ao nome de base do arquivo (como `Example.mp4` -> `Example_c.mp4`)
- Cria uma cadeia de caracteres da linha de comando para executar a conversão
- Inicia um processo `ffmpeg` usando a linha de comando
- Aguarda que o vídeo seja processado

> [!NOTE]
> Se você souber que os vídeos já estão compactados usando o H.264 e têm dimensões apropriadas, será possível gravar novamente `CompressVideo()` para ignorar a compactação.

O método retorna o nome do arquivo de saída compactado.

## <a name="upload-and-moderate-the-video"></a>Carregar e moderar o vídeo

O vídeo deve ser armazenado nos Serviços de Mídia do Azure antes que possa ser processado pelo serviço do Content Moderation. A classe `Program` em `Program.cs` tem um método curto `CreateVideoStreamingRequest()` que retorna um objeto representando a solicitação de streaming usada para enviar o vídeo.

[!code-csharp[CreateVideoStreamingRequest](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=120-133)]

O objeto `UploadVideoStreamRequest` resultante é definido em `UploadVideoStreamRequest.cs` (e o pai, `UploadVideoRequest`, em `UploadVideoRequest.cs`). Essas classes não são mostradas aqui; elas são curtas e servem apenas para manter os dados de vídeo compactados e as respectivas informações do vídeo. Outra classe somente dados, `UploadAssetResult` (`UploadAssetResult.cs`) é usada para manter os resultados do processo de upload. Agora, é possível reconhecer essas linhas em `ProcessVideo()`:

[!code-csharp[ProcessVideoSnippet](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=91-104)]

Essas linhas executam as seguintes tarefas:

- Criar um `UploadVideoStreamRequest` para carregar o vídeo compactado
- Definir o sinalizador `GenerateVTT` da solicitação se o usuário solicitou uma transcrição de texto
- Chamar `CreateAzureMediaServicesJobToModerateVideo()` para executar o upload e receber o resultado

## <a name="examine-video-moderation-code"></a>Examinar o código de moderação de vídeo

O método `CreateAzureMediaServicesJobToModerateVideo()` está em `VideoModerator.cs`, que contém a maior parte do código que interage com os Serviços de Mídia do Azure. O código-fonte do método é mostrado na seguinte extração.

[!code-csharp[CreateAzureMediaServicesJobToModerateVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=230-283)]

Esse código executa as seguintes tarefas:

- Cria um trabalho do AMS para que o processamento seja feito
- Adiciona tarefas para codificar o arquivo de vídeo, moderando-o e gerando uma transcrição de texto
- Envia o trabalho, carregando o arquivo e iniciando o processamento
- Recupera os resultados de moderação, a transcrição do texto (se solicitado) e outras informações

## <a name="sample-video-moderation-output"></a>Saída de moderação de vídeo de exemplo

O resultado do trabalho de moderação de vídeo (consulte [início rápido de moderação de vídeo](video-moderation-api.md) é uma estrutura de dados JSON que contém os resultados de moderação. Esses resultados incluem uma divisão dos fragmentos (instantâneos) no vídeo, cada um contendo eventos (recortes) com quadros chave que foram sinalizados para revisão. Cada quadro chave é pontuado pela probabilidade de conter conteúdo para adulto. O exemplo a seguir mostra uma resposta JSON:

```json
{
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
        "start": 0,
        "duration": 18000
    },
    {
        "start": 18000,
        "duration": 3600,
        "interval": 3600,
        "events": [
        [
        {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
        }
        ]
    ]
    },
    {
        "start": 18386372,
        "duration": 119149,
        "interval": 119149,
        "events": [
        [
        {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
        }
    ]
    ]
    }
]
}
```

Uma transcrição do áudio do vídeo também é produzida quando o sinalizador `GenerateVTT` é definido.

> [!NOTE]
> O aplicativo de console usa a [API do Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) para gerar transcrições da faixa de áudio do vídeo carregado. Os resultados são fornecidos no formato WebVTT. Para obter mais informações sobre esse formato, consulte [Formato de faixas de texto de vídeo na Web](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

## <a name="create-a-human-review"></a>Criar uma análise humana

O processo de moderação retorna uma lista de quadros chave do vídeo, juntamente com uma transcrição das faixas de áudio. A próxima etapa é criar uma análise na ferramenta de análise do Content Moderator para moderadores humanos. Voltando ao método `ProcessVideo()` em `Program.cs`, você verá a chamada para o método `CreateVideoReviewInContentModerator()`. Este método está na classe `videoReviewApi`, que está em `VideoReviewAPI.cs`, e é mostrado aqui.

[!code-csharp[CreateVideoReviewInContentModerator](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=42-69)]

`CreateVideoReviewInContentModerator()` chama vários outros métodos para executar as seguintes tarefas:

> [!NOTE]
> O aplicativo de console usa a biblioteca [FFmpeg](https://ffmpeg.org/) para gerar miniaturas. Essas miniaturas (imagens) correspondem aos carimbos de data/hora do quadro na saída de moderação de vídeo.

|Tarefa|Métodos|Arquivo|
|-|-|-|
|Extrair os quadros chave do vídeo e criar miniaturas|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Examine a transcrição de texto, se disponível, para localizar áudio com conteúdo para adulto|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Preparar e enviar uma solicitação de análise de vídeo para inspeção humana|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

A tela a seguir mostra os resultados das etapas anteriores.

![Exibição padrão de análise de vídeo](images/video-tutorial-default-view.PNG)

## <a name="process-the-transcript"></a>Processar a transcrição

Até agora, o código apresentado neste tutorial concentrou-se no conteúdo visual. A análise do conteúdo de fala é um processo separado e opcional que, conforme mencionado, usa uma transcrição gerada a partir do áudio. Agora, é o momento de saber como as transcrições de texto são criadas e usadas no processo de análise. A tarefa de geração de transcrição compete ao serviço do [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content).

O aplicativo executa as seguintes tarefas:

|Tarefa|Métodos|Arquivo|
|-|-|-|
|Determina se as transcrições de texto devem ser geradas|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Em caso afirmativo, envia um trabalho de transcrição como parte da moderação|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Obtém uma cópia local da transcrição|`GenerateTranscript()`|`VideoModerator.cs`|
|Sinaliza quadros do vídeo que contêm áudio inapropriado|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Adiciona os resultados à análise|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Configuração de tarefa

Vamos direto à etapa de envio do trabalho de transcrição. `CreateAzureMediaServicesJobToModerateVideo()` chama (já descrito) `ConfigureTranscriptTask()`.

[!code-csharp[ConfigureTranscriptTask](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=295-304)]

A configuração da tarefa de transcrição é lida a partir do arquivo `MediaIndexerConfig.json` na pasta `Lib` da solução. Os ativos do AMS são criados para o arquivo de configuração e para a saída do processo de transcrição. Quando o trabalho do AMS é executado, essa tarefa cria uma transcrição de texto da faixa de áudio do arquivo de vídeo.

> [!NOTE]
> O aplicativo de exemplo reconhece a fala apenas em inglês dos EUA.

### <a name="transcript-generation"></a>Geração de transcrição

A transcrição é publicada como um ativo do AMS. Para examinar a transcrição de conteúdo censurável, o aplicativo baixa o ativo dos Serviços de Mídia do Azure. `CreateAzureMediaServicesJobToModerateVideo()` chama `GenerateTranscript()`, mostrado aqui, para recuperar o arquivo.

[!code-csharp[GenerateTranscript](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=351-370)]

Após algumas configurações necessárias do AMS, o download real é executado chamando `DownloadAssetToLocal()`, uma função genérica que copia um ativo do AMS para um arquivo local.

## <a name="moderate-the-transcript"></a>Moderar a transcrição

Com a transcrição em mãos, ela é examinada e utilizada na análise. A criação da análise é competência de `CreateVideoReviewInContentModerator()`, que chama `GenerateTextScreenProfanity()` para realizar o trabalho. Por sua vez, esse método chama `TextScreen()`, que contém a maior parte da funcionalidade.

`TextScreen()` realiza as seguintes tarefas:

- Analisa a transcrição para legendas e carimbos de data/hora
- Envia cada legenda para moderação de texto
- Sinaliza quaisquer quadros que possam ter conteúdo de fala censurável

Examinaremos cada uma dessas tarefas detalhadamente:

### <a name="initialize-the-code"></a>Inicializar o código

Primeiro, inicialize todas as variáveis e coleções.

[!code-csharp[TextScreen](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=515-527)]

### <a name="parse-the-transcript-for-captions"></a>Analisar a transcrição de legendas

Em seguida, analise a transcrição formatada de VTT para legendas e carimbos de data/hora. A ferramenta de análise exibe essas legendas na Guia Transcrição na tela de análise de vídeo. Os carimbos de data/hora são usados para sincronizar as legendas com os quadros de vídeo correspondentes.

[!code-csharp[TextScreen2](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=528-567)]

### <a name="moderate-captions-with-the-text-moderation-service"></a>Moderar legendas com o serviço de moderação de texto

Em seguida, examinaremos as legendas de texto analisadas com a API de Texto do Content Moderator.

> [!NOTE]
> A chave de serviço do Content Moderator possui um limite de taxa de RPS (solicitações por segundo). Se exceder o limite, o SDK lançará uma exceção com um código de erro 429.
>
> Uma chave de camada gratuita tem um limite de taxa de RPS.

[!code-csharp[TextScreen3](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=568-653)]

### <a name="text-moderation-breakdown"></a>Detalhamento da moderação de texto

`TextScreen()` é um método substancial, então, vamos dividi-lo.

1. Primeiro, o método lê o arquivo de transcrição linha por linha. Ele ignora linhas e linhas em branco contendo um `NOTE` com uma pontuação de confiança. Ele extrai os carimbos de data/hora e os itens de texto das *indicações* no arquivo. Uma indicação representa o texto da faixa de áudio e inclui horas de início e término. Uma indicação começa com a linha de carimbo de data/hora com a cadeia de caracteres `-->`. É acompanhada por uma ou mais linhas de texto.

1. Instâncias de `CaptionScreentextResult` (definido em `TranscriptProfanity.cs`) são usadas para manter as informações analisadas de cada indicação.  Quando uma nova linha de carimbo de data/hora é detectada, ou um comprimento máximo de texto de 1024 caracteres é alcançado, um novo `CaptionScreentextResult` é adicionado a `csrList`. 

1. O método seguinte envia cada indicação para a API de Moderação de Texto. Ele chama ambos `ContentModeratorClient.TextModeration.DetectLanguageAsync()` e `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, que são definidos no assembly `Microsoft.Azure.CognitiveServices.ContentModerator`. Para evitar ser limitado por taxa, o método pausará por um segundo antes de enviar cada indicação.

1. Após receber os resultados do serviço de Moderação de Texto, o método os analisa para verificar se atendem aos limites de confiança. Esses valores são estabelecidos em `App.config` como `OffensiveTextThreshold`, `RacyTextThreshold` e `AdultTextThreshold`. Por fim, os próprios termos censuráveis também são armazenados. Todos os quadros dentro do intervalo de tempo da indicação são marcados como contendo texto para adulto, ofensivo e/ou inadequado.

1. `TextScreen()` retorna um `TranscriptScreenTextResult` que contém o resultado de moderação de texto do vídeo como um todo. Esse objeto inclui sinalizadores e pontuações para os vários tipos de conteúdo censurável, junto com uma lista de todos os termos censuráveis. O chamador, `CreateVideoReviewInContentModerator()` chama `UploadScreenTextResult()` para anexar essas informações à análise, de modo que esteja disponível para revisores humanos.

A tela a seguir mostra o resultado das etapas de geração e moderação da transcrição.

![Exibição de transcrição de moderação de vídeo](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Saída do programa

A seguinte saída da linha de comando do programa mostra as várias tarefas à medida que são concluídas. Além disso, o resultado de moderação (no formato JSON) e a transcrição de fala estão disponíveis no mesmo diretório dos arquivos de vídeo originais.

```console
Microsoft.ContentModerator.AMSComponentClient
Enter the fully qualified local path for Uploading the video :
"Your File Name.MP4"
Generate Video Transcript? [y/n] : y

Video compression process started...
Video compression process completed...

Video moderation process started...
Video moderation process completed...

Video review process started...
Video Frames Creation inprogress...
Frames(83) created successfully.
Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
Video review successfully completed...

Total Elapsed Time: 00:05:56.8420355
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurará um aplicativo que modera o conteúdo de vídeo – incluindo o conteúdo de transcrição – e cria análises na ferramenta de Análise. Em seguida, saiba mais sobre os detalhes da moderação de vídeo.

> [!div class="nextstepaction"]
> [Moderação de vídeo](./video-moderation-human-review.md)
