---
title: Usando o castLabs para fornecer licenças Widevine aos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo descreve como é possível usar os serviços de mídia do Azure (AMS) para oferecer um fluxo dinamicamente é criptografado pelo AMS com os DRMs do PlayReady e Widevine. A licença do PlayReady vem do servidor de licenças do PlayReady dos serviços de mídia e a licença do Widevine é fornecida pelo servidor de licenças do castLabs.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: Mingfeiy;willzhan;Juliako
ms.openlocfilehash: dfb82e91b0f65b85d34b7e20d57ed9929469321f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61232556"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Usando o castLabs para fornecer licenças Widevine para os Serviços de Mídia do Azure 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Visão geral

Este artigo descreve como é possível usar os serviços de mídia do Azure (AMS) para oferecer um fluxo dinamicamente é criptografado pelo AMS com os DRMs do PlayReady e Widevine. A licença do PlayReady vem do servidor de licenças do PlayReady dos serviços de mídia e a licença do Widevine é fornecida pelo servidor de licenças do **castLabs** .

Para reproduzir o conteúdo de streaming protegido por CENC (PlayReady e/ou Widevine), use o [Player de Mídia do Azure](https://amsplayer.azurewebsites.net/azuremediaplayer.html). Consulte o [documento sobre AMP](https://amp.azure.net/libs/amp/latest/docs/) para obter detalhes.

O diagrama a seguir demonstra uma arquitetura de integração de alto nível dos serviços de mídia do Azure e do castLabs.

![integração](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Configuração típica do sistema

* O conteúdo de mídia é armazenado em AMS.
* As IDs de chave de chaves de conteúdo são armazenados em castLabs e AMS.
* O castLabs e o AMS têm autenticação de token interna. As seções a seguir discutem os tokens de autenticação. 
* Quando um cliente solicita o fluxo do vídeo, o conteúdo é criptografado dinamicamente com **Criptografia comum** (CENC) e dinamicamente fornecido pelo AMS para DASH e Smooth Streaming. Também fornecemos criptografia de fluxo elementar PlayReady M2TS para protocolo de streaming do HLS.
* A licença do PlayReady é recuperada do servidor de licença do AMS e a licença Widevine é recuperada do servidor de licenças castLabs. 
* O Player de Mídia decide automaticamente quais licenças buscar com base na capacidade de plataforma do cliente. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Geração de token de autenticação para obter uma licença

O castLabs e o AMS oferecem suporte ao formato de token JWT (JSON Web Token) usado para autorizar uma licença. 

### <a name="jwt-token-in-ams"></a>Token JWT no AMS

A tabela a seguir descreve o token JWT no AMS. 

| Emissor | Cadeia de caracteres do emissor do Secure Token Service (STS) escolhido |
| --- | --- |
| Público-alvo |Cadeia de caracteres do público-alvo do STS usado |
| Declarações |Um conjunto de declarações |
| NotBefore |Validade inicial do token |
| Expira |Validade final do token |
| SigningCredentials |A chave que é compartilhada entre o servidor de licenças do PlayReady, servidor de licenças do castLabs e STS, pode ser simétrica ou assimétrica. |

### <a name="jwt-token-in-castlabs"></a>Token JWT no castLabs

A tabela a seguir descreve o token JWT no castLabs. 

| NOME | DESCRIÇÃO |
| --- | --- |
| optData |Uma cadeia de caracteres JSON que contém informações sobre você. |
| crt |Uma cadeia de caracteres JSON que contém informações sobre o ativo, suas informações de licença e seus direitos de reprodução. |
| iat |A data e horário correntes na época. |
| jti |Um identificador exclusivo sobre esse token (cada token pode ser usado apenas uma vez no sistema do castLabs). |

## <a name="sample-solution-setup"></a>Configuração da solução de exemplo

A [solução de exemplo](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) consiste em dois projetos:

* Um aplicativo de console que pode ser usado para definir as restrições de DRM em um ativo já incluído para o PlayReady e o Widevine.
* Um aplicativo da Web que distribui os tokens, que poderia ser visto como uma versão MUITO SIMPLIFICADA de um STS.

Para usar o aplicativo de console.

1. Altere o app.config para configurar credenciais AMS, credenciais do castLabs, configuração de STS e chave compartilhada.
2. Carregue um ativo no AMS.
3. Obtenha o UUID do ativo carregado e altere a linha 32 no arquivo Program.cs:
   
      var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();
4. Use um AssetId para nomear o ativo no sistema do castLabs (linha 44 no arquivo Program.cs).
   
   Você deve definir o AssetId para o **castLabs**; ele precisa ser uma sequência alfanumérica e exclusiva.
5. Execute o programa.

Para usar o aplicativo Web (STS):

1. Altere o web.config para configurar o ID do comerciante do castlabs, a configuração de STS e a chave compartilhada.
2. Implante os Sites do Azure.
3. Navegue até o site.

## <a name="playing-back-a-video"></a>Reproduzir um vídeo

Para reproduzir um vídeo criptografado com uma criptografia comum (PlayReady e/ou Widevine), você pode usar o [Player de Mídia do Azure](https://amsplayer.azurewebsites.net/azuremediaplayer.html). Ao executar o aplicativo de console, a ID de chave de conteúdo e a URL de manifesto são exibidos.

1. Abra uma nova guia e inicie seu STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Vá para [Player de Mídia do Azure](https://amsplayer.azurewebsites.net/azuremediaplayer.html).
3. Cole na URL de streaming.
4. Clique na caixa de seleção **Opções avançadas** .
5. Selecione PlayReady e/ou Widevine na lista suspensa **Proteção** .
6. Cole o token que você obteve no seu STS na caixa de texto do token. 
   
   O servidor de licença do castLab não precisa do prefixo "Portador =" na frente do token. Nesse caso, remova-o antes de enviar o token.
7. Atualize o player.
8. O vídeo deve estar em reprodução.

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

