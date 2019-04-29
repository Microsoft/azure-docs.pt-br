---
title: Proteger seu conteúdo com os Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da proteção de conteúdo com os Serviços de Mídia.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 8259b58c7f30b63084e970bd9aed99642a43226f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216162"
---
# <a name="content-protection-overview"></a>Visão geral de proteção do conteúdo 

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [diretrizes de migração da v2 para v3](../latest/migrate-from-v2-to-v3.md)

É possível usar os Serviços de Mídia do Azure para proteger sua mídia desde o momento que ela sai do seu computador até o armazenamento, processamento e entrega. Com os Serviços de Mídia, é possível entregar o conteúdo ao vivo e sob demanda criptografado dinamicamente com a criptografia AES (AES-128) ou qualquer um dos três principais sistemas DRM (gerenciamento de direitos digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados. 

A seguinte imagem ilustra o fluxo de trabalho de proteção de conteúdo dos Serviços de Mídia: 

![Proteger com o PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Este artigo explica conceitos e terminologia relevantes para a compreensão da proteção de conteúdo com os Serviços de Mídia. O artigo também fornece links para artigos que descrevem como proteger o conteúdo. 

## <a name="dynamic-encryption"></a>Criptografia dinâmica
 É possível usar os Serviços de Mídia para entregar conteúdo criptografado dinamicamente com chave não criptografada AES ou criptografia DRM usando o PlayReady, o Widevine ou o FairPlay. No momento, você pode criptografar os formatos HLS (HTTP Live Streaming), MPEG DASH e Smooth Streaming. Não há suporte para criptografia em downloads progressivos. Cada método de criptografia fornece suporte aos seguintes protocolos de streaming:

- AES: MPEG-DASH, Smooth Streaming e HLS
- PlayReady: MPEG-DASH, Smooth Streaming e HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Para criptografar um ativo, é necessário associar uma chave de conteúdo de criptografia ao seu ativo e também configurar uma política de autorização para a chave. As chaves de conteúdo podem ser especificadas ou geradas automaticamente pelos Serviços de Mídia.

Você também precisa configurar a política de entrega do ativo. Se deseja fazer streaming de um ativo criptografado de armazenamento, certifique-se de especificar como deseja entregá-lo, configurando a política de entrega do ativo.

Quando um fluxo é solicitado por um player, os Serviços de Mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando a chave não criptografada AES ou a criptografia DRM. Para descriptografar o streaming, o player solicita a chave do serviço de entrega de chaves dos Serviços de Mídia. Para decidir se o usuário está autorizado para obter a chave ou não, o serviço avalia as políticas de autorização que você especificou para a chave.

## <a name="aes-128-clear-key-vs-drm"></a>Chave AES-128 não criptografada vs. DRM
Geralmente, os clientes se perguntam se devem utilizar criptografia AES ou um sistema DRM. A principal diferença entre os dois sistemas é que, com a criptografia AES, a chave de conteúdo é transmitida ao cliente em um formato não criptografado. Como resultado, a chave usada para criptografar o conteúdo pode ser exibida em um rastreamento de rede no cliente em texto sem formatação. A criptografia de chave AES-128 não criptografada é adequada para casos de uso em que o visualizador é uma parte confiável (por exemplo, criptografar vídeos corporativos distribuídos em uma empresa para serem visualizados pelos funcionários).

PlayReady, Widevine e FairPlay fornecem um maior nível de criptografia em comparação com a criptografia de chave não criptografada AES-128. A chave de conteúdo é transmitida em um formato criptografado. Além disso, a descriptografia é identificada em um ambiente seguro no nível do sistema operacional, no qual é mais difícil para um usuário mal-intencionado atacar. O DRM é recomendado para casos de uso em que o visualizador pode não ser uma parte confiável e você exige o mais alto nível de segurança.

## <a name="storage-encryption"></a>Criptografia do armazenamento
Você pode usar a criptografia de armazenamento para criptografar o conteúdo não criptografado localmente usando criptografia AES de 256 bits. Você pode carregá-lo no Armazenamento do Azure, no qual ele está armazenado criptografado e em repouso. Ativos protegidos pela criptografia de armazenamento são descriptografados automaticamente e posicionados em um sistema de arquivos criptografados antes da codificação. Os ativos são opcionalmente criptografados novamente antes de carregar novamente como um novo ativo de saída. O caso de uso principal para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada com criptografia forte de alta qualidade em repouso no disco.

Para entregar um ativo de armazenamento criptografado, você deve configurar a política de entrega do ativo para que os Serviços de Mídia saibam como você deseja entregar seu conteúdo. Antes que seu ativo possa ser transmitido por streaming, o servidor de streaming descriptografa e faz streaming do conteúdo utilizando a política de entrega especificada (por exemplo, AES, criptografia comum ou sem criptografia).

## <a name="types-of-encryption"></a>Tipos de criptografia
O PlayReady e Widevine utilizam criptografia comum (modo AES CTR). O FairPlay utiliza a criptografia de modo AES CBC. A criptografia de chave não criptografada AES-128 utiliza criptografia de envelope.

## <a name="licenses-and-keys-delivery-service"></a>Serviço de entrega de licenças e chaves
Os Serviços de Mídia fornecem um serviço de entrega de chaves para entrega de licenças DRM (PlayReady, Widevine, FairPlay) e chaves AES para clientes autorizados. Você pode usar o [Portal do Azure](media-services-portal-protect-content.md), a API REST ou o SDK dos Serviços de Mídia para .NET para configurar políticas de autenticação e autorização para suas licenças e chaves.

## <a name="control-content-access"></a>Controlar o acesso de conteúdo
É possível controlar quem tem acesso ao seu conteúdo, configurando a política de autorização de chave de conteúdo. A política de autorização de chave de conteúdo fornece suporte para restrição de token ou aberta.

### <a name="open-authorization"></a>Autorização aberta
Com uma política de autorização aberta, a chave de conteúdo é enviada para qualquer cliente (sem restrição).

### <a name="token-authorization"></a>Autorização de token
Com uma política de autorização restrita por token, a chave de conteúdo somente será enviada a um cliente que tenha um JWT (Token Web JSON) ou SWT (Token Web Simples) válido na solicitação de licença/chave. Esse token deve ser emitido por um STS (serviço de token de segurança). É possível usar o Azure Active Directory como um STS ou implantar um STS personalizado. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. O serviço de entrega de chaves dos Serviços de Mídia retornará a licença/chave solicitada ao cliente se o token for válido e as declarações no token corresponderem àquelas configuradas para a licença/chave.

Ao configurar a política restrita do token, você deve especificar os parâmetros de chave de verificação primária, emissor e público. A chave de verificação primária contém a chave com a qual o token foi assinado. O emissor é o serviço de token seguro que emite o token. O público, às vezes chamado de escopo, descreve a intenção do token ou do recurso ao qual o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

## <a name="streaming-urls"></a>URLs de streaming
Se o ativo foi criptografado com mais de um DRM, use uma marcação de criptografia na URL de streaming: (formato='m3u8-aapl' criptografia='xxx').

As seguintes considerações se aplicam:

* Não é possível especificar mais do que um tipo de criptografia.
* O tipo de criptografia não precisa ser especificado na URL se apenas uma criptografia foi aplicada no ativo.
* O tipo de criptografia diferencia as letras maiúsculas de minúsculas.
* Os seguintes tipos de criptografia podem ser especificados:
  * **cenc**: Para PlayReady ou Widevine (criptografia comum)
  * **cbcs-aapl**: Para FairPlay (criptografia AES CBC)
  * **cbc**: Para criptografia de envelope AES

## <a name="next-steps"></a>Próximas etapas
Os artigos a seguir descrevem as próximas etapas para ajudar na introdução à proteção de conteúdo:

* [Proteger com criptografia de armazenamento](media-services-rest-storage-encryption.md)
* [Proteger com criptografia AES](media-services-protect-with-aes128.md)
* [Proteger com PlayReady e/ou Widevine](media-services-protect-with-playready-widevine.md)
* [Proteger com FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Links relacionados

* [Autenticação do token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrar o aplicativo OWIN com base no MVC dos Serviços de Mídia do Azure com o Azure Active Directory e restringir a entrega da chave de conteúdo restrito com base em declarações JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
