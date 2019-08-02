---
title: Referência da API de Tradução de Fala
titleSuffix: Azure Cognitive Services
description: Documentação de referência para a API de Tradução de Fala.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: reference
ms.date: 05/18/2018
ms.author: swmachan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0f083a6ca3079128aad4aba3a53013df378a6106
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446899"
---
# <a name="translator-speech-api"></a>API de Tradução de Fala

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Esse serviço oferece uma API de streaming para transcrever fala conversacional de um idioma em texto de outro idioma. A API também integra os recursos de texto em fala para falar o texto traduzido novamente. A API de Tradução de Fala permite cenários como a tradução em tempo real de conversas conforme visto no Skype Translator.

Com a API de Tradução de Fala, os aplicativos cliente transmitem por streaming áudio de fala para o serviço e recebem de volta um fluxo de resultados baseados em texto, incluindo o texto reconhecido no idioma de origem e sua tradução no idioma de destino. Os resultados de texto são produzidos pela aplicação do ASR (Reconhecimento Automático de Fala) ativado por redes neurais profundas no fluxo de áudio de entrada. A saída de ASR bruta é aprimorada ainda por uma nova técnica chamada TrueText para refletir melhor a intenção do usuário. Por exemplo, TrueText remove quebras de fluências (hesitações e pigarros), palavras repetidas e restaura a pontuação e o uso de maiúsculas adequados. A capacidade de mascarar ou excluir linguagem vulgar também está incluída. Os mecanismos de reconhecimento e tradução são especificamente treinados para lidar com a fala conversacional. O serviço de Tradução de Fala usa a detecção de silêncio para determinar o fim de um enunciado. Após uma pausa na atividade de voz, o serviço transmitirá novamente um resultado final para o enunciado concluído. O serviço também pode enviar de volta resultados parciais, que fornecem traduções e reconhecimentos intermediários de um enunciado em andamento. Para os resultados finais, o serviço oferece a capacidade de sintetizar a fala (texto em fala) do texto falado nos idiomas de destino. O áudio de conversão de texto em fala é criado no formato especificado pelo cliente. Os formatos WAV e MP3 estão disponíveis.

A API de Tradução de Fala usa o protocolo WebSocket para fornecer um canal de comunicação full duplex entre o cliente e o servidor. Um aplicativo exigirá estas etapas para usar o serviço:

