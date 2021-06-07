---
title: Tutorial – Migrar serviços Web do Google Maps | Microsoft Azure Mapas
description: Tutorial sobre como migrar serviços Web do Google Maps para o Microsoft Azure Mapas
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d2b80dfa091d6a2b892c8baf8e9c35084c5bee4e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076381"
---
# <a name="tutorial-migrate-web-service-from-google-maps"></a>Tutorial: Migrar serviço Web do Google Maps

O Azure Mapas e o Google Maps dão acesso a APIs espaciais por meio de serviços Web REST. As interfaces de API dessas plataformas executam funcionalidades semelhantes. No entanto, cada uma delas usa convenções de nomenclatura e objetos de resposta diferentes.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Geocódigo direto e reverso
> * Pesquisar pontos de interesse
> * Calcular rotas e instruções
> * Recuperar uma imagem do mapa
> * Calcular uma matriz de distância
> * Obter detalhes do fuso horário

Você também aprenderá:

> [!div class="checklist"]
> * Qual serviço REST do Azure Mapas usar ao migrar de um Serviço Web do Google Maps
> * Dicas sobre como aproveitar ao máximo os serviços do Azure Mapas
> * Insights sobre outros serviços relacionados ao Azure Mapas

A tabela mostra as APIs de serviço dos Azure Mapas, que têm uma funcionalidade semelhante às APIs de serviço do Google Maps listadas.

