---
title: Entrega de licença e criptografia de DRM nos Serviços de Mídia
description: Saiba como usar a criptografia dinâmica de DRM e o serviço de entrega de licença para entregar fluxos criptografados com licenças do Microsoft PlayReady, do Google Widevine ou do Apple FairPlay.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: dab265c41bc1c951a31ff764c9214e7f21ee3df4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279554"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Tutorial: Usar o serviço de entrega de licença e criptografia dinâmica do DRM

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Embora este tutorial use os exemplos do [SDK do .NET](/dotnet/api/microsoft.azure.management.media.models.liveevent), as etapas gerais são as mesmas para [API REST](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event) ou outros [SDKs](media-services-apis-overview.md#sdks) com suporte.

É possível usar os Serviços de Mídia do Azure para entregar seus fluxos criptografados com licenças do Microsoft PlayReady, Google Widevine ou Apple FairPlay. Para obter uma explicação detalhada, confira [Proteção de conteúdo com criptografia dinâmica](drm-content-protection-concept.md).

Além disso, os Serviços de Mídia fornecem um serviço para entregar licenças de DRM do PlayReady, do Widevine e do FairPlay. Quando um usuário solicitar conteúdo protegido por DRM, o aplicativo do player solicitará uma licença do serviço de licença dos Serviços de Mídia. Se o aplicativo do player estiver autorizado, o serviço de licença dos Serviços de Mídia emitirá uma licença para o player. Uma licença contém a chave de descriptografia que pode ser usada pelo player cliente para descriptografar e transmitir o conteúdo.

Este artigo se baseia no exemplo [Criptografando com DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

O exemplo descrito neste artigo produz o seguinte resultado:

![AMS com vídeo protegido por DRM no Player de Mídia do Azure](./media/drm-protect-with-drm-tutorial/ams_player.png)

Este tutorial mostra como:

> [!div class="checklist"]
> * Criar uma transformação de codificação.
> * Defina a chave de assinatura usada para a verificação de seu token.
> * Definir os requisitos na política de chave de conteúdo.
> * Criar um StreamingLocator com a política de streaming especificada.
> * Criar uma URL usada para reproduzir o arquivo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes itens são necessários para concluir o tutorial:

* Examine o artigo [Visão geral da proteção de conteúdo](drm-content-protection-concept.md).
* Examine [Projetar proteção de conteúdo de diversos DRMs com controle de acesso](architecture-design-multi-drm-system.md).
* Instale o Visual Studio Code ou o Visual Studio.
* Crie uma nova conta dos Serviços de Mídia do Azure, conforme descrito [neste início rápido](./account-create-how-to.md).
* Obtenha as credenciais necessárias para usar as APIs dos Serviços de Mídia seguindo as [APIs de acesso](./access-api-howto.md)
* Defina os valores apropriados no arquivo de configuração do aplicativo (appsettings.json).

## <a name="download-code"></a>Código de download

Clone um repositório do GitHub que contenha o exemplo de .NET completo discutido neste artigo em sua máquina usando o comando a seguir:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
O exemplo de “Criptografar com DRM” está localizado na pasta [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> O exemplo cria recursos exclusivos toda vez que você executa o aplicativo. Normalmente, você reutilizará recursos existentes como transformações e políticas (se os recursos existentes apresentarem configurações necessárias).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a usar as APIs de Serviços de Mídia com a SDK .NET

Para começar a usar a APIs de Serviços de Mídia do Azure com o .NET, crie um objeto **AzureMediaServicesClient**. Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. No código que você clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no arquivo de configuração local.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Criar um ativo de saída  

O [Ativo](assets-concept.md) de saída armazena o resultado do trabalho de codificação.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Obter ou criar uma transformação de codificação

Ao criar um novo exemplo de [Transformação](transform-jobs-concept.md), você precisa especificar o que deseja produzir como uma saída. O parâmetro necessário é um objeto `transformOutput`, como mostrado no código a seguir. Cada TransformOutput contém um **Predefinição**. Predefinição descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que serão usadas para gerar o TransformOutput desejado. O exemplo descrito neste artigo usa uma predefinição chamada **AdaptiveStreaming**. A predefinição codifica o vídeo de entrada em uma escada de taxa de bits gerada automaticamente (pares de resolução de taxa de bits) com base na taxa de bits e resolução de entrada e produz arquivos ISO MP4 com vídeo H.264 e áudio AAC correspondentes a cada par de resolução de taxa de bits. 

Antes de criar uma nova **Transformação**, você deve verificar se já existe uma usando o método **Get**, conforme mostrado no código a seguir.  Em Serviços de Mídia v3, os métodos **Get** em entidades retornam **nulo** se a entidade não existe (uma verificação de maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Enviar Trabalho

Conforme mecionado acima, o objeto de **Transformação** é a receita e um [Trabalho](transform-jobs-concept.md) é a solicitação real para os Serviços de Mídia para aplicar a **Transformação** a um determinado conteúdo de vídeo ou áudio de entrada. O **Trabalho** especifica informações como a localização do vídeo de entrada e a localização da saída.

Neste tutorial, criamos a entrada do trabalho com base em um arquivo que é ingerido diretamente de uma [URL de origem HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguarde a conclusão do trabalho

O trabalho leva algum tempo para ser concluído. Quando isso ocorrer, você desejará ser notificado. O exemplo de código a seguir mostra como sondar o serviço para o status do **Trabalho**. Sondagem não é uma prática recomendada para aplicativos de produção devido à latência potencial. A sondagem pode ser acelerada, se houver uso excessivo em uma conta. Os desenvolvedores devem usar a Grade de Eventos. Consulte [Rotear eventos para um ponto de extremidade personalizado de web](monitoring/job-state-events-cli-how-to.md).

O **Trabalho** normalmente passa pelos seguintes estados: **Agendado**, **Enfileirado**, **Processando**, **Concluído** (o estado final). Se o trabalho tiver encontrado um erro, você receberá o estado do **Erro**. Se o trabalho estiver para ser cancelado, você obterá **Cancelando** e **Cancelado** quando estiver pronto.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Criar uma política de chave de conteúdo

Uma chave de conteúdo fornece acesso seguro aos seus ativos. É necessário criar uma [política de chave de conteúdo](drm-content-key-policy-concept.md) ao criptografar o conteúdo com um DRM. A política configura o modo como a chave de conteúdo é entregue aos clientes finais. A chave de conteúdo é associada ao Localizador de Streaming. Os Serviços de Mídia também fornecem o serviço de distribuição de chaves, que distribui chaves de criptografia e licenças para usuários autorizados.

É preciso definir os requisitos (restrições) na **Política de Chave de Conteúdo** que precisa ser atendida para fornecer as chaves com a configuração especificada. Neste exemplo, definimos as configurações e os requisitos a seguir:

* Configuração

    As licenças do [PlayReady](drm-playready-license-template-concept.md) e do [Widevine](drm-widevine-license-template-concept.md) são configuradas de modo que possam ser entregues pelo serviço de entrega de licença dos Serviços de Mídia. Embora esse aplicativo de exemplo não configure a licença do [FairPlay](drm-fairplay-license-overview.md), ele contém um método que pode ser usado para configurar o FairPlay. Você pode adicionar a configuração do FairPlay como outra opção.

* Restrição

    O aplicativo define uma restrição de tipo de token JWT (Token Web JSON) na política.

Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo. Para descriptografar o fluxo, o player solicita a chave do serviço de distribuição de chaves. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que você especificou para a chave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Criar um Localizador de Streaming

Depois que a codificação for concluída e a política de chave de conteúdo for definida, a próxima etapa é disponibilizar o vídeo no ativo de saída para os clientes o reproduzirem. Há duas etapas para disponibilizar o vídeo:

1. Crie um [Localizador de Streaming](stream-streaming-locators-concept.md).
2. Compile as URLs de streaming que os clientes podem usar.

O processo de criação de um **Localizador de Streaming** é chamado de publicação. Por padrão, o **Localizador de streaming** é válido imediatamente depois que você faz as chamadas à API. Ela vai durar até ser excluída, a menos que você configure as horas de início e de término opcionais.

Ao criar um **Localizador de streaming**, você precisará especificar o `StreamingPolicyName` desejado. Neste tutorial, estamos usando uma das Políticas de Streaming predefinidas, que informa como publicar o conteúdo para streaming de Serviços de Mídia do Azure. Neste exemplo, definimos a StreamingLocator.StreamingPolicyName à política do “Predefined_MultiDrmCencStreaming”. As criptografias PlayReady e Widevine são aplicadas e a chave é entregue ao cliente de reprodução com base nas licenças de DRM configuradas. Se você também quiser criptografar seu fluxo com CBCS (FairPlay), use “Predefined_MultiDrmStreaming”.

> [!IMPORTANT]
> Ao usar uma [Política de Streaming](stream-streaming-policy-concept.md) personalizada, você deve criar um conjunto limitado de políticas para a conta de Serviço de Mídia e reutilizá-los para os Localizadores de Streaming sempre que as mesmas opções e protocolos de criptografia forem necessários. Sua conta de Serviço de Mídia tem uma cota para o número de entradas de StreamingPolicy. Você não deve criar um StreamingPolicy para cada StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Obter um token de teste

Neste tutorial, especificamos que a política de chave de conteúdo tenha uma restrição de token. A política restrita do token deve ser acompanhada por um token emitido por um Serviço de Token de Segurança (STS). Os Serviços de Mídia dão suporte a tokens no formato [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) e é isso que configuramos no exemplo.

O ContentKeyIdentifierClaim é usado em ContentKeyPolicy, o que significa que o token apresentado para o serviço de entrega de chave deve ter o identificador do ContentKey nele. No exemplo, nós não especificamos uma chave de conteúdo ao criar o Localizador de Streaming, o sistema cria uma senha aleatória para nós. Para gerarmos o teste de token, devemos obter o ContentKeyId para colocar na declaração ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Criar uma URL de streaming

Agora que o [StreamingLocator](/rest/api/media/streaminglocators) foi criado, é possível obter as URLs de streaming. Para criar uma URL, você precisa concatenar o nome de host [StreamingEndpoint](/rest/api/media/streamingendpoints) e o caminho do **Localizador de Streaming**. Neste exemplo, o *padrão* do **Ponto de Extremidade de Streaming** é usado. Ao criar uma conta de Serviço de Mídia, este *padrão* **Ponto de Extremidade de Streaming** estará em um estado parado, portanto você precisa chamar **Iniciar**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Ao executar o aplicativo, você vê a seguinte tela:

![Proteger com DRM](./media/drm-protect-with-drm-tutorial/playready_encrypted_url.png)

Você pode abrir um navegador e colar a URL resultante para inicializar a página de demonstração do Player de Mídia do Azure com a URL e o token já preenchidos para você.

## <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos em sua conta de Serviços de Mídia

Em geral, você deve limpar tudo, exceto os objetos que planeja reutilizar (normalmente, você reutilizará Transformações, StreamingLocators etc). Se desejar que sua conta seja limpa após fazer experimentos, exclua os recursos que não planeja reutilizar. Por exemplo, o seguinte código exclui o trabalho, os ativos criados e a política de chave de conteúdo:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais de qualquer um dos recursos em seu grupo de recursos, incluindo as contas dos Serviços de Mídia e de armazenamento que você criou neste tutorial, exclua o grupo de recursos criados anteriormente.

Execute este comando da CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>Observações adicionais

* O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

Fazer Check-out

> [!div class="nextstepaction"]
> [Proteger com AES-128](drm-playready-license-template-concept.md)