## <a name="1-getting-started"></a>1. Introdução
Para acessar a API de texto do tradutor, você precisará [se inscrever no Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Authentication

Use a chave de assinatura para autenticar-se. A API de Tradução de Fala dá suporte a dois modos de autenticação:

* **Usando um token de acesso:** em seu aplicativo, obtenha um token de acesso do serviço de token. Use sua chave de assinatura da API de Tradução de Fala para obter um token de acesso do serviço de autenticação dos Serviços Cognitivos do Azure. O token de acesso é válido por 10 minutos. Obtenha um novo token de acesso a cada 10 minutos e continue usando o mesmo token de acesso para solicitações repetidas dentro desses 10 minutos.

* **Usando uma chave de assinatura diretamente:** em seu aplicativo, passe sua chave de assinatura como um valor no cabeçalho `Ocp-Apim-Subscription-Key`.

Trate a sua chave de assinatura e o token de acesso como segredos que devem ficar ocultos da exibição.

## <a name="3-query-languages"></a>3. Idiomas de consulta
**Consulte o recurso de Idiomas para o conjunto atual de idiomas compatíveis.** O [recurso de idiomas](languages-reference.md) expõe os conjuntos de idiomas e vozes disponíveis para reconhecimento de fala, tradução de texto e texto em fala. Cada idioma ou voz recebe um identificador que a API de Tradução de Fala usa para identificar o mesmo idioma ou voz.

## <a name="4-stream-audio"></a>4. Transmitir áudio por streaming
**Abra uma conexão e comece a transmitir por streaming áudio ao serviço.** A URL do serviço é `wss://dev.microsofttranslator.com/speech/translate`. Parâmetros e formatos de áudio esperados pelo serviço são descritos abaixo, na operação `/speech/translate`. Um dos parâmetros é usado para passar o token de acesso da Etapa 2 acima.

## <a name="5-process-the-results"></a>5. Processar os resultados
**Processe os resultados transmitidos por streaming de volta do serviço.** O formato dos resultados parciais, dos resultados finais e dos segmentos de áudio de texto em fala são descritos na documentação da operação `/speech/translate` abaixo.

Exemplos de códigos demonstrando o uso da API de Tradução de Fala estão disponíveis no [site do Microsoft Translator no GitHub](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Notas de implementação

GET /speech/translate Estabelece uma sessão para a tradução de fala

### <a name="connecting"></a>Conexão
Antes de se conectar ao serviço, examine a lista de parâmetros fornecidos mais adiante nesta seção. Uma solicitação de exemplo é:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

A solicitação especifica que inglês falado será transmitidos por streaming para o serviço e traduzido para italiano. Cada resultado de reconhecimento final gerará uma resposta de áudio de texto em fala com a voz feminina chamada Elsa. Observe que a solicitação inclui credenciais no `Ocp-Apim-Subscription-Key header`. A solicitação também segue uma melhor prática configurando um identificador global exclusivo no cabeçalho `X-ClientTraceId`. Um aplicativo cliente deve registrar em log a ID de rastreamento para que ela possa ser usada para solucionar problemas quando eles ocorrerem.

### <a name="sending-audio"></a>Enviando de áudio
Quando a conexão é estabelecida, o cliente começa a transmitir áudio por streaming para o serviço. O cliente envia o áudio em partes. Cada bloco é transmitido usando uma mensagem do Websocket do tipo Binário.

A entrada de áudio está em WAVE (Formato de Arquivo de Áudio de Forma de Onda, mais comumente conhecido como WAV devido à sua extensão de nome de arquivo). O aplicativo cliente deve transmitir por streaming um áudio PCM de 16 bits de um único canal amostrado a 16 kHz. O primeiro conjunto de bytes transmitidos por streaming pelo cliente incluirá o cabeçalho WAV. Um cabeçalho de 44 bytes para um único fluxo PCM de 16 bits de canal assinado amostrado a 16 kHz é:

|Offset|Value|
|:---|:---|
|0 a 3|"RIFF"|
|4 a 7|0|
|8 a 11|"WAVE"|
|12 a 15|"fmt"|
|16 a 19|16|
|20 a 21|1|
|22 a 23|1|
|24 a 27|16000|
|28 a 31|32000|
|32 a 33|2|
|34 a 35|16|
|36 a 39|"data"|
|40 a 43|0|

Observe que o tamanho total do arquivo (bytes 4 a 7) e o tamanho dos "dados" (bytes 40 a 43) estão definidos como zero. Isso é aceitável para o cenário de streaming em que o tamanho total não é necessariamente conhecido antecipadamente.

Após enviar o cabeçalho WAV RIFF (), o cliente envia partes dos dados de áudio. O cliente normalmente transmitirá por streaming partes de tamanho fixo representando uma duração fixa (por exemplo, fluxo de 100 ms de áudio por vez).

### <a name="signal-the-end-of-the-utterance"></a>Sinalizar o final da declaração
A API de Tradução de Fala retorna a transcrição e a tradução do fluxo de áudio conforme você envia o áudio. A transcrição final, a tradução final e o áudio traduzido serão retornados para você apenas após o final da declaração. Em alguns casos, talvez você queira forçar o final da declaração. Envie 2,5 segundos de silêncio para forçar o final da declaração.

### <a name="final-result"></a>Resultado final
Um resultado de reconhecimento de fala final é gerado ao fim de um enunciado. Um resultado é transmitido do serviço para o cliente usando uma mensagem do WebSocket do tipo Texto. O conteúdo da mensagem é a serialização JSON de um objeto com as seguintes propriedades:

* `type`: constante de cadeia de caracteres para identificar o tipo de resultado. O valor é definitivo para resultados definitivos.
* `id`: identificador de cadeia de caracteres atribuído ao resultado de reconhecimento.
* `recognition`: texto reconhecido no idioma de origem. O texto pode ser uma cadeia de caracteres vazia no caso de um reconhecimento falso.
* `translation`: texto reconhecido traduzido para o idioma de destino.
* `audioTimeOffset`: deslocamento de tempo do início do reconhecimento em tiques (1 tique = 100 nanossegundos). O deslocamento é relativo ao início do streaming.
* `audioTimeSize`: duração em tiques (100 nanossegundos) do reconhecimento.
* `audioStreamPosition`: deslocamento de byte do início do reconhecimento. O deslocamento é relativo ao início do fluxo.
* `audioSizeBytes`: tamanho em bytes de reconhecimento.

Observe que o posicionamento de reconhecimento no fluxo de áudio não está incluído nos resultados por padrão. O recurso `TimingInfo` deve ser selecionado pelo cliente (consulte o parâmetro `features`).

A seguir está uma amostra de resultado final:

```
{
  type: "final"
  id: "23",
  recognition: "what was said",
  translation: "translation of what was said",
  audioStreamPosition: 319680,
  audioSizeBytes: 35840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 21900000
}
```

### <a name="partial-result"></a>Resultado parcial
Resultados de reconhecimento de fala parciais ou intermediários não são transmitidos para o cliente por padrão. O cliente pode usar o parâmetro de consulta de recursos para solicitá-los.

Um resultado parcial é transmitido do serviço para o cliente usando uma mensagem do WebSocket do tipo Texto. O conteúdo da mensagem é a serialização JSON de um objeto com as seguintes propriedades:

* `type`: constante de cadeia de caracteres para identificar o tipo de resultado. O valor é parcial para resultados parciais.
* `id`: identificador de cadeia de caracteres atribuído ao resultado de reconhecimento.
* `recognition`: texto reconhecido no idioma de origem.
* `translation`: texto reconhecido traduzido para o idioma de destino.
* `audioTimeOffset`: deslocamento de tempo do início do reconhecimento em tiques (1 tique = 100 nanossegundos). O deslocamento é relativo ao início do streaming.
* `audioTimeSize`: duração em tiques (100 nanossegundos) do reconhecimento.
* `audioStreamPosition`: deslocamento de byte do início do reconhecimento. O deslocamento é relativo ao início do fluxo.
* `audioSizeBytes`: tamanho em bytes de reconhecimento.

Observe que o posicionamento de reconhecimento no fluxo de áudio não está incluído nos resultados por padrão. O recurso TimingInfo deve ser selecionado pelo cliente (veja o parâmetro de recursos).

A seguir está uma amostra de resultado final:

```
{
  type: "partial"
  id: "23.2",
  recognition: "what was",
  translation: "translation of what was",
  audioStreamPosition: 319680,
  audioSizeBytes: 25840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 11900000
}
```

### <a name="text-to-speech"></a>Conversão de texto em fala
Quando o recurso de texto em fala está habilitado (veja o parâmetro `features` abaixo), um resultado final é seguido pelo áudio do texto traduzido falado. Dados de áudio são agrupados em bloco e enviados do serviço ao cliente como uma sequência de mensagens do Websocket do tipo Binário. Um cliente pode detectar o fim do fluxo verificando o bit FIN de cada mensagem. A última mensagem Binária terá seu bit FIN definido como um para indicar o fim do fluxo. O formato do fluxo depende do valor do parâmetro `format`.

### <a name="closing-the-connection"></a>Encerrando a conexão
Quando um aplicativo cliente tiver terminado de transmitir por streaming um áudio e tiver recebido o último resultado final, ele deverá fechar a conexão iniciando o handshake de fechamento do WebSocket. Há condições que farão o servidor encerrar a conexão. Os seguintes códigos de WebSocket Fechado podem ser recebidos pelo cliente:

* `1003 - Invalid Message Type`: o servidor está encerrando a conexão porque ele não pode aceitar o tipo de dados recebido. Isso geralmente ocorre quando o áudio recebido não inicia com um cabeçalho correto.
* `1000 - Normal closure`: a conexão foi fechada depois que a solicitação foi atendida. O servidor fechará a conexão: quando nenhum áudio for recebido do cliente por um longo período; quando silêncio for transmitido por um longo período; quando uma sessão atingir a duração máxima permitida (aproximadamente 90 minutos).
* `1001 - Endpoint Unavailable`: indica que o servidor ficará indisponível. O aplicativo cliente pode tentar reconectar-se com um limite no número de repetições.
* `1011 - Internal Server Error`: a conexão será fechada pelo servidor devido a um erro no servidor.

### <a name="parameters"></a>parâmetros

|Parâmetro|Value|DESCRIÇÃO|Tipo de Parâmetro|Tipo de Dados|
|:---|:---|:---|:---|:---|
|api-version|1.0|Versão da API solicitada pelo cliente. Os valores permitidos são: `1.0`.|query   |string|
|from|(vazio)   |Especifica o idioma da fala recebida. O valor é um dos identificadores de idioma do escopo de `speech` na resposta da API de idiomas.|query|string|
|para|(vazio)|Especifica o idioma para o qual traduzir o texto transcrito. O valor é um dos identificadores de idioma do escopo de `text` na resposta da API de idiomas.|query|string|
|recursos|(vazio)   |Conjunto separado por vírgulas de recursos selecionados pelo cliente. Os recursos disponíveis incluem:<ul><li>`TextToSpeech`: especifica que o serviço deve retornar o áudio traduzido da frase traduzida final.</li><li>`Partial`: especifica que o serviço deve retornar resultados intermediários de reconhecimento enquanto o áudio está transmitindo por streaming para o serviço.</li><li>`TimingInfo`: especifica que o serviço deve retornar informações de tempo associadas a cada reconhecimento.</li></ul>Por exemplo, um cliente especificaria `features=partial,texttospeech` para receber resultados parciais e texto em fala, mas não informações de tempo. Observe que os resultados finais sempre são transmitidos por streaming para o cliente.|query|string|
|voice|(vazio)|Identifica que voz usar para renderização de texto em fala do texto traduzido. O valor é um dos identificadores de voz do escopo de tts na resposta da API de idiomas. Se uma voz não for especificada, o sistema escolherá uma automaticamente quando o recurso de texto em fala for habilitado.|query|string|
|format|(vazio)|Especifica o formato de texto no fluxo de áudio de texto em fala retornado pelo serviço. As opções disponíveis são:<ul><li>`audio/wav`: fluxo de áudio de forma de onda. O cliente deve usar o cabeçalho WAV para interpretar corretamente o formato de áudio. Áudio WAV para texto em fala é PCM de canal único de 16 bits com uma taxa de amostragem de 24 kHz ou 16 kHz.</li><li>`audio/mp3`: fluxo de áudio em MP3.</li></ul>O padrão é `audio/wav`.|query|string|
|ProfanityAction    |(vazio)    |Especifica como o serviço deve tratar linguagens vulgares reconhecidas na fala. As ações válidas são:<ul><li>`NoAction`: linguagens vulgares são deixadas como estão.</li><li>`Marked`: linguagens vulgares são substituídas por um marcador. Veja o parâmetro `ProfanityMarker`.</li><li>`Deleted`: linguagens vulgares são excluídas. Por exemplo, se a palavra `"jackass"` for tratado como uma linguagem vulgar, a frase `"He is a jackass."` se tornará `"He is a .".`</li></ul>O padrão é Marcado.|query|string|
|ProfanityMarker|(vazio)    |Especifica como linguagens vulgares detectadas são tratadas quando `ProfanityAction` é definido como `Marked`. As opções válidas são:<ul><li>`Asterisk`: linguagens vulgares são substituídas pela cadeia de caracteres `***`. Por exemplo, se a palavra `"jackass"` for tratado como uma linguagem vulgar, a frase `"He is a jackass."` se tornará `"He is a ***.".`</li><li>`Tag`: linguagem vulgar fica entre uma marca XML de linguagem vulgar. Por exemplo, se a palavra `"jackass"` for tratado como uma linguagem vulgar, a frase `"He is a jackass."` se tornará `"He is a <profanity>jackass</profanity>."`.</li></ul>O padrão é `Asterisk`.|query|string|
|Authorization|(vazio)  |Especifica o valor do token de portador do cliente. Use o prefixo `Bearer` seguido pelo valor referente ao valor `access_token` retornado pelo serviço de token de autenticação.|cabeçalho   |string|
|Ocp-Apim-Subscription-Key|(vazio)|Obrigatório se o cabeçalho `Authorization` não for especificado.|cabeçalho|string|
|access_token|(vazio)   |Maneira alternativa de passar um token de acesso OAuth válido. O token de portador geralmente é fornecido com o cabeçalho `Authorization`. Algumas bibliotecas de websocket não permitem que o código do cliente defina cabeçalhos. Nesse caso, o cliente pode usar o parâmetro de consulta `access_token` para passar um token válido. Ao usar um token de acesso para autenticação, se o cabeçalho `Authorization` não estiver definido, `access_token` deverá ser definido. Se o cabeçalho e o parâmetro de consulta estiverem ambos definidos, o parâmetro de consulta será ignorado. Os clientes só devem usar um método para passar o token.|query|string|
|subscription-key|(vazio)   |Maneira alternativa de passar a chave de assinatura. Algumas bibliotecas de websocket não permitem que o código do cliente defina cabeçalhos. Nesse caso, o cliente pode usar o parâmetro de consulta `subscription-key` para passar uma chave de assinatura válida. Ao usar uma chave de assinatura para autenticação, se o cabeçalho `Ocp-Apim-Subscription-Key` não estiver definido, a chave de assinatura deverá ser definida. Se o cabeçalho e o parâmetro de consulta estiverem ambos definidos, o parâmetro de consulta será ignorado. Os clientes só devem usar um método para passar o `subscription key`.|query|string|
|X-ClientTraceId    |(vazio)    |Um GUID gerado pelo cliente usado para rastrear uma solicitação. Para solução de problemas apropriada, os clientes devem fornecer um novo valor com cada solicitação e registrá-lo em log.<br/>Em vez de usar um cabeçalho, esse valor pode ser passado com o parâmetro de consulta `X-ClientTraceId`. Se o cabeçalho e o parâmetro de consulta estiverem ambos definidos, o parâmetro de consulta será ignorado.|cabeçalho|string|
|X-CorrelationId|(vazio)    |Um identificador gerado pelo cliente usado para correlacionar vários canais em uma conversa. Várias sessões de tradução de fala podem ser criadas para permitir conversas entre os usuários. Nesse cenário, todas as sessões de tradução de fala usam a mesma ID de correlação para associar os canais entre si. Isso facilita o rastreamento e o diagnóstico. O identificador deve estar em conformidade com: `^[a-zA-Z0-9-_.]{1,64}$`<br/>Em vez de usar um cabeçalho, esse valor pode ser passado com o parâmetro de consulta `X-CorrelationId`. Se o cabeçalho e o parâmetro de consulta estiverem ambos definidos, o parâmetro de consulta será ignorado.|cabeçalho|string|
|X-ClientVersion|(vazio)    |Identifica a versão do aplicativo cliente. Exemplo: "2.1.0.123".<br/>Em vez de usar um cabeçalho, esse valor pode ser passado com o parâmetro de consulta `X-ClientVersion`. Se o cabeçalho e o parâmetro de consulta estiverem ambos definidos, o parâmetro de consulta será ignorado.|cabeçalho|string|
|X-OsPlatform|(vazio)   |Identifica o nome e a versão do sistema operacional em que o aplicativo cliente está sendo executado. Exemplos: "Android 5.0", "iOS 8.1.3", "Windows 8.1".<br/>Em vez de usar um cabeçalho, esse valor pode ser passado com o parâmetro de consulta `X-OsPlatform`. Se o cabeçalho e o parâmetro de consulta estiverem ambos definidos, o parâmetro de consulta será ignorado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|Modelo de Resposta|Cabeçalhos|
|:--|:--|:--|:--|
|101    |Atualização de WebSocket.|Valor de Exemplo de Modelo <br/> Objeto {}|X-RequestId<br/>Um valor que identifica a solicitação para fins de solução de problemas.<br/>string|
|400    |Solicitação inválida. Verifique os parâmetros de entrada para garantir que sejam válidos. O objeto de resposta inclui uma descrição mais detalhada do erro.|||
|401    |Não autorizado. Verifique se as credenciais estão definidas, se são válidas e se sua assinatura do Azure Data Market está em situação regular com um saldo disponível.|||
|500    |Ocorreu um erro. Se o erro persistir, relate-o com o identificador de rastreamento do cliente (X-ClientTraceId) ou com o identificador de solicitação (X-RequestId).|||
|503    |Servidor temporariamente não disponível. Tente novamente a solicitação. Se o erro persistir, relate-o com o identificador de rastreamento do cliente (X-ClientTraceId) ou com o identificador de solicitação (X-RequestId).|||
