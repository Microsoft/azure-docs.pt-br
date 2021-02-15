---
title: Definir filtros nos Serviços de Mídia do Azure
description: Este tópico descreve como criar filtros para que seu cliente possa usá-los na transmissão de seções específicas de um fluxo. Os Serviços de Mídia criam manifestos dinâmicos para atingir esse streaming seletivo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: bb5561ced93c3f5a899c6e48fdab0f14e52914bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291538"
---
# <a name="filters"></a>Filtros

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ao entregar seu conteúdo aos clientes (eventos de transmissão ao vivo ou vídeo sob demanda), seu cliente pode precisar de mais flexibilidade do que o descrito no arquivo de manifesto do ativo padrão. Os serviços de mídia do Azure oferecem [manifestos dinâmicos](filters-dynamic-manifest-overview.md) com base em filtros predefinidos. 

Os filtros são regras do lado do servidor que permitem que seus clientes façam coisas como: 

- Reproduza apenas uma seção de um vídeo (em vez de reproduzir o vídeo inteiro). Por exemplo:
  - Reduzir o manifesto para mostrar um subclipe de um evento ao vivo ("filtragem de subclipe"), ou
  - Corte do início de um vídeo ("corte de um vídeo").
- Entregue apenas as execuções especificadas e / ou faixas de idioma especificadas que são suportadas pelo dispositivo usado para reproduzir o conteúdo ("filtragem de renderização"). 
- Ajuste a Janela de Apresentação (DVR) para fornecer uma duração limitada da janela do DVR no leitor ("ajustar a janela de apresentação").

Os serviços de mídia permitem que você crie **filtros de conta** e **filtros de ativo** para seu conteúdo. Além disso, você pode associar seus filtros criados previamente a um **localizador de streaming**.

## <a name="defining-filters"></a>Definir filtros

Existem dois tipos de filtros: 

* [Filtros de conta](/rest/api/media/accountfilters) (global) - podem ser aplicados a qualquer recurso na conta dos Serviços de Mídia do Azure, têm vida útil da conta.
* [Filtros de ativos](/rest/api/media/assetfilters) (local) - só podem ser aplicados a um ativo ao qual o filtro foi associado na criação, têm uma vida útil do recurso. 

**Filtros de conta** e tipos de **filtros de ativos** têm exatamente as mesmas propriedades para definir/descrever o filtro. Exceto ao criar o **Filtro de ativos**, você precisa especificar o nome do ativo ao qual deseja associar o filtro.

Dependendo do seu cenário, você decide qual tipo de filtro é mais adequado (Filtro de ativos ou Filtro de conta). Os filtros de conta são adequados para perfis de dispositivos (filtragem de renderização) em que os filtros de recursos podem ser usados para cortar um recurso específico.

Você usa as seguintes propriedades para descrever os filtros. 

