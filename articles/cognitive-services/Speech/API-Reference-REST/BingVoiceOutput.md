---
title: API de Conversão de Texto em Fala do Serviço de Fala da Microsoft | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use a API de conversão de texto em fala para proporcionar a conversão de texto em fala em tempo real em uma variedade de vozes e idiomas
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: priyar
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a046bec5d81d828d88716d31c84e9cbcdcea1a08
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515432"
---
# <a name="bing-text-to-speech-api"></a>API de conversão de texto em fala do Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>Introdução

Com a API de conversão de texto em fala no Bing, seu aplicativo pode enviar solicitações HTTP para um servidor de nuvem, onde o texto é instantaneamente sintetizado em uma fala que soa como humana e é retornada como um arquivo de áudio. Esta API pode ser usada em vários contextos diferentes para fornecer conversão de texto em fala em tempo real em uma variedade de vozes e idiomas diferentes.

## <a name="VoiceSynReq"></a>Solicitação de síntese de voz

### <a name="Subscription"></a>Token de autorização

Cada solicitação de síntese de voz requer um token de acesso do JWT (Token Web JSON). O token de acesso do JWT é passado no cabeçalho da solicitação de fala. O token tem um prazo de expiração de 10 minutos. Para obter informações sobre como se inscrever e obter chaves de API que são usadas para recuperar os tokens de acesso válidos do JWT, consulte [Assinatura de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/).

A chave de API é passada para o serviço de token. Por exemplo:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

As informações de cabeçalho necessárias para o token de acesso são as seguintes.

NOME| Formatar | DESCRIÇÃO
----|----|----
Ocp-Apim-Subscription-Key | ASCII | Sua chave de assinatura

O serviço de token retorna o token de acesso do JWT como `text/plain`. Em seguida, o JWT é passado como `Base64 access_token` para o ponto de extremidade de fala como um cabeçalho de autorização prefixado com a cadeia de caracteres `Bearer`. Por exemplo:

`Authorization: Bearer [Base64 access_token]`

Os clientes devem usar o ponto de extremidade a seguir para acessar o serviço de conversão de texto em fala:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Até que você tenha adquirido um token de acesso com sua chave de assinatura conforme descrito anteriormente, esse link gera um erro de resposta `403 Forbidden`.

### <a name="Http"></a>Cabeçalhos HTTP

A tabela a seguir mostra os cabeçalhos HTTP que são usados para solicitações de síntese de voz.

Cabeçalho |Value |Comentários
----|----|----
Content-Type | application/ssml+xml | O tipo do conteúdo de entrada.
X-Microsoft-OutputFormat | **1.** ssml-16khz-16bit-mono-tts <br> **2.** raw-16khz-16bit-mono-pcm <br>**3.** audio-16khz-16kbps-mono-siren <br> **4.** riff-16khz-16kbps-mono-siren <br> **5.** riff-16khz-16bit-mono-pcm <br> **6.** audio-16khz-128kbitrate-mono-mp3 <br> **7.** audio-16khz-64kbitrate-mono-mp3 <br> **8.** audio-16khz-32kbitrate-mono-mp3 | O formato de áudio de saída.
X-Search-AppId | Um GUID (somente hexadecimal, sem traços) | Uma ID que identifica exclusivamente o aplicativo cliente. Pode ser a ID de armazenamento de aplicativos. Se uma não estiver disponível, a ID pode ser gerada pelo usuário para um aplicativo.
X-Search-ClientID | Um GUID (somente hexadecimal, sem traços) | Uma ID que identifica exclusivamente uma instância de aplicativo para cada instalação.
User-Agent | Nome do aplicativo | O nome do aplicativo é necessário e deve ter menos de 255 caracteres.
Authorization | Token de autorização |  Veja a seção <a href="#Subscription">Token de autorização</a>.

### <a name="InputParam"></a>Parâmetros de entrada

