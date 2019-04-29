---
title: Configurar política de autorização de chave de conteúdo usando o portal do Azure | Microsoft Docs
description: Saiba como configurar uma política de autorização para uma chave de conteúdo.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: b046ce5a8647abe601a6327667241d98445ce1e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61130521"
---
# <a name="configure-a-content-key-authorization-policy"></a>Configurar uma política de autorização de chave de conteúdo
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Visão geral
 É possível usar os Serviços de Mídia do Azure para distribuir streams MPEG-DASH, Smooth Streaming e HTTP Live Streaming (HLS) protegidos com a criptografia AES por meio do uso de chaves de criptografia de 128 bits ou do [gerenciamento de direitos digitais (DRM) do PlayReady](https://www.microsoft.com/playready/overview/). Com os Serviços de Mídia, também é possível distribuir streams DASH criptografados com o DRM do Widevine. O PlayReady e o Widevine são criptografados de acordo com a especificação de criptografia comum (ISO/IEC 23001-7 CENC).

Os Serviços de Mídia também fornecem um Serviço de Entrega de Chaves/Licenças por meio do qual os clientes podem obter chaves AES ou licenças do PlayReady/Widevine para reproduzir o conteúdo criptografado.

Este artigo mostra como usar o Portal do Azure para configurar a política de autorização de chave de conteúdo. A chave pode ser usada posteriormente para criptografar dinamicamente o conteúdo. Observe que, no momento, você pode criptografar os formatos HLS, MPEG DASH, e Smooth Streaming. Não é possível criptografar downloads progressivos.

Quando um player solicita um stream definido para ser criptografado dinamicamente, os Serviços de Mídia usam a chave configurada para criptografar dinamicamente o conteúdo usando a criptografia AES ou DRM. Para descriptografar o fluxo, o player solicita a chave do serviço de distribuição de chaves. Para determinar se o usuário está autorizado a obter a chave, o serviço avalia as políticas de autorização que você especificou para a chave.

Se você pretende ter várias chaves de conteúdo ou se deseja especificar uma URL de serviço de entrega de chave/licença diferente do serviço de entrega de chaves dos Serviços de Mídia, use o SDK do .NET dos Serviços de Mídia ou as APIs REST. Para obter mais informações, consulte:

* [Configurar uma política de autorização de chave de conteúdo usando o SDK do .NET dos Serviços de Mídia](media-services-dotnet-configure-content-key-auth-policy.md)
* [Configurar uma política de autorização de chave de conteúdo usando a API REST dos Serviços de Mídia](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Algumas considerações se aplicam
* Quando sua conta dos Serviços de Mídia é criada, um ponto de extremidade de streaming padrão é adicionado à sua conta em estado "Parado". Para começar a transmitir seu conteúdo e aproveitar o empacotamento dinâmico e a criptografia dinâmica, o ponto de extremidade de streaming deve estar no estado “Executando”. 
* O ativo deve conter um conjunto de MP4s de taxa de bits adaptável ou arquivos de Smooth Streaming de taxa de bits adaptável. Para obter mais informações, consulte [Codificar um ativo](media-services-encode-asset.md).
* O serviço de entrega de chave armazena em cache ContentKeyAuthorizationPolicy e seus objetos relacionados (opções e restrições da política) por 15 minutos. Você pode criar uma ContentKeyAuthorizationPolicy e especificar o uso de uma restrição de token, testá-la e, em seguida, atualizar a política para a restrição aberta. Esse processo leva aproximadamente 15 minutos antes de a política mudar para a versão aberta.
* O ponto de extremidade de streaming dos Serviços de Mídia define o valor do cabeçalho Access-Control-Allow-Origin do CORS na resposta de simulação como o curinga “\*”. Esse valor funciona bem com a maioria dos players, incluindo o Player de Mídia do Azure, Roku e JWPlayer e outros. No entanto, alguns players que usam dashjs não funcionam, porque o modo de credenciais definido para "incluir" XMLHttpRequest em seu dashjs não permite o curinga “\*” como o valor de Access-Control-Allow-Origin. Como uma solução alternativa para essa limitação em dashjs, se você hospedar seu cliente a partir de um único domínio os Serviços de Mídia podem especificar esse domínio no cabeçalho da resposta de simulação. Para obter assistência, abra um tíquete de suporte por meio do portal do Azure.

## <a name="configure-the-key-authorization-policy"></a>Configurar a política de autorização da chave
Para configurar a política de autorização da chave, selecione a página **PROTEÇÃO DE CONTEÚDO** .

Os Serviços de Mídia oferecem suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. A política de autorização de chave de conteúdo pode ter restrições de autorização abertas, de token ou de IP. (IP pode ser configurado com REST ou o SDK do .NET.)

### <a name="open-restriction"></a>Restrição aberta
A restrição aberta significa que o sistema entrega a chave para qualquer pessoa que faça uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Restrição de token
Para escolher a política com restrição de token, selecione o botão **TOKEN**.

A política restrita do token deve ser acompanhada por um token emitido por um serviço de token de segurança (STS). Os Serviços de Mídia oferecem suporte a tokens nos formatos simple web token ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e Token Web JSON (JWT). Para obter mais informações, consulte [Autenticação de JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Os Serviços de Mídia não oferecem um STS. Você pode criar um STS personalizado para emitir tokens. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. Se o token for válido e as declarações no token corresponderem às configuradas para a chave de conteúdo, o serviço de distribuição de chave dos Serviços de Mídia retornará a chave criptografada para o cliente.

Ao configurar a política restrita do token, você deve especificar os parâmetros de chave de verificação primária, emissor e público. A chave de verificação primária contém a chave com a qual o token foi assinado. O emissor é o STS que emite o token. A audiência (às vezes chamada de escopo) descreve a intenção do token ou o recurso que o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

### <a name="playready"></a>PlayReady
Ao proteger o conteúdo com PlayReady, uma das coisas que você precisa especificar na sua política de autorização é uma cadeia de caracteres XML que define o modelo de licença do PlayReady. Por padrão, a seguinte política é definida:

    <PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
          <LicenseTemplates>
            <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
              <ContentKey i:type="ContentEncryptionKeyFromHeader" />
              <LicenseType>Nonpersistent</LicenseType>
              <PlayRight>
                <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
              </PlayRight>
            </PlayReadyLicenseTemplate>
          </LicenseTemplates>
        </PlayReadyLicenseResponseTemplate>

Você pode selecionar o botão **importar política xml** e fornecer um XML diferente em conformidade com o esquema XML definido na [visão geral do modelo de licença PlayReady dos Serviços de Mídia](media-services-playready-license-template-overview.md).

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