| API de serviço do Google Maps | API de serviço do Azure Mapas                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Instruções              | [Route](/rest/api/maps/route)                                     |                         
| Matriz de Distância         | [Matrix de Rota](/rest/api/maps/route/postroutematrixpreview)       |                         
| Geocodificação               | [Pesquisar](/rest/api/maps/search)                                   |                         
| Pesquisa de locais           | [Pesquisar](/rest/api/maps/search)                                   |                         
| Preenchimento automático de locais      | [Pesquisar](/rest/api/maps/search)                                   |                         
| Ajustar à estrada            | Confira a seção [Calcular rotas e instruções](#calculate-routes-and-directions).  |        
| Limites de Velocidade            | Confira a seção [Geocodificação inversa de uma coordenada](#reverse-geocode-a-coordinate).         |         
| Mapa estático              | [Render](/rest/api/maps/render/getmapimage)                       |                         
| Fuso horário               | [Fuso horário](/rest/api/maps/timezone)                              |                         
| Elevação               | [Elevação (versão prévia)](/rest/api/maps/elevation)                   | 

As seguintes APIs de serviço não estão atualmente disponíveis nos Azure Mapas:

- Geolocalização – o Azure Mapas tem um serviço chamado Geolocalização, porém ele fornece um endereço IP para informações de localização. No entanto, ele atualmente não é compatível com torres de celular ou triangulação WiFi.
- Detalhes e fotos de locais – Números de telefone e a URL de site estão disponíveis na API de pesquisa dos Azure Mapas.
- URLs de mapa
- Estradas mais próximas – isso pode ser feito usando o SDK da Web, conforme mostrado [aqui](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic), mas não está disponível como um serviço no momento.
- Exibição estática de rua

O Azure Mapas tem vários serviços Web REST adicionais que podem ser interessantes:

- [Operações Espaciais](/rest/api/maps/spatial): Descarregue operações e cálculos espaciais complexos, como cerca geográfica, para um serviço.
- [Tráfego](/rest/api/maps/traffic): Acesse o fluxo de tráfego e os dados de incidentes em tempo real.

## <a name="prerequisites"></a>Pré-requisitos

1. Entre no [portal do Azure](https://portal.azure.com). Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. [Faça uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](how-to-manage-authentication.md).

## <a name="geocoding-addresses"></a>Como geocodificar endereços

O geocódigo é o processo de converter um endereço em uma coordenada. Por exemplo, "1 Microsoft Way, Redmond, WA" é convertido em longitude: -122,1298, latitude: 47,64005. Em seguida, as coordenadas podem ser usadas para diferentes tipos de finalidades, como posicionar um centro de um marcador em um mapa.

O Azure Mapas fornece vários métodos para endereços com geocódigo:

- [**Geocódigo de endereço em forma livre**](/rest/api/maps/search/getsearchaddress): especifique uma cadeia de caracteres de endereço individual e processe a solicitação imediatamente. "1 Microsoft Way, Redmond, WA" é um exemplo de uma cadeia de caracteres de endereço individual. Essa API é recomendada se você precisa geocodificar endereços individuais rapidamente.
- [**Geocódigo de endereço estruturado**](/rest/api/maps/search/getsearchaddressstructured): Especifique as partes de um endereço (como o nome da rua, a cidade, o país/região e o CEP) e processe a solicitação imediatamente. Essa API é recomendada se você precisa geocodificar endereços individuais rapidamente e se os dados já foram analisados nas respectivas partes de endereço individuais.
- [**Geocódigo de endereço de lote**](/rest/api/maps/search/postsearchaddressbatchpreview): Crie uma solicitação contendo até 10 mil endereços e faça com que elas sejam processadas durante um período de tempo. Todos os endereços serão geocodificados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado. Isso é recomendado para geocódigo de conjuntos de dados grandes.
- [**Pesquisa difusa**](/rest/api/maps/search/getsearchfuzzy): Essa API combina geocódigo com pesquisa de ponto de interesse. Essa API usa uma cadeia de caracteres de forma livre. Essa cadeia de caracteres pode ser um endereço, um lugar, um ponto de referência, um ponto de interesse ou uma categoria de ponto de interesse. Essa API processa a solicitação quase em tempo real. Essa API é recomendada para os aplicativos em que os usuários pesquisam endereços ou pontos de interesse na mesma caixa de texto.
- [**Pesquisa de lote difusa**](/rest/api/maps/search/postsearchfuzzybatchpreview): Crie uma solicitação contendo até 10 mil endereços, locais, pontos de referência ou pontos de interesse e faça com que eles sejam processados durante um período. Todos os dados serão processados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Google Maps | Parâmetro comparável da API do Azure Mapas  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` e `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` – cidade<br/>`municipalitySubdivision` – bairro, sub/supercidade<br/>`countrySubdivision` – Estado ou província<br/>`countrySecondarySubdivision` – município<br/>`countryTertiarySubdivision` – distrito<br/>`countryCode` – código de duas letras do país/região |
| `key`                       | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md). |
| `language`                  | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).  |
| `region`                    | `countrySet`                       |

Um exemplo de como usar o serviço de pesquisa está documentado [aqui](how-to-search-for-address.md). Lembre-se de examinar as [melhores práticas de pesquisa](how-to-use-best-practices-for-search.md).

> [!TIP]
> As APIs de pesquisa difusa e geocódigo de endereço com forma livre podem ser usadas no modo de preenchimento automático adicionando `&typeahead=true` à URL de solicitação. Isso instruirá o servidor de que o texto de entrada é provavelmente parcial, e a pesquisa entrará no modo preditivo.

## <a name="reverse-geocode-a-coordinate"></a>Geocodificação inversa de uma coordenada

O geocódigo inverso é o processo de conversão de coordenadas geográficas em um endereço aproximado. Coordenadas com "longitude: -122,1298, latitude: 47,64005" são convertidas em "1 Microsoft Way, Redmond, WA".

O Azure Mapas fornece vários métodos de geocódigo inverso:

- [**Geocodificador inverso de endereço**](/rest/api/maps/search/getsearchaddressreverse): especifique uma coordenada geográfica individual para obter o endereço aproximado correspondente a essa coordenada. Processa a solicitação quase em tempo real.
- [**Geocodificador inverso entre ruas**](/rest/api/maps/search/getsearchaddressreversecrossstreet): especifique uma coordenada geográfica individual para obter informações de travessas próximas e processar a solicitação imediatamente. Por exemplo, você poderá receber as travessas 1ª Avenida e Rua Principal.
- [**Geocodificador inverso de endereço de lote**](/rest/api/maps/search/postsearchaddressreversebatchpreview): Crie uma solicitação contendo até 10 mil coordenadas e faça com que elas sejam processadas durante um período de tempo. Todos os dados serão processados em paralelo no servidor. Quando a solicitação for concluída, você poderá baixar o conjunto completo de resultados.

Esta tabela faz uma referência cruzada dos parâmetros da API do Google Maps com os parâmetros comparáveis da API dos Azure Mapas.

| Parâmetro da API do Google Maps   | Parâmetro comparável da API do Azure Mapas   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md). |
| `language`                  | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).  |
| `latlng`                    | `query`  |
| `location_type`             | *N/A*     |
| `result_type`               | `entityType`    |

Examine [Melhores práticas de pesquisa](how-to-use-best-practices-for-search.md).

A API de geocódigo inverso dos Azure Mapas tem alguns recursos adicionais, que não estão disponíveis no Google Maps. Esses recursos podem ser úteis para integração ao seu aplicativo, durante a migração dele:

* Recuperar dados de limite de velocidade
* Recuperar informações de uso da estrada: estrada local, via arterial, acesso limitado, rampa etc.
* Recuperar o lado da rua na qual uma coordenada está localizada

## <a name="search-for-points-of-interest"></a>Pesquisar pontos de interesse

Os dados do ponto de interesse podem ser pesquisados no Google Maps usando a API de Pesquisa de Locais. Essa API fornece três maneiras diferentes de pesquisar pontos de interesse:

* **Localizar local do texto:** Pesquisa um ponto de interesse com base em seu nome, endereço ou número de telefone.
* **Pesquisa de Proximidade**: Pesquisa pontos de interesse dentro de uma determinada distância de uma localização.
* **Pesquisa de Texto:** pesquisa locais usando um texto de forma livre, que inclui informações de ponto de interesse e localização. Por exemplo, "pizza em Nova York" ou "restaurantes perto da main st".

O Azure Mapas fornece várias APIs de pesquisa para pontos de interesse:

- [**Pesquisa de POI**](/rest/api/maps/search/getsearchpoi): Pesquise pontos de interesse pelo nome. Por exemplo, "Starbucks".
- [**Pesquisa de categoria de POI**](/rest/api/maps/search/getsearchpoicategory): Pesquise pontos de interesse pela categoria. Por exemplo, "restaurante".
- [**Pesquisa de proximidade**](/rest/api/maps/search/getsearchnearby): Pesquisa pontos de interesse dentro de uma determinada distância de uma localização.
- [**Pesquisa difusa**](/rest/api/maps/search/getsearchfuzzy): Essa API combina geocódigo com pesquisa de ponto de interesse. Essa API usa uma cadeia de caracteres de forma livre que pode ser um endereço, um local, um ponto de referência, um ponto de interesse ou uma categoria de ponto de interesse. Ela processa a solicitação quase em tempo real. Essa API é recomendada para os aplicativos em que os usuários pesquisam endereços ou pontos de interesse na mesma caixa de texto.
- [**Pesquisar dentro da geometria**](/rest/api/maps/search/postsearchinsidegeometry): Pesquise pontos de interesse dentro de uma geometria especificada. Por exemplo, pesquise um ponto de interesse em um polígono.
- [**Pesquisar ao longo da rota**](/rest/api/maps/search/postsearchalongroute): Pesquise pontos de interesse que estejam ao longo de um caminho de rota especificado.
- [**Pesquisa de lote difusa**](/rest/api/maps/search/postsearchfuzzybatchpreview): crie uma solicitação que contenha até 10.000 endereços, locais, pontos de referência ou pontos de interesse. Processa a solicitação durante um período. Todos os dados serão processados em paralelo no servidor. Quando a solicitação concluir o processamento, você poderá baixar o conjunto completo de resultados.

Atualmente, os Azure Mapas não têm uma API comparável à API de Pesquisa de Texto do Google Maps.

> [!TIP]
> As APIs de pesquisa de POI, pesquisa de categoria de POI e pesquisa difusa podem ser usadas no modo de preenchimento automático adicionando `&typeahead=true` à URL de solicitação. Isso instruirá o servidor de que o texto de entrada é provavelmente parcial. A API realizará a pesquisa no modo preditivo.

Examine a documentação de [melhores práticas de pesquisa](how-to-use-best-practices-for-search.md).

### <a name="find-place-from-text"></a>Localizar local do texto

Use a [pesquisa de POI](/rest/api/maps/search/getsearchpoi) e a [pesquisa difusa](/rest/api/maps/search/getsearchfuzzy) dos Azure Mapas para pesquisar pontos de interesse por nome ou endereço.

A tabela faz uma referência cruzada dos parâmetros da API do Google Maps com os parâmetros comparáveis da API dos Azure Mapas.

| Parâmetro da API do Google Maps | Parâmetro comparável da API do Azure Mapas |
|---------------------------|-------------------------------------|
| `fields`                  | *N/A*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/A*                               |
| `key`                     | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md). |
| `language`                | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).  |
| `locationbias`            | `lat`, `lon` e `radius`<br/>`topLeft` e `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Pesquisa de proximidade

Use a API [Pesquisa de proximidade](/rest/api/maps/search/getsearchnearby) para recuperar pontos de interesse próximos nos Azure Mapas.

A tabela mostra os parâmetros da API do Google Maps com os parâmetros comparáveis da API dos Azure Mapas.

| Parâmetro da API do Google Maps | Parâmetro comparável da API do Azure Mapas  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md). |
| `keyword`                   | `categorySet` e `brandSet`        |
| `language`                  | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).  |
| `location`                  | `lat` e `lon`                     |
| `maxprice`                  | *N/A*                               |
| `minprice`                  | *N/A*                               |
| `name`                      | `categorySet` e `brandSet`        |
| `opennow`                   | *N/A*                               |
| `pagetoken`                 | `ofs` e `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/A*                               |
| `type`                      | `categorySet –` Confira a documentação de [categorias de pesquisa compatíveis](supported-search-categories.md).   |

## <a name="calculate-routes-and-directions"></a>Calcular rotas e instruções

Calcule as rotas e o trajeto usando os Azure Mapas. O Azure Mapas tem muitas das mesmas funcionalidades que o serviço de roteiros do Google Maps, como:

* Horas de chegada e partida.
* Rotas de tráfego preditivas e baseadas em tempo real.
* Meios de transporte diferentes. Como automóvel, a pé e bicicleta.

> [!NOTE]
> O Azure Mapas exige que todos os marcos sejam coordenadas. Os endereços precisam ser geocodificados primeiro.

O serviço de roteiros do Azure Mapas fornece as seguintes APIs para calcular rotas:

- [**Calcular rota**](/rest/api/maps/route/getroutedirections): Calcule uma rota e tenha a solicitação seja processada imediatamente. Essa API é compatível com solicitações GET e POST. Solicitações POST são recomendadas ao especificar um grande número de marcos ou ao usar muitas das opções de rota para garantir que a solicitação de URL não se torne longa demais e cause problemas. A Direção da Rota POST nos Azure Mapas tem uma opção que pode admitir milhares de [pontos de suporte](/rest/api/maps/route/postroutedirections#supportingpoints) e os usará para recriar um caminho de rota lógico entre eles (ajustar à estrada). 
- [**Rota de lote**](/rest/api/maps/route/postroutedirectionsbatchpreview): Crie uma solicitação contendo até 1.000 solicitação de rota e faça com que elas sejam processadas durante um período de tempo. Todos os dados serão processados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado.
- [**Serviços Mobilidade (versão prévia) **](/rest/api/maps/mobility): Calcule rotas e instruções usando o trânsito público.

A tabela faz uma referência cruzada dos parâmetros da API do Google Maps com os parâmetros comparáveis da API dos Azure Mapas.

| Parâmetro da API do Google Maps    | Parâmetro comparável da API do Azure Mapas  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – coordenadas no formato `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md). |
| `language`                     | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A*: este recurso está relacionado ao geocódigo. Use o parâmetro *countrySet* ao usar a API de geocódigo do Azure Mapas.  |
| `traffic_model`               | *N/A* – só é possível especificar se os dados de tráfego devem ser usados com o parâmetro *traffic*. |
| `transit_mode`                | Confira a [Documentação dos serviços Mobilidade (versão prévia)](/rest/api/maps/mobility) |
| `transit_routing_preference` | Confira a [Documentação dos serviços Mobilidade (versão prévia)](/rest/api/maps/mobility) |
| `units`                        | *N/A* – o Azure Mapas usa apenas o sistema métrico.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Por padrão, a API de rotas dos Azure Mapas retorna apenas um resumo. Ela retorna a distância e os horários, além das coordenadas do caminho da rota. Use o parâmetro `instructionsType` para recuperar as instruções curva a curva. Além disso, use o parâmetro `routeRepresentation` para filtrar o resumo e o caminho da rota.

