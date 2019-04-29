---
title: Definir filtros nos Serviços de Mídia do Azure
description: Este tópico descreve como criar filtros para que seu cliente possa usá-los na transmissão de seções específicas de um fluxo. Os Serviços de Mídia criam manifestos dinâmicos para atingir esse streaming seletivo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 61b877c322fcd58472990c328beea2e309502bce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734568"
---
# <a name="define-account-filters-and-asset-filters"></a>Definir filtros de contas e de ativos  

Ao fornecer conteúdo aos clientes (eventos de transmissão ao vivo ou vídeo sob demanda) seu cliente pode precisar de mais flexibilidade que o que é descrito no arquivo de manifesto do ativo padrão. Os Serviços de Mídia do Azure permitem definir filtros de conta e filtros de recursos para o seu conteúdo. 

Os filtros são regras do lado do servidor que permitem que seus clientes façam coisas como: 

- Reproduza apenas uma seção de um vídeo (em vez de reproduzir o vídeo inteiro). Por exemplo: 
  - Reduzir o manifesto para mostrar um subclipe de um evento ao vivo ("filtragem de subclipe"), ou
  - Corte do início de um vídeo ("corte de um vídeo").
- Entregue apenas as execuções especificadas e / ou faixas de idioma especificadas que são suportadas pelo dispositivo usado para reproduzir o conteúdo ("filtragem de renderização"). 
- Ajuste a Janela de Apresentação (DVR) para fornecer uma duração limitada da janela do DVR no leitor ("ajustar a janela de apresentação").

