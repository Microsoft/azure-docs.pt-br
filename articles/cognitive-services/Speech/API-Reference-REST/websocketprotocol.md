---
title: Protocolo WebSocket da Fala do Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Documentação do protocolo da Fala do Bing com base em WebSockets
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d6601f57d87b518b2061df64174818432b822755
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515333"
---
# <a name="bing-speech-websocket-protocol"></a>Protocolo WebSocket da Fala do Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A Fala do Bing é uma plataforma baseada em nuvem que apresenta os algoritmos mais avançados disponíveis para converter áudio falado em texto. O protocolo da Fala do Bing define a [configuração da conexão](#connection-establishment) entre aplicativos clientes e o serviço e as mensagens de reconhecimento de fala trocadas entre as partes ([mensagens originadas do cliente](#client-originated-messages) e [mensagens originadas do serviço](#service-originated-messages)). Além disso, [mensagens de telemetria](#telemetry-schema) e [tratamento de erros](#error-handling) são descritas.

## <a name="connection-establishment"></a>Estabelecimento de conexão

O protocolo do Serviço de Fala segue a especificação de padrão WebSocket [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). Uma conexão WebSocket começa com uma solicitação HTTP que contém os cabeçalhos HTTP que indicam que o desejo do cliente para atualizar a conexão para um WebSocket em vez de usar a semântica HTTP. O servidor indica o desejo de participar na conexão WebSocket, retornando um HTTP `101 Switching Protocols` resposta. Após a troca desse handshake, cliente e o serviço mantém o soquete aberto e começam a usar um protocolo baseado em mensagem para enviar e receber informações.

Para iniciar o handshake do WebSocket, o aplicativo cliente envia uma solicitação GET de HTTPS para o serviço. Isso inclui os cabeçalhos de atualização de WebSocket padrão junto com outros cabeçalhos que são específicos para fala.

```HTTP
GET /speech/recognition/interactive/cognitiveservices/v1 HTTP/1.1
Host: speech.platform.bing.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Authorization: t=EwCIAgALBAAUWkziSCJKS1VkhugDegv7L0eAAJqBYKKTzpPZOeGk7RfZmdBhYY28jl&p=
X-ConnectionId: A140CAF92F71469FA41C72C7B5849253
Origin: https://speech.platform.bing.com
```

O serviço responde com:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Todas as solicitações de fala requerem a criptografia [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security). Não há suporte para o uso de solicitações de fala não criptografada. Há suporte para a seguinte versão TLS:

* TLS 1.2

### <a name="connection-identifier"></a>Identificador de Conexão

