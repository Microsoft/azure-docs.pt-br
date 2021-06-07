---
title: Analisar vídeos com os Serviços de Mídia v3
description: Saiba como analisar vídeos usando os Serviços de Mídia do Azure.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 5155ffed26daaf53b991a84971929500057d0007
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275270"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Tutorial: Analisar vídeos com os Serviços de Mídia v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este tutorial mostra como analisar vídeos com os Serviços de Mídia do Azure. Há muitos cenários em que você talvez queira obter ideias profundas sobre vídeos gravados ou conteúdo de áudio. Por exemplo, para obter maior satisfação do cliente, as organizações podem executar processamento de fala para texto para converter gravações de suporte do cliente em um catálogo pesquisável com índices e painéis.

Este tutorial mostra como:

> [!div class="checklist"]
> * Baixar o aplicativo de exemplo descrito no tópico.
> * Examine o código que analisa o vídeo especificado.
> * Execute o aplicativo.
> * Examine a saída.
> * Limpe os recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Conformidade, Privacidade e Segurança
 
É importante lembrar que você precisa obedecer a todas as leis aplicáveis em seu uso do Video Indexer. Você não deve usar o Video Indexer nem qualquer outro serviço do Azure de maneira que viole direitos de outras pessoas. Antes de carregar qualquer vídeo, incluindo dados biométricos, no serviço do Video Indexer para processamento e armazenamento, você precisa ter todos os direitos apropriados, incluindo todos os consentimentos apropriados, dos indivíduos mostrados no vídeo. Para saber mais sobre a conformidade, a privacidade e a segurança no Video Indexer, leia os [Termos dos Serviços Cognitivos](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/) do Azure. Para saber mais sobre as obrigações de privacidade e o tratamento de seus dados pela Microsoft, confira a [Política de Privacidade](https://privacy.microsoft.com/PrivacyStatement), os [OST (Termos dos Serviços Online)](https://www.microsoft.com/licensing/product-licensing/products) e o [DPA (Adendo de Processamento de Dados)](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) da Microsoft. Mais informações sobre privacidade, incluindo sobre retenção de dados, exclusão/destruição, estão disponíveis no OST e [aqui](../video-indexer/faq.md). Ao usar o Video Indexer, você concorda em vincular-se aos Termos dos Serviços Cognitivos, o OST, o DPA e à Política de Privacidade.

## <a name="prerequisites"></a>Pré-requisitos

- Se o Visual Studio não estiver instalado, obtenha o [Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Crie uma conta de Serviços de Mídia](./account-create-how-to.md).<br/>Lembre-se dos valores que você usou para o nome do grupo de recursos e o nome da conta de Serviços de Mídia.
- Siga as etapas em [Acessar API de Serviços de Mídia com a CLI do Azure](./access-api-howto.md) e salve as credenciais. Você precisará usá-las para acessar a API.

## <a name="download-and-configure-the-sample"></a>Baixar e configurar o exemplo

Clone um repositório do GitHub que contém o exemplo de .NET em sua máquina usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

O exemplo está localizado na pasta [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

Abra [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) no projeto que você baixou. Substitua os valores pelas credenciais que você obteve de [acesso às APIs](./access-api-howto.md).

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Examinar o código que analisa o vídeo especificado

Esta seção examina funções definidas no arquivo [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) do projeto *AnalyzeVideos*.

O exemplo conclui as ações a seguir:

1. Cria uma **Transformação** e um **Trabalho** que analisa o seu vídeo.
2. Cria um **Ativo** de entrada e carrega o vídeo para ele. O ativo é usado como entrada do trabalho.
3. Cria um ativo de saída que armazena a saída do trabalho.
4. Envia o trabalho.
5. Verifica o status do trabalho.
6. Baixa os arquivos resultantes da execução do trabalho.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a usar as APIs de Serviços de Mídia com a SDK .NET

Para começar a usar a APIs de Serviços de Mídia do Azure com o .NET, é necessário criar um objeto **AzureMediaServicesClient**. Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. No código que você clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no arquivo de configuração local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Criar um ativo de entrada e carregar um arquivo local para ele 

A função **CreateInputAsset** cria um novo [Ativo](/rest/api/media/assets) de entrada e carrega o arquivo de vídeo local especificado para ele. Este Ativo é usado como entrada para o trabalho de codificação. Em Serviços de Mídia v3, a entrada para um trabalho pode ser um ativo ou pode ser o conteúdo que você disponibiliza a sua conta do Serviços de Mídia por meio de URLs de HTTPS. Para saber como codificar com base em uma URL HTTPS, confira [este](job-input-from-http-how-to.md) artigo.  

Em Serviços de Mídia v3, você usará as APIs de Armazenamento do Microsoft Azure para carregar os arquivos. O snippet .NET a seguir mostra como.

A função a seguir conclui estas ações:

* Cria um ativo.
* Obtém uma [URL SAS](../../storage/common/storage-sas-overview.md) gravável no [contêiner no armazenamento](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container) do Ativo.

    Se estiver usando a função [ListContainerSas](/rest/api/media/assets/listcontainersas) do ativo para obter URLs SAS, observe que a função retorna várias URLs SAS, pois há duas chaves de conta de armazenamento para cada conta de armazenamento. Uma conta de armazenamento tem duas chaves porque permite a rotação contínua das chaves da conta de armazenamento (por exemplo, alterar a primeira enquanto se usa a outra e, em seguida, começar a usar a nova primeira chave e realizar a rotação da segunda). A primeira URL SAS representa a chave de armazenamento 1 e a segunda representa a chave de armazenamento 2.
* Carrega o arquivo para o contêiner no armazenamento usando a URL de SAS.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Criar um ativo de saída para armazenar o resultado do trabalho

O [Ativo](/rest/api/media/assets) de saída armazena o resultado do seu trabalho. O projeto define a função **DownloadResults** que faz o download dos resultados desse ativo de saída para a pasta de "saída", para que você possa ver o que tem.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Criar uma transformação e um trabalho que analisa os vídeos

Ao codificar ou processar o conteúdo nos Serviços de Mídia do Microsoft Azure, é um padrão comum configurar as configurações de codificação como uma receita. Em seguida, você pode enviar um **Trabalho** para aplicar essa fórmula a um vídeo. Ao enviar novos Trabalhos para cada novo vídeo, você aplicará essa fórmula a todos os vídeos na biblioteca. Uma receita nos Serviços de Mídia do Microsoft Azure é chamada de **Transformação**. Para obter mais informações, consulte [Transformações e Trabalhos](./transform-jobs-concept.md). O exemplo descrito neste tutorial define uma receita que analisa o vídeo especificado.

#### <a name="transform"></a>Transformar

Ao criar um novo exemplo de [Transformação](/rest/api/media/transforms), você precisa especificar o que deseja produzir como uma saída. **TransformOutput** é um parâmetro obrigatório. Cada **TransformOutput** contém um **Predefinição**. **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que serão usadas para gerar o **TransformOutput** desejado. Neste exemplo, a predefinição **VideoAnalyzerPreset** é usada e o idioma ("en-US") é passado para o construtor (`new VideoAnalyzerPreset("en-US")`). Esta predefinição permite extrair várias informações de áudio e vídeo de um vídeo. Você pode usar a predefinição **AudioAnalyzerPreset** se você precisa extrair as várias ideias de áudio de um vídeo.

Ao criar uma **Transformação**, verifique primeiro se já existe uma usando o método **Get**, conforme mostrado no código a seguir. Em Serviços de Mídia v3, os métodos **Get** em entidades retornam **nulo** se a entidade não existe (uma verificação de maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Trabalho

Conforme mecionado acima, o objeto de [Transformação](/rest/api/media/transforms) é a receita e um [Trabalho](/rest/api/media/jobs) é a solicitação real para os Serviços de Mídia para aplicar a **Transformação** a um determinado conteúdo de vídeo ou áudio de entrada. O **Trabalho** especifica informações como a localização do vídeo de entrada e a localização da saída. Você pode especificar a localização do vídeo usando: URLs HTTPS, URLs SAS ou Ativos que fazem parte de sua conta do Serviço de Mídia.

Neste exemplo, a entrada de trabalho é um vídeo local.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Aguarde a conclusão do trabalho

O trabalho leva algum tempo para ser concluído. Quando isso ocorrer, você desejará ser notificado. Há diferentes opções para ser notificado sobre a conclusão do [Trabalho](/rest/api/media/jobs). A opção mais simples (mostrada aqui) é usar a sondagem.

Sondagem não é uma prática recomendada para aplicativos de produção devido à latência potencial. A sondagem pode ser acelerada, se houver uso excessivo em uma conta. Os desenvolvedores devem usar a Grade de Eventos.

A Grade de Eventos foi projetada para alta disponibilidade, desempenho consistente e dimensionamento dinâmico. Com a Grade de Eventos, seus aplicativos podem escutar e reagir a eventos de praticamente todos os serviços do Azure, bem como de origens personalizadas. A manipulação de eventos simples e reativa baseada em HTTP ajuda você a criar soluções eficientes por meio da filtragem e do roteamento de eventos. Para obter mais informações, confira [Encaminhar eventos para um ponto de extremidade da Web personalizado](monitoring/job-state-events-cli-how-to.md).

O **Trabalho** normalmente passa pelos seguintes estados: **Agendado**, **Enfileirado**, **Processando**, **Concluído** (o estado final). Se o trabalho tiver encontrado um erro, você receberá o estado do **Erro**. Se o trabalho estiver para ser cancelado, você receberá **Cancelando** e **Cancelado** quando estiver pronto.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Códigos de erro do trabalho

Confira [Códigos de erro](/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>Faça o download do resultado do trabalho

A função a seguir baixa os resultados do [Ativo](/rest/api/media/assets) de saída para a pasta de "saída", para que você possa examinar os resultados do trabalho.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos em sua conta de Serviços de Mídia

[!INCLUDE [clean-up-warning](includes/clean-up-warning.md)]

Em geral, você deve limpar tudo, exceto os objetos que planeja reutilizar (normalmente, você reutilizará Transformações e persistirá StreamingLocators). Se desejar que sua conta seja limpa após fazer experimentos, exclua os recursos que não planeja reutilizar. Por exemplo, o seguinte código exclui o trabalho e o ativo de saída:

### <a name="delete-resources-with-code"></a>Excluir recursos com código

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

Use também a CLI.

[!INCLUDE [clean-up-resources-cli](includes/clean-up-resources-cli.md)]

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

Pressione CTRL+F5 para executar o aplicativo *AnalyzeVideos*.

Quando executamos o programa, o trabalho produz miniaturas de cada face que encontrar no vídeo. Ele também produz o arquivo insights.json.

## <a name="examine-the-output"></a>Examinar a saída

O arquivo de saída de análise de vídeos é chamado insights.json. Esse arquivo contém informações sobre o vídeo. Você pode encontrar uma descrição dos elementos encontrados no arquivo json no artigo [Inteligência de mídia](./analyze-video-audio-files-concept.md).

## <a name="multithreading"></a>Multithreading

> [!WARNING]
> Os SDKs dos Serviços de Mídia do Azure v3 não são thread-safe. Ao trabalhar com aplicativos multithread, você deve gerar um novo objeto AzureMediaServicesClient por thread.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: carregar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md)
