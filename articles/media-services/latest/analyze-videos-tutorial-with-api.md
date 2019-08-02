---
title: Analisar os vídeos com os Serviços de Mídia do Azure | Microsoft Docs
description: Siga as etapas deste tutorial para analisar vídeos usando os Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d31d102300cf23e068aee6bec9ea6d253e874dca
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653967"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Tutorial: Analisar vídeos com os Serviços de Mídia v3

> [!NOTE]
> Embora o tutorial use os exemplos do [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet), as etapas gerais são as mesmas para [API REST](https://docs.microsoft.com/rest/api/media/liveevents), [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest) ou outros [SDKs](media-services-apis-overview.md#sdks) suportados.

Este tutorial mostra como analisar vídeos com os Serviços de Mídia do Azure. Há muitos cenários em que você talvez queira obter ideias profundas sobre vídeos gravados ou conteúdo de áudio. Por exemplo, para obter maior satisfação do cliente, as organizações podem executar processamento de fala para texto para converter gravações de suporte do cliente em um catálogo pesquisável com índices e painéis. Em seguida, podem obter ideias sobre seus negócios como uma lista de reclamações comuns, fontes de tais reclamações e outras informações úteis.

Este tutorial mostra como:    
 
> [!div class="checklist"]
> * Baixe o aplicativo de exemplo descrito no tópico
> * Examinar o código que analisa o vídeo especificado
> * Execute o aplicativo
> * Examinar a saída
> * Limpar recursos

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se o Visual Studio não estiver instalado, você poderá obter o [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).<br/>Lembre-se dos valores que você usou para o nome do grupo de recursos e o nome da conta de Serviços de Mídia.
- Siga as etapas em [Acessar API de Serviços de Mídia com a CLI do Azure](access-api-cli-how-to.md) e salve as credenciais. Você precisará usá-las para acessar a API.

## <a name="download-and-configure-the-sample"></a>Baixar e configurar o exemplo

Clone um repositório do GitHub que contém o exemplo de .NET em sua máquina usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

O exemplo está localizado na pasta [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

Abra [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) no projeto que você baixou. Substitua os valores pelas credenciais que você obteve de [acesso às APIs](access-api-cli-how-to.md).

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Examinar o código que analisa o vídeo especificado

Esta seção examina funções definidas no arquivo [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) do projeto *AnalyzeVideos*.

O exemplo executa as ações a seguir:

1. Cria uma **Transformação** e um **Trabalho** que analisa o seu vídeo.
2. Cria um **Ativo** de entrada e carrega o vídeo para ele. O ativo é usado como entrada do trabalho.
3. Cria um ativo de saída que armazena a saída do trabalho. 
4. Envia o trabalho.
5. Verifica o status do trabalho.
6. Baixa os arquivos resultantes da execução do trabalho. 

> [!NOTE]
> Ao usar as predefinições do Video ou Audio Analyzer, use o portal do Azure para definir sua conta como 10 Unidades Reservada para Mídia S3. Para saber mais, confira [Processamento de mídia de escala](media-reserved-units-cli-how-to.md).

### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a usar as APIs de Serviços de Mídia com a SDK .NET

Para começar a usar a APIs de Serviços de Mídia do Azure com o .NET, é necessário criar um objeto **AzureMediaServicesClient**. Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. No código que você clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no arquivo de configuração local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Criar um ativo de entrada e carregar um arquivo local para ele 

A função **CreateInputAsset** cria um novo [Ativo](https://docs.microsoft.com/rest/api/media/assets) de entrada e carrega o arquivo de vídeo local especificado para ele. Este Ativo é usado como entrada para o trabalho de codificação. Em Serviços de Mídia v3, a entrada para um trabalho pode ser um ativo ou pode ser o conteúdo que você disponibiliza a sua conta do Serviços de Mídia por meio de URLs de HTTPS. Se você quiser saber como codificar a partir de uma URL HTTPS, consulte [este](job-input-from-http-how-to.md) artigo.  

Em Serviços de Mídia v3, você usará as APIs de Armazenamento do Microsoft Azure para carregar os arquivos. O snippet .NET a seguir mostra como.

A função a seguir realiza essas ações:

* Cria um ativo 
* Obtém uma [URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) gravável ao contêiner [do Ativo no armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)
* Carrega o arquivo para o contêiner no armazenamento usando a URL de SAS

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Criar um ativo de saída para armazenar o resultado do trabalho 

O [Ativo](https://docs.microsoft.com/rest/api/media/assets) de saída armazena o resultado do seu trabalho. O projeto define a função **DownloadResults** que faz o download dos resultados desse ativo de saída para a pasta de "saída", para que você possa ver o que tem.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Criar uma transformação e um trabalho que analisa os vídeos

Ao codificar ou processar o conteúdo nos Serviços de Mídia do Microsoft Azure, é um padrão comum para configurar as configurações de codificação como uma receita. Em seguida, você pode enviar um **Trabalho** para aplicar essa fórmula a um vídeo. Ao enviar novos trabalhos para cada novo vídeo, você está aplicando essa fórmula em todos os vídeos na biblioteca. Uma receita nos Serviços de Mídia do Microsoft Azure é chamada de **Transformação**. Para obter mais informações, consulte [Transformações e Trabalhos](transform-concept.md). O exemplo descrito neste tutorial define uma receita que analisa o vídeo especificado. 

#### <a name="transform"></a>Transformar

Ao criar uma instância de [Transformação](https://docs.microsoft.com/rest/api/media/transforms), você precisa especificar o que deseja produzir como uma saída; **TransformOutput** é um parâmetro necessário. Cada **TransformOutput** contém um **Predefinição**. **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que serão usadas para gerar o **TransformOutput** desejado. Neste exemplo, a predefinição **VideoAnalyzerPreset** é usada e o idioma ("en-US") é passado para o construtor (`new VideoAnalyzerPreset("en-US")`). Esta predefinição permite extrair várias informações de áudio e vídeo de um vídeo. Você pode usar a predefinição **AudioAnalyzerPreset** se você precisa extrair as várias ideias de áudio de um vídeo. 

Ao criar uma **Transformação**, você deverá verificar primeiro se já existe uma usando o método **Get**, conforme mostrado no código a seguir.  Em Serviços de Mídia v3, os métodos **Get** em entidades retornam **nulo** se a entidade não existe (uma verificação de maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Trabalho

Conforme mecionado acima, o objeto de [Transformação](https://docs.microsoft.com/rest/api/media/transforms) é a receita e um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs) é a solicitação real para os Serviços de Mídia para aplicar a **Transformação** a um determinado conteúdo de vídeo ou áudio de entrada. O **Trabalho** especifica informações como o local da entrada vídeo e o local da saída. Você pode especificar a localização do vídeo usando: URLs HTTPS, URLs SAS ou Ativos que fazem parte de sua conta do Serviço de Mídia. 

Neste exemplo, a entrada de trabalho é um vídeo local.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Aguarde a conclusão do trabalho

Este trabalho levará algum tempo para concluir e quando você desejar ser notificado. Há diferentes opções para ser notificado sobre a conclusão do [Trabalho](https://docs.microsoft.com/rest/api/media/jobs). A opção mais simples (que é mostrada aqui) é usar a sondagem. 

Sondagem não é uma prática recomendada para aplicativos de produção devido à latência potencial. A sondagem pode ser acelerada, se houver uso excessivo em uma conta. Os desenvolvedores devem usar a Grade de Eventos.

A Grade de Eventos foi projetada para alta disponibilidade, desempenho consistente e dimensionamento dinâmico. Com a Grade de Eventos, seus aplicativos podem escutar e reagir a eventos de praticamente todos os serviços do Azure, bem como de origens personalizadas. A manipulação de eventos simples e reativa baseada em HTTP ajuda você a criar soluções eficientes por meio da filtragem e do roteamento de eventos. Consulte [Rotear eventos para um ponto de extremidade personalizado de web](job-state-events-cli-how-to.md).

O **Trabalho** normalmente passa pelos seguintes estados: **Agendado**, **Enfileirado**, **Processando**, **Concluído** (o estado final). Se o trabalho encontrou um erro, você receberá o estado do **Erro**. Se o trabalho está no processo de ser cancelado, você obterá **Cancelando** e **Cancelado** quando estiver pronto.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]


### <a name="job-error-codes"></a>Códigos de erro do trabalho

Confira [Códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>Faça o download do resultado do trabalho

A função a seguir realiza os downloads dos resultados do [Ativo](https://docs.microsoft.com/rest/api/media/assets) de saída para a pasta de "saída", para que você possa examinar os resultados do trabalho. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Limpar os recursos em sua conta de Serviços de Mídia

Em geral, você deve limpar tudo, exceto os objetos que planeja reutilizar (normalmente, você reutiliza transformações e persiste StreamingLocators). Se desejar para sua conta para ser limpa depois de testar, você deve excluir os recursos que não planeja reutilizar. Por exemplo, o código a seguir exclui trabalhos.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

Pressione CTRL+F5 para executar o aplicativo *AnalyzeVideos*.

Quando executamos o programa, o trabalho produz miniaturas de cada face que encontrar no vídeo. Ele também produz o arquivo insights.json.

## <a name="examine-the-output"></a>Examinar a saída

O arquivo de saída de análise de vídeos é chamado insights.json. Esse arquivo contém informações sobre o vídeo. Você pode encontrar uma descrição dos elementos encontrados no arquivo json no artigo [Inteligência de mídia](intelligence-concept.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que você criou neste tutorial, exclua o grupo de recursos criados anteriormente. 

Execute este comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Os SDKs dos Serviços de Mídia do Azure v3 não são thread-safe. Ao trabalhar com aplicativos multithread, você deve gerar um novo objeto AzureMediaServicesClient por thread.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: carregar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md)