O aerviço de fala requer que todos os clientes incluem uma ID exclusiva para identificar a conexão. Os clientes *devem* incluir o cabeçalho *X ConnectionId* quando forem iniciados um handshake WebSocket. O cabeçalho *X ConnectionId* deve ser um [valor identificador universalmente exclusivo](https://en.wikipedia.org/wiki/Universally_unique_identifier) (UUID). Solicitações de atualização de WebSocket que não incluem o *X ConnectionId*, não especificam um valor para o cabeçalho *X ConnectionId*, ou não incluem uma opção válida o valor UUID são rejeitados pelo serviço com uma resposta `400 Bad Request` HTTP.

### <a name="authorization"></a>Autorização

Além dos cabeçalhos padrão de handshake do WebSocket, as solicitações de fala exigem um cabeçalho de *autorização*. As solicitações de conexão sem esse cabeçalho são rejeitadas pelo serviço com uma resposta HTTP `403 Forbidden`.

O cabeçalho de *autorização* deve conter um token de acesso do JSON Web Token (JWT).

Para obter informações sobre como se inscrever e obter chaves de API que são usadas para recuperar os tokens de acesso JWT válidos, consulte a página [Assinatura serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/).

A chave de API é passada para o serviço de token. Por exemplo:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

As seguintes informações de cabeçalho são necessárias para o token de acesso.

| NOME | Formatar | DESCRIÇÃO |
|----|----|----|
| Ocp-Apim-Subscription-Key | ASCII | Sua chave de assinatura |

O serviço de token retorna o token de acesso do JWT como `text/plain`. Em seguida, o JWT é passado como um `Base64 access_token` para o handshake como um cabeçalho de *Autorização* prefixado com a cadeia de caracteres `Bearer`. Por exemplo:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Cookies

Os clientes *devem* ter suporte a cookies HTTP como especificado em [RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>Redirecionamento de Http

Os clientes *devem* oferecer suporte os mecanismos de redirecionamento padrão especificados pela [especificação do protocolo HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Pontos de Extremidade SSH

Os clientes *devem* usar um ponto de extremidade apropriado do serviço de fala. O ponto de extremidade é baseado no modo de reconhecimento e idioma. A tabela abaixo mostra alguns exemplos.

| Modo | Path | URI de serviço |
| -----|-----|-----|
| Interativo | /speech/recognition/interactive/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Conversação | /speech/recognition/conversation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Ditado | /speech/recognition/dictation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Para saber mais, confira a [URI de serviço](../GetStarted/GetStartedREST.md#service-uri).

### <a name="report-connection-problems"></a>Relatar problemas de conexão

Os clientes devem relatar imediatamente todos os problemas encontrados ao fazer uma conexão. O protocolo de mensagem para conexões com falha de geração de relatórios é descrito em [telemetria de falha de Conexão](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Limitações de duração da conexão

Quando comparados a conexões de HTTP do serviço web típico, conexões WebSocket duram um *longo* tempo. O Serviço de Fala impõe limitações na duração das conexões WebSocket para o serviço:

 * A duração máxima para qualquer conexão WebSocket ativa é 10 minutos. Uma conexão está ativa, se o serviço ou o cliente envia as mensagens do WebSocket por essa conexão. O serviço encerra a conexão sem aviso quando o limite for atingido. Os clientes devem desenvolver cenários de usuário que não exigem que a conexão permaneça ativa ou próxima do tempo de vida útil máximo de conexão.

 * A duração máxima para qualquer conexão WebSocket inativa é de 180 segundos. Uma conexão está inativa se o serviço, nem o cliente enviou uma mensagem do WebSocket sobre a conexão. Depois que o tempo de vida máximo inativo for atingido, o serviço encerra a conexão WebSocket inativa.

## <a name="message-types"></a>Tipos de Mensagem

Depois que uma conexão WebSocket é estabelecida entre o cliente e o serviço, o cliente e o serviço podem enviar mensagens. Esta seção descreve o formato dessas mensagens WebSocket.

O [IETF RFC 6455](https://tools.ietf.org/html/rfc6455) especifica que as mensagens do WebSocket podem transmitir dados usando uma codificação binária ou um texto. As duas codificações usam formatos diferentes durante a transmissão. Cada formato é otimizado para codificação eficiente, transmissão e decodificação do conteúdo da mensagem.

### <a name="text-websocket-messages"></a>Mensagens de WebSocket de texto

As mensagens WebSocket de texto executam conteúdo de informações textuais que consiste em uma seção de cabeçalhos e um corpo separado por par de nova linha de retorno de carro duplo familiar usado para mensagens HTTP. E, como mensagens HTTP, as mensagens WebSocket especificam cabeçalhos no formato *nome: valor*  separado por um par de nova linha de retorno de carro único. Qualquer texto incluído em uma mensagem de texto WebSocket *deve* usar a codificação [UTF-8](https://tools.ietf.org/html/rfc3629).

As mensagens WebSocket de texto devem especificar um caminho de mensagem no cabeçalho *Caminho*. O valor deste cabeçalho deve ser um dos tipos de mensagem de protocolo de fala definidos neste documento.

### <a name="binary-websocket-messages"></a>Mensagens WebSocket binárias

As mensagens WebSocket binárias carregam um conteúdo binário. No protocolo do Serviço de Fala, áudio é transmitido e recebido do serviço por meio de mensagens binárias do WebSocket. Todas as outras mensagens são mensagens do de texto do WebSocket.

Como as mensagens de texto do WebSocket, as mensagens binárias do WebSocket consistem em um cabeçalho e uma seção de corpo. Os 2 primeiros bytes da especificação de mensagem binária do WebSocket, em ordem [big-endian](https://en.wikipedia.org/wiki/Endianness), o tamanho de número inteiro de 16 bits da seção de cabeçalho. O tamanho da seção de cabeçalho mínimo é 0 bytes. O tamanho máximo é 8.192 bytes. O texto nos cabeçalhos de mensagens do WebSocket binários *deve* usar a codificação [US-ASCII](https://tools.ietf.org/html/rfc20).

Cabeçalhos em uma mensagem do WebSocket binários são codificados no mesmo formato, como mensagens de WebSocket do texto. O formato *nome: valor* é separado por um par de nova linha de retorno de carro único. As mensagens WebSocket de texto devem especificar um caminho de mensagem no cabeçalho *Caminho*. O valor deste cabeçalho deve ser um dos tipos de mensagem de protocolo de fala definidos neste documento.

As mensagens de texto e binárias WebSocket são usadas no protocolo de serviço de fala.

## <a name="client-originated-messages"></a>Mensagens de origem de cliente

Depois que a conexão é estabelecida, o cliente e o serviço podem começar a enviar mensagens. Esta seção descreve o formato e o conteúdo de mensagens que os aplicativos cliente enviam ao serviço de fala. A seção [Mensagens originadas do serviço](#service-originated-messages) apresenta as mensagens que se originam no serviço de fala e são enviadas para os aplicativos cliente.

As principais mensagens enviadas pelo cliente para os serviços são `speech.config`, `audio`, e `telemetry` mensagens. Antes de considerarmos cada mensagem detalhadamente, os cabeçalhos de mensagem necessários comuns para todas essas mensagens são descritos.

### <a name="required-message-headers"></a>Cabeçalhos de mensagem necessários

Os cabeçalhos a seguir são necessários para todas as mensagens de origem de cliente.

| Cabeçalho | Value |
|----|----|
| Path | Caminho da mensagem conforme especificado neste documento |
| X-RequestId | UUID no formato de "no-dash" |
| X-Timestamp | Carimbo de hora do relógio cliente UTC no formato ISO 8601 |

#### <a name="x-requestid-header"></a>Cabeçalho X-RequestId

Solicitações de origem de cliente são identificadas exclusivamente pelo cabeçalho da mensagem *X RequestId*. Este cabeçalho é necessário para todas as mensagens de origem de cliente. O valor de cabeçalho *X RequestId* deve ser um UUID em forma de "não-traço", por exemplo, *123e4567e89b12d3a456426655440000*. Não *pode* estar na forma canônica *123e4567-e89b-12d3-a456-426655440000*. As solicitações sem um cabeçalho *X RequestId* ou com um valor de cabeçalho que usa um formato incorreto para UUIDs faz com que o serviço encerre a conexão WebSocket.

#### <a name="x-timestamp-header"></a>Cabeçalho X-Timestamp

Cada mensagem enviada ao serviço de fala por um aplicativo cliente *deve* incluir um cabeçalho *X Timestamp*. O valor para esse cabeçalho é o tempo quando o cliente envia a mensagem. As solicitações sem um cabeçalho *X RequestId* ou com um valor de cabeçalho que usa um formato incorreto para UUIDs faz com que o serviço encerre a conexão WebSocket.

O valor do cabeçalho *X Timestamp* deve estar no formato 'aaaa'-'MM'-'dd ' T'HH ': 'mm':'ss '.' fffffffZ' onde 'fffffff' é uma fração de segundo. Por exemplo, '12,5' significa ' 12 + 5/10 segundos e '12.526' significa ' 12 mais 526/1000 segundos'. Esse formato está em conformidade com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e, ao contrário do cabeçalho HTTP padrão *Data*, ele pode fornecer a resolução de milissegundo. Aplicativos cliente podem arredondar carimbos de data / hora para o milissegundo mais próximo. Aplicativos cliente precisam garantir que o relógio do dispositivo com precisão controla o tempo usando um [servidor Network Time Protocol (NTP)](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>Mensagem `speech.config`

O Serviço de Fala precisa saber as características de seu aplicativo para fornecer o melhor reconhecimento de fala possível. Os dados de características necessárias incluem informações sobre o dispositivo e o sistema operacional que habilita o seu aplicativo. Forneça essas informações na `speech.config` mensagem.

Os clientes *devem* enviar uma `speech.config` mensagem imediatamente depois de estabelecer a conexão ao serviço de fala e antes de enviar qualquer `audio` mensagem. Você precisa enviar uma `speech.config` mensagem apenas uma vez por conexão.

| Campo | DESCRIÇÃO |
|----|----|
| Codificação de mensagem WebSocket | Text |
| Body | O conteúdo útil como uma estrutura JSON |

#### <a name="required-message-headers"></a>Cabeçalhos de mensagem necessários

| Nome do cabeçalho | Value |
|----|----|
| Path | `speech.config` |
| X-Timestamp | Carimbo de hora do relógio cliente UTC no formato ISO 8601 |
| Content-Type | application/json; charset=utf-8 |

Assim como acontece com todas as mensagens originadas pelo cliente no protocolo de Serviço de Fala, a `speech.config` mensagem *deve* incluir um cabeçalho *X-Timestamp* que registra a hora do relógio cliente UTC quando a mensagem for enviada para o serviço. A `speech.config` mensagem *não* exigem um cabeçalho *X RequestId* porque esta mensagem não está associada uma solicitação de fala em particular.

#### <a name="message-payload"></a>Conteúdo da mensagem
O conteúdo da `speech.config` mensagem é uma estrutura JSON que contém informações sobre o aplicativo. O exemplo a seguir mostra esse formato. Informações de contexto de cliente e o dispositivo estão incluídas no elemento de *contexto* da estrutura de JSON.

```JSON
{
  "context": {
    "system": {
      "version": "2.0.12341",
    },
    "os": {
      "platform": "Linux",
      "name": "Debian",
      "version": "2.14324324"
    },
    "device": {
      "manufacturer": "Contoso",
      "model": "Fabrikan",
      "version": "7.341"
      }
    },
  }
}
```

##### <a name="system-element"></a>Elemento do sistema

O elemento system.version da `speech.config` mensagem contém a versão de fala de software do SDK usado pelo aplicativo cliente ou dispositivo. Valor na forma *major.minor.build.branch* Você pode omitir o componente de *ramificação* se não for aplicável.

##### <a name="os-element"></a>Elemento do SO

| Campo | DESCRIÇÃO | Uso |
|-|-|-|
| os.platform | A plataforma OS que hospeda o aplicativo, por exemplo, Windows, Android, iOS ou SO Linux |Obrigatório |
| os.name | Nome de produto do sistema operacional, por exemplo, Debian ou Windows 10 | Obrigatório |
| os.version | A versão do sistema operacional no formulário *major.minor.build.branch* | Obrigatório |

##### <a name="device-element"></a>Elemento de dispositivo

| Campo | DESCRIÇÃO | Uso |
|-|-|-|
| device.manufacturer | O fabricante de hardware do dispositivo | Obrigatório |
| device.model | O modelo do dispositivo. | Obrigatório |
| device.version | A versão do software do dispositivo fornecida pelo fabricante do dispositivo. Esse valor especifica uma versão do dispositivo que pode ser acompanhado pelo fabricante. | Obrigatório |

### <a name="message-audio"></a>Mensagem `audio`

Aplicativos cliente habilitados para fala enviam áudio para serviço de fala, convertendo o fluxo de áudio em uma série de blocos de áudio. Cada parte do áudio executa um segmento do áudio falado que deve ser transcrito pelo serviço. O tamanho máximo de um único bloco de áudio é 8.192 bytes. As mensagens do fluxo de áudio são *mensagens WebSocket binárias*.

Os clientes usam a `audio` mensagem para enviar uma parte de áudio para o serviço. Os clientes leem o áudio do microfone em partes e enviam essas partes para serviço de fala de transcrição. A primeira `audio` mensagem deve conter um cabeçalho bem formado corretamente que especifica que o áudio está de acordo com um dos formatos de codificação com suporte no serviço. Mensagens `audio` adicionais contêm apenas dados lidos do microfone de fluxo de áudio de binário.

Os clientes podem, opcionalmente, enviar um `audio` mensagem com um corpo de comprimento zero. Essa mensagem informa que o serviço que o cliente conhece o usuário parou de falar, a expressão é concluída e o microfone será desativado.

O serviço de fala usa a primeira `audio` mensagem que contém um identificador exclusivo para sinalizar o início de um novo ciclo de solicitação/resposta ou *ativar*. Depois que o serviço recebe uma `audio` mensagem com um novo identificador de solicitação, descarta as mensagens na fila ou não enviadas que estão associadas a qualquer ativação anterior.

| Campo | DESCRIÇÃO |
|-------------|----------------|
| Codificação de mensagem WebSocket | Binary |
| Body | Dados binários para a parte de áudio. O tamanho máximo é de 8.192 bytes. |

#### <a name="required-message-headers"></a>Cabeçalhos de mensagem necessários

Os cabeçalhos a seguir são necessários `audio` para todas as mensagens.

| Cabeçalho         |  Value     |
| ------------- | ---------------- |
| Path | `audio` |
| X-RequestId | UUID no formato de "no-dash" |
| X-Timestamp | Carimbo de hora do relógio cliente UTC no formato ISO 8601 |
| Content-Type | O tipo de conteúdo de áudio. O tipo deve ser *áudio/x-wav* (PCM) ou *áudio/silk* (SILK). |

#### <a name="supported-audio-encodings"></a>Codificações com suporte de áudio

Esta seção descreve os codecs de áudio com suporte pelo Serviço de Fala.

##### <a name="pcm"></a>PCM

O serviço de fala aceita áudio de modulação (PCM) do código de pulso descompactados. O áudio é enviado para o serviço na formatação [WAV](https://en.wikipedia.org/wiki/WAV), portanto a primeira parte do áudio *deve* conter um cabeçalho válido [Resource Interchange File Format](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) (RIFF). Se um cliente inicia uma vez com uma parte de áudio que *não* incluem um cabeçalho RIFF válido, o serviço rejeita a solicitação e encerra a conexão WebSocket.

O áudio PCM *deve* ser amostrado em 16 kHz com 16 bits por amostra e um canal (*riff kHz e 16-bit 16-mono-pcm*). O Serviço de Fala não dá suporte a fluxos de áudio estéreo e rejeita os fluxos de áudio que não usam a taxa de bits especificada, a taxa de amostra ou o número de canais.

##### <a name="opus"></a>Opus

Opus é um codec de áudio aberto, livre de royalties, altamente versátil. O serviço de fala dá suporte a Opus a uma taxa de bits constante de `32000` ou `16000`. Somente o `OGG` contêiner para Opus atualmente tem suporte especificado pelo `audio/ogg` tipo mime.

Para usar Opus, modifique o [exemplo de JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) e altere o `RecognizerSetup` método para retornar.

```javascript
return SDK.CreateRecognizerWithCustomAudioSource(
            recognizerConfig,
            authentication,
            new SDK.MicAudioSource(
                     new SDK.OpusRecorder(
                     {
                         mimeType: "audio/ogg",
                         bitsPerSecond: 32000
                     }
              )
          ));
```

#### <a name="detect-end-of-speech"></a>Detecção final de fala

Humanos não sinalizam explicitamente quando terminaram de falar. Qualquer aplicativo que aceita fala como entrada tem duas opções para tratar o fim de fala em um fluxo de áudio: detecção de fim de fala do serviço e detecção de fim de fala do cliente. Essas duas opções, detecção de final de fala de serviço geralmente fornece uma melhor experiência do usuário.

##### <a name="service-end-of-speech-detection"></a>Detecção de final de fala de serviço

Para criar a experiência ideal de fala viva-voz, os aplicativos permitem que o serviço detecte quando o usuário termina de falar. Os clientes enviam áudio do microfone como partes de *áudio* até que o serviço detecta silêncio e responde com uma `speech.endDetected` mensagem.

##### <a name="client-end-of-speech-detection"></a>Detecção de final de fala de serviço

Aplicativos cliente que permitem ao usuário sinalizar o final de fala de alguma forma também podem fornecer o serviço desse sinal. Por exemplo, um aplicativo cliente pode ter um botão "Parar" ou "Mudo" que o usuário pode pressionar. Para sinalizar o final de fala, os aplicativos cliente enviam uma mensagem de parte de *áudio* com um corpo de comprimento zero. O Serviço de Fala interpreta essa mensagem como o final do fluxo de áudio de entrada.

### <a name="message-telemetry"></a>Mensagem `telemetry`

Aplicativos cliente *devem* reconhecer o final de cada vez, enviando telemetria sobre a a ativação do Serviço de Fala. A confirmação de final de turno permite que o Serviço de Fala garanta que todas as mensagens necessárias para a conclusão da solicita e sua resposta sejam recebidas adequadamente pelo cliente. A confirmação de final de turno também permite que o serviço de fala verifique se os aplicativos cliente estão sendo executados conforme esperado. Essa informação é inestimável se você precisar de ajuda para a solução de problemas do seu aplicativo habilitado para fala.

Os clientes devem reconhecer o fim de uma curva, enviando uma `telemetry` mensagem logo após receber uma `turn.end` mensagem. Os clientes devem tentar confirmar `turn.end` assim que possível. Se um aplicativo cliente não reconhece o fim de turno, o serviço de fala pode encerrar a conexão com um erro. Os clientes devem enviar somente uma `telemetry` mensagem para cada solicitação e resposta identificada pelo valor *X RequestId*.

| Campo | DESCRIÇÃO |
| ------------- | ---------------- |
| Codificação de mensagem WebSocket | Text |
| Path | `telemetry` |
| X-Timestamp | Carimbo de hora do relógio cliente UTC no formato ISO 8601 |
| Content-Type | `application/json` |
| Body | Uma estrutura JSON que contém informações de cliente sobre a ativar |

O esquema para o corpo da `telemetry` mensagem é definido na seção [esquema de Telemetria](#telemetry-schema).

#### <a name="telemetry-for-interrupted-connections"></a>Telemetria para conexões interrompidas

Se a conexão de rede falha por algum motivo durante uma vez e o cliente *não* recebe uma `turn.end` mensagem do serviço, o cliente envia um `telemetry` mensagem. Esta mensagem descreve a solicitação com falha na próxima vez que o cliente fizer uma conexão ao serviço. Não é necessário que os clientes imediatamente tentem uma conexão para enviar a `telemetry` mensagem. A mensagem pode ser armazenada em buffer no cliente e enviada por uma conexão futura solicitada pelo usuário. A `telemetry` mensagem para a solicitação com falha *deve* usar o valor *X RequestId* na solicitação com falha. Pode ser enviada ao serviço assim que uma conexão é estabelecida, sem esperar para enviar ou receber para outras mensagens.

## <a name="service-originated-messages"></a>Mensagens de origem de serviço

Esta seção descreve as mensagens que se originam no Serviço de Fala e são enviadas ao cliente. O Serviço de Fala mantém um registro de recursos do cliente e gera as mensagens exigidas por cada cliente, portanto não que todos os clientes recebam todas as mensagens que são descritas aqui. Para resumir, as mensagens são referenciadas pelo valor do cabeçalho de *Caminho*. Por exemplo, nos referimos a uma mensagem de texto WebSocket com o valor de *Caminho* como `speech.hypothesis` uma mensagem speech.hypothesis.

### <a name="message-speechstartdetected"></a>Mensagem `speech.startDetected`

A `speech.startDetected` mensagem indica que o Serviço de Fala detectado fala no fluxo de áudio.

| Campo | DESCRIÇÃO |
| ------------- | ---------------- |
| Codificação de mensagem WebSocket | Text |
| Path | `speech.startDetected` |
| Content-Type | application/json; charset=utf-8 |
| Body | Estrutura JSON que contém informações sobre as condições quando o início da fala foi detectado. O campo de *deslocamento* nessa estrutura especifica o deslocamento (em unidades de 100 nanossegundos) quando a fala for detectada no fluxo de áudio, em relação ao início do fluxo. |

#### <a name="sample-message"></a>Mensagem de exemplo

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>Mensagem `speech.hypothesis`

Durante o reconhecimento de fala, o serviço de fala gera periodicamente hipóteses sobre as palavras do serviço reconhecido. O Serviço de Fala envia essas hipóteses ao cliente aproximadamente a cada 300 milissegundos. O `speech.hypothesis` adequado *somente* para melhorar a experiência de voz do usuário. Você não deve ter qualquer dependência no conteúdo ou a precisão do texto nessas mensagens.

 A `speech.hypothesis` mensagem é aplicável para os clientes que têm alguns recursos de processamento de texto e fornece comentários quase em tempo real de reconhecimento em andamento para a pessoa que está falando.

| Campo | DESCRIÇÃO |
| ------------- | ---------------- |
| Codificação de mensagem WebSocket | Text |
| Path | `speech.hypothesis` |
| X-RequestId | UUID no formato de "no-dash" |
| Content-Type | aplicativo/json |
| Body | Hipótese de fala de estrutura JSON |

#### <a name="sample-message"></a>Mensagem de exemplo

```HTML
Path: speech.hypothesis
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Text": "this is a speech hypothesis",
  "Offset": 0,
  "Duration": 23600000
}
```

O elemento de *Deslocamento* especifica o deslocamento (em unidades de 100 nanossegundos) no qual a expressão foi reconhecida, em relação ao início do fluxo de áudio.

O elemento de *Duração* (em unidades de 100 nanossegundos) dessa frase de fala.

Os clientes não devem fazer suposições sobre a frequência, tempo ou texto contido em uma hipótese de fala ou a consistência do texto qualquer uma das duas hipóteses de fala. As hipóteses são instantâneas apenas para o processo de transcrição no serviço. Não representam um acúmulo estável de transcrição. Por exemplo, uma hipótese de fala primeiro pode conter as palavras "fine fun", e a segunda hipótese pode conter as palavras "localizar engraçado." O Serviço de Fala não executa qualquer pós-processamento (por exemplo, o uso de maiúsculas, pontuação) no texto na hipótese de fala.

### <a name="message-speechphrase"></a>Mensagem `speech.phrase`

Quando o serviço de fala determina que ele tem informações suficientes para produzir um resultado de reconhecimento que não é alterado, o serviço produzirá uma `speech.phrase` mensagem. O Serviço de Fala produz esses resultados após detectar que o usuário concluiu uma sentença ou frase.

| Campo | DESCRIÇÃO |
| ------------- | ---------------- |
| Codificação de mensagem WebSocket | Text |
| Path | `speech.phrase` |
| Content-Type | aplicativo/json |
| Body | Estrutura JSON de frase de fala |

O esquema JSON de frase de fala inclui os seguintes campos: `RecognitionStatus`, `DisplayText`, `Offset`, e `Duration`. Para obter mais informações sobre esses campos, consulte [respostas de Transcrição](../concepts.md#transcription-responses).

#### <a name="sample-message"></a>Mensagem de exemplo

```HTML
Path: speech.phrase
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": 0,
  "Duration": 12300000
}
```

### <a name="message-speechenddetected"></a>Mensagem `speech.endDetected`

A `speech.endDetected` mensagem especifica que o aplicativo cliente deve parar o streaming de áudio para o serviço.

| Campo | DESCRIÇÃO |
| ------------- | ---------------- |
| Codificação de mensagem WebSocket | Text |
| Path | `speech.endDetected` |
| Body | A estrutura JSON que contém o deslocamento ao final da fala foi detectado. O deslocamento é representado no deslocamento de unidades de 100 nanossegundos desde o início de áudio que é usado para o reconhecimento. |
| Content-Type | application/json; charset=utf-8 |

#### <a name="sample-message"></a>Mensagem de exemplo

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

O elemento de *Deslocamento* especifica o deslocamento (em unidades de 100 nanossegundos) no qual a expressão foi reconhecida, em relação ao início do fluxo de áudio.

### <a name="message-turnstart"></a>Mensagem `turn.start`

O `turn.start` sinaliza o início de uma curva da perspectiva do serviço. A `turn.start` mensagem sempre é a primeira mensagem de resposta para qualquer solicitação que é recebida. Se você não tiver recebido uma `turn.start` mensagem, suponha que o estado de conexão de serviço é inválido.

| Campo | DESCRIÇÃO |
| ------------- | ---------------- |
| Codificação de mensagem WebSocket | Text |
| Path | `turn.start` |
| Content-Type | application/json; charset=utf-8 |
| Body | Estrutura JSON |

#### <a name="sample-message"></a>Mensagem de exemplo

```HTML
Path: turn.start
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "context": {
    "serviceTag": "7B33613B91714B32817815DC89633AFA"
  }
}
```

O corpo da `turn.start` mensagem é uma estrutura JSON que contém o contexto para o início da ativação. O elemento de *contexto* contém uma propriedade *serviceTag*. Essa propriedade especifica um valor de marca que o serviço associou com a ativação. Esse valor pode ser usado pela Microsoft se precisar de ajuda para solucionar problemas de falhas em seu aplicativo.

### <a name="message-turnend"></a>Mensagem `turn.end`

O `turn.end` sinaliza o início de uma curva da perspectiva do serviço. A `turn.end` mensagem sempre é a última mensagem de resposta para qualquer solicitação que é recebida. Os clientes podem usar o recebimento da mensagem como um sinal para atividades de limpeza e fazer a transição para um estado ocioso. Se você não tiver recebido uma `turn.end` mensagem, suponha que o estado de conexão de serviço é inválido. Nesses casos, feche a conexão existente para o serviço e se reconecte.

| Campo | DESCRIÇÃO |
| ------------- | ---------------- |
| Codificação de mensagem WebSocket | Text |
| Path | `turn.end` |
| Body | Nenhum |

#### <a name="sample-message"></a>Mensagem de exemplo

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Esquema de telemetria

O corpo da mensagem de *telemetria* é uma estrutura JSON que contém informações de cliente sobre uma vez ou uma conexão de tentativa. A estrutura é composta de carimbos de hora do cliente que registram quando ocorrem eventos de cliente. Cada carimbo de data / hora deve estar no formato ISO 8601, conforme descrito na seção intitulada "Cabeçalho X-Timestamp." As  mensagens de *Telemetria* que não especificam os campos obrigatórios na estrutura de JSON ou que não usam o formato de carimbo de data / hora correta podem fazer com que o serviço encerre a conexão ao cliente. Os clientes *devem* fornecer valores válidos para todos os campos obrigatórios. Os clientes *devem* fornecer valores para os campos opcionais quando apropriado. Os valores mostrados nos exemplos nesta seção são apenas para fins ilustrativos.

Esquema de telemetria é dividido nas seguintes partes: recebeu os carimbos de hora e métricas. O formato e o uso de cada parte é especificado nas seções a seguir.

### <a name="received-message-time-stamps"></a>Carimbos de hora de mensagem recebida

Os clientes devem incluir valores de tempo de recebimento para todas as mensagens recebidas após conectar-se ao serviço. Esses valores devem registrar o tempo quando o cliente *recebeu* cada mensagem da rede. O valor não deve registrar qualquer outra hora. Por exemplo, o cliente não deve registrar a hora quando *agiu* na mensagem. Os carimbos de hora de mensagem recebida são especificados em uma matriz de *nome: pares* de valor. Especifica o nome do par de valor de *Caminho* da mensagem. O valor do par Especifica o tempo em que a mensagem foi recebida do cliente. Ou, se mais de uma mensagem do nome especificado foi recebida, o valor do par é uma matriz de carimbos de data / hora que indica quando as mensagens são recebidas.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Os clientes *devem* confirmar o recebimento de todas as mensagens enviadas pelo serviço, incluindo os carimbos de hora para essas mensagens no corpo JSON. Se um cliente não conseguir confirmar o recebimento de uma mensagem, o serviço pode encerrar a conexão.

### <a name="metrics"></a>metrics

Os clientes devem incluir informações sobre eventos que ocorreram durante o tempo de vida de uma solicitação. As métricas a seguir têm suporte: `Connection`, `Microphone`, e `ListeningTrigger`.

### <a name="metric-connection"></a>Métrica`Connection`

A `Connection` métrica especifica detalhes sobre as tentativas de conexão pelo cliente. A métrica deve incluir os carimbos de hora de quando a conexão WebSocket foi iniciada e concluída. A `Connection` métrica é necessária *somente para a primeira ativação de uma conexão*. Ativações subsequentes não precisam incluir essas informações. Se um cliente fizer várias tentativas de conexão antes de uma conexão ser estabelecida, informações sobre *todas as* as tentativas de conexão devem ser incluídas. Para obter mais informações, consulte [Telemetria de falha de conexão](#connection-failure-telemetry).

| Campo | DESCRIÇÃO | Uso |
| ----- | ----------- | ----- |
| NOME | `Connection` | Obrigatório |
| ID | O valor do identificador de conexão que foi usado no cabeçalho *X ConnectionId* para esta solicitação de conexão | Obrigatório |
| Iniciar | A hora em que o cliente enviou a solicitação de conexão | Obrigatório |
| End | A hora quando o cliente recebe a notificação de que a conexão foi estabelecida com êxito, em casos de erro, rejeitada, recusada ou com falha | Obrigatório |
| Erro | Uma descrição do erro que ocorreu, se houver. Se a operação de gatilho for bem-sucedida, os clientes devem omitir esse campo. O comprimento máximo deste campo é de 50 caracteres. | Necessário para casos de erro, caso contrário omitidos |

A descrição do erro deve ser no máximo de 50 caracteres e idealmente deve ser um dos valores listados na tabela a seguir. Se a condição de erro não coincidir com um desses valores, os clientes podem usar uma descrição sucinta da condição de erro usando [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) sem espaço em branco. A capacidade de enviar uma mensagem de *telemetria* requer uma conexão para o serviço, portanto apenas transitório ou condições de erro temporárias podem ser relatadas na mensagem de *telemetria*. As condições de erro que *permanentemente* bloqueiam um cliente de estabelecer uma conexão para o serviço evitam que o cliente envie uma mensagem para o serviço, incluindo mensagens de *telemetria*.

| Erro | Uso |
| ----- | ----- |
| DNSfailure | O cliente não pôde se conectar ao serviço devido a uma falha DNS na pilha de rede. |
| NoNetwork | O cliente tentou uma conexão, mas a pilha de rede relatou que nenhuma rede física estava disponível. |
| NoAuthorization | A conexão de cliente falhou ao tentar adquirir um token de autorização para a conexão. |
| NoResources | O cliente ficou sem algum recurso local (por exemplo, memória) ao tentar estabelecer uma conexão. |
| Proibido | O cliente não pôde se conectar ao serviço porque o serviço retornou um HTTP `403 Forbidden` código de status sobre a solicitação de atualização de WebSocket. |
| Não Autorizado | O cliente não pôde se conectar ao serviço porque o serviço retornou um HTTP `401 Unauthorized` código de status sobre a solicitação de atualização de WebSocket. |
| BadRequest | O cliente não pôde se conectar ao serviço porque o serviço retornou um HTTP `400 Bad Request` código de status sobre a solicitação de atualização de WebSocket. |
| ServerUnavailable | O cliente não pôde se conectar ao serviço porque o serviço retornou um HTTP `503 Server Unavailable` código de status sobre a solicitação de atualização de WebSocket. |
| ServerError | O cliente não pôde se conectar ao serviço porque o serviço retornou um `HTTP 500` código de status de erro interno na solicitação de atualização de WebSocket. |
| Tempo limite | Solicitação de conexão do cliente atingiu o tempo limite sem uma resposta do serviço. O campo *Final*  contém a hora em que o cliente atingiu o tempo limite e interrompida aguardando a conexão. |
| ClientError | O cliente finalizou a conexão devido a um erro interno do cliente. |

### <a name="metric-microphone"></a>Métrica `Microphone`

A `Microphone` métrica é necessária para todas as ativações de fala. Essa métrica mede o tempo no cliente durante o qual a entrada de áudio está sendo usada ativamente para uma solicitação de fala.

Use os exemplos a seguir como diretrizes para gravar os valores de tempo *Iniciar* tempo valores para a `Microphone` métrica em seu aplicativo cliente:

* Um aplicativo cliente exige que um usuário deve pressionar um botão físico para iniciar o microfone. Após o pressionamento do botão, o aplicativo cliente lê a entrada do microfone e envia ao serviço de fala. O valor *Iniciar* para a `Microphone` métrica registra o tempo após o envio por push do botão quando o microfone é inicializado e está pronto para fornecer entrada. O valor *Final* para a `Microphone` métrica registra a hora em que o aplicativo cliente interrompeu o fluxo de áudio para o serviço depois que ele recebeu o `speech.endDetected` mensagem do serviço.

* Um aplicativo cliente usa um spotter de palavra-chave que está escutando "sempre". Somente depois que spotter de palavra-chave detecta uma frase de gatilho falada o aplicativo cliente coleta a entrada do microfone e envia ao serviço de fala. O valor de *Início* para a `Microphone` métrica registra o tempo quando o spotter da palavra-chave notificou o cliente para começar a usar a entrada do microfone. O valor *Final* para a `Microphone` métrica registra a hora em que o aplicativo cliente interrompeu o fluxo de áudio para o serviço depois que ele recebeu o `speech.endDetected` mensagem do serviço.

* Um aplicativo cliente tem acesso a um fluxo constante de áudio e executa a detecção de silêncio/fala sobre esse fluxo de áudio em um *módulo de detecção de fala*. O valor de *Início* para a `Microphone` métrica registra o tempo quando o *módulo de detecção de fala* notificou o cliente para começar a usar a entrada do fluxo de áudio. O valor *Final* para a `Microphone` métrica registra a hora em que o aplicativo cliente interrompeu o fluxo de áudio para o serviço depois que ele recebeu o `speech.endDetected` mensagem do serviço.

* Um aplicativo cliente está processando a segunda ativação de uma solicitação de multi-desativação e é informado por uma mensagem de resposta de serviço para ativar o microfone para coletar informações para a segunda ativação. O valor de *Início* para a `Microphone` métrica registra a hora quando o aplicativo cliente habilita o microfone e começa a usar a entrada dessa fonte de áudio. O valor *Final* para a `Microphone` métrica registra a hora em que o aplicativo cliente interrompeu o fluxo de áudio para o serviço depois que ele recebeu o `speech.endDetected` mensagem do serviço.

O valor de tempo *Final* para a `Microphone` métrica registra a hora em que o aplicativo cliente interrompeu o streaming de entrada de áudio. Na maioria das situações, esse evento ocorre logo após o cliente receber a `speech.endDetected` mensagem do serviço. Aplicativos cliente podem verificar que estão corretamente em conformidade com o protocolo, garantindo que o valor de hora *Final* para a `Microphone` métrica que ocorre depois do valor temporal de recebimento para a `speech.endDetected` mensagem. E, como normalmente há um atraso entre o final de uma ativação e o início de outra, os clientes podem verificar a conformidade do protocolo, garantindo que o horário de *Iniciar* da `Microphone` métrica para ativar qualquer subsequente  registra corretamente a hora quando o cliente *iniciou* usando o microfone para entrada de áudio de fluxo para o serviço.

| Campo | DESCRIÇÃO | Uso |
| ----- | ----------- | ----- |
| NOME | Microfone | Obrigatório |
| Iniciar | A hora de quando o cliente iniciou usando a entrada de áudio do microfone ou outro fluxo de áudio ou recebeu um gatilho de spotter a palavra-chave | Obrigatório |
| End | A hora em que o cliente interrompeu usando o fluxo de áudio ou microfone | Obrigatório |
| Erro | Uma descrição do erro que ocorreu, se houver. Se as operações de microfone forem bem-sucedidas, os clientes devem omitir esse campo. O comprimento máximo deste campo é de 50 caracteres. | Necessário para casos de erro, caso contrário omitidos |

### <a name="metric-listeningtrigger"></a>Métrica `ListeningTrigger`
A `ListeningTrigger` métrica mede o tempo quando o usuário executa a ação que inicia a entrada de fala. O `ListeningTrigger` métrica é opcional, mas os clientes que podem fornecer essa métrica serão incentivados a fazê-lo.

Use os exemplos a seguir como diretrizes para valores de tempo de gravação *Iniciar* e *Final* para a `ListeningTrigger` métrica em seu aplicativo cliente.

* Um aplicativo cliente exige que um usuário deve pressionar um botão físico para iniciar o microfone. O valor *Iniciar*para esta métrica registra a hora de envio do botão. O valor *Final* registra a hora quando o envio por push do botão for concluído.

* Um aplicativo cliente usa um spotter de palavra-chave que está escutando "sempre". Depois que spotter de palavra-chave detecta uma frase de gatilho falada, o aplicativo cliente coleta a entrada do microfone e envia ao Serviço de Fala. O valor *Iniciar* para esta métrica registra a hora em que palavra-chave do spotter recebeu áudio, em seguida, foi detectado como a frase de gatilho. O valor *Final* registra a hora em que a última palavra da frase gatilho foi falada pelo usuário.

* Um aplicativo cliente tem acesso a um fluxo constante de áudio e executa a detecção de silêncio/fala sobre esse fluxo de áudio em um *módulo de detecção de fala*. O valor *Iniciar* para essa métrica registra a hora em que o *módulo de detecção de fala* recebeu o áudio que foi então detectado como fala. O valor *Final* registra a hora quando o *módulo de detecção de fala* detectou a fala.

* Um aplicativo cliente está processando a segunda ativação de uma solicitação de multi-desativação e é informado por uma mensagem de resposta de serviço para ativar o microfone para coletar informações para a segunda ativação. O aplicativo cliente *não* deve incluir uma `ListeningTrigger` métrica para isso por sua vez.

| Campo | DESCRIÇÃO | Uso |
| ----- | ----------- | ----- |
| NOME | ListeningTrigger | Opcional |
| Iniciar | A hora de início do gatilho de escuta do cliente | Obrigatório |
| End | A hora de início do gatilho de escuta do cliente | Obrigatório |
| Erro | Uma descrição do erro que ocorreu, se houver. Se a operação de gatilho for bem-sucedida, os clientes devem omitir esse campo. O comprimento máximo deste campo é de 50 caracteres. | Necessário para casos de erro, caso contrário omitidos |

#### <a name="sample-message"></a>Mensagem de exemplo

O exemplo a seguir mostra uma mensagem de telemetria com partes ReceivedMessages e métricas:

```HTML
Path: telemetry
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000
X-Timestamp: 2016-08-16T15:03:54.183Z

{
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.171Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ],
  "Metrics": [
    {
      "Name": "Connection",
      "Id": "A140CAF92F71469FA41C72C7B5849253",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:48.000Z",
    },
    {
      "Name": "ListeningTrigger",
      "Start": "2016-08-16T15:03:48.776Z",
      "End": "2016-08-16T15:03:48.777Z",
    },
    {
      "Name": "Microphone",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:51.921Z",
    },
  ],
}
```

## <a name="error-handling"></a>Tratamento de erros

Esta seção descreve os tipos de mensagens de erro e as condições que seu aplicativo precisa tratar.

### <a name="http-status-codes"></a>Códigos de status HTTP

Durante a solicitação de atualização WebSocket, o serviço de fala pode retornar qualquer um dos códigos de status HTTP padrão, como `400 Bad Request`, etc. Seu aplicativo deve tratar corretamente essas condições de erro.

#### <a name="authorization-errors"></a>Erros de autorização

Se autorização incorreta for fornecida durante a atualização de WebSocket, o serviço de fala retorna um código de status HTTP `403 Forbidden`. Entre as condições que podem disparar esse código de erro são:

* Cabeçalho de *Autorização* faltante

* Token de autorização inválido

* Token de autorização expirado

A `403 Forbidden` mensagem de erro não indica um problema com o serviço de fala. Essa mensagem de erro indica um problema com o aplicativo cliente.

### <a name="protocol-violation-errors"></a>Erros de violação do protocolo

Se o Serviço de Fala detectar qualquer violação do protocolo de um cliente, o serviço termina a conexão WebSocket depois de retornar um *código de status* e *motivo* para o término. Os aplicativos cliente podem usar essas informações para solucionar e corrigir as violações.

#### <a name="incorrect-message-format"></a>Formato de mensagem incorreto

Se um cliente envia uma mensagem binária ou texto para o serviço que não é codificado no formato correto fornecido nesta especificação, o serviço fecha a conexão com um código de status de *Dados de Conteúdo Inválido 1007*.

O serviço retorna este código de status para uma variedade de motivos, como mostrado nos exemplos a seguir:

* “Formato de mensagem incorreto A mensagem binária tem prefixo de tamanho de cabeçalho inválido". O cliente enviou uma mensagem binária que tem um prefixo de tamanho de cabeçalho inválido.

* “Formato de mensagem incorreto A mensagem binária tem prefixo de tamanho de cabeçalho inválido". O cliente enviou uma mensagem binária que tem um prefixo de tamanho de cabeçalho inválido.

* “Formato de mensagem incorreto Falha de cabeçalhos de mensagem binária decodificação em UTF-8." O cliente enviou uma mensagem binária que contém cabeçalhos que não foram corretamente codificados em UTF-8.

* “Formato de mensagem incorreto A mensagem de texto não contém dados." O cliente enviou uma mensagem de texto que contém o corpo de dados.

* “Formato de mensagem incorreto Falha de decodificação de mensagem de texto em UTF-8." O cliente enviou uma mensagem de texto que não foi corretamente codificada em UTF-8.

* “Formato de mensagem incorreto A mensagem de texto não contém nenhum separador de cabeçalho". O cliente enviou uma mensagem de texto que não continha um separador de cabeçalho ou usou o separador de cabeçalho incorreto.

#### <a name="missing-or-empty-headers"></a>Cabeçalhos ausentes ou vazios

Se um cliente envia uma mensagem que não tenha os cabeçalhos necessários *X RequestId* ou *Caminho*, o serviço fecha a conexão com um código de status de *erro de protocolo 1002*. A mensagem é "Cabeçalho ausente/vazio. {Nome do cabeçalho}."

#### <a name="requestid-values"></a>Valores de RequestId

Se um cliente envia uma mensagem que especifica um cabeçalho *X RequestId* com um formato incorreto, o serviço fecha a conexão e retorna um status de *Erro de protocolo 1002*. A mensagem “Solicitação inválida. o valor do cabeçalho X-RequestId não foi especificado no formato UUID de não-dash”.

#### <a name="audio-encoding-errors"></a>Erros de codificação de áudio

Se um cliente enviar uma parte de áudio que inicia uma vez e o formato de áudio ou codificação não se adequa à especificação necessária, o serviço fecha a conexão e retorna um código de status de *dados de carga inválido 1007*. A mensagem indica o formato de codificação de origem do erro.

#### <a name="requestid-reuse"></a>Reutilizar RequestId

Após uma ativação ser finalizada, se um cliente envia uma mensagem que reutiliza o identificador da solicitação dessa ativação, o serviço fecha a conexão e retorna um código de status *Erro de protocolo 1002*. A mensagem “Solicitação inválida. A reutilização de identificadores de solicitação não é permitida."

## <a name="connection-failure-telemetry"></a>Telemetria de falha de conexão

Para garantir a melhor experiência possível ao usuário, os clientes devem informar o Serviço de Fala de carimbos de hora para pontos de verificação importantes em uma conexão usando a mensagem de *telemetria*. Também é importante que os clientes informem o serviço de conexões que foram tentados, mas falharam.

Para cada tentativa de conexão falha, os clientes criam uma mensagem de *telemetria* com um valor de cabeçalho único *X RequestId*. Porque o cliente não pôde estabelecer uma conexão, o campo *ReceivedMessages* no corpo JSON pode ser omitido. Somente a `Connection` entrada no campo *Métricas* é incluída. Essa entrada inclui o início e os carimbos de data / hora de término, bem como a condição de erro que foi encontrada.

### <a name="connection-retries-in-telemetry"></a>Novas tentativas de conexão na telemetria

Os clientes devem diferenciar *novas tentativas* de *várias tentativas de conexão* pelo evento que dispara a tentativa de conexão. As tentativas de conexão que são executadas por meio de programação sem qualquer entrada do usuário são novas tentativas. Várias tentativas de conexão que são executadas em resposta à entrada do usuário são várias tentativas de conexão. Os clientes dão a cada conexão ativada pelo usuário uma tentativa única de *X RequestId* e *mensagem* de telemetria. Os clientes reutilizam o *X RequestId* para repetições programáticas. Se várias tentativas foram feitas para uma tentativa de conexão única, cada tentativa de repetição é incluída como uma `Connection` entrada na mensagem de *telemetria*.

Por exemplo, suponha que um usuário faz o gatilho de palavra-chave para iniciar uma conexão e a primeira tentativa de conexão falhar devido a erros de DNS. No entanto, uma segunda tentativa é feita por meio de programação pelo cliente e é bem-sucedida. Como o cliente tentou a conexão sem a necessidade de entrada adicional do usuário, o cliente usa uma única mensagem de *telemetria* com várias `Connection` entradas para descrever a conexão.

Como outro exemplo, suponha que um usuário faz o gatilho de palavra-chave para iniciar uma conexão e essa tentativa de conexão falhar após três tentativas. O cliente desiste, para de tentar se conectar ao serviço e informa ao usuário que algo deu errado. O usuário, em seguida, faz o gatilho de palavra-chave novamente. Neste momento, suponha que o cliente se conecta ao serviço. Depois de se conectar, o cliente enviará imediatamente uma mensagem de*telemetria* para o serviço que contém três `Connection` entradas que descrevem as falhas de conexão. Após o recebimento da `turn.end` mensagem, o cliente envia outra mensagem de *telemetria* que descreve a conexão bem-sucedida.

## <a name="error-message-reference"></a>Referência de mensagens de erro

### <a name="http-status-codes"></a>Códigos de status HTTP

| Código de status HTTP | DESCRIÇÃO | solução de problemas |
| - | - | - |
| 400 Solicitação Inválida | O cliente enviou uma solicitação de conexão WebSocket que estava incorreta. | Verifique se você forneceu todos os parâmetros necessários e os cabeçalhos HTTP e que os valores estão corretos. |
| 401 Não Autorizado | O cliente não incluiu as informações de autorização necessárias. | Verifique se você está enviando o cabeçalho de *Autorização* na conexão WebSocket. |
| 403 Proibido | O cliente enviou informações de autorização, mas eram inválidas. | Verifique que você não está enviando um valor inválido ou no cabeçalho de *Autorização*. |
| 404 Não Encontrado | O cliente tentou acessar um caminho de URL que não tem suporte. | Verifique se você estiver usando a URL correta para a conexão WebSocket. |
| Erro de servidor 500 | O serviço encontrou um erro interno e não pode realizar a solicitação. | Na maioria dos casos, esse erro é transitório. Tente novamente a solicitação. |
| 503 Serviço Indisponível | O serviço estava indisponível para tratar a solicitação. | Na maioria dos casos, esse erro é transitório. Tente novamente a solicitação. |

### <a name="websocket-error-codes"></a>Códigos de erro de WebSocket

| Código WebSocketsStatus | DESCRIÇÃO | solução de problemas |
| - | - | - |
| Encerramento normal de 1000 | O serviço fechou a conexão WebSocket sem erro. | Se o encerramento do WebSocket foi inesperado, releia a documentação para garantir que você entende como e quando o serviço pode encerrar a conexão WebSocket. |
| Erro de protocolo de 1002 | O cliente não conseguiu aderir aos requisitos de protocolo. | Certifique-se de que você entenda a documentação do protocolo e é clara sobre os requisitos. Leia a documentação anterior sobre os motivos de erro para ver se está violando os requisitos de protocolo. |
| Dados de Conteúdo Inválido 1007 | O cliente enviou um conteúdo inválido em uma mensagem de protocolo. | Verifique a última mensagem enviada para o serviço quanto a erros. Leia a documentação anterior sobre erros de conteúdo. |
| Erro de servidor 1011 | O serviço encontrou um erro interno e não pode realizar a solicitação. | Na maioria dos casos, esse erro é transitório. Tente novamente a solicitação. |

## <a name="related-topics"></a>Tópicos relacionados

Consulte um [SDK de JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) que é uma implementação do protocolo WebSocket com base no serviço de fala.