A API de roteiros dos Azure Mapas traz recursos adicionais que não estão disponíveis no Google Maps. Ao migrar seu aplicativo, considere o uso desses recursos; talvez eles sejam úteis.

* Suporte para tipo de rota: mais curta, mais rápida, emocionante e mais econômica no consumo de combustível.
* Suporte para modos de viagem adicionais: ônibus, motocicleta, táxi, caminhão e van.
* Suporte para 150 marcos.
* Calcular vários tempos de viagem em uma solicitação; tráfego histórico, tráfego ao vivo, sem tráfego.
* Evite tipos de estrada adicionais: estradas com pista para veículos de alta ocupação, estradas não pavimentadas e estradas já usadas.
* Especifique áreas personalizadas a serem evitadas.
* Limite a elevação que a rota poderá subir.
* Rota com base nas especificações do motor. Calcule as rotas para veículos elétricos ou de combustão com base nas especificações do motor e do combustível ou da carga restante.
* Suporte a parâmetros de rota de veículo comercial. Como dimensões do veículo, peso, número de eixos e tipo de carga.
* Especifique a velocidade máxima do veículo.

Além disso, o serviço de rota dos Azure Mapas dá suporte ao [cálculo de intervalos roteáveis](/rest/api/maps/route/getrouterange). O cálculo de intervalos roteáveis também é conhecido como isócronos. Isso envolve a geração de um polígono que abrange uma área que pode ser percorrida em qualquer direção, começando em um ponto de origem. Tudo isso conforme um período ou uma quantidade de carga ou combustível especificado.

