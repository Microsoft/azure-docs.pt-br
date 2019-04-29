---
title: Usar o serviço de entrega de licença de criptografia dinâmica do DRM com os Serviços de Mídia do Azure | Microsoft Docs
description: É possível usar os Serviços de Mídia do Azure para entregar seus fluxos criptografados com licenças do Microsoft PlayReady, Google Widevine ou Apple FairPlay.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f53ae122e9888f3e537a3557b6ac5bd76856c2eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995696"
---
# <a name="use-drm-dynamic-encryption-and-license-delivery-service"></a>Usar o serviço de entrega de licença e criptografia dinâmica do DRM

É possível usar os Serviços de Mídia do Azure para fornecer transmissões MPEG-DASH, Smooth Streaming e HLS (HTTP Live Streaming) protegidos com o [DRM (gerenciamento de direitos digitais) do PlayReady](https://www.microsoft.com/playready/overview/). Também é possível usar os Serviços de Mídia para fornecer fluxos DASH criptografados com as licenças de DRM do **Google Widevine**. O PlayReady e o Widevine são criptografados de acordo com a especificação de criptografia comum (ISO/IEC 23001-7 CENC). Os Serviços de Mídia também permitem a criptografia do seu conteúdo HLS com o **Apple FairPlay** (AES-128 CBC). 

Além disso, os Serviços de Mídia fornecem um serviço para entregar licenças de DRM do PlayReady, Widevine e FairPlay. Quando um usuário solicitar conteúdo protegido por DRM, o player de mídia solicitará uma licença do serviço de licença dos Serviços de Mídia. Se o player de mídia estiver autorizado, o serviço de licença dos Serviços de Mídia emitirá uma licença para o player. Uma licença contém a chave de descriptografia que pode ser usada pelo player cliente para descriptografar e transmitir o conteúdo.

Este artigo se baseia no exemplo [Criptografando com DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM). Entre outras coisas, o exemplo demonstra como:

* Criar uma transformação de codificação que usa uma predefinição de interna para a codificação de taxa de bits adaptável e ingere um arquivo diretamente de uma [URL de origem HTTPs](job-input-from-http-how-to.md).
* Defina a chave de assinatura usada para a verificação de seu token.
* Defina os requisitos (restrições) na política de chave de conteúdo que deve ser atendida para fornecer as chaves com a configuração especificada. 

    * Configuração 
    
        Neste exemplo, as licenças do [PlayReady](playready-license-template-overview.md) e do [Widevine](widevine-license-template-overview.md) são configuradas de modo que possam ser entregues pelo serviço de entrega de licença dos Serviços de Mídia. Mesmo assim, esse aplicativo de exemplo não configura a licença do [FairPlay](fairplay-license-overview.md), ele contém um método que pode ser usado para configurar o FairPlay. Se desejar, você pode adicionar a configuração do FairPlay como outra opção.

    * Restrição

        O aplicativo define uma restrição de tipo de token JWT na política.

* Crie um StreamingLocator para o ativo especificado e com o nome de política de streaming especificado. Nesse caso, é usada a política predefinida. Ela define duas chaves de conteúdo no StreamingLocator: AES-128 (envelope) e CENC (PlayReady e Widevine).  
    
    Depois de criar o StreamingLocator, o ativo de saída será publicado e ficará disponível para clientes para ser reproduzido.

    > [!NOTE]
    > Verifique se o StreamingEndpoint do qual você deseja transmitir está no estado Executando.

* Crie uma URL para o Player de Mídia do Azure que inclua o manifesto DASH e o token do PlayReady necessários para reproduzir o conteúdo do PlayReady criptografado. O exemplo define a expiração do token para 1 hora. 

    Você pode abrir um navegador e colar a URL resultante para inicializar a página de demonstração do Player de Mídia do Azure com a URL e o token já preenchidos para você.  

    ![Proteger com DRM](./media/protect-with-drm/playready_encrypted_url.png)

> [!NOTE]
> Você pode criptografar cada ativo com vários tipos de criptografia (AES-128, PlayReady, Widevine, FairPlay). Consulte [Tipos de criptografia e protocolos de streaming](content-protection-overview.md#streaming-protocols-and-encryption-types) para ver o que faz sentido combinar.

O exemplo descrito neste artigo produz o seguinte resultado:

![AMS com vídeo protegido por DRM](./media/protect-with-drm/ams_player.png)

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir o tutorial.

* Examine o artigo [Visão geral da proteção de conteúdo](content-protection-overview.md).
* Reveja [Projetar proteção de conteúdo de diversos DRMs com controle de acesso](design-multi-drm-system-with-access-control.md)
* Instalar Visual Studio Code ou Visual Studio
* Crie uma nova conta dos Serviços de Mídia do Azure, conforme descrito [neste início rápido](create-account-cli-quickstart.md).
* Obtenha as credenciais necessárias para usar as APIs dos Serviços de Mídia seguindo as [APIs de acesso](access-api-cli-how-to.md)
* Defina os valores apropriados no arquivo de configuração do aplicativo (appsettings.json).

## <a name="download-code"></a>Código de download

Clone um repositório do GitHub que contenha o exemplo de .NET completo discutido neste artigo em sua máquina usando o comando a seguir:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
O exemplo de “Criptografar com DRM” está localizado na pasta [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> O exemplo cria recursos exclusivos toda vez que você executa o aplicativo. Normalmente, você vai reutilizar recursos existentes como transformações e políticas (se os recursos existentes apresentarem configurações necessárias). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Começar a usar as APIs de Serviços de Mídia com a SDK .NET

Para começar a usar a APIs de Serviços de Mídia do Azure com o .NET, é necessário criar um objeto **AzureMediaServicesClient**. Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. No código que você clonou no início do artigo, a função **GetCredentialsAsync** cria o objeto ServiceClientCredentials com base nas credenciais fornecidas no arquivo de configuração local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Criar um ativo de saída  

O [Ativo](https://docs.microsoft.com/rest/api/media/assets) de saída armazena o resultado do trabalho de codificação.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Obter ou criar uma transformação de codificação

Ao criar um novo exemplo de [Transformação](https://docs.microsoft.com/rest/api/media/transforms), você precisa especificar o que deseja produzir como uma saída. O parâmetro necessário é um objeto **TransformOutput**, como mostrado no código a seguir. Cada **TransformOutput** contém um **Predefinição**. **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que serão usadas para gerar o **TransformOutput** desejado. O exemplo descrito neste artigo usa uma predefinição chamada **AdaptiveStreaming**. A predefinição codifica o vídeo de entrada em uma escada de taxa de bits gerada automaticamente (pares de resolução de taxa de bits) com base na taxa de bits e resolução de entrada e produz arquivos ISO MP4 com vídeo H. 264 e correspondente a cada par de resolução de taxa de bits de áudio AAC. 

Antes de criar uma nova [Transformação](https://docs.microsoft.com/rest/api/media/transforms), você deve verificar se já existe uma usando o método **Get**, conforme mostrado no código a seguir.  Em Serviços de Mídia v3, os métodos **Get** em entidades retornam **nulo** se a entidade não existe (uma verificação de maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Enviar Trabalho

Conforme mecionado acima, o objeto de [Transformação](https://docs.microsoft.com/rest/api/media/transforms) é a receita e um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs) é a solicitação real para os Serviços de Mídia para aplicar a **Transformação** a um determinado conteúdo de vídeo ou áudio de entrada. O **Trabalho** especifica informações como o local da entrada vídeo e o local da saída.

Neste tutorial, criamos a entrada do trabalho com base em um arquivo que é ingerido diretamente de uma [URL de origem HTTPs](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Aguarde a conclusão do trabalho

Este trabalho levará algum tempo para concluir e quando você desejar ser notificado. O exemplo de código a seguir mostra como sondar o serviço para o status do [Trabalho](https://docs.microsoft.com/rest/api/media/jobs). Sondagem não é uma prática recomendada para aplicativos de produção devido à latência potencial. A sondagem pode ser acelerada, se houver uso excessivo em uma conta. Os desenvolvedores devem usar a Grade de Eventos. Consulte [Rotear eventos para um ponto de extremidade personalizado de web](job-state-events-cli-how-to.md).

O **Trabalho** normalmente passa pelos seguintes estados: **Agendado**, **Enfileirado**, **Processando**, **Concluído** (o estado final). Se o trabalho encontrou um erro, você receberá o estado do **Erro**. Se o trabalho está no processo de ser cancelado, você obterá **Cancelando** e **Cancelado** quando estiver pronto.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>Criar um ContentKeyPolicy

Uma chave de conteúdo fornece acesso seguro aos seus ativos. Você precisará criar uma política de chave de conteúdo que define como a chave de conteúdo é entregue para os clientes finais. A chave de conteúdo é associada a StreamingLocator. Os Serviços de Mídia também fornecem o serviço de distribuição de chaves, que distribui chaves de criptografia e licenças para usuários autorizados. 

É preciso definir os requisitos (restrições) na política de chave de conteúdo que deve ser atendida para fornecer as chaves com a configuração especificada. Neste exemplo, definimos as configurações e os requisitos a seguir:

* Configuração 

    As licenças do [PlayReady](playready-license-template-overview.md) e do [Widevine](widevine-license-template-overview.md) são configuradas de modo que possam ser entregues pelo serviço de entrega de licença dos Serviços de Mídia. Mesmo assim, esse aplicativo de exemplo não configura a licença do [FairPlay](fairplay-license-overview.md), ele contém um método que pode ser usado para configurar o FairPlay. Você pode adicionar a configuração do FairPlay como outra opção.

* Restrição

    O aplicativo define uma restrição de tipo de token JWT na política.

Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo. Para descriptografar o fluxo, o player solicita a chave do serviço de distribuição de chaves. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia a política de chave de conteúdo que você especificou para a chave.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>Criar um StreamingLocator

Depois que a codificação for concluída e a política de chave de conteúdo for definida, a próxima etapa é disponibilizar o vídeo no ativo de saída para os clientes o reproduzirem. Para fazer isso em duas etapas: 

1. Crie um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. Compile as URLs de streaming que os clientes podem usar. 

O processo de criação de um **StreamingLocator** é chamado de publicação. Por padrão, o **StreamingLocator** é válido imediatamente após você fazer as chamadas de API e dura até ser excluído, a menos que você configure os horários de início e término opcionais. 

Ao criar um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), você precisará especificar o **StreamingPolicyName** desejado. Neste tutorial, estamos usando uma das StreamingPolicies predefinidas, que informa como publicar o conteúdo para streaming de Serviços de Mídia do Azure. Neste exemplo, definimos a StreamingLocator.StreamingPolicyName à política do “Predefined_MultiDrmCencStreaming”. Essa política indica que você deseja que duas chaves de conteúdo (envelope e CENC) sejam geradas e definidas no localizador. Assim, as criptografias do envelope, PlayReady e Widevine são aplicadas (a chave é entregue ao cliente de reprodução com base nas licenças de DRM configuradas). Se você também quiser criptografar seu fluxo com CBCS (FairPlay), use “Predefined_MultiDrmStreaming”. 

> [!IMPORTANT]
> Ao usar a [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizada, você deve criar um conjunto limitado de tais políticas para sua conta de serviço de mídia e reutilizá-los para o seu StreamingLocators, sempre que a mesmo opção de criptografia e protocolos sejam necessários. Sua conta de Serviço de Mídia tem uma cota para o número de entradas de StreamingPolicy. Você não deve criar um novo StreamingPolicy para cada StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Obter um token de teste
        
Neste tutorial, especificamos que a política de chave de conteúdo tenha uma restrição de token. A política restrita do token deve ser acompanhada por um token emitido por um Serviço de Token de Segurança (STS). Os Serviços de Mídia oferecem suporte a tokens no formato [Token Web JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) e isso é o que nós configuramos no exemplo.

O ContentKeyIdentifierClaim é usado em ContentKeyPolicy, o que significa que o token apresentado para o serviço de entrega de chave deve ter o identificador do ContentKey nele. No exemplo, nós não especificamos uma chave de conteúdo ao criar o StreamingLocator, o sistema cria uma senha aleatória para nós. Para gerar o teste de token, devemos obter o ContentKeyId para colocar na declaração ContentKeyIdentifierClaim.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>Criar uma URL de streaming de DASH

Agora que o [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) foi criado, é possível obter as URLs de streaming. Para criar uma URL, você precisa concatenar o nome de host [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) e o caminho **StreamingLocator**. Neste exemplo, o *padrão* **StreamingEndpoint** é usado. Ao criar uma conta de Serviço de Mídia, este *padrão* **StreamingEndpoint** estará em um estado parado, portanto você precisa chamar **Iniciar**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Limpar os recursos em sua conta de Serviços de Mídia

Em geral, você deve limpar tudo, exceto os objetos que planeja reutilizar (normalmente, você reutilizará transformações e persistirá StreamingLocators, etc.). Se desejar para sua conta para ser limpa depois de testar, você deve excluir os recursos que não planeja reutilizar.  Por exemplo, o código a seguir exclui trabalhos.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="next-steps"></a>Próximas etapas

Confira como [proteger com AES-128](protect-with-aes128.md)
