---
title: Visão geral para o Microsoft Azure Mapas
description: Conheça os serviços e as funcionalidades do Microsoft Azure Mapas e como usá-los em seus aplicativos.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, references_regions
ms.openlocfilehash: 55955f883425651749809da951832484f098aeaf
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256116"
---
# <a name="what-is-azure-maps"></a>O que é o Azure Mapas?

O Azure Mapas é uma coleção de serviços geoespaciais e SDKs que usa novos dados de mapeamento para fornecer um contexto geográfico para aplicativos Web e móveis. O Azure Mapas oferece:

* APIs REST para renderizar mapas de vetor e raster em vários estilos e imagens de satélite.
* Serviços de Criador (versão prévia) para criar e renderizar mapas com base em dados de mapa do interior privados.
* Serviços de pesquisa para localizar endereços, locais e pontos de interesse em todo o mundo.
* Opções de roteiros variadas, como ponto a ponto, multiponto, otimização multiponto, isócrono, veículo elétrico, veículo comercial, tráfego influenciado e roteiros de matriz.
* Modo de exibição de fluxo de tráfego e de incidentes, para aplicativos que exigem informações de tráfego em tempo real.
* Serviços Mobilidade (versão prévia) para solicitar informações de trânsito público, planejar rotas mesclando diferentes modos de viagem e chegadas em tempo real.
* Serviços de Fuso horário e Geolocalização (versão prévia).
* Serviços Elevação (versão prévia) com Modelo de Elevação Digital
* Serviços de delimitação geográfica e armazenamento de dados de mapeamento, com informações de localização hospedadas no Azure.
* Inteligência de local por meio de análise geoespacial.

Além disso, os serviços do Azure Mapas também são disponibilizados pelo SDK da Web e pelo SDK do Android. Essas ferramentas ajudam os desenvolvedores a desenvolver e dimensionar rapidamente soluções que integram as informações de localização às soluções do Azure.

Inscreva-se hoje mesmo para obter uma [conta do Azure Mapas](https://azure.microsoft.com/services/azure-maps/) gratuita e comece a desenvolver.

O vídeo a seguir explica os Mapas do Azure com detalhes:

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny]

## <a name="map-controls"></a>Controles de mapeamento

### <a name="web-sdk"></a>SDK da Web

O SDK da Web dos Azure Mapas permite que você personalize mapas interativos com as imagens e o conteúdo próprios. Use esse mapa interativo para seus aplicativos Web ou móveis. Esse controle de mapa utiliza o WebGL, o que permite renderizar grandes conjuntos de dados com alto desempenho. Você pode desenvolver com o SDK usando JavaScript ou TypeScript.

:::image type="content" source="./media/about-azure-maps/intro_web_map_control.png" alt-text="Exemplo de mapa de alteração de população criado usando o SDK da Web do Azure Mapas":::

### <a name="android-sdk"></a>SDK do Android

Use o SDK do Android do Azure Mapas para criar aplicativos de mapeamento móvel.

:::image type="content" source="./media/about-azure-maps/android_sdk.png" border="false" alt-text="Exemplos de mapa em um dispositivo móvel":::

## <a name="services-in-azure-maps"></a>Serviços dos Mapas do Azure

O Azure Mapas é composto pelos serviços a seguir, que podem oferecer contexto geográfico aos aplicativos Azure.

### <a name="data-service-preview"></a>Serviço Dados (versão prévia)

Os dados são fundamentais para os mapas. Use o serviço Dados para carregar e armazenar dados geoespaciais para uso com operações espaciais ou composição de imagens.  Levar os dados do cliente para mais perto do serviço do Azure Mapas reduzirá a latência, aumentará a produtividade e criará novos cenários nos seus aplicativos. Para obter detalhes sobre esse serviço, confira a [Documentação do serviço Dados](/rest/api/maps/data).

### <a name="geolocation-service-preview"></a>Serviço Geolocalização (versão prévia)

Use o serviço de Geolocalização para visualizar o código de duas letras de país/região recuperado para um endereço IP. Esse serviço pode ajudar você a aprimorar a experiência do usuário fornecendo o conteúdo do aplicativo personalizado com base na localização geográfica.

Para obter mais detalhes, leia a [documentação do serviço de Geolocalização](/rest/api/maps/geolocation).

### <a name="mobility-services-preview"></a>Serviços Mobilidade (versão prévia) 

