---
title: "Configurar política de autorização de chave de conteúdo usando o portal do Azure | Microsoft Docs"
description: "Saiba como configurar uma política de autorização para uma chave de conteúdo."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 5a35c7255a1c30a693862589c14f6a22a1900790
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-content-key-authorization-policy"></a>Configurar a Política de Autorização de Chave de Conteúdo
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Visão geral
Os Serviços de Mídia do Microsoft Azure permitem distribuir fluxos MPEG-DASH, Smooth Streaming e HLS (HTTP-Live-Streaming) protegidos com a criptografia AES (usando chaves de criptografia de 128 bits) ou com o [DRM do Microsoft PlayReady](https://www.microsoft.com/playready/overview/). O AMS também permite distribuir fluxos DASH criptografados com o DRM do Widevine. PlayReady e Widevine são criptografados de acordo com a especificação de criptografia comum (ISO/IEC 23001-7 CENC).

Os Serviços de Mídia também fornecem um **Serviço de Entrega de Chaves/Licenças** por meio do qual os clientes podem obter chaves AES ou licenças do PlayReady/Widevine para reproduzir o conteúdo criptografado.

Este tópico mostra como usar o Portal do Azure para configurar a política de autorização de chave de conteúdo. A chave pode ser usada posteriormente para criptografar dinamicamente o conteúdo. Observe que, no momento, você pode criptografar o seguintes formatos de streaming: HLS, MPEG DASH, e Smooth Streaming. Não é possível criptografar downloads progressivos.

Quando um player solicita um fluxo definido para ser criptografado dinamicamente, os Serviços de Mídia usam a chave configurada para criptografar dinamicamente o conteúdo usando a criptografia AES ou DRM. Para descriptografar o fluxo, o player solicitará a chave do serviço de distribuição de chaves. Para decidir se o usuário está autorizado para obter a chave ou não, o serviço avalia as políticas de autorização que você especificou para a chave.

Se você pretende ter várias chaves de conteúdo ou se deseja especificar uma URL de **Serviço de Entrega de Chave/Licença** diferente do serviço de entrega de chaves dos Serviços de Mídia, use o SDK do .NET dos Serviços de Mídia ou as APIs REST.

[Configurar política de autorização de chave de conteúdo usando o SDK do .NET dos Serviços de Mídia](media-services-dotnet-configure-content-key-auth-policy.md)

[Configurar política de autorização de chave de conteúdo usando a API REST dos Serviços de Mídia](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Algumas considerações se aplicam:
* Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para começar a transmitir seu conteúdo e aproveitar o empacotamento dinâmico e a criptografia dinâmica, o ponto de extremidade de streaming deve estar no estado **Executando**. 
* O ativo deve conter um conjunto de MP4s de taxa de bits adaptável ou arquivos de Smooth Streaming de taxa de bits adaptável. Para obter mais informações, consulte [Codificar um ativo](media-services-encode-asset.md).
* O serviço de entrega de chave armazena em cache ContentKeyAuthorizationPolicy e seus objetos relacionados (opções e restrições da política) por 15 minutos.  Se você criar um ContentKeyAuthorizationPolicy e optar por usar uma restrição "Token", testá-lo e, em seguida, atualizar a política de restrição "Aberta", levará aproximadamente 15 minutos antes da política alternar para a versão "Aberta" da política.

## <a name="how-to-configure-the-key-authorization-policy"></a>Como: configurar a política de autorização da chave
Para configurar a política de autorização da chave, selecione a página **PROTEÇÃO DE CONTEÚDO** .

Os serviços de mídia oferecem suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. A política de autorização de chave de conteúdo pode ter restrições de autorização **abertas**, de **token** ou de **IP** (a de **IP** pode ser configurada com REST ou SDK do .NET).

### <a name="open-restriction"></a>Restrição aberta
A restrição **aberta** significa que o sistema fornecerá a chave para qualquer pessoa que fizer uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Restrição de token
Para escolher a política com restrição de token, pressione o botão **TOKEN** .

A política restrita do **token** deve ser acompanhada por um token emitido por um **Serviço de Token Seguro** (STS). Os Serviços de Mídia oferecem suporte a tokens no formato **Token Simples da Web** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e no formato **Token Web JSON** (JWT). Para obter informações, consulte [Autenticação de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Os Serviços de Mídia não fornecem **Serviços de Token Seguro**. Você pode criar um STS personalizado ou usar o Microsoft Azure ACS para emitir tokens. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. O serviço de distribuição de chaves dos serviços de mídia retornará a chave de criptografia para o cliente se o token for válido e as declarações no token corresponderem aos configurados para a chave de conteúdo. Para saber mais, consulte [Uso do Azure ACS para emitir tokens](http://mingfeiy.com/acs-with-key-services).

Ao configurar a política com restrição de **TOKEN**, você deve definir valores para **chave de verificação**, **emissor** e **Público**. A chave de verificação primária contém a chave que o token foi assinado, o emissor é o serviço de token seguro que emite o token. A audiência (às vezes chamada de escopo) descreve a intenção do token ou o recurso que o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

### <a name="playready"></a>PlayReady
Ao proteger o conteúdo com **PlayReady**, uma das coisas que você precisa especificar na sua política de autorização é uma cadeia de caracteres XML que define o modelo de licença do PlayReady. Por padrão, a seguinte política é definida:

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1"> <LicenseTemplates> <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices> <ContentKey i:type="ContentEncryptionKeyFromHeader" /> <LicenseType>Nonpersistent</LicenseType> <PlayRight> <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput> </PlayRight> </PlayReadyLicenseTemplate> </LicenseTemplates> </PlayReadyLicenseResponseTemplate>

Você pode clicar no botão **Importar a política XML** e fornecer um XML diferente que está em conformidade com o esquema XML definido [aqui](media-services-playready-license-template-overview.md).

## <a name="next-step"></a>Próxima etapa
Revise os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

