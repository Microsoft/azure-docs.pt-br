---
title: Usar a API do Video Indexer
titleSuffix: Azure Media Services
description: Este artigo descreve como começar a usar a API do Video Indexer dos Serviços de Mídia do Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/07/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: fcd194e2503610db314f6a975a4afb1d27962f8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028161"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Tutorial: Usar a API do Video Indexer

O Video Indexer consolida várias tecnologias de inteligência artificial (AI) de áudio e vídeo oferecidas pela Microsoft em um único serviço integrado, simplificando o trabalho de desenvolvimento. As APIs são projetadas para permitir que os desenvolvedores se concentrem no consumo de tecnologias de IA de Mídia sem se preocuparem com escala, alcance global, disponibilidade e confiabilidade das plataformas de nuvem. Você pode usar a API para fazer carregar seus arquivos, obter insights de vídeo detalhados, obter URLs de insights e widgets de player que permitem inserção e muito mais.

Quando cria uma conta do Video Indexer, você pode escolher uma conta de avaliação gratuita (em que você obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (em que você não está limitado pela cota). O Video Indexer fornece até 600 minutos de indexação gratuita para usuários do site e até 2400 minutos de indexação gratuita para usuários da API com uma conta de avaliação gratuita. Com uma opção paga, você cria uma conta do Video Indexer que está [conectada à sua assinatura do Azure e a uma conta dos Serviços de Mídia do Azure](connect-to-azure.md). Você paga pelos minutos indexados. Para obter mais informações, confira [Preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/).

Este artigo mostra como os desenvolvedores podem aproveitar a [API do Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Inscrever-se à API

1. Entre no [Portal do Desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/).

    Examine uma nota de versão sobre [as informações de logon](release-notes.md#october-2020).
    
     ![Conexão ao Portal do Desenvolvedor do Video Indexer](./media/video-indexer-use-apis/sign-in.png)

   > [!Important]
   > * Você deve usar o mesmo provedor usado quando se inscreveu no Video Indexer.
   > * As contas pessoais Google e Microsoft (Outlook/Live) só podem ser usadas para contas de avaliação gratuita. Contas conectadas ao Azure exigem o Azure AD.
   > * Só é permitida apenas uma conta ativa por email. Se um usuário tenta entrar com a conta user@gmail.com no LinkedIn e, em seguida, com a conta user@gmail.com no Google, este último exibirá uma página de erro, informando que o usuário já existe.
2. Inscrever-se.

    Selecione a guia [Produtos](https://api-portal.videoindexer.ai/products). Em seguida, selecione a autorização e inscrever-se.
    
    ![Guia Produtos no Portal do Desenvolvedor do Video Indexer](./media/video-indexer-use-apis/authorization.png)

    > [!NOTE]
    > Novos usuários são automaticamente inscritos na Autorização.
    
    Depois de assinar, você pode encontrar sua assinatura em **produtos**  ->  **autorização**. Na página assinatura, você encontrará as chaves primária e secundária. As chaves devem ser protegidas. As chaves só devem ser usadas pelo seu código do servidor. Elas não devem estar disponíveis no lado do cliente (.js, .html, e assim por diante).

    ![Assinatura e chaves no Portal do Desenvolvedor do Video Indexer](./media/video-indexer-use-apis/subscriptions.png)

> [!TIP]
> O usuário do Video Indexer pode usar uma chave de assinatura única para se conectar a várias contas do aplicativo. Depois você pode vincular essas contas do Video Indexer para diferentes contas de Serviços de Mídia.

## <a name="obtain-access-token-using-the-authorization-api"></a>Obter o token de acesso usando a API de autorização

Depois de assinar a API de Autorização, você receberá tokens de acesso. Esses tokens de acesso são usados para autenticar na API de operações.

Cada chamada para a API de operações deve estar associada a um token de acesso, correspondendo ao escopo de autorização da chamada.

- Nível de usuário: os tokens de acesso em nível de usuário permitem executar operações no nível do **usuário**. Por exemplo, obter contas associadas.
- Nível de conta: os tokens de acesso em nível de conta permitem executar operações no nível da **conta** ou no nível do **vídeo**. Por exemplo, carregar vídeo, listar todos os vídeos, obter insights de vídeo, e assim por diante.
- Nível de vídeo: os tokens de acesso em nível de vídeo permitem executar operações em um **vídeo** específico. Por exemplo, obter insights sobre vídeos, baixar legendas, obter widgets, e assim por diante.

Você pode controlar se esses tokens são somente leitura ou se eles permitem edições especificando **allowEdit=true/false**.

Na maioria dos cenários de servidor para servidor, você provavelmente usará o mesmo token de **conta**, pois ele abrange operações de **conta** e operações de **vídeo**. No entanto, se estiver planejando fazer chamadas do lado do cliente para o Video Indexer (por exemplo, do JavaScript), convém usar um token de acesso de **vídeo** para impedir que os clientes obtenham acesso a toda a conta. Essa também é a razão pela qual ao inserir o código do cliente do Video Indexer em seu cliente (por exemplo, usando **Obter Widget de Insights** ou **Obter Widget de Player**) você deve fornecer um token de acesso de **vídeo**.

Para facilitar, você pode usar a **API de autorização**> **GetAccounts** para obter suas contas sem obter primeiro um token de usuário. Você também pode pedir para obter as contas com tokens válidos, permitindo que você ignore uma chamada adicional para obter um token de conta.

Os tokens de acesso expiram após 1 hora. Certifique-se de que seu token de acesso seja válido antes de usar a API de operações. Se ele expirar, chame a API de Autorização novamente para obter um novo token de acesso.

Você está pronto para começar a integração com a API. Encontre [a descrição detalhada de cada API REST do Indexador de Vídeo](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>ID da Conta

O parâmetro de ID da conta é obrigatório em todas as chamadas da API operacional. O ID da conta é um GUID que pode ser obtido de uma das seguintes maneiras:

* Use o **site do Video Indexer** para obter a ID da conta:

    1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
    2. Navegue até a **configurações** página.
    3. Copie a ID de conta.

        ![Configurações e ID da conta do Video Indexer](./media/video-indexer-use-apis/account-id.png)

* Use o **Portal do Desenvolvedor do Video Indexer** para obter a ID da conta de maneira programática.

    Use a API [Obter conta](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?).

    > [!TIP]
    > Você pode gerar tokens de acesso para as contas definindo `generateAccessTokens=true`.

* Obtenha o ID da conta a partir do URL de uma página do player em sua conta.

    Quando você assiste a um vídeo, o ID aparece após a `accounts`seção e antes da`videos` seção.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Recomendações

Esta seção lista algumas recomendações ao usar a API do Video Indexer.

- Se você estiver planejando carregar um vídeo, é recomendável colocá-lo em algum local de rede pública (por exemplo, uma conta de armazenamento de BLOBs do Azure). Obter o link para vídeo e fornecer a URL como o parâmetro de arquivo de upload.

    A URL fornecida ao Video Indexer deve apontar para um arquivo de mídia (áudio ou vídeo). Uma verificação fácil para a URL (ou URL SAS) é colá-la em um navegador, se o arquivo começar a ser reproduzido ou baixado, provavelmente é uma boa URL. Se o navegador renderizar uma visualização, provavelmente não é um link para um arquivo, mas para uma página HTML.

- Quando você chama a API que recebe insights de vídeo para o vídeo especificado, você obtém uma saída JSON detalhada como o conteúdo da resposta. [Ver detalhes sobre o JSON retornado neste tópico](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Exemplo de código

O seguinte snippet de código em C# demonstra o uso de todas as APIs do indexador de vídeo juntos.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2"; // replace with the account's location, or with “trial” if this is a trial account
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de concluir este tutorial, exclua os recursos que você não está planejando usar.

## <a name="see-also"></a>Confira também

- [Visão geral do indexador vídeo](video-indexer-overview.md)
- [Regiões](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Próximas etapas

- [Verificação dos detalhes da saída JSON](video-indexer-output-json-v2.md)
- Veja o [código de exemplo](https://github.com/Azure-Samples/media-services-video-indexer) que demonstra um aspecto importante do carregamento e indexação de um vídeo. Seguir o código fornecerá uma boa ideia de como usar nossa API para funcionalidades básicas. Lembre-se de ler os comentários embutidos e seguir nossas práticas recomendadas.

