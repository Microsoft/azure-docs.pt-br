---
title: Reprodução de gravações – Azure
description: Você pode usar a análise de vídeo ao vivo em IoT Edge para gravação contínua de vídeo, na qual você pode gravar vídeo na nuvem por semanas ou meses. Você também pode limitar a gravação a clipes que são de interesse, por meio da gravação baseada em eventos. Este artigo fala sobre como reproduzir gravações.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 0351f10d9fac3ad7e3b4fdd5fd549eb7c0023694
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99490887"
---
# <a name="playback-of-recordings"></a>Reprodução de gravações 

## <a name="pre-read"></a>Pré-leitura  

* [Reprodução de vídeo](video-playback-concept.md)
* [Gravação de vídeo contínua](continuous-video-recording-concept.md)
* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)

## <a name="background"></a>Tela de fundo  

Você pode usar a análise de vídeo ao vivo em IoT Edge para [gravação contínua de vídeo](continuous-video-recording-concept.md) (CVR), no qual você pode gravar vídeo na nuvem por semanas ou meses. Você também pode limitar sua gravação a clipes que são de interesse, por meio de EVR ( [gravação de vídeo baseada em eventos](event-based-video-recording-concept.md) ). 

Sua conta de serviço de mídia está vinculada a uma conta de armazenamento do Azure e, ao gravar vídeo na nuvem, o conteúdo é gravado em um [ativo de serviço de mídia](terminology.md#asset). Os serviços de mídia permitem [transmitir esse conteúdo](terminology.md#streaming), após a conclusão da gravação, ou enquanto a gravação está em andamento. Os serviços de mídia fornecem os recursos necessários para entregar fluxos por meio de protocolos HLS ou MPEG-DASH a dispositivos de reprodução (clientes). Consulte o artigo [reprodução de vídeo](video-playback-concept.md) para obter mais detalhes. Você usaria as APIs do serviço de mídia para gerar as URLs de streaming necessárias – usadas pelos clientes para reproduzir o vídeo & áudio. Para construir a URL de streaming para um ativo, consulte [criar um localizador de streaming e URLs de compilação](../latest/create-streaming-locator-build-url.md). Depois que a URL de streaming tiver sido criada para um ativo, você poderá e deverá armazenar a associação da URL com a fonte de vídeo (ou seja, câmera) no seu sistema de gerenciamento de conteúdo.

Por exemplo, ao transmitir via HLS, a URL de streaming ficaria assim `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . Para o MPEG-DASH, ele ficaria como `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd` .

Isso retorna um arquivo de manifesto, que, entre outras coisas, descreve a duração geral do fluxo que está sendo entregue e se ele representa conteúdo previamente registrado ou o feed ' ativo ' em andamento.

### <a name="live-vs-vod"></a>Ao vivo versus VoD  

Protocolos de streaming como HLS e MPEG-DASH foram criados para lidar com cenários como streaming de vídeos ao vivo, bem como streaming de conteúdo sob demanda/previamente registrado, como programas de TV e filmes. Para vídeos ao vivo, os clientes HLS e MPEG-DASH são projetados para iniciar a reprodução a partir da hora ' mais recente ' em diante. Com os filmes, no entanto, os visualizadores esperam poder começar desde o início e saltar se escolherem. Os manifestos HLS e MPEG-DASH têm sinalizadores que indicam aos clientes se o vídeo representa um fluxo ao vivo ou se é um conteúdo previamente gravado.
Esse conceito também se aplica a fluxos HLS e MPEG-DASH de ativos que contêm vídeo gravado usando a análise de vídeo ao vivo no IoT Edge.

## <a name="browsing-and-selective-playback-of-recordings"></a>Navegação e reprodução seletiva de gravações  

Considere o cenário em que você usou a análise de vídeo ao vivo em IoT Edge para gravar vídeo somente de 8:00 a 10h em dias em que uma escola esteve aberta, para todo o ano acadêmico. Ou talvez você esteja gravando vídeo somente de 7h a 19h em feriados nacionais. Em qualquer um desses dois cenários, ao tentar navegar e exibir a gravação de vídeo, você precisaria de:

* Uma maneira de determinar quais datas/horas de vídeo você tem em uma gravação.
* Uma maneira de selecionar uma parte (por exemplo, 9h a 11h em novos anos) dessa gravação para reprodução.

Os serviços de mídia fornecem uma API de consulta (availableMedia) para resolver o primeiro problema e os filtros de intervalo de tempo (StartTime, endTime) para resolver o segundo.

## <a name="query-api"></a>API de Consulta 

Ao usar CVR, os dispositivos de reprodução (clientes) não podem solicitar um manifesto que abranja a reprodução de toda a gravação.  Uma gravação de vários anos produziria um arquivo de manifesto muito grande para reprodução e seria complicado analisar em partes utilizáveis no lado do cliente.  O cliente precisa saber quais intervalos de DateTime têm dados na gravação para que ele possa fazer solicitações válidas sem muito trabalho de adivinhação. É aí que entra a nova API de consulta – os clientes agora podem usar essa API do lado do servidor para descobrir conteúdo.

Em que o valor de precisão pode ser um de: ano, mês, dia ou completo (como mostrado abaixo). 

|Precisão|ano|mês|dia|completa|
|---|---|---|---|---|
|Consulta|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|Resposta|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|Resposta de fidelidade total. Se não houvesse nenhuma lacuna, o início seria StartTime e End seria endTime.|
|Restrições|&#x2022;StartTime <= endTime<br/>&#x2022;ambos devem estar no formato aaaa; caso contrário, erro de retorno.<br/>&#x2022;valores podem ser separados por qualquer número de anos.<br/>&#x2022;valores são inclusivos.|&#x2022;StartTime <= endTime<br/>&#x2022;ambos devem estar no formato AAAA-MM, caso contrário, erro de retorno.<br/>&#x2022;valores podem ter no máximo 12 meses de distância.<br/>&#x2022;valores são inclusivos.|&#x2022;StartTime <= endTime<br/>&#x2022;ambos devem estar no formato AAAA-MM-DD, caso contrário, erro de retorno.<br/>&#x2022;valores podem ter no máximo 31 dias de diferença.<br/>Os valores são inclusivos.|&#x2022;StartTime < endTime<br/>&#x2022;valores podem ter no máximo 25 horas de diferença.<br/>&#x2022;valores são inclusivos.|

#### <a name="additional-request-format-considerations"></a>Considerações sobre o formato de solicitação adicional

* Todas as horas estão em UTC
* O parâmetro de cadeia de caracteres de consulta de precisão é necessário.  
* Os parâmetros de cadeia de caracteres de consulta StartTime e endTime são necessários para os valores de mês, dia e precisão total.  
* Os parâmetros de cadeia de caracteres de consulta StartTime e endTime são opcionais para o valor de precisão do ano (None, or ou both têm suporte).  

    * Se StartTime for omitido, será considerado o primeiro ano na gravação.
    * Se endTime for omitido, será considerado o último ano na gravação.
    * Por exemplo, se a gravação começou em 2011 e continuou até 2020, então:

        * /availableMedia? precisão = ano é o mesmo que/availableMedia? precisão = ano&StartTime = 2011&endTime = 2020
        * /availableMedia? precisão = ano&StartTime = 2015 é o mesmo que/availableMedia? precisão = ano&StartTime = 2015&endTime = 2020
        * /availableMedia? Precision = year&endTime = 2018 é o mesmo que/availableMedia? precisão = ano&StartTime = 2011&endTime = 2018

Se nenhum dado de mídia estiver disponível para os intervalos de tempo, uma lista vazia será retornada.

Se o ativo não contiver uma gravação de um grafo de mídia, uma resposta HTTP 400 será retornada com uma mensagem de erro explicando que esse recurso está disponível apenas para conteúdo registrado por meio de um grafo de mídia.

Se a duração do tempo especificada pelos parâmetros for maior do que o permitido pelo intervalo de tempo máximo para um determinado tipo de consulta, um HTTP 400 será retornado com uma mensagem de erro explicando o intervalo de tempo máximo permitido para o tipo de consulta solicitado.

Supondo que o intervalo de tempo seja válido para os parâmetros fornecidos, nenhum erro será retornado para consultas fora da janela de tempo dos dados na gravação.  O que significa que se a gravação iniciou 7 horas, mas o cliente solicitar a mídia disponível de {UtcNow – 24 horas} para UtcNow, então, retornaremos as {UtcNow – 7} horas de dados.

### <a name="response-format"></a>Formato de resposta  

A chamada availableMedia retorna um conjunto de valores de tempo.

A resposta será um corpo JSON, com valores de intervalos de intervalo sendo uma matriz de datas de UTC do ISO 8601 para o ano (no formato aaaa), mês (no formato AAAA-MM) ou dia (AAAA-MM-DD) dependendo da precisão solicitada.  Os intervalos de tempo completos conterão um valor de início e de término formatado como uma data/hora de UTC do ISO 8601 (no formato AAAA-MM-DDThh: mm: SS. sssZ).

Para a consulta availableMedia, a API será desativada da linha do tempo do vídeo. Qualquer descontinuidades na linha do tempo aparecerá como lacunas na resposta.

#### <a name="response-example-for-availablemedia"></a>Exemplo de resposta para availableMedia

##### <a name="example-1-live-recording-with-no-gaps"></a>Exemplo 1: gravação dinâmica sem intervalos

Aqui está uma resposta que mostra toda a mídia disponível para 21 de dezembro de 2019, em que a gravação foi de 100% concluída. A resposta tem apenas um par de início/término.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00.000Z", 
         "end":"2019-12-22T00:00:00.000Z"
    }
   ]
}
```

##### <a name="example-2-live-recording-with-a-2-second-gap"></a>Exemplo 2: gravação dinâmica com um intervalo de 2 segundos

Suponha que, por algum motivo, a câmera não tenha enviado um vídeo válido para um intervalo de 2 segundos em um dia. Aqui está uma resposta mostrando toda a mídia disponível para 21 de dezembro de 2019:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Exemplo 3: gravação ao vivo com uma lacuna de 8 horas

Suponhamos que a câmera e/ou a instalação local tenha perdido a energia por um período de 8 horas durante o dia, das 4 AM UTC ao dia UTC e não havia nenhuma fonte de energia de backup. Aqui está uma resposta mostrando toda a mídia disponível para esse dia

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>Exemplo 4: gravação dinâmica em que nenhum vídeo é gravado em feriados de verão

Suponha que você gravou vídeo somente quando a escola estava na sessão e a gravação foi interrompida de 17 de junho até 6 de setembro. Uma consulta para os meses disponíveis seria semelhante a:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=month&startTime=2019-01&endTime=2019-12
{
   "timeRanges":[
    {
         "start":"2019-01", 
         "end":"2019-06"
    },
    {
         "start":"2019-09", 
         "end":"2019-12"
    }
   ]
}
```

Se você tiver solicitado os dias disponíveis em junho, verá:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2019-06-01&endTime=2019-06-30
{
   "timeRanges":[
    {
         "start":"2019-06-01", 
         "end":"2019-06-17"
    }
   ]
}
```

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>Exemplo 5: gravação dinâmica em que nenhum vídeo é gravado nos fins de semana ou feriados

Suponha que você tenha gravado o vídeo somente durante o horário comercial. Uma consulta de dias disponíveis seria parecida com

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2020-02-01&endTime=2020-02-29
{
   "timeRanges":[
    {
         "start":"2020-02-03", 
         "end":"2020-02-07"
    },
    {
         "start":"2020-02-10", 
         "end":"2020-02-14"
    },
    {
         "start":"2020-02-18", // Monday Feb 17th was a holiday
         "end":"2020-02-21"
    },
    {
         "start":"2020-02-24", 
         "end":"2020-02-28"
    }
   ]
}
```

## <a name="time-range-filters"></a>Filtros de intervalo de tempo

Como mencionado acima, esses filtros ajudam você a selecionar partes da sua gravação (por exemplo, de 9h a 11h em novos anos) para reprodução. Ao transmitir via HLS, a URL de streaming ficaria assim `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . Para selecionar uma parte da sua gravação, você adicionaria um parâmetro StartTime e um endTime, como: `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8` . Assim, os filtros de intervalo de tempo são modificadores de URL usados para descrever a parte da linha do tempo da gravação que é incluída no manifesto de streaming:

* `starttime` é um carimbo de data/hora ISO 8601 que descreve a hora de início desejada da linha do tempo de vídeo no manifesto retornado.
* `endtime` é um carimbo de data/hora ISO 8601 que descreve a hora de término desejada da linha do tempo de vídeo retornada no manifesto.

O comprimento máximo (em tempo) desse manifesto não pode exceder 24 horas.

Aqui estão as restrições nos filtros.

|startTime| endTime |Result|
|---|---|---|
|Absent |Absent |Retorna a parte mais recente do vídeo no ativo, até um comprimento máximo de 4 horas.<br/><br/>Se o ativo não tiver sido gravado (nenhum novo dado de vídeo chegando) recentemente, um manifesto sob demanda (VoD) será retornado. Caso contrário, um manifesto ao vivo será retornado.|
|Presente|Absent|    Retorne um manifesto com qualquer vídeo disponível que seja mais recente do que o StartTime, se esse manifesto fosse menor do que 24 horas.<br/>Se o tamanho do manifesto exceder 24 horas, retorne um erro.<br/>Se o ativo não tiver sido gravado (nenhum novo dado de vídeo chegando) recentemente, um manifesto sob demanda (VoD) será retornado. Caso contrário, um manifesto ao vivo será retornado.
|Absent|Presente |Erro – se StartTime estiver presente, endTime será obrigatório.|
|Presente|Presente|Retorne um manifesto VoD com qualquer vídeo disponível entre StartTime e endTime.<br/>A extensão (StartTime, endTime) não pode exceder 24 horas.|

### <a name="response-examples"></a>Exemplos de resposta  

#### <a name="example-1-live-recording-with-no-gaps"></a>Exemplo 1: gravação dinâmica sem intervalos

Aqui está uma resposta que mostra toda a mídia disponível para 21 de dezembro de 2019, em que a gravação foi de 100% concluída. A resposta tem apenas um par de início/término.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Quando a gravação é contínua, você pode solicitar manifestos HLS ou DASH para qualquer parte do tempo dentro desse par de início/término, desde que o intervalo seja de 24 horas ou menos. Um exemplo de uma solicitação de manifesto de HLS de 4 horas para o anterior seria:

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>Exemplo 2: gravação dinâmica com um intervalo de 2 segundos

Suponha que, por algum motivo, a câmera não tenha enviado um vídeo válido para um intervalo de 2 segundos em um dia. Aqui está uma resposta mostrando a mídia disponível para 21 de dezembro de 2019:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Com uma gravação como a anterior, novamente, você pode solicitar manifestos HLS e DASH com quaisquer pares StartTime/endTime, desde que o span tenha sido inferior a 24 horas. Se esses valores estiverem espalhados pela lacuna em 04:01:08AM UTC, o manifesto retornado sinalizaria a descontinuidade na linha do tempo de mídia, de acordo com as especificações relevantes para esses protocolos.

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Exemplo 3: gravação ao vivo com uma lacuna de 8 horas

Suponhamos que a câmera e/ou a instalação local tenha perdido a energia por um período de 8 horas durante o dia, das 4 AM UTC ao dia UTC e não havia nenhuma fonte de energia de backup. Aqui está uma resposta que mostra toda a mídia disponível para esse dia.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Com tal gravação:

* Se você solicitar um manifesto onde os filtros de intervalo de inicialização/endTime estivessem dentro das partes ' disponíveis ' da linha do tempo, ou seja, da meia-noite a 4AM, ou do meio-dia à meia-noite, o serviço retornará um manifesto que cobre o tempo de StartTime a endTime, como:

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* Se você solicitar um manifesto em que o StartTime e a endTime estavam dentro do "buraco" no meio – digamos, de 8:00 a 10h UTC, o serviço se comportaria da mesma forma que se um filtro de ativos fosse resultar em um resultado vazio.

    [Esta é uma solicitação que obtém uma resposta vazia] `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* Se você solicitar um manifesto em que apenas um dos StartTime ou endTime está dentro do ' buraco ', o manifesto retornado incluiria apenas uma parte desse TimeSpan. Ele ajustaria o valor de StartTime ou endTime para o limite válido mais próximo. Por exemplo, se você solicitou um fluxo de 3 horas de 10h para às 13:00, a resposta conteria um valor de mídia de 1 hr por 12-meio-dia para às 13:00

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    O manifesto retornado começaria em 2019-12-21T12:00:00.000 Z, embora a solicitação tenha solicitado um início de 10h. Ao criar um sistema de gerenciamento de vídeo com um plug-in do Player, é necessário ter cuidado para sinalizar isso ao visualizador. Um visualizador sem reconhecimento seria confundido com o motivo pelo qual a linha do tempo de reprodução estava começando no meio-dia e não 10h conforme solicitado

## <a name="recording-and-playback-latencies"></a>Latências de gravação e reprodução

Ao usar a análise de vídeo ao vivo em IoT Edge para registrar em um ativo, você especificará uma propriedade segmentLength que informa ao módulo para agregar uma duração mínima de vídeo (em segundos) antes de ser gravado na nuvem. Por exemplo, se segmentLength for definido como 300, o módulo acumulará 5 minutos de vídeo antes de carregar o "bloco" de 1 5 minutos e, em seguida, entrará no modo de acumulação nos próximos 5 minutos e carregará novamente. O aumento do segmentLength tem o benefício de reduzir os custos de transação do armazenamento do Azure, pois o número de leituras e gravações não será mais frequente do que uma vez a cada segmentLength segundos.

Consequentemente, o streaming do vídeo dos serviços de mídia será atrasado por, pelo menos, muito tempo. 

Outro fator que determina a latência de reprodução (o atraso entre a hora em que um evento ocorre na frente da câmera, até o momento em que pode ser exibido em um dispositivo de reprodução) é a duração [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) do grupo de imagens. Como a [redução do atraso de fluxos ao vivo usando 3 técnicas simples](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641) explica, mais tempo, a duração do GOP, maior a latência. É comum ter câmeras IP usadas em cenários de vigilância e segurança configurados para usar o GOPS segundos por mais de 30 segundos. Isso tem um grande impacto na latência geral.

## <a name="next-steps"></a>Próximas etapas

[Tutorial de gravação de vídeo contínuo](continuous-video-recording-tutorial.md)