Examine a documentação de [melhores práticas de roteamento](how-to-use-best-practices-for-routing.md).

## <a name="retrieve-a-map-image"></a>Recuperar uma imagem do mapa

O Azure Mapas fornece uma API para renderizar as imagens de mapa estático contendo dados sobrepostos. A API [Renderização de imagem do mapa](/rest/api/maps/render/getmapimagerytile) dos Azure Mapas é comparável à API de mapa estático do Google Maps.

> [!NOTE]
> Os Azure Mapas exigem que o centro, todas as localizações do marcador e do caminho sejam coordenadas no formato "longitude, latitude". Por outro lado, o Google Maps usa o formato "latitude, longitude". Os endereços precisarão ser geocodificados primeiro.

A tabela faz uma referência cruzada dos parâmetros da API do Google Maps com os parâmetros comparáveis da API dos Azure Mapas.

| Parâmetro da API do Google Maps | Parâmetro comparável da API do Azure Mapas  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – especificado como parte do caminho da URL. No momento, há suporte apenas para PNG. |
| `key`                       | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md). |
| `language`                  | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).  |
| `maptype`                   | `layer` e `style` – confira a documentação de [Estilos de mapa compatíveis](supported-map-styles.md). |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* – esse é um recurso relacionado a geocódigo. Use o parâmetro `countrySet` ao usar a API de geocódigo do Azure Mapas.  |
| `scale`                     | *N/A*                              |
| `size`                      | `width` e `height` – pode ter um tamanho de até 8192x8192. |
| `style`                     | *N/A*                              |
| `visible`                   | *N/A*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> No sistema de peças dos Azure Mapas, as peças são o dobro do tamanho das peças de mapa usadas no Google Maps. Assim, o valor do nível de zoom no Azure Mapas aparecerá um nível de zoom mais próximo no Azure Mapas em comparação ao Google Maps. Para compensar essa diferença, reduza o nível de zoom nas solicitações que você está migrando.