As solicitações à API de conversão de texto em fala do Bing são feitas usando chamadas HTTP POST. Os cabeçalhos são especificados na seção anterior. O corpo contém uma entrada de SSML (Linguagem de marcação de síntese de fala) que representa o texto a ser sintetizado. Para obter uma descrição da marcação usada para controlar aspectos de fala, como o idioma e o gênero de do falante, consulte a [Especificação W3C da SSML](https://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>O tamanho máximo da entrada da SSML com suporte é 1.024 caracteres, incluindo todas as marcas.

###  <a name="SampleVoiceOR"></a>Exemplo: solicitação de saída de voz

A seguir, há um exemplo de uma solicitação de saída de voz:

```HTTP
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: application/ssml+xml
Host: speech.platform.bing.com
Content-Length: 197
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```

## <a name="VoiceOutResponse"></a>Resposta da saída de voz

A API de conversão de texto em fala do Bing usa o HTTP POST para enviar o áudio de volta ao cliente. A resposta de API contém o fluxo de áudio e o codec e corresponde ao formato de saída solicitado. O áudio retornado para uma determinada solicitação não deve exceder 15 segundos.

### <a name="SuccessfulRecResponse"></a>Exemplo: resposta de síntese bem-sucedida

O código a seguir é um exemplo de uma resposta JSON para uma solicitação de síntese de voz bem-sucedida. Os comentários e a formatação do código são usados somente para fins deste exemplo e são omitidos da resposta real.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Exemplo: falha de síntese

O exemplo de código a seguir mostra uma resposta JSON a uma falha de consulta da síntese de voz:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Respostas de erro

Erro | DESCRIÇÃO
----|----
HTTP/400 Bad Request | Um parâmetro obrigatório está faltando, é vazio ou nulo, ou o valor passado como um parâmetro obrigatório ou opcional é inválido. Um motivo para obter a resposta “inválida” é passar um valor de cadeia de caracteres maior do que o comprimento permitido. Inclui uma breve descrição do parâmetro problemático.
HTTP/401 Unauthorized | A solicitação não foi autorizada.
HTTP/413 RequestEntityTooLarge  | A entrada da SSML é maior do que a que tem suporte.
HTTP/502 BadGateway | Há um problema relacionado à rede ou ao servidor.

Há um exemplo de uma resposta de erro a seguir:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Alterando a saída de voz via SSML

A API de conversão de texto em fala da Microsoft oferece suporte para SSML 1.0 conforme definido na [SSML (Linguagem de marcação de síntese de fala) versão 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/) do W3C. Esta seção mostra exemplos sobre como alterar certas características de saída de voz gerada como taxa de fala, pronúncia, etc., usando marcas de SSML.

1. Adicionando quebra

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
   ```

2. Alterar taxa de fala

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

3. Pronúncia

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
   ```

4. Alterar volume

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

5. Alterar densidade

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

6. Alterar contorno de prosódia

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
   ```

> [!NOTE]
> Observe que os dados de áudio devem ser WAV de 8 K ou 16 K arquivados no seguinte formato: **Código CRC** (CRC-32): 4 bytes (DWORD) com o intervalo válido 0x00000000 ~ 0xFFFFFFFF; **Sinalizador de formato de áudio**: 4 bytes (DWORD) com o intervalo válido 0x00000000 ~ 0xFFFFFFFF; **Contagem de exemplo**: 4 bytes (DWORD) com o intervalo válido 0x00000000 ~ 0x7FFFFFFF; **Tamanho do corpo binário**: 4 bytes (DWORD) com o intervalo válido 0x00000000 ~ 0x7FFFFFFF; **Corpo binário**: n bytes.

## <a name="SampleApp"></a>Aplicativo de exemplo

Para obter detalhes de implementação, consulte o [aplicativo de exemplo de conversão de texto em fala do Visual C#.NET](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Localidades com suporte e as fontes de voz

A tabela a seguir identifica algumas das localidades com suporte e fontes de voz relacionadas.

Local | Gênero | Mapeamento do nome do serviço
---------|--------|------------
ar-EG* | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ar-EG, Hoda)”
ar-SA | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ar-SA, Naayf)”
bg-BG | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (bg-BG, Ivan)”
ca-ES | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ca-ES, HerenaRUS)”
cs-CZ | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (cs-CZ, Jakub)”
da-DK | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (da-DK, HelleRUS)”
de-AT | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-AT, Michael)”
de-CH | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-CH, Karsten)”
de-DE | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, Hedda)”
de-DE | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, HeddaRUS)”
de-DE | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, Stefan, Apollo)”
el-GR | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (el-GR, Stefanos)”
en-AU | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-AU, Catherine)”
en-AU | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-AU, HayleyRUS)”
en-CA | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-CA, Linda)”
en-CA | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-CA, HeatherRUS)”
en-GB | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, Susan, Apollo)”
en-GB | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, HazelRUS)”
en-GB | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, George, Apollo)”
en-IE | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IE, Sean)”
en-IN | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, Heera, Apollo)”
en-IN | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, PriyaRUS)”
en-IN | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, Ravi, Apollo)”
en-US | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, ZiraRUS)”
en-US | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, JessaRUS)”
en-US | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, BenjaminRUS)”
es-ES | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, Laura, Apollo)”
es-ES | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, HelenaRUS)”
es-ES | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, Pablo, Apollo)”
es-MX | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-MX, HildaRUS)”
es-MX | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-MX, Raul, Apollo)”
fi-FI | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fi-FI, HeidiRUS)”
fr-CA | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CA, Caroline)”
fr-CA | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CA, HarmonieRUS)”
fr-CH | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CH, Guillaume)”
fr-FR | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, Julie, Apollo)”
fr-FR | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, HortenseRUS)”
fr-FR | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, Paul, Apollo)”
he-IL| Masculino| “Voz da Conversão de Texto em Fala do Microsoft Server Speech (he-IL, Asaf)”
hi-IN | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Kalpana, Apollo)”
hi-IN | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Kalpana)”
hi-IN | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Hemant)”
hr-HR | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hr-HR, Matej)”
hu-HU | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hu-HU, Szabolcs)”
id-ID | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (id-ID, Andika)”
it-IT | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (it-IT, Cosimo, Apollo)”
it-IT | Feminino | "Conversão de Texto em Fala do Microsoft Server (it-IT, LuciaRUS)"
ja-JP | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, Ayumi, Apollo)”
ja-JP | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, Ichiro, Apollo)”
ja-JP | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, HarukaRUS)”
ko-KR | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ko-KR, HeamiRUS)”
ms-MY | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ms-MY, Rizwan)”
nb-NO | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (nb-NO, HuldaRUS)”
nl-NL | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (nl-NL, HannaRUS)”
pl-PL | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pl-PL, PaulinaRUS)”
pt-BR | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-BR, HeloisaRUS)”
pt-BR | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-BR, Daniel, Apollo)”
pt-PT | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-PT, HeliaRUS)”
ro-RO | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ro-RO, Andrei)”
ru-RU | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ru-RU, Irina, Apollo)”
ru-RU | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ru-RU, Pavel, Apollo)”
ru-RU | Feminino | "Conversão de Texto em Fala do Microsoft Server (ru-RU, EkaterinaRUS)"
sk-SK | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sk-SK, Filip)”
sl-SI | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sl-SI, Lado)”
sv-SE | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sv-SE, HedvigRUS)”
ta-IN | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ta-IN, Valluvar)”
th-TH | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (th-TH, Pattara)”
tr-TR | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (tr-TR, SedaRUS)”
vi-VN | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (vi-VN, An)”
zh-CN | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, HuihuiRUS)”
zh-CN | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, Yaoyao, Apollo)”
zh-CN | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, Kangkang, Apollo)”
zh-HK | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, Tracy, Apollo)”
zh-HK | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, TracyRUS)”
zh-HK | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, Danny, Apollo)”
zh-TW | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, Yating, Apollo)”
zh-TW | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, HanHanRUS)”
zh-TW | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, Zhiwei, Apollo)”

 *ar-EG oferece suporte para MSA (árabe padrão moderno).

> [!NOTE]
> Observe que os nomes de serviço anteriores **Voz da Conversão de Texto em Fala do Microsoft Server Speech (cs-CZ, Vit)** e **Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IE Shaun)** serão substituídos após 31/03/2018 para otimizar os recursos da API de Fala do Bing. Atualize seu código com os nomes atualizados.

## <a name="TrouNSupport"></a>Solução de problemas e suporte

Poste todas as perguntas e problemas no fórum do MSDN do [Serviço da Fala do Bing](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService). Inclua detalhes completos, como:

* Um exemplo da cadeia de caracteres de solicitação completa.
* Se aplicável, a saída completa de uma solicitação com falha, o que inclui IDs do log.
* A porcentagem de solicitações com falha.
