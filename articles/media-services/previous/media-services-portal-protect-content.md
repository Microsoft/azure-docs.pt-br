---
title: Configurar políticas de proteção de conteúdo usando o portal do Azure | Microsoft Docs
description: Este artigo demonstra como usar o portal do Azure para configurar as políticas de proteção de conteúdo. O artigo também mostra como habilitar a criptografia dinâmica para seus ativos.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: c6620158965271ba99f80c2fbd36504fdbea7ecf
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010772"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Configurar políticas de proteção de conteúdo usando o portal do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).   > não há novos recursos ou funcionalidades sendo adicionados aos serviços de mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](../latest/index.yml). Além disso, consulte s [diretrizes de migração da v2 para a v3](../latest/migrate-v-2-v-3-migration-introduction.md)
>

 Com os Serviços de Mídia do Azure, você pode proteger sua mídia desde o momento que ela sai do seu computador até o armazenamento, processamento e entrega. É possível usar os Serviços de Mídia para distribuir o conteúdo criptografado dinamicamente com a criptografia AES usando chaves de criptografia de 128 bits. Você também pode usá-los com criptografia comum (CENC) usando o PlayReady e/ou gerenciamento de direitos digitais (DRM) do Widevine e o FairPlay da Apple. 

Os Serviços de Mídia fornecem um serviço para entregar as licenças DRM e as chaves de limpeza AES aos clientes autorizados. É possível usar o portal do Azure para criar uma política de autorização de chave/licença para todos os tipos de criptografias.

Este artigo demonstra como configurar a política de proteção de conteúdo usando o portal. O artigo também mostra como aplicar a criptografia dinâmica em seus ativos.

## <a name="start-to-configure-content-protection"></a>Iniciar a configuração da proteção de conteúdo
Para usar o portal para configurar a proteção de conteúdo global usando sua conta de Serviços de Mídia, execute as seguintes etapas:

1. No [portal](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia.

1. Selecione **configurações**  >  **proteção de conteúdo**.

    ![Proteção de conteúdo](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>política de autorização de chave/licença
Os Serviços de Mídia oferecem suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave ou de licença. Você deve configurar a política de autorização da chave de conteúdo. Seu cliente deve, então, atender à política antes que a chave/licença possa ser fornecida a ele. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização: abertas ou de token.

É possível usar o portal para criar uma política de autorização de chave/licença para todos os tipos de criptografias.

### <a name="open-authorization"></a>Autorização aberta
A restrição aberta significa que o sistema entrega a chave a qualquer pessoa que faça uma solicitação de chave. Essa restrição pode ser útil para o teste. 

### <a name="token-authorization"></a>Autorização de token
A política restrita do token deve ser acompanhada por um token emitido por um Serviço de Token de Segurança (STS). Os Serviços de Mídia oferecem suporte a tokens nos formatos Simple Web Token (SWT) e Token Web JSON (JWT). Os Serviços de Mídia não oferecem um STS. Você pode criar um STS personalizado ou usar o Serviço de Controle de Acesso do Azure para emitir tokens. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. Se o token for válido, e as declarações no token corresponderem àquelas configuradas para a chave (ou licença), o serviço de distribuição de chaves dos Serviços de Mídia retornará a chave (ou licença) solicitada para o cliente.

Ao configurar a política restrita do token, você deve especificar os parâmetros de chave de verificação primária, emissor e público. A chave de verificação primária contém a chave com a qual o token foi assinado. O emissor é o serviço de token seguro que emite o token. A audiência (às vezes chamada de escopo) descreve a intenção do token ou o recurso que o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

![política de autorização de chave/licença](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Modelo de licença PlayReady
O modelo de licença do PlayReady define a funcionalidade que está habilitada em sua licença do PlayReady. Para obter mais informações sobre o modelo de licença do PlayReady, consulte [Visão geral do modelo de licença do PlayReady dos Serviços de Mídia](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Não persistente
Se você configurar uma licença como não persistente, ela só será mantida na memória enquanto o player usar a licença.  

![Proteção de conteúdo não persistente](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Persistente
Se você configurar uma licença como persistente, ela é salva no armazenamento persistente no cliente.

![Proteção de conteúdo persistente](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Modelo de licença do Widevine
O modelo de licença do Widevine define a funcionalidade que está habilitada em suas licenças do Widevine.

### <a name="basic"></a>Básico
Quando você seleciona **Básico**, o modelo é criado com todos os valores padrões.

### <a name="advanced"></a>Avançado
Para obter mais informações sobre o modelo de direitos do Widevine, consulte a [Visão geral do modelo de licença do Widevine](media-services-widevine-license-template-overview.md).

![Proteção de conteúdo avançada](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configuração do FairPlay
Para habilitar a criptografia do FairPlay, selecione **Configuração do FairPlay**. Depois selecione o **Certificado do aplicativo** e insira a **Chave Secreta do Aplicativo**. Para obter mais informações sobre a configuração e os requisitos do FairPlay, consulte [Proteger seu conteúdo HLS com o FairPlay da Apple ou o Microsoft PlayReady](media-services-protect-hls-with-FairPlay.md).

![Configuração do FairPlay](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Aplique a criptografia dinâmica em seu ativo
Para aproveitar a criptografia dinâmica, codifique o arquivo de origem em um conjunto de arquivos MP4 de taxa de bits adaptável.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Selecionar o ativo que você deseja criptografar
Para ver todos os seus ativos, selecione **configurações**  >  **ativos**.

![Opção de ativos](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Criptografar com AES ou DRM
Quando você seleciona **Encrypt** para um ativo, há duas opções: **AES** ou **DRM**. 

#### <a name="aes"></a>AES
A criptografia da chave de limpeza do AES é habilitada em todos os protocolos de streaming: Smooth Streaming, HLS e MPEG-DASH.

![Configuração de criptografia](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. Depois de selecionar o **DRM**, você vê políticas de proteção de conteúdo diferentes (que devem ser configuradas neste ponto) e um conjunto de protocolos de streaming:

    a. **PlayReady e Widevine com o MPEG-DASH** – criptografam dinamicamente seu stream MPEG DASH com os DRMs do PlayReady e do Widevine.

    b. **PlayReady e Widevine com o MPEG-DASH+ FairPlay com HLS** – criptografam dinamicamente seu stream MPEG DASH com os DRMs do PlayReady e do Widevine. Essa opção também criptografa seus streams HLS com o FairPlay.

    c. **PlayReady somente com Smooth Streaming, HLS e MPEG-DASH** – criptografam dinamicamente o Smooth Streaming, HLS e streams MPEG-DASH com o DRM do PlayReady.

    d. **Widevine apenas com MPEG-DASH** – criptografa dinamicamente seu MPEG-DASH com o DRM do Widevine.
    
    e. **FairPlay apenas com HLS** – criptografa dinamicamente seu stream HLS com o FairPlay.

1. Para ativar a criptografia de FairPlay, na folha **Configurações Globais de Proteção de Conteúdo**, selecione **Configuração do FairPlay**. Em seguida, selecione o **certificado do aplicativo** e insira a **chave secreta do aplicativo**.

    ![Tipo de criptografia](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Depois de fazer a seleção da criptografia, selecione **Aplicar**.

>[!NOTE] 
>Se você planeja executar um HLS criptografado para AES no Safari, consulte a postagem de blog [HLS criptografado no Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="additional-notes"></a>Observações adicionais

* O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