Os serviços Mobilidade do Azure Mapas aprimoram o tempo de desenvolvimento para aplicativos com recursos de trânsito públicos, como roteamento de trânsito e pesquisa para interrupções de trânsito públicos próximos. Os usuários podem recuperar informações detalhadas sobre as interrupções de trânsito, linhas e cronogramas. O serviço Mobilidade também permite que os usuários recuperem geometrias de parada e de linha, alertas de paradas, linhas e áreas de serviço, bem como entradas de trânsito e alertas de serviço em tempo real. Além disso, os serviços Mobilidade fornecem funcionalidades de roteamento com opções de planejamento de viagem multimodal. O planejamento de viagens multimodal incorpora opções de movimentação, semiciclo e trânsito público, tudo em uma viagem. Os usuários também podem acessar os roteiros passo a passo multimodais detalhados.

Para saber mais sobre o serviço, confira a [documentação dos serviços Mobilidade](/rest/api/maps/mobility).

### <a name="render-service"></a>Serviço de Renderização

O [serviço Renderização V2 (versão prévia)](/rest/api/maps/renderv2) apresenta uma nova versão da [API Obter Bloco do Mapa V2](/rest/api/maps/renderv2/getmaptilepreview). A API Obter Bloco do Mapa V2 agora permite que os clientes solicitem blocos de estrada do Azure Mapas, blocos meteorológicos ou os blocos de mapa criados usando o Criador do Azure Mapas. É recomendável que você use a nova API Obter Bloco de Mapa V2.  

:::image type="content" source="./media/about-azure-maps/intro_map.png" border="false" alt-text="Exemplo de mapa do serviço Renderização V2":::

Para mais detalhes, leia a [documentação do serviço de Renderização V2](/rest/api/maps/renderv2).

Para saber mais sobre o serviço de Renderização V1 que está em GA (disponibilidade geral), confira a [documentação do serviço Renderização V1](/rest/api/maps/render).  

### <a name="route-service"></a>Serviço de Roteamento

Os serviços de rota podem ser usados para calcular os ETAs (tempos de chegada estimados) para cada rota solicitada. AS APIs de rota consideram fatores como informações de tráfego em tempo real e dados de tráfego históricos, como as velocidades de estrada típicas no dia da semana e hora do dia solicitados. As APIs retornam as rotas mais curtas ou mais rápidas disponíveis para vários destinos de cada vez em sequência ou em ordem otimizada com base em tempo ou distância. Ele permite que os desenvolvedores calculem as instruções entre vários modos de viagem como carro, caminhão, bicicleta ou caminhada e veículo elétrico. O serviço também considera entradas como hora de partida, restrições de peso ou transporte de material perigoso.

:::image type="content" source="./media/about-azure-maps/intro_route.png" border="false" alt-text="Exemplo de mapa do Serviço de Roteiros":::

O serviço de Roteiros oferece recursos avançados de conjunto, como:

* Processamento em lote de várias solicitações de roteiro.
* Matrizes de tempo e distância de viagem entre um conjunto de origens e destinos.
* Localização de rotas ou distâncias que os usuários podem viajar com base em requisitos de tempo ou de combustível.

Para obter detalhes sobre as funcionalidades de roteamento, leia a [documentação do serviço de Roteiros](/rest/api/maps/route).

### <a name="search-service"></a>Serviço Search

