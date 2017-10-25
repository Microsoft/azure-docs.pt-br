---
title: "Configurando as políticas de proteção de conteúdo com o portal do Azure | Microsoft Docs"
description: "Este artigo demonstra como usar o portal do Azure para configurar as políticas de proteção de conteúdo. O artigo também mostra como habilitar a criptografia dinâmica para seus ativos."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: 67b3fa9936daebeafb7e87fe3a7b0c7e0105b3b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>Configurando as políticas de proteção de conteúdo com o portal do Azure
> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> 
> 

## <a name="overview"></a>Visão geral
Os Serviços de Mídia do Microsoft Azure (AMS) permitem proteger a mídia do momento em que ela deixa o computador e durante o armazenamento, processamento e entrega. Os Serviços de Mídia permitem entregar o conteúdo criptografado dinamicamente com a criptografia AES (usando chaves de criptografia de 128 bits) e a CENC (criptografia comum) usando o PlayReady e/ou Widevine DRM e Apple FairPlay. 

O AMS fornece um serviço para entregar as licenças DRM e as chaves de limpeza AES aos clientes autorizados. O portal do Azure permite que você crie uma **política de autorização de chave/licença** para todos os tipos de criptografias.

Este artigo demonstra como configurar as políticas de proteção de conteúdo com o portal do Azure. O artigo também mostra como aplicar a criptografia dinâmica em seus ativos.


> [!NOTE]
> Se você usou o portal clássico do Azure para criar políticas de proteção, as políticas poderão não aparecer no [portal do Azure](https://portal.azure.com/). No entanto, todas as antigas políticas ainda existirão. Você pode examiná-las usando o SDK do .NET dos Serviços de Mídia do Azure ou a ferramenta [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases) (para ver as políticas, clique com o botão direito no ativo -> Exibir informações (F4) -> clique na guia Chaves de conteúdo -> clique na chave). 
> 
> Se você quiser criptografar seu ativo usando as novas políticas, configure-as com o portal do Azure, clique em Salvar e reaplique a criptografia dinâmica. 
> 
> 

## <a name="start-configuring-content-protection"></a>Iniciar a configuração da proteção de conteúdo
Para usar o portal para começar a configurar a proteção de conteúdo, global para sua conta AMS, faça o seguinte:

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia do Azure.
2. Selecione **Configurações** > **Proteção de conteúdo**.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>política de autorização de chave/licença
O AMS oferece várias maneiras de autenticar os usuários que fazem solicitações de licença ou chave. A política de autorização da chave de conteúdo deve ser configurada por você e atendida pelo cliente para que a chave/licença seja entregue ao cliente. A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização: **aberta** ou **de token**.

O portal do Azure permite que você crie uma **política de autorização de chave/licença** para todos os tipos de criptografias.

### <a name="open"></a>Aberto
Restrição aberta significa que o sistema entregará a chave para qualquer pessoa que fizer uma solicitação de chave. Essa restrição pode ser útil para o teste. 

### <a name="token"></a>restrição
A política restrita do token deve ser acompanhada por um token emitido por um Secure Token Service (STS). Os serviços de mídia oferecem suporte a tokens no formato Simple Web Tokens (SWT) e no formato JSON Web Token (JWT). Os serviços de mídia não fornecem Secure Token Services. Você pode criar um STS personalizado ou usar o Microsoft Azure ACS para emitir tokens. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. O serviço de distribuição de chaves dos Serviços de Mídia retornará a chave de criptografia para o cliente se o token for válido e as declarações no token corresponderem àquelas configuradas para a chave (ou licença).

Ao configurar a política restrita do token, você deve especificar os parâmetros da chave de verificação primária, do emissor e do público-alvo. A chave de verificação primária contém a chave que o token foi assinado, o emissor é o serviço de token seguro que emite o token. A audiência (às vezes chamada de escopo) descreve a intenção do token ou o recurso que o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-rights-template"></a>Modelo de direitos do PlayReady
Para obter informações detalhadas sobre o modelo de direitos do PlayReady, consulte [Visão Geral do Modelo de Licença do PlayReady dos Serviços de Mídia](media-services-playready-license-template-overview.md).

### <a name="non-persistent"></a>Não persistente
Se você configurar a licença como não persistente, ela só será mantida na memória enquanto o player estiver usando a licença.  

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Persistente
Se você configurar a licença como persistente, ela será salva no armazenamento persistente no cliente.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-rights-template"></a>Modelo de direitos do Widevine
Para obter informações detalhadas sobre o modelo de direitos do Widevine, consulte [Visão Geral do Modelo de Licença do Widevine](media-services-widevine-license-template-overview.md).

### <a name="basic"></a>Basic
Quando você selecionar **Básico**, o modelo será criado com todos os valores padrões.

### <a name="advanced"></a>Avançado
Para obter uma explicação detalhada sobre a opção avançada das configurações do Widevine, consulte [este](media-services-widevine-license-template-overview.md) tópico.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configuração do FairPlay
Para habilitar a criptografia do FairPlay, você precisa fornecer o Certificado do Aplicativo e a Chave de Segredo do Aplicativo (ASK) com a opção Configuração do FairPlay. Para obter informações detalhadas sobre a configuração e os requisitos do FairPlay, consulte [este](media-services-protect-hls-with-fairplay.md) artigo.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Aplique a criptografia dinâmica em seu ativo
Para aproveitar a criptografia dinâmica, você precisa codificar o arquivo de origem em um conjunto de arquivos MP4 de taxa de bits adaptável.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Selecionar o ativo que você deseja criptografar
Para ver todos os seus ativos, selecione **Configurações** > **Ativos**.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Criptografar com AES ou DRM
Depois de pressionar **Criptografar** em um ativo, você verá duas opções: **AES** ou **DRM**. 

#### <a name="aes"></a>AES
A criptografia da chave de limpeza do AES será habilitada em todos os protocolos de streaming: Smooth Streaming, HLS e MPEG-DASH.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
Quando você selecionar a guia DRM, verá diferentes opções de políticas de proteção de conteúdo (que você deve ter configurado agora) + um conjunto de protocolos de streaming.

* **PlayReady e Widevine com o MPEG-DASH** - irão criptografar dinamicamente seu fluxo MPEG DASH com os DRMs do PlayReady e do Widevine.
* **PlayReady e Widevine com o MPEG-DASH+ FairPlay com HLS** - irão criptografar dinamicamente seu fluxo MPEG DASH com os DRMs do PlayReady e do Widevine. Também irão criptografar seus fluxos HLS com o FairPlay.
* **PlayReady somente com Smooth Streaming, HLS e MPEG-DASH** - irão criptografar dinamicamente o Smooth Streaming, HLS e fluxos MPEG-DASH com o DRM do PlayReady.
* **Widevine apenas com MPEG-DASH** - irá criptografar dinamicamente seu MPEG-DASH com o DRM do Widevine.
* **FairPlay apenas com HLS** - irá criptografar dinamicamente seu fluxo HLS com o FairPlay.

Para habilitar a criptografia do FairPlay, você precisa fornecer o Certificado do Aplicativo e a Chave de Segredo do Aplicativo (ASK) com a opção Configuração do FairPlay da folha de configurações Proteção de Conteúdo.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection009.png)

Após fazer a seleção da criptografia, pressione **Aplicar**.

>[!NOTE] 
>Se você pretende executar um HLS criptografado para AES no Safari, visite [este blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Próximas etapas
Examine os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