Os Serviços de Mídia oferecem [Manifestos dinâmicos](filters-dynamic-manifest-overview.md) com base em filtros predefinidos. Depois que você definir filtros, seus clientes poderão usá-los na URL de streaming. Os filtros podem ser aplicados a protocolos de streaming de taxa de bits adaptável: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela a seguir mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`<br/>Para HLS v3, use: `format=m3u8-aapl-v3`.|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="define-filters"></a>Definir filtros

Há dois tipos de filtros de ativo: 

* [Filtros de conta](https://docs.microsoft.com/rest/api/media/accountfilters) (global) - podem ser aplicados a qualquer recurso na conta dos Serviços de Mídia do Azure, têm vida útil da conta.
* [Filtros de ativos](https://docs.microsoft.com/rest/api/media/assetfilters) (local) - só podem ser aplicados a um ativo ao qual o filtro foi associado na criação, têm uma vida útil do recurso. 

Os filtros [Account Filter](https://docs.microsoft.com/rest/api/media/accountfilters) e [Asset Filter](https://docs.microsoft.com/rest/api/media/assetfilters) têm exatamente as mesmas propriedades para definir / descrever o filtro. Exceto ao criar o **Filtro de ativos**, você precisa especificar o nome do ativo ao qual deseja associar o filtro.

Dependendo do seu cenário, você decide qual tipo de filtro é mais adequado (Filtro de ativos ou Filtro de conta). Os filtros de conta são adequados para perfis de dispositivos (filtragem de renderização) em que os filtros de recursos podem ser usados para cortar um recurso específico.

Você usa as seguintes propriedades para descrever os filtros. 

|NOME|DESCRIÇÃO|
|---|---|
|firstQuality|A primeira taxa de bits de qualidade do filtro.|
|presentationTimeRange|O intervalo de tempo de apresentação. Esta propriedade é usada para filtrar os pontos de início / fim do manifesto, a duração da janela de apresentação e a posição de início ao vivo. <br/>Para mais informações, consulte [PresentationTimeRange](#presentationtimerange).|
|faixas|As condições de seleção de faixas. Para obter mais informações, consulte [faixas](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Use essa propriedade com **filtros de ativo**. Não é recomendável definir a propriedade com **filtros de conta**.

|NOME|DESCRIÇÃO|
|---|---|
|**endTimestamp**|Aplica-se ao Video on Demand (VoD).<br/>Para a apresentação de transmissão ao vivo, ela será silenciosamente ignorada e aplicada quando as extremidades de apresentação e o fluxo se torna VoD.<br/>Isso é um valor longo que representa um ponto de extremidade absoluto da apresentação, arredondado para o próximo início de GOP mais próximo. A unidade é a escala de tempo, portanto, seria um endTimestamp de 1800000000 para 3 minutos.<br/>Use startTimestamp e endTimestamp para cortar os fragmentos que estarão na lista de reprodução (manifesto).<br/>Por exemplo, startTimestamp = 40000000 e endTimestamp = 100000000 usando a escala de tempo padrão irá gerar uma lista de reprodução que contém os fragmentos de intervalo entre 4 segundos e 10 segundos da apresentação VoD. Se um fragmento ultrapassar o limite, o fragmento inteiro será incluído no manifesto.|
|**forceEndTimestamp**|Aplica-se à transmissão ao vivo.<br/>Indica se a propriedade endTimestamp deve estar presente. Se for true, endTimestamp deve ser especificado ou um código de solicitação incorreta é retornado.<br/>Valores permitidos: false, true.|
|**liveBackoffDuration**|Aplica-se à transmissão ao vivo.<br/> Esse valor define a posição mais recentes ao vivo em que um cliente pode buscar.<br/>Usando essa propriedade, você pode atrasar a posição de reprodução ao vivo e criar um buffer do lado do servidor para os jogadores.<br/>A unidade para essa propriedade é a escala de tempo (veja abaixo).<br/>O máximo ao vivo-a duração é de 300 segundos (3000000000).<br/>Por exemplo, um valor de 2000000000 significa que o conteúdo mais recente disponível é de 20 segundos atrasado da borda ao vivo real.|
|**presentationWindowDuration**|Aplica-se à transmissão ao vivo.<br/>Use presentationWindowDuration para aplicar uma janela deslizante de fragmentos para incluir em uma lista de reprodução.<br/>A unidade para essa propriedade é a escala de tempo (veja abaixo).<br/>Por exemplo, definir presentationWindowDuration = 1200000000 para aplicar uma janela deslizante de dois minutos. Mídia dentro de 2 minutos da borda ao vivo será incluída na lista de reprodução. Se um fragmento ultrapassar o limite, todo o fragmento será incluído na lista de reprodução. A duração mínima da janela de apresentação é de 60 segundos.|
|**startTimestamp**|Aplica-se ao vídeo sob demanda (VoD) ou transmissão ao vivo.<br/>Isso é um valor longo que representa um ponto de partida absoluto do fluxo. O valor é arredondado para o próximo início de GOP mais próximo. A unidade é a escala de tempo, portanto, seria um startTimestamp de 150000000 para 15 segundos.<br/>Use startTimestamp e endTimestampp para cortar os fragmentos que estarão na lista de reprodução (manifesto).<br/>Por exemplo, startTimestamp = 40000000 e endTimestamp = 100000000 usando a escala de tempo padrão irá gerar uma lista de reprodução que contém os fragmentos de intervalo entre 4 segundos e 10 segundos da apresentação VoD. Se um fragmento ultrapassar o limite, o fragmento inteiro será incluído no manifesto.|
|**Escala de tempo**|Aplica-se a todos os carimbos de hora e durações em um intervalo de tempo de apresentação, especificado como o número de incrementos em um segundo.<br/>O padrão é 10000000 - dez milhões de incrementos em um segundo, em que cada incremento seria longo de 100 nanossegundos.<br/>Por exemplo, se você desejar definir um startTimestamp em 30 segundos, você usaria um valor de 300000000 ao usar a escala de tempo padrão.|

### <a name="tracks"></a>Faixas

Você especifica uma lista de condições de propriedade de controle de filtro (FilterTrackPropertyConditions) com base no qual as faixas do seu fluxo (transmissão ao vivo ou vídeo sob demanda) devem ser incluídas no manifesto criado dinamicamente. Os filtros são combinados usando uma operação lógica **E** e **OU**.

As condições de propriedade da faixa de filtro descrevem tipos de trilha, valores (descritos na tabela a seguir) e operações (Equal, NotEqual). 

|NOME|DESCRIÇÃO|
|---|---|
|**Bitrate**|Use a taxa de bits da faixa para filtragem.<br/><br/>O valor recomendado é um intervalo de bitrates, em bits por segundo. Por exemplo, "0-2427000".<br/><br/>Nota: embora você possa usar um valor de taxa de bits específico, como 250000 (bits por segundo), essa abordagem não é recomendada, pois as taxas de bits exatas podem variar de um ativo para outro.|
|**FourCC**|Use o valor de FourCC da faixa para filtragem.<br/><br/>O valor é o primeiro elemento do formato de codecs, conforme especificado na [6381 RFC](https://tools.ietf.org/html/rfc6381). Atualmente, há suporte para os seguintes codecs: <br/>Vídeo: "Avc1", "hev1", "hvc1"<br/>Para áudio: "Mp4a", "ec-3"<br/><br/>Para determinar os valores de FourCC para faixas em um ativo, obter e examine o arquivo de manifesto.|
|**Linguagem**|Use a linguagem da faixa para filtragem.<br/><br/>O valor é a tag de um idioma que você deseja incluir, conforme especificado no RFC 5646. Por exemplo, “in”|
|**Nome**|Use o nome da faixa para filtragem.|
|**Tipo**|Use o tipo da faixa para filtragem.<br/><br/>Os seguintes valores são permitidos: "video", "áudio" ou "texto".|

## <a name="associate-filters-with-streaming-locator"></a>Associar filtros de localizador de Streaming

Você pode especificar uma lista de filtros de ativo ou conta, que se aplica a localizador de Streaming. O [empacotador dinâmico](dynamic-packaging-overview.md) se aplica a esta lista de filtros junto com aqueles seu cliente especifica a URL. Essa combinação gera uma [manifesto dinâmica](filters-dynamic-manifest-overview.md), que se baseia nos filtros na URL + filtros que você especificar no localizador de Streaming. É recomendável que você use esse recurso se você deseja aplicar filtros, mas não quiser expor os nomes de filtro na URL.

## <a name="definition-example"></a>Exemplo de definição

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir mostram como criar filtros programaticamente.  

- [Criar filtros com APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Criar filtros com .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Criar filtros com a CLI](filters-dynamic-manifest-cli-howto.md)

