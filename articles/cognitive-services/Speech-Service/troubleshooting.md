---
title: Solucionar problemas do SDK de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: Este artigo traz informações para ajudar você a resolver possíveis problemas ao usar o SDK de Fala.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 3beda852b3e711ee969b7f80f8f326285cc2cad6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558892"
---
# <a name="troubleshoot-the-speech-sdk"></a>Solucionar Problemas do SDK de Fala

Este artigo traz informações para ajudar você a resolver possíveis problemas ao usar o SDK de Fala.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Erro: falha na atualização de WebSocket com um erro de autenticação (403)

Você pode ter o ponto de extremidade incorreto para a região ou o serviço. Verifique se o URI está correto.

Além disso, pode haver um problema com a chave de assinatura ou o token de autorização. Para obter mais informações, confira a próxima seção.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Erro: HTTP 403 Proibido ou HTTP 401 Não Autorizado

Esse erro geralmente é causado por problemas de autenticação. As solicitações de conexão sem um cabeçalho `Ocp-Apim-Subscription-Key` ou `Authorization` válido são rejeitadas com o status 403 ou 401.

* Se estiver usando uma chave de assinatura para autenticação, você poderá ver o erro porque:

    - A chave de assinatura está ausente ou é inválida
    - Você excedeu a cota de uso da assinatura

* Se estiver usando um token de autorização para autenticação, você poderá ver o erro porque:

    - O token de autorização é inválido
    - O token de autorização expirou

### <a name="validate-your-subscription-key"></a>Validar a chave de assinatura

Você pode verificar se tem uma chave de assinatura válida executando um dos comandos a seguir.

> [!NOTE]
> Substitua `YOUR_SUBSCRIPTION_KEY` e `YOUR_REGION` por sua própria chave de assinatura e a região associada.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

Se você inseriu uma chave de assinatura válida, o comando retorna um token de autorização, caso contrário, um erro será retornado.

### <a name="validate-an-authorization-token"></a>Validar um token de autorização

Se você usar um token de autorização para autenticação, execute um dos comandos a seguir para verificar se o token de autorização ainda é válido. Os tokens são válidos por 10 minutos.

> [!NOTE]
> Substitua `YOUR_AUDIO_FILE` pelo caminho para o arquivo de áudio pré-gravado. Substitua `YOUR_ACCESS_TOKEN` pelo token de autorização retornado na etapa anterior. Substitua `YOUR_REGION` pela região correta.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'

    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

Se você inseriu um token de autorização válido, o comando retorna a transcrição para o seu arquivo de áudio, caso contrário, um erro será retornado.

---

## <a name="error-http-400-bad-request"></a>Erro: HTTP 400 (Solicitação Incorreta)

Esse erro normalmente ocorre quando o corpo da solicitação contém dados de áudio inválidos. Só há suporte para o formato WAV. Além disso, verifique os cabeçalhos da solicitação para garantir que você especificou os valores apropriados para `Content-Type` e `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Erro: HTTP 408 Tempo Limite de Solicitação

O erro provavelmente ocorre porque nenhum dado de áudio está sendo enviado para o serviço. Esse erro também pode ser causado por problemas de rede.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>"RecognitionStatus" na resposta é "InitialSilenceTimeout"

Geralmente, esse problema é causado por dados de áudio. Você poderá ver esse erro porque:

* Há um longo trecho de silêncio no início do áudio. Nesse caso, o serviço interrompe o reconhecimento após alguns segundos e retorna `InitialSilenceTimeout`.

* O áudio usa um formato de codec sem suporte, fazendo com que os dados de áudio sejam tratados como silêncio.

## <a name="next-steps"></a>Próximas etapas

* [Examinar as notas de versão](releasenotes.md)