|Nome|Descrição|
|---|---|
|firstQuality|A primeira taxa de bits de qualidade do filtro.|
|presentationTimeRange|O intervalo de tempo de apresentação. Esta propriedade é usada para filtrar os pontos de início / fim do manifesto, a duração da janela de apresentação e a posição de início ao vivo. <br/>Para mais informações, consulte [PresentationTimeRange](#presentationtimerange).|
|faixas|As condições de seleção de faixas. Para obter mais informações, consulte [faixas](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Use essa propriedade com **filtros de ativo**. Não é recomendável definir a propriedade com **filtros de conta**.

|Nome|Descrição|
|---|---|
|**endTimestamp**|Aplica-se ao Video on Demand (VoD).<br/>Para a apresentação de transmissão ao vivo, ela é silenciosamente ignorada e aplicada quando a apresentação termina e o fluxo se torna VoD.<br/>Esse é um valor longo que representa um ponto de extremidade absoluto da apresentação, arredondado para o próximo início da próxima GOP. A unidade é a escala de tempo, de modo que um carimbo de data/hora de 1800000000 seria por 3 minutos.<br/>Use startTimestamp e EndTimestamp para cortar os fragmentos que estarão na playlist (manifesto).<br/>Por exemplo, startTimestamp = 40000000 e EndTimestamp = 100000000 usando a escala de espera padrão gerará uma lista de reprodução que contém fragmentos entre 4 segundos e 10 segundos da apresentação VoD. Se um fragmento ultrapassar o limite, o fragmento inteiro será incluído no manifesto.|
|**forceEndTimestamp**|Aplica-se somente à transmissão ao vivo.<br/>Indica se a propriedade EndTimestamp deve estar presente. Se for true, EndTimestamp deverá ser especificado ou um código de solicitação inadequado será retornado.<br/>Valores permitidos: false, true.|
|**liveBackoffDuration**|Aplica-se somente à transmissão ao vivo.<br/> Esse valor define a última posição dinâmica que um cliente pode buscar.<br/>Usando essa propriedade, você pode atrasar a posição de reprodução dinâmica e criar um buffer do lado do servidor para players.<br/>A unidade dessa propriedade é TIMESCALE (veja abaixo).<br/>A duração máxima de retirada ao vivo é de 300 segundos (3000000000).<br/>Por exemplo, um valor de 2000000000 significa que o conteúdo mais recente disponível é de 20 segundos atrasados do Live Edge real.|
|**presentationWindowDuration**|Aplica-se somente à transmissão ao vivo.<br/>Use presentationWindowDuration para aplicar uma janela deslizante de fragmentos para incluir em uma lista de reprodução.<br/>A unidade dessa propriedade é TIMESCALE (veja abaixo).<br/>Por exemplo, definir presentationWindowDuration = 1200000000 para aplicar uma janela deslizante de dois minutos. Mídia dentro de 2 minutos da borda ao vivo será incluída na lista de reprodução. Se um fragmento ultrapassar o limite, todo o fragmento será incluído na lista de reprodução. A duração mínima da janela de apresentação é de 60 segundos.|
|**startTimestamp**|Aplica-se a VoD (vídeo por demanda) ou transmissão ao vivo.<br/>Esse é um valor longo que representa um ponto de partida absoluto do fluxo. O valor é arredondado para o próximo início de GOP mais próximo. A unidade é a escala de espera, portanto, um startTimestamp de 150 milhões seria por 15 segundos.<br/>Use startTimestamp e endTimestampp para cortar os fragmentos que estarão na playlist (manifesto).<br/>Por exemplo, startTimestamp = 40000000 e EndTimestamp = 100000000 usando a escala de espera padrão gerará uma lista de reprodução que contém fragmentos entre 4 segundos e 10 segundos da apresentação VoD. Se um fragmento ultrapassar o limite, o fragmento inteiro será incluído no manifesto.|
|**escala**|Aplica-se a todos os carimbos de data/hora e durações em um intervalo de tempo de apresentação, especificado como o número de incrementos em um segundo.<br/>O padrão é 10 milhões-10 milhões incrementos em um segundo, em que cada incremento seria 100 nanossegundos.<br/>Por exemplo, se você quiser definir um startTimestamp em 30 segundos, usará um valor de 300 milhões ao usar a escala de tempo padrão.|

### <a name="tracks"></a>Faixas

Você especifica uma lista de condições de propriedade de controle de filtro (FilterTrackPropertyConditions) com base nas faixas de seu fluxo (transmissão ao vivo ou vídeo sob demanda) que devem ser incluídas no manifesto criado dinamicamente. Os filtros são combinados usando uma operação lógica **E** e **OU**.

As condições de propriedade da faixa de filtro descrevem tipos de trilha, valores (descritos na tabela a seguir) e operações (Equal, NotEqual). 

|Nome|Descrição|
|---|---|
|**720p**|Use a taxa de bits da faixa para filtragem.<br/><br/>O valor recomendado é um intervalo de bitrates, em bits por segundo. Por exemplo, "0-2427000".<br/><br/>Nota: embora você possa usar um valor de taxa de bits específico, como 250000 (bits por segundo), essa abordagem não é recomendada, pois as taxas de bits exatas podem variar de um ativo para outro.|
|**FourCC**|Use o valor de FourCC da faixa para filtragem.<br/><br/>O valor é o primeiro elemento do formato de codecs, conforme especificado na [6381 RFC](https://tools.ietf.org/html/rfc6381). Atualmente, há suporte para os seguintes codecs: <br/>Vídeo: "Avc1", "hev1", "hvc1"<br/>Para áudio: "Mp4a", "ec-3"<br/><br/>Para determinar os valores de FourCC para faixas em um ativo obter e examine o arquivo de manifesto.|
|**Idioma**|Use a linguagem da faixa para filtragem.<br/><br/>O valor é a tag de um idioma que você deseja incluir, conforme especificado no RFC 5646. Por exemplo, “in”|
|**Nome**|Use o nome da faixa para filtragem.|
|**Tipo**|Use o tipo da faixa para filtragem.<br/><br/>Os seguintes valores são permitidos: "video", "áudio" ou "texto".|

### <a name="example"></a>Exemplo

O exemplo a seguir define um filtro de transmissão ao vivo: 

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

## <a name="associating-filters-with-streaming-locator"></a>Associando filtros com o localizador de streaming

Você pode especificar uma lista de [ativos ou filtros de conta](filters-concept.md) no [localizador de streaming](/rest/api/media/streaminglocators/create#request-body). O [Gerenciador dinâmico](dynamic-packaging-overview.md) aplica essa lista de filtros junto com aqueles que seu cliente especifica na URL. Essa combinação gera um [manifesto dinâmico](filters-dynamic-manifest-overview.md), que é baseado em filtros na URL + filtros especificados no localizador de streaming. 

Veja os exemplos a seguir:

* [Associar filtros ao localizador de streaming-.NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Associar filtros ao localizador de streaming – CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Atualizando filtros
 
Os **localizadores de streaming** não são atualizáveis enquanto os filtros podem ser atualizados. 

Não é recomendável atualizar a definição de filtros associados a um **localizador de streaming**publicado ativamente, especialmente quando a CDN está habilitada. Os servidores de streaming e CDNs podem ter caches internos que podem resultar na retorno de dados armazenados em cache obsoletos. 

Se a definição de filtro precisar ser alterada, considere criar um novo filtro e adicioná-lo à URL do **localizador de streaming** ou publicar um novo **localizador de streaming** que faça referência diretamente ao filtro.

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir mostram como criar filtros programaticamente.  

- [Criar filtros com APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Criar filtros com o .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Criar filtros com a CLI](filters-dynamic-manifest-cli-howto.md)
