---
Título: Criptografar vídeo com o AES-128: Descrição dos Serviços de Mídia do Azure: Saiba como criptografar vídeo com a criptografia AES de 128 bits e como usar o serviço de distribuição de chaves nos Serviços de Mídia do Azure.
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: mídia ms.tgt_pltfrm: na ms.devlang: na ms.topic: tutorial ms.date: 17/03/2021 ms.author: inhenkel

---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Tutorial: Criptografar vídeo com o AES-128 e usar o serviço de distribuição de chaves

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Embora o tutorial use os exemplos do [SDK do .NET](/dotnet/api/microsoft.azure.management.media.models.liveevent), as etapas gerais são as mesmas para [API REST](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event) ou outros [SDKs](media-services-apis-overview.md#sdks) suportados.

Você pode usar os Serviços de Mídia para a distribuição HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming criptografado com o AES usando chaves de criptografia de 128 bits. Os Serviços de Mídia também fornecem o serviço de distribuição de chaves, que distribui chaves de criptografia para usuários autorizados. Se desejar que os Serviços de Mídia criptografem dinamicamente seu vídeo, você associará uma chave de criptografia com um Localizador de Streaming e configurará políticas de chave de conteúdo. Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo com o AES-128. Para descriptografar o fluxo, o player solicita a chave do serviço de distribuição de chaves. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que você especificou para a chave.

Você pode criptografar cada ativo com vários tipos de criptografia (AES-128, PlayReady, Widevine, FairPlay). Consulte [Tipos de criptografia e protocolos de streaming](drm-content-protection-concept.md#streaming-protocols-and-encryption-types) para ver o que faz sentido combinar. Além disso, confira [Como proteger com DRM](drm-protect-with-drm-tutorial.md).

A saída do exemplo nesse artigo inclui uma URL para o Player de Mídia do Azure, a URL de manifesto e o token AES necessários para reproduzir o conteúdo. O exemplo define a expiração do token JWT (Token Web JSON) para uma hora. Você pode abrir um navegador e colar a URL resultante para inicializar a página de demonstração do Player de Mídia do Azure com a URL e o token preenchidos para você já no seguinte formato: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

Este tutorial mostra como:

> [!div class="checklist"]
> * Baixar o exemplo [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) descrito no artigo.
> * Começar a usar as APIs de Serviços de Mídia com o SDK do .NET.
> * Criar um ativo de saída.
> * Criar uma transformação de codificação.
> * Enviar um trabalho.
> * Aguardar a conclusão do trabalho.
> * Criar uma política de chave de conteúdo
> * Configurar a política para usar a restrição de token JWT.
> * Criar um Localizador de Streaming.
> * Configurar o Localizador de Streaming para criptografar o vídeo com o AES (ClearKey).
> * Obter um token de teste.
> * Criar uma URL de streaming.
> * Limpe os recursos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir o tutorial.

* Examine o artigo [Visão geral da proteção de conteúdo](drm-content-protection-concept.md).
* Instale o Visual Studio Code ou o Visual Studio.
* [Crie uma conta de Serviços de Mídia](./account-create-how-to.md).
* Obtenha as credenciais necessárias para usar as APIs dos Serviços de Mídia seguindo as [APIs de acesso](./access-api-howto.md).

## <a name="download-code"></a>Código de download

Clone um repositório do GitHub que contenha o exemplo de .NET completo discutido neste artigo em sua máquina usando o comando a seguir:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

O exemplo de "Criptografar com AES-128" está localizado na pasta [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES).

> [!NOTE]
> O exemplo cria recursos exclusivos toda vez que você executa o aplicativo. Normalmente, você reutilizará recursos existentes como transformações e políticas (se os recursos existentes apresentarem configurações necessárias).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a usar as APIs de Serviços de Mídia com a SDK .NET

Para começar a usar a APIs de Serviços de Mídia do Azure com o .NET, crie um objeto **AzureMediaServicesClient**. Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. No código que você clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no arquivo de configuração local.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Criar um ativo de saída  

O [Ativo](/rest/api/media/assets) de saída armazena o resultado do trabalho de codificação.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Obter ou criar uma transformação de codificação

Ao criar um novo exemplo de [Transformação](/rest/api/media/transforms), você precisa especificar o que deseja produzir como uma saída. O parâmetro necessário é um objeto **TransformOutput**, como mostrado no código a seguir. Cada **TransformOutput** contém um **Predefinição**. **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que serão usadas para gerar o **TransformOutput** desejado. O exemplo descrito neste artigo usa uma predefinição chamada **AdaptiveStreaming**. A predefinição codifica o vídeo de entrada em uma escada de taxa de bits gerada automaticamente (pares de resolução de taxa de bits) com base na taxa de bits e resolução de entrada e depois produz arquivos ISO MP4 com vídeo H.264 e áudio AAC correspondentes a cada par de resolução de taxa de bits.

Antes de criar uma [Transformação](/rest/api/media/transforms), você deve verificar se já existe uma usando o método **Get**, conforme mostrado no código a seguir. Em Serviços de Mídia v3, os métodos **Get** em entidades retornam **nulo** se a entidade não existe (uma verificação de maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Enviar Trabalho

Conforme mecionado acima, o objeto de [Transformação](/rest/api/media/transforms) é a receita e um [Trabalho](/rest/api/media/jobs) é a solicitação real para os Serviços de Mídia para aplicar a **Transformação** a um determinado conteúdo de vídeo ou áudio de entrada. O **Trabalho** especifica informações como a localização do vídeo de entrada e a localização da saída.

Neste tutorial, criamos a entrada do trabalho com base em um arquivo que é ingerido diretamente de uma [URL de origem HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguarde a conclusão do trabalho

O trabalho leva algum tempo para ser concluído. Quando isso ocorrer, você desejará ser notificado. O exemplo de código a seguir mostra como sondar o serviço para o status do [Trabalho](/rest/api/media/jobs). Sondagem não é uma prática recomendada para aplicativos de produção devido à latência potencial. A sondagem pode ser acelerada, se houver uso excessivo em uma conta. Os desenvolvedores devem usar a Grade de Eventos. Para obter mais informações, confira [Encaminhar eventos para um ponto de extremidade da Web personalizado](monitoring/job-state-events-cli-how-to.md).

O **Trabalho** normalmente passa pelos seguintes estados: **Agendado**, **Enfileirado**, **Processando**, **Concluído** (o estado final). Se o trabalho tiver encontrado um erro, você receberá o estado do **Erro**. Se o trabalho estiver para ser cancelado, você obterá **Cancelando** e **Cancelado** quando estiver pronto.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Criar uma política de chave de conteúdo

Uma chave de conteúdo fornece acesso seguro aos seus ativos. Você precisa criar uma **política de chave de conteúdo** que define como a chave de conteúdo é entregue para os clientes finais. A chave de conteúdo é associada ao **Localizador de Streaming**. Os Serviços de Mídia também fornecem o serviço de distribuição de chaves, que distribui chaves de criptografia para usuários autorizados.

Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a criptografia AES (neste caso, usando criptografia AES.) Para descriptografar o fluxo, o player solicita a chave do serviço de distribuição de chaves. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que você especificou para a chave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Criar um Localizador de Streaming

Depois que a codificação for concluída e a política de chave de conteúdo for definida, a próxima etapa é disponibilizar o vídeo no ativo de saída para os clientes o reproduzirem. Há duas etapas para disponibilizar o vídeo:

1. Crie um [Localizador de Streaming](/rest/api/media/streaminglocators).
2. Compile as URLs de streaming que os clientes podem usar.

O processo de criação de um **Localizador de Streaming** é chamado de publicação. Por padrão, o **Localizador de streaming** é válido imediatamente depois que você faz as chamadas à API. Ela vai durar até ser excluída, a menos que você configure as horas de início e de término opcionais.

Ao criar um [Localizador de Streaming](/rest/api/media/streaminglocators), você precisará especificar o **StreamingPolicyName** desejado. Neste tutorial, estamos usando uma das PredefinedStreamingPolicies, que informa aos Serviços de Mídia do Azure como publicar o conteúdo para streaming. Neste exemplo, a criptografia de Envelope AES é aplicada (também conhecida como criptografia ClearKey, porque a chave é entregue ao cliente de reprodução via HTTPS, em vez de via uma licença DRM).

> [!IMPORTANT]
> Ao usar uma [StreamingPolicy](/rest/api/media/streamingpolicies) personalizada, será necessário estruturar um conjunto limitado dessas políticas para sua conta de Serviço de Mídia e reutilizá-las em seus Localizadores de Streaming, sempre que as mesmas opções e protocolos de criptografia forem necessários. Sua conta de Serviço de Mídia tem uma cota para o número de entradas de StreamingPolicy. Você não deve criar uma StreamingPolicy para cada Localizador de Streaming.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Obter um token de teste

Neste tutorial, especificamos que a política de chave de conteúdo tenha uma restrição de token. A política restrita do token deve ser acompanhada por um token emitido por um Serviço de Token de Segurança (STS). Os Serviços de Mídia dão suporte a tokens no formato [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) e é isso que configuramos no exemplo.

O ContentKeyIdentifierClaim é usado na **política de chave de conteúdo**, o que significa que o token apresentado para o serviço de Distribuição de Chaves precisa conter o identificador do conteúdo de chaves. No exemplo, nós não especificamos uma chave de conteúdo ao criar o Localizador de Streaming, o sistema criou uma senha aleatória para nós. Para gerarmos o teste de token, devemos obter o ContentKeyId para colocar na declaração ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Criar uma URL de streaming de DASH

Agora que o [Localizador de Streaming](/rest/api/media/streaminglocators) foi criado, é possível obter as URLs de streaming. Para criar uma URL, você precisa concatenar o nome de host [StreamingEndpoint](/rest/api/media/streamingendpoints) e o caminho do **Localizador de Streaming**. Neste exemplo, o *padrão* do **Ponto de Extremidade de Streaming** é usado. Ao criar uma conta de Serviço de Mídia, este *padrão* **Ponto de Extremidade de Streaming** estará em um estado parado, portanto você precisa chamar **Iniciar**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos em sua conta de Serviços de Mídia

Em geral, você deve limpar tudo, exceto os objetos que planeja reutilizar (normalmente, você reutilizará Transformações, Localizadores de Streaming, etc). Se desejar que sua conta seja limpa após fazer experimentos, exclua os recursos que não planeja reutilizar. Por exemplo, o seguinte código exclui o trabalho, os ativos criados e a política de chave de conteúdo:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que você criou neste tutorial, exclua o grupo de recursos criados anteriormente.

Execute este comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="additional-notes"></a>Observações adicionais

* O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Proteger com DRM](drm-protect-with-drm-tutorial.md)