O Serviço de pesquisa ajuda os desenvolvedores a procurar por endereços, locais, listagens de negócios por nome ou categoria e outras informações geográficas. Além disso, os serviços podem [inverter os endereços código geográfico](https://en.wikipedia.org/wiki/Reverse_geocoding) e fazer o cruzamento de ruas baseado em latitudes e longitudes.

:::image type="content" source="./media/about-azure-maps/intro_search.png" border="false" alt-text="Exemplo de uma pesquisa em um mapa":::

O Serviço de pesquisa também fornece recursos avançados, como:

* Pesquisar ao longo da rota.
* Pesquisar dentro de uma área mais ampla.
* Criar lote de um grupo de solicitações de pesquisa.
* Pesquise estações de carregamento de veículo elétrico e dados de POI (ponto de interesse) por nome de marca.

Para obter detalhes sobre as funcionalidades de pesquisa, leia a [documentação do serviço de Pesquisa](/rest/api/maps/search).

### <a name="spatial-service"></a>Serviço espacial

O serviço Espacial analisa rapidamente as informações de local para ajudar a informar os clientes sobre eventos em andamento que ocorrem. Ele permite a análise praticamente em tempo real e a modelagem preditiva de eventos.

O serviço permite que os clientes aprimorem nativamente a inteligência de localização deles com uma biblioteca de cálculos matemáticos geoespaciais comuns. Os cálculos comuns incluem o ponto mais próximo, a distância do círculo máximo e buffers. Para saber mais sobre o serviço e os vários recursos, leia a [documentação do serviço Espacial](/rest/api/maps/spatial).

### <a name="timezone-service"></a>Serviço de Fuso horário

O serviço de Fuso horário permite consultar informações de fuso horário atuais, históricas e futuras. Você pode usar os pares de latitude e longitude ou uma [ID do IANA](https://www.iana.org/) como entrada. O serviço de Fuso Horário também permite:

* Converter IDs de fuso horário do Microsoft Windows em fusos horários IANA.
* Buscar a diferença do fuso horário em relação ao UTC.
* Obter a hora atual em um fuso horário selecionado.

Uma resposta JSON normal para uma consulta para o serviço de Fuso horário é semelhante ao seguinte exemplo:

```JSON
{
  "Version": "2020a",
  "ReferenceUtcTimestamp": "2020-07-31T19:15:14.4570053Z",
  "TimeZones": [
    {
      "Id": "America/Los_Angeles",
      "Names": {
        "ISO6391LanguageCode": "en",
        "Generic": "Pacific Time",
        "Standard": "Pacific Standard Time",
        "Daylight": "Pacific Daylight Time"
      },
      "ReferenceTime": {
        "Tag": "PDT",
        "StandardOffset": "-08:00:00",
        "DaylightSavings": "01:00:00",
        "WallTime": "2020-07-31T12:15:14.4570053-07:00",
        "PosixTzValidYear": 2020,
        "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
      }
    }
  ]
}
```

Para obter detalhes sobre esse serviço, leia a [documentação do serviço de Fuso horário](/rest/api/maps/timezone).

### <a name="traffic-service"></a>Serviço de Tráfego

O serviço Tráfego é um conjunto de serviços Web que os desenvolvedores podem usar para aplicativos Web e móveis que requerem informações de tráfego. O serviço fornece dois tipos de dados:

* Fluxo de tráfego: velocidades observadas em tempo real e tempos de deslocamento para todas as estradas principais na rede.
* Incidentes de tráfego: uma visão atualizada sobre congestionamento e incidentes na rede rodoviária.

![Exemplo de um mapa com as informações de tráfego](media/about-azure-maps/intro_traffic.png)

Para obter mais informações, confira a [documentação do serviço de Tráfego](/rest/api/maps/traffic).

### <a name="weather-services"></a>Serviços meteorológicos

Os serviços Clima oferecem APIs que os desenvolvedores podem usar para recuperar informações de clima para uma localização específica. As informações contêm detalhes como data e hora de observação, breve descrição das condições climáticas, ícone de clima, sinalizadores de indicador precipitação, temperatura e informações de velocidade de vento. Detalhes adicionais, como temperatura RealFeel™ e índice UV, também são retornados.

Os desenvolvedores podem usar a [API Obter Clima ao longo da rota](/rest/api/maps/weather/getweatheralongroute) para recuperar informações sobre o clima em uma rota específica. Além disso, o serviço dá suporte à geração de notificações de clima para marcos que são afetados por riscos meteorológicos, como inundação ou chuva pesada.

A API [Obter Bloco de Mapa V2](/rest/api/maps/renderv2/getmaptilepreview) permite que você solicite blocos de radar e satélite passados, atuais e futuros.

![Exemplo de mapa com blocos de radar de clima em tempo real](media/about-azure-maps/intro_weather.png)

### <a name="maps-creator-service-preview"></a>Serviço de Criador de Mapas (versão prévia) 

O Serviço Criador de Mapa é um pacote de serviços Web que os desenvolvedores podem usar para criar aplicativos com recursos de mapa com base em dados de mapa do interior.

O Criador de Mapas fornece três serviços principais:

* [Serviço de conjunto de dados](/rest/api/maps/dataset). Use o serviço de conjunto de dados para criar um conjunto de dados de um pacote do Drawing convertido. Para obter informações sobre os requisitos de pacotes do Drawing, confira os Requisitos do pacote do Drawing.

* [Serviço de conversão](/rest/api/maps/dataset). Use o serviço de conversão para converter um arquivo de design DWG em dados de pacote de desenho para mapas do interior.

* [Serviço de conjunto de peças](/rest/api/maps/tileset). Use o serviço de conjunto de peças para criar uma representação de um conjunto de dados baseada em vetor. Os aplicativos podem usar um conjunto de peças para apresentar uma exibição do conjunto de dados baseada em peças visuais.

* [Serviço de Estado do recurso](/rest/api/maps/featurestate). Use o serviço Estado do Recurso para dar suporte ao estilo de mapa dinâmico. O estilo de mapa dinâmico permite que os aplicativos reflitam eventos em tempo real em espaços fornecidos pelos sistemas de IoT.

* [Serviço WFS](/rest/api/maps/featurestate). Use o serviço WFS para consultar seus dados de mapa do interior. O serviço WFS segue os padrões da [API do Open Geospatial Consortium](http://docs.opengeospatial.org/is/17-069r3/17-069r3.html) para consultar um único conjunto de dados.

### <a name="elevation-service-preview"></a>Serviço Elevação (versão prévia)

O serviço Elevação do Azure Mapas é um serviço Web que os desenvolvedores podem usar para recuperar dados de elevação de qualquer lugar na superfície da Terra.

O serviço Elevação permite que você recupere dados de elevação em dois formatos:

* **Formato de varredura GeoTIFF**. Use a [API Renderizar V2 – Obter Bloco do Mapa](/rest/api/maps/renderv2) para recuperar dados de elevação no formato de bloco.

* **Formato GeoJSON**. Use as [APIs de Elevação](/rest/api/maps/elevation) para solicitar dados de elevação de amostra ao longo de caminhos, dentro de uma caixa delimitadora definida ou em coordenadas específicas. 

:::image type="content" source="./media/about-azure-maps/elevation.png" alt-text="Exemplo de mapa usando dados de elevação":::


## <a name="programming-model"></a>Modelo de programação

O Azure Mapas foi criado para a mobilidade e podem ajudar a desenvolver aplicativos de plataforma cruzada. Ele usa um modelo de programação de linguagem independente e dão suporte à saída JSON por meio de [APIs REST](/rest/api/maps/).

Além disso, o Azure Mapas oferece um [controle de mapeamento JavaScript](/javascript/api/azure-maps-control) conveniente com um modelo de programação simples. O desenvolvimento é rápido e fácil para aplicativos Web e móveis.





## <a name="power-bi-visual"></a>Visual do Power BI

O visual do Azure Mapas para Power BI fornece um rico conjunto de visualizações de dados para dados espaciais sobre um mapa. É estimado que mais de 80% dos dados corporativos tenham um contexto de localização. O visual do Azure Mapas oferece uma solução sem código para obter informações sobre como esse contexto de localização se relaciona aos seus dados corporativos e os influencia.

:::image type="content" source="./media/about-azure-maps/intro-power-bi.png" border="false" alt-text="O Power BI Desktop com o visual do Azure Mapas exibindo dados corporativos":::

Para obter mais informações, confira a documentação Introdução ao [visual do Azure Mapas do Power BI](power-bi-visual-getting-started.md).

## <a name="usage"></a>Uso

Para acessar os serviços do Azure Mapas, vá para o [portal do Azure](https://portal.azure.com) e crie uma conta do Azure Mapas.

Os Mapas do Azure usam um esquema de autenticação baseado em chave. Quando você cria sua conta, duas chaves são geradas. Para autenticar os serviços do Azure Mapas, você pode usar qualquer chave.

Observação: o Azure Mapas compartilha consultas de endereço/localização fornecidas pelo cliente ("Consultas") com o TomTom de terceiros para fins de funcionalidade de mapeamento. As consultas não estão vinculadas a nenhum cliente ou usuário final quando compartilhadas com o TomTom e não podem ser usadas para identificar indivíduos. Os serviços Mobilidade e Clima, que incluem a integração com o Moovit e o AccuWeather, estão atualmente em [VERSÃO PRÉVIA](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

No momento, a Microsoft está no processo de adição do TomTom, Moovit e AccuWeather à lista de Subcontratados do Online Services.

## <a name="supported-regions"></a>Regiões com suporte

Os serviços do Azure Mapas estão disponíveis no momento, exceto nos seguintes países/regiões:

* China
* Coreia do Sul

Verifique se a localização de seu endereço IP atual está em um dos países/regiões com suporte.

## <a name="next-steps"></a>Próximas etapas

Experimente um aplicativo de exemplo que demonstre o Azure Mapas:

[Início Rápido: Criar um aplicativo Web](quick-demo-map-app.md)

Mantenha-se atualizado sobre o Azure Mapas:

[Blog do Azure Mapas](https://azure.microsoft.com/blog/topics/azure-maps/)