Para obter mais informações, confira o [Guia de instruções sobre a API de renderização de imagem do mapa](how-to-render-custom-data.md).

Além de poder gerar uma imagem de mapa estático, o serviço de renderização dos Azure Mapas permite acessar diretamente as peças de mapa no formato de varredura (PNG) e vetor:

- [**Bloco de mapa**](/rest/api/maps/render/getmaptile): Recupere blocos de varredura (PNG) e de vetor para os mapas base (estradas, limites, segundo plano).
- [**Bloco de imagens do mapa**](/rest/api/maps/render/getmapimagerytile): Recupere blocos de imagem aérea e de satélite.

> [!TIP]
> Muitos aplicativos do Google Maps mudaram de experiências de mapa interativo para imagens de mapa estático há alguns anos. Isso foi feito como um método de redução de custos. Nos Azure Mapas, geralmente é mais econômico usar o controle de mapa interativo no SDK da Web. O controle de mapa interativo é cobrado de acordo com o número de cargas de peça. As peças de mapa nos Azure Mapas são grandes. Geralmente, são necessárias apenas algumas peças para recriar a mesma exibição de mapa como um mapa estático. As peças de mapa são armazenadas em cache automaticamente pelo navegador. Assim, o controle de mapa interativo normalmente gera uma fração de uma transação ao reproduzir uma exibição de mapa estático. A panorâmica e o zoom carregarão mais peças; no entanto, há opções no controle de mapa para desabilitar esse comportamento. O controle de mapa interativo também fornece muito mais opções de visualização do que os serviços de mapa estático.

### <a name="marker-url-parameter-format-comparison"></a>Comparação de formato de parâmetro de URL do marcador

**Antes: Google Maps**

Adicione marcadores usando o parâmetro `markers` na URL. O parâmetro `markers` usa um estilo e uma lista de locais a serem renderizados no mapa com esse estilo, conforme mostrado abaixo:

```text
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Para adicionar outros estilos, use os parâmetros `markers` para a URL com um estilo e um conjunto de localizações diferentes.

Especifique as localizações de marcador com o formato "latitude, longitude".

Adicione estilos de marcador com o formato `optionName:value`, com vários estilos separados por caracteres de barra vertical (\|) da seguinte maneira: "optionName1:value1\|optionName2:value2". Observe que os nomes e os valores de opção são separados por dois-pontos (:). Use os seguintes nomes de opções de estilo para marcadores de estilo no Google Maps:

* `color` – a cor do ícone do marcador padrão. Pode ser uma cor hexadecimal de 24 bits (`0xrrggbb`) ou um dos valores a seguir: `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
* `label` – um caractere alfanumérico maiúsculo para exibir na parte superior do ícone.
* `size` – o tamanho do marcador. Pode ser `tiny`, `mid` ou `small`.

Use os seguintes nomes de opções de estilo para ícones personalizados no Google Maps:

* `anchor` – especifica como alinhar a imagem do ícone à coordenada. Pode ser um valor de pixel (x,y) ou um dos valores a seguir: `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft` ou `bottomright`.
* `icon` – uma URL que aponta para a imagem do ícone.

Por exemplo, vamos adicionar um marcador vermelho de tamanho médio ao mapa em longitude: -110, latitude: 45:

```text
&markers=color:red|size:mid|45,-110
```

![Marcador do Google Maps](media/migrate-google-maps-web-services/google-maps-marker.png)

**Depois: Azure Mapas**

Adicione marcadores a uma imagem de mapa estático especificando o parâmetro `pins` na URL. Como o Google Maps, especifique um estilo e uma lista de localizações no parâmetro. O parâmetro `pins` pode ser especificado várias vezes para dar suporte a marcadores com estilos diferentes.

```text
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Para usar outros estilos, adicione parâmetros `pins` extras à URL com um estilo e um conjunto de localizações diferentes.

Nos Azure Mapas, a localização do marcador precisa estar no formato "longitude latitude". O Google Maps usa o formato "latitude, longitude". Um espaço, não uma vírgula, separa a longitude e a latitude no formato dos Azure Mapas.

O `iconType` especifica o tipo de marcador a ser criado. Pode ter um dos valores a seguir:

* `default` – o ícone de marcação padrão.
* `none` – nenhum ícone é exibido, somente os rótulos serão renderizados.
* `custom` – especifica que um ícone personalizado deve ser usado. Uma URL que aponta para a imagem do ícone pode ser adicionada ao final do parâmetro `pins` após as informações de localização do marcador.
* `{udid}` – uma UDID (ID de dados exclusiva) para um ícone armazenado na plataforma de armazenamento de dados do Azure Mapas.

Adicione estilos de marcador com o formato `optionNameValue`. Separe vários estilos com os caracteres de barra vertical (\|). Por exemplo: `iconType|optionName1Value1|optionName2Value2`. Os nomes e os valores de opções não são separados. Use os seguintes nomes de opções de estilo para marcadores de estilo:

* `al` – especifica a opacidade (alfa) do marcador. Escolha um número entre 0 e 1.
* `an` – especifica a âncora do marcador. Especifique valores de pixel x e y no formato "x y".
* `co` – a cor do marcador. Especifique uma cor hexa de 24 bits: `000000` a `FFFFFF`.
* `la` – especifica a âncora do rótulo. Especifique valores de pixel x e y no formato "x y".
* `lc` – a cor do rótulo. Especifique uma cor hexa de 24 bits: `000000` a `FFFFFF`.
* `ls` – o tamanho do rótulo em pixels. Escolha um número maior que 0.
* `ro` – um valor em graus para girar o ícone. Escolha um número entre -360 e 360.
* `sc` – um valor de escala para o ícone de marcador. Escolha um número maior que 0.

Especifique valores de rótulo para cada localização do marcador. Essa abordagem é mais eficiente do que aplicar um só valor de rótulo a todos os marcadores na lista de localizações. O valor do rótulo pode ser uma cadeia de caracteres com vários caracteres. Coloque a cadeia de caracteres entre aspas simples para garantir que ela não seja confundida com um valor de estilo ou de localização.

Vamos adicionar um ícone padrão vermelho (`FF0000`), com o rótulo "Space Needle", posicionado abaixo (15 50). O ícone está na longitude: -122,349300, latitude: 47,620180:

```text
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

![Marcador do Azure Mapas](media/migrate-google-maps-web-services/azure-maps-marker.png)

Adicione três marcadores com os valores de rótulo '1', '2' e '3':

