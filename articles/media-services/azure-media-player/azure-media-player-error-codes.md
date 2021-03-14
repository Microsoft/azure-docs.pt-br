---
title: Player de Mídia do Azure códigos de erro
description: Uma referência de código de erro para Player de Mídia do Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 89f063ab50cfd880552e80a77ba798e4d06de5b3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709573"
---
# <a name="error-codes"></a>Códigos do Erro #

Quando a reprodução não puder ser iniciada ou interrompida, um evento de erro será acionado e a `error()` função retornará um código e uma mensagem opcional para ajudar o desenvolvedor do aplicativo a obter mais detalhes. `error().message` Não é a mensagem exibida para o usuário.  A mensagem exibida para o usuário é baseada em `error().code` bits 27-20, consulte a tabela abaixo.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Códigos de erro, bits [31-28] (4 bits) ##

Descreva a área do erro.

- 0 – desconhecido
- 1-AMP
- 2-AzureHtml5JS
- 3-relâmpago
- 4-silverlightss
- 5-HTML5
- 6-Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Códigos de erro, bits [27-0] (28 bits) ##

Descreva os detalhes do erro, o bits 27-20 fornece um alto nível, os bits 19-0 fornecem mais detalhes, se disponíveis.


| amp. errorCode. nomes | Códigos, bits [27-0] (28 bits) | Descrição |
|---|---:|---|
| **Intervalo de erros de MEDIA_ERR_ABORTED (0x0100000-0x01FFFFF)** | | |
| abortedErrUnknown | 0x0100000 | Erro de anulação genérica |
| abortedErrNotImplemented | 0x0100001 | Anular erro, não implementado |
| abortedErrHttpMixedContentBlocked | 0x0100002 | Erro de anulação, conteúdo misto bloqueado – geralmente ocorre ao carregar um `http://` fluxo de uma `https://` página |
| **MEDIA_ERR_NETWORK valor inicial de erros (0x0200000-0x02FFFFF)** | | |
| networkErrUnknown | 0x0200000 | Erro de rede genérica |
| networkErrHttpBadUrlFormat | 0x0200190 | Resposta de erro http 400 |
| networkErrHttpUserAuthRequired | 0x0200191 | Resposta de erro http 401 |
| networkErrHttpUserForbidden | 0x0200193 | Resposta de erro HTTP 403 |
| networkErrHttpUrlNotFound | 0x0200194 | Resposta de erro http 404 |
| networkErrHttpNotAllowed | 0x0200195 | Resposta de erro http 405 |
| networkErrHttpGone | 0x020019A | Resposta de erro http 410 |
| networkErrHttpPreconditionFailed | 0x020019C | Resposta de erro http 412 |
| networkErrHttpInternalServerFailure | 0x02001F4 | Resposta de erro http 500
| networkErrHttpBadGateway | 0x02001F6 | Resposta de erro http 502 |
| networkErrHttpServiceUnavailable | 0x02001F7 | Resposta de erro HTTP 503 |
| networkErrHttpGatewayTimeout | 0x02001F8 | Resposta de erro http 504 |
| networkErrTimeout | 0x0200258 | Erro de tempo limite de rede
| networkErrErr | 0x0200259 | Resposta de erro de conexão de rede |
| **Erros de MEDIA_ERR_DECODE (0x0300000-0x03FFFFF)** | | |
| decodeErrUnknown | 0x0300000 | Erro de decodificação genérico |
| **Erros de MEDIA_ERR_SRC_NOT_SUPPORTED (0x0400000-0x04FFFFF)** | | |
| srcErrUnknown | 0x0400000 | Origem genérica sem suporte erro |
| srcErrParsePresentation | 0x0400001 | Erro de análise de apresentação |
| srcErrParseSegment | 0x0400002 | Erro de análise de segmento |
| srcErrUnsupportedPresentation | 0x0400003 | Apresentação sem suporte |
| srcErrInvalidSegment | 0x0400004 | Segmento inválido |
| **MEDIA_ERR_ENCRYPTED valor inicial de erros (0x0500000-0x05FFFFF)** | | |
| encryptErrUnknown | 0x0500000 | Erro genérico criptografado | 
| encryptErrDecrypterNotFound | 0x0500001 | Decrypter não encontrado |
| encryptErrDecrypterInit | 0x0500002 | Erro de inicialização de Decrypter |
| encryptErrDecrypterNotSupported | 0x0500003 | Decrypter não tem suporte |
| encryptErrKeyAcquire | 0x0500004 | Falha na aquisição de chave |
| encryptErrDecryption | 0x0500005 | Falha na descriptografia do segmento |
| encryptErrLicenseAcquire | 0x0500006 | Falha na aquisição da licença |
| **SRC_PLAYER_MISMATCH valor inicial de erros (0x0600000-0x06FFFFF)** | | |
| srcPlayerMismatchUnknown | 0x0600000 | O genérico não corresponde ao Tech Player para reproduzir a origem |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |O plug-in flash não está instalado, se instalado, a origem pode ser reproduzida. *Ou* O flash 30 está instalado e reproduzindo conteúdo AES.  Se esse for o caso, tente um navegador diferente. O flash 30 não tem suporte hoje a partir de 7 de junho. Consulte [problemas conhecidos](azure-media-player-known-issues.md) para obter mais detalhes. Observação: se 0x00600003, o flash e o Silverlight não estão instalados, se especificado no techOrder.|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | O plug-in do Silverlight não está instalado, se instalado, a origem pode ser reproduzida. Observação: se 0x00600003, o flash e o Silverlight não estão instalados, se especificado no techOrder. |
| | 0x00600003 | O flash e o Silverlight não estão instalados, se especificado no techOrder. |
| **Erros desconhecidos (0x0FF00000)** | | |
| errUnknown | 0xFF00000 | Erros desconhecidos |

## <a name="user-error-messages"></a>Mensagens de erro do usuário ##

A mensagem de usuário exibida é baseada nos bits 27-20 do código de erro.

- MEDIA_ERR_ABORTED (1)-"você anulou a reprodução do vídeo"
- MEDIA_ERR_NETWORK (2)-"um erro de rede fez com que o download do vídeo falhasse no sentido de parte".
- MEDIA_ERR_DECODE (3)-"a reprodução do vídeo foi anulada devido a um problema de corrupção ou porque os recursos usados pelo vídeo para o qual o seu navegador não dava suporte".
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"não foi possível carregar o vídeo porque o servidor ou a rede falhou ou porque não há suporte para o formato."
- MEDIA_ERR_ENCRYPTED (5)-"o vídeo está criptografado e não temos as chaves para descriptografá-lo".
- SRC_PLAYER_MISMATCH (6)-"nenhuma fonte compatível foi encontrada para este vídeo."
- MEDIA_ERR_UNKNOWN (0xFF)-"ocorreu um erro desconhecido."

## <a name="examples"></a>Exemplos ##

### <a name="0x10600001"></a>0x10600001 ##

"Nenhuma fonte compatível foi encontrada para este vídeo." é exibido para o usuário final.

Não há um Tech Player que possa reproduzir as fontes solicitadas, mas se o plug-in flash estiver instalado, é provável que uma fonte possa ser reproduzida.  

### <a name="0x20200194"></a>0x20200194 ###

"Um erro de rede fez com que o download do vídeo falhasse como parte." é exibido para o usuário final.

AzureHtml5JS falhou ao reproduzir de uma resposta http 404.

### <a name="categorizing-errors"></a>Categorizando erros ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>Capturando um erro específico ###

O código a seguir captura apenas 404 erros:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)