```text
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

![Vários marcadores dos Azure Mapas](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)

### <a name="path-url-parameter-format-comparison"></a>Comparação de formato de parâmetro de URL do caminho

**Antes: Google Maps**

Adicione linhas e polígonos a uma imagem de mapa estático usando o parâmetro `path` na URL. O parâmetro `path` usa um estilo e uma lista de localizações a serem renderizados no mapa, conforme mostrado abaixo:

```text
&path=pathStyles|pathLocation1|pathLocation2|...
```

Use outros estilos adicionando parâmetros `path` extras à URL com um estilo e um conjunto de localizações diferentes.

As localizações de caminho são especificadas com o formato `latitude1,longitude1|latitude2,longitude2|…`. Os caminhos podem ser codificados ou conter endereços para pontos.

Adicione estilos de caminho com o formato `optionName:value` e separe vários estilos pelos caracteres de barra vertical (\|). Separe nomes e valores de opções com dois-pontos (:). Desta forma: `optionName1:value1|optionName2:value2`. Os seguintes nomes de opção de estilo podem ser usados para caminhos de estilo no Google Maps:

* `color` – a cor do caminho ou do contorno do polígono. Pode ser uma cor hexa de 24 bits (`0xrrggbb`), uma cor hexa de 32 bits (`0xrrggbbbaa`) ou um dos seguintes valores: preto, marrom, verde, roxo, amarelo, azul, cinza, laranja, vermelho ou branco.
* `fillColor` – a cor com a qual preencher a área de caminho (polígono). Pode ser uma cor hexa de 24 bits (`0xrrggbb`), uma cor hexa de 32 bits (`0xrrggbbbaa`) ou um dos seguintes valores: preto, marrom, verde, roxo, amarelo, azul, cinza, laranja, vermelho ou branco.
* `geodesic` – indica se o caminho deve ser uma linha que segue a curvatura da terra.
* `weight` – a espessura da linha do caminho em pixels.

Adicione uma opacidade de linha vermelha e uma espessura de pixel ao mapa entre as coordenadas no parâmetro de URL. No exemplo abaixo, a linha tem uma opacidade de 50% e uma espessura de quatro pixels. As coordenadas são longitude: -110, latitude: 45 e longitude: -100, latitude: 50.

```text
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

![Polilinha do Google Maps](media/migrate-google-maps-web-services/google-maps-polyline.png)

**Depois: Azure Mapas**

Adicione linhas e polígonos a uma imagem de mapa estático especificando o parâmetro `path` na URL. Como o Google Maps, especifique um estilo e uma lista de localizações nesse parâmetro. Especifique o parâmetro `path` várias vezes para renderizar vários círculos, linhas e polígonos com estilos diferentes.

```text
&path=pathStyles||pathLocation1|pathLocation2|...
```

Quando se trata de localizações de caminho, os Azure Mapas exigem que as coordenadas estejam no formato "longitude latitude". O Google Maps usa o formato "latitude, longitude". Um espaço, não uma vírgula, separa a longitude e a latitude no formato dos Azure Mapas. Os Azure Mapas não dão suporte a caminhos ou endereços codificados em pontos. Carregue conjuntos de dados maiores como um arquivo GeoJSON na API de Armazenamento de Dados dos Azure Mapas, conforme documentado [aqui](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Adicione estilos de caminho com o formato `optionNameValue`. Separe vários estilos por caracteres de barra vertical (\|) da seguinte maneira: `optionName1Value1|optionName2Value2`. Os nomes e os valores de opções não são separados. Use os seguintes nomes de opções de estilo para caminhos de estilo nos Azure Mapas:

* `fa` – a opacidade da cor de preenchimento (alfa) usada ao renderizar polígonos. Escolha um número entre 0 e 1.
* `fc` – a cor de preenchimento usada para renderizar a área de um polígono.
* `la` – a linha de opacidade (alfa) usada ao renderizar linhas e o contorno de polígonos. Escolha um número entre 0 e 1.
* `lc` – a cor da linha usada para renderizar linhas e o contorno de polígonos.
* `lw` – a largura da linha em pixels.
* `ra` – especifica um raio de círculos em metros.

Adicione uma opacidade de linha vermelha e uma espessura de pixel entre as coordenadas no parâmetro de URL. No exemplo abaixo, a linha tem uma opacidade de 50% e uma espessura de quatro pixels. As coordenadas têm os seguintes valores: longitude: -110, latitude: 45 e longitude: -100, latitude: 50.

```text
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

![Polilinha do Azure Mapas](media/migrate-google-maps-web-services/azure-maps-polyline.png)

## <a name="calculate-a-distance-matrix"></a>Calcular uma matriz de distância

Os Azure Mapas fornecem a API de matriz de distância. Use essa API para calcular os tempos de viagem e as distâncias entre um conjunto de localizações, com uma matriz de distância. Ela é comparável à API de matriz de distância do Google Maps.

- [**Matriz de rota**](/rest/api/maps/route/postroutematrixpreview): calcula de maneira assíncrona os tempos de viagem e as distâncias de um conjunto de origens e destinos. Dá suporte a até 700 células por solicitação. Esse é o número de origens multiplicado pelo número de destinos. Com essa restrição em mente, exemplos de possíveis dimensões de matriz são: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Uma solicitação para a API de matriz de distância só pode ser feita usando uma solicitação POST com as informações de origem e destino no corpo da solicitação. Além disso, o Azure Mapas requer que todas as origens e destinos sejam coordenadas. Os endereços precisarão ser geocodificados primeiro.

Esta tabela faz uma referência cruzada dos parâmetros da API do Google Maps com os parâmetros comparáveis da API dos Azure Mapas.

| Parâmetro da API do Google Maps      | Parâmetro comparável da API do Azure Mapas  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – especifique no corpo da solicitação POST como GeoJSON. |
| `key`                          | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md). |
| `language`                     | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – especifique no corpo da solicitação POST como GeoJSON.  |
| `region`                       | *N/A*: este recurso está relacionado ao geocódigo. Use o parâmetro `countrySet` ao usar a API de geocódigo do Azure Mapas. |
| `traffic_model`                | *N/A* – só é possível especificar se os dados de tráfego devem ser usados com o parâmetro `traffic`. |
| `transit_mode`                 | *N/A*: no momento, não há suporte para matrizes de distância baseadas em trânsito.  |
| `transit_routing_preference`   | *N/A*: no momento, não há suporte para matrizes de distância baseadas em trânsito.  |
| `units`                        | *N/A* – o Azure Mapas usa apenas o sistema métrico. |

> [!TIP]
> Todas as opções avançadas de roteiros disponíveis na API de roteiros dos Azure Mapas são compatíveis com a API de matriz de distância dos Azure Mapas. As opções avançadas de roteiros incluem: roteiros de caminhão, especificações do motor etc.

Examine a documentação de [melhores práticas de roteamento](how-to-use-best-practices-for-routing.md).

## <a name="get-a-time-zone"></a>Obter um fuso horário

Os Azure Mapas fornecem uma API para recuperar o fuso horário de uma coordenada. A API de fuso horário do Azure Mapas é comparável à API de fuso horário no Google Maps:

- [**Fuso horário por coordenada**](/rest/api/maps/timezone/gettimezonebycoordinates): especifique uma coordenada e receba os detalhes de fuso horário da coordenada.

Esta tabela faz uma referência cruzada dos parâmetros da API do Google Maps com os parâmetros comparáveis da API dos Azure Mapas.

| Parâmetro da API do Google Maps | Parâmetro comparável da API do Azure Mapas   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md).       |
| `language`                  | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Além dessa API, os Azure Mapas fornecem várias APIs de fuso horário. Essas APIs convertem a hora com base nos nomes ou nas IDs do fuso horário:

- [**Fuso horário por ID**](/rest/api/maps/timezone/gettimezonebyid): Retorna informações de fuso horário atuais, históricas e futuras para a ID de fuso horário do IANA especificada.
- [**IANA de enumeração de fuso horário**](/rest/api/maps/timezone/gettimezoneenumiana): Retorna uma lista completa de IDs de fuso horário do IANA. As atualizações ao serviço IANA são refletidas no sistema dentro de um dia.
- [**Enumeração de fuso horário no Windows**](/rest/api/maps/timezone/gettimezoneenumwindows): Retorna uma lista completa de IDs de fuso horário do Windows.
- [**Fuso horário versão IANA**](/rest/api/maps/timezone/gettimezoneianaversion): Retorna o número de versão atual do IANA usado pelo Azure Mapas.
- [**Fuso horário Windows para IANA**](/rest/api/maps/timezone/gettimezonewindowstoiana): Retorna uma ID do IANA correspondente, dada uma ID de fuso horário do Windows válida. Várias IDs do IANA podem ser retornadas para uma ID do Windows individual.

## <a name="client-libraries"></a>Bibliotecas de cliente

O Azure Mapas fornece bibliotecas de cliente para as seguintes linguagens de programação:

* JavaScript, TypeScript, Node.js – [documentação](how-to-use-services-module.md) \| [pacote NPM](https://www.npmjs.com/package/azure-maps-rest)

Estas bibliotecas de clientes open-source destinam-se a outras linguagens de programação:

* .NET Standard 2.0 – [projeto GitHub](https://github.com/perfahlen/AzureMapsRestServices) \| [pacote NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="clean-up-resources"></a>Limpar recursos

Não há recursos a serem limpos.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os serviços REST do Azure Mapas:

> [!div class="nextstepaction"]
> [Melhores práticas para pesquisar](how-to-use-best-practices-for-search.md)
