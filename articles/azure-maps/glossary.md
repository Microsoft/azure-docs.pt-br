---
title: Glossário do Azure Mapas | Microsoft Docs
description: Glossário dos termos comumente usados associados ao Azure Mapas, serviços baseados na localização e GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 11e49d362508e9720cf65fc20e020930b5f5c3ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769831"
---
# <a name="glossary"></a>Glossário

O exemplo a seguir é uma lista de palavras comuns usadas com mapas do Azure Mapas.

## <a name="a"></a>O 

<a name="address-validation"></a> **Validação de endereço**: processo de verificar a existência de um endereço.

<a name="advanced-routing"></a> **Roteamento avançado**: uma coleção de serviços que executam operações avançadas usando dados de roteamento de estrada, como calcular intervalos acessíveis (isócronos), matrizes de distância e solicitações de roteamento em lote.

<a name="aerial-imagery"></a> **Imagem aérea**: Ver [Imagens de satélite](#satellite-imagery). 

<a name="along-a-route-search"></a> **Pesquisa ao longo da rota**: uma consulta espacial que procura por dados que estão dentro de um tempo de desvio especificado ou de uma distância de caminho de rota.

<a name="altitude"></a> **Altitude**: a altura ou a elevação vertical de um ponto acima de uma superfície de referência. Medidas de altitude são baseadas no dado referência fornecida, como o nível médio do mar. Consulte também elevação.

<a name="ambiguous"></a> **Ambíguo**: estado de incerteza na classificação de dados que existe quando um objeto pode receber adequadamente dois ou mais valores para determinado atributo. Por exemplo, geocodificação "CA" dois resultados ambíguos são retornados; "Canadá" e "Califórnia" como "CA" é um código de país e estado para cada um, respectivamente. 

<a name="annotation"></a> **Anotação**: texto ou elementos gráficos exibidos no mapa para fornecer informações ao usuário. A anotação pode identificar ou descrever uma entidade de mapa específico, fornecer informações gerais sobre uma área do mapa ou fornecer informações sobre o mapa em si.

<a name="antimeridian"></a> **Antimeridiano**: também conhecido como Meridiano 180<sup>º</sup>, é o ponto onde se encontram -180 graus e 180 graus de longitude. Que é o oposto do primeiro meridiano no globo.

<a name="application-programming-interface-api"></a> **Interface de programação de aplicativo (API)**: uma especificação que permite aos desenvolvedores criar aplicativos.

<a name="api-key"></a> **Chave de API**: Confira Chave do Azure Mapas.

<a name="area-of-interest-aoi"></a> **Área de Interesse (AOI)**: a extensão usada para definir uma área de foco para a produção de um mapa ou de um banco de dados.

<a name="asset-tracking"></a> **Acompanhamento de ativos**: processo de controlar o local de um ativo, como uma pessoa, veículo ou algum outro objeto.

<a name="asynchronous-request"></a> **Solicitação assíncrona**: solicitação HTTP que abre uma conexão e faz uma solicitação para o servidor que retorna um identificador para a solicitação assíncrona e, em seguida, encerra a conexão. O servidor continua a processar a solicitação e o usuário pode verificar o status usando o identificador. Quando a solicitação terminar o processamento, o usuário pode então fazer o download da resposta. Esse tipo de solicitação normalmente é usado por muito tempo processos em execução.

<a name="autocomplete"></a> **Preenchimento automático**: recurso em um aplicativo que prevê o restante de uma palavra que um usuário está digitando. 

<a name="autosuggest"></a> **Sugestão automática**: recurso em um aplicativo que prevê possibilidades lógicas para o que o usuário está digitando.

<a name="azure-location-based-services-lbs"></a> **Serviços do azure baseados na localização (LBS)**: nome anterior do Azure Mapas quando estava em versão prévia.

<a name="azure-maps-key"></a> **Chave do Azure Mapas**: uma Chave do Azure Mapas é uma cadeia de caracteres exclusiva usada para autenticar a solicitação de serviço ou aplicativo do Azure Mapas de um usuário. 

## <a name="b"></a>b

<a name="base-map"></a> **Mapa base**: parte de um aplicativo de mapa que exibe informações de referência, como estradas, pontos de referência e fronteiras políticas.

<a name="batch-request"></a> **Solicitação em lote**: processo de combinar várias solicitações em uma única solicitação.

<a name="bearing"></a> **Ângulo de auxílio**: direção horizontal de um ponto em relação a outro ponto. Isso é expresso como um ângulo em relação ao norte de 0 a 360 graus no sentido horário. 

<a name="boundary"></a> **Limite**: uma linha ou polígono que separa entidades políticas adjacentes, como países, distritos e propriedades. Um limite é uma linha que pode ou não pode seguir características físicas, como rios, montanhas ou muros.

<a name="bounds"></a> **Limites**: Veja [Caixa delimitadora](#bounding-box).

<a name="bounding-box"></a> **Caixa delimitadora**: um conjunto de coordenadas usado para representar uma área retangular no mapa. 

## <a name="c"></a>C

<a name="cadastre"></a> **Cadastro**: registro de terra e propriedades registradas. Consulte também [Lote](#parcel).

<a name="camera"></a> **Câmera**: no contexto de um controle de mapa interativo, uma câmera define o campo de visualização dos mapas. O visor da câmera é determinado com base em vários parâmetros de mapa; centro, nível de zoom, densidade, influência. 

<a name="centroid"></a> **Centroide**: o centro geométrico de um recurso. O centroide de uma linha seria o ponto médio, enquanto o centroide de um polígono seria seu centro da área.

<a name="choropleth-map"></a> **Mapa coroplético**: um mapa temático em que as áreas são sombreadas em proporção a uma medida de uma variável estatística que está sendo exibida no mapa. Por exemplo, colorir o limite de cada estado dos EUA com base em sua população relativa a todos os outros estados.

<a name="concave-hull"></a> **Envoltória côncava**: forma que representa uma geometria côncava possível que inclui todas as formas no conjunto de dados especificado. A forma gerada é semelhante ao encapsulamento de dados com encapsulamento de plástico e, em seguida, aquecendo, assim causando extensões grandes entre os pontos para cavar em direção aos outros pontos de dados.

<a name="consumption-model"></a> **Modelo de consumo**: informações que definem a taxa em que um veículo consome combustível ou eletricidade. Consulte também a [documentação do modelo de consumo](consumption-model.md).

<a name="control"></a> **Controle**: um componente independente ou reutilizável que consiste em uma interface gráfica do usuário que define um conjunto de comportamentos para a interface. Por exemplo, um controle de mapa, geralmente é a parte da interface do usuário que carrega um mapa interativo.

<a name="convex-hull"></a> **Envoltória convexa**: forma que representa uma geometria convexa possível que inclui todas as formas no conjunto de dados especificado. A forma gerada é semelhante ao encapsulamento de uma banda elástica ao torno do conjunto de dados.

<a name="coordinate"></a> **Coordenada**: consiste nos valores de longitude e latitude usados para representar um local em um mapa.

<a name="coordinate-system"></a> **Sistema de coordenadas**: uma estrutura de referência usada para definir as posições dos pontos no espaço em duas ou três dimensões.

<a name="country-code"></a> **Código do país**: identificador exclusivo para um país com base no padrão ISO. ISO2 é um código de dois caracteres para um país (por exemplo, EUA), que representa um código de três caracteres ISO3 (por exemplo, EUA).

<a name="country-subdivision"></a> **Subdivisão de país**: uma subdivisão de primeiro nível de um país, comumente conhecida como estado ou província.

<a name="country-secondary-subdivision"></a> **Subdivisão de país secundária**: uma subdivisão de segundo nível de um país, geralmente conhecida como região.

<a name="country-tertiary-subdivision"></a> **Subdivisão de país terciária**: uma subdivisão de terceiro nível de um país, normalmente uma área nomeada, como um bairro.

<a name="cross-street"></a> **Cruzamento**: ponto de interseção de duas ou mais ruas.

<a name="cylindrical-projection"></a> **Projeção cilíndrica**: uma projeção que transforma os pontos de um esferoide ou esfera em um cilindro tangente ou secante. O cilindro, em seguida, é dividido de cima para baixo e combinado em um plano.

## <a name="d"></a>D

<a name="datum"></a> **Dado**: as especificações de referência de uma sistema de medida, um sistema de posições de coordenadas em uma superfície (um dado horizontal) ou alturas acima ou abaixo da superfície (um dado vertical).

<a name="dbf-file"></a> **Arquivo DBF**: um formato de arquivo de banco de dados usado em combinação com Shapefiles (SHP).

<a name="degree-minutes-seconds-dms"></a> **Grau Minuto Segundo (DMS)**: unidade de medida para descrever a latitude e a longitude. Um grau é 1/360<sup>º</sup> de um círculo. Um grau é dividido em 60 minutos e um minuto é dividido em 60 segundos.

<a name="delaunay-triangulation"></a> **Triangulação de Delaunay**: técnica para criar uma malha de triângulos contíguos e não sobrepostos de um conjunto de dados de pontos. O círculo circunscrito de cada triângulo não contém pontos do conjunto de dados em seu interior.

<a name="demographics"></a> **Dados demográficos**: características estatísticas (como idade, taxa de nascimento e renda) de uma população humana.

<a name="destination"></a> **Destino**: ponto ou o local para onde alguém está viajando.

<a name="digital-elevation-model-dem"></a> **Modelo de elevação digital (DEM)**: conjunto de dados de valores de elevação relacionados a uma superfície, capturados ao longo de uma área em intervalos regulares usando um dado em comum. DEMs normalmente são usados para representar alívio de terreno.

<a name="dijkstra's-algorithm"></a> **Algoritmo de Dijkstra**: algoritmo que examina a conectividade de uma rede para localizar o caminho mais curto entre dois pontos.

<a name="distance-matrix"></a> **Matriz de distância**: uma matriz que contém informações de tempo e distância de viagem entre um conjunto de origens e destinos. 

## <a name="e"></a>E

<a name="elevation"></a> **Elevação**: distância vertical de um ponto ou um objeto acima ou abaixo de uma superfície de referência ou dado (geralmente, o nível do mar). A elevação geralmente se refere à altura vertical de terra.

<a name="envelope"></a> **Envelope**: Veja [Caixa delimitadora](#bounding-box).

<a name="extended-postal-code"></a> **Código postal estendido**: código postal que pode incluir informações adicionais. Por exemplo, nos EUA, os códigos postais têm cinco dígitos, mas um código postal estendido, conhecido como zip + 4, incluirá quatro dígitos adicionais. Esses dígitos adicionais são usados para identificar um segmento geográfico dentro da área de entrega de cinco dígitos, como um bloco de cidade, um grupo de apartmentos ou uma caixa postal, que auxilia na classificação de mensagens eficiente e entrega.

<a name="extent"></a> **Extensão**: Veja [Caixa delimitadora](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a> **Autenticação federada**: método de autenticação que permite que um único logon/mecanismo de autenticação seja usado em vários aplicativos Web e móveis. 

<a name="feature"></a> **Recurso**: objeto que combina uma geometria com informações de metadados adicionais. 

<a name="feature-collection"></a> **Coleção de recursos**: coleção de objetos de recurso.

<a name="find-along-route"></a> **Encontrar ao longo da rota**: uma consulta espacial que procura por dados que estão dentro de um tempo de desvio especificado ou de uma distância de caminho de rota.

<a name="find-nearby"></a> **Localizar próximo**: uma consulta espacial que procura uma distância linear fixa (em linha reta) a partir de um ponto.

<a name="fleet-management"></a> **Gerenciamento de frota**: gerenciamento de veículos comerciais, como carros, caminhões, navios e aviões. A gestão de frotas pode incluir uma variedade de funções, como financiamento de veículo, manutenção, telemática (rastreamento e diagnóstico) bem como driver, velocidade, combustível, gerenciamento de segurança e integridade. Gestão de frotas é um processo usado por empresas que dependem de transporte em seus negócios para minimizar os riscos e reduzir os custos gerais de transporte e da equipe, garantindo a conformidade com a legislação do governo.

<a name="free-flow-speed"></a> **Velocidade de fluxo livre**: a velocidade do fluxo livre esperada em condições ideais. Normalmente, o limite de velocidade.

<a name="free-form-address"></a> **Endereço de forma livre**: um endereço completo representado como uma única linha de texto.

<a name="fuzzy-search"></a> **Pesquisa difusa**: pesquisa que usa uma cadeia de caracteres de formato livre de texto que pode ser um endereço ou ponto de interesse. 

## <a name="g"></a>G

<a name="geocode"></a> **Código geográfico**: endereço ou local que foi convertido em uma coordenada que pode ser usada para exibir o local em um mapa. 

<a name="geocoding"></a> **Geocodificação**: também conhecido como geocodificação de encaminhamento, é o processo de converter um endereço de dados de localização em coordenadas. 

<a name="geodesic-path"></a> **Caminho geodésico**: o caminho mais curto entre dois pontos em uma superfície curva. Quando renderizados no Azure Mapas esse caminho é exibido como uma linha curva devido a projeção Mercator.

<a name="geofence"></a> **Cerca geográfica**: uma região geográfica definida que pode ser usada para disparar eventos quando um dispositivo entra ou existe na região.

<a name="geojson"></a> **GeoJSON**: formato comum de arquivo baseado em JSON usado para armazenar dados de vetor geográficos, como pontos, linhas e polígonos. **Observação**: o Azure Mapas usa uma versão estendida do GeoJSON como [documentado aqui](extend-geojson.md).

<a name="geometry"></a> **Geometria**: representa um objeto espacial, como um ponto, linha ou polígono.

<a name="geometrycollection"></a> **GeometryCollection**: coleção de objetos de geometria.

<a name="geopol"></a> **GeoPol**: refere-se a dados confidenciais geopoliticamente, como fronteiras disputadas e nomes de locais.

<a name="georeference"></a> **Georeferência**: processo de alinhar dados geográficos ou imagens a um sistema de coordenadas conhecido. Esse processo pode consistir em mudança, girar, dimensionar ou distorcer os dados.

<a name="georss"></a> **GeoRSS**: extensão de um XML para adicionar dados espaciais ao RSS feeds.

<a name="gis"></a> **GIS**: abreviação em inglês de "Sistema de Informações Geográficas". Termo comum usado para descrever o setor de mapeamento.

<a name="gml"></a> **GML**: também conhecido como Linguagem de Marcação de Geografia. Uma extensão de arquivo XML para armazenar dados espaciais.

<a name="gps"></a> **GPS**: também conhecido como Global Positioning System, é um sistema de satélites usado para determinar a posição de dispositivos na Terra. Os satélites em órbita transmiten sinais que um receptor GPS em qualquer lugar na Terra calcula seu próprio local por meio de trilateração.

<a name="gpx"></a> **GPX**: também conhecido como formato GPS eXchange, é um formato de arquivo XML normalmente criado a partir de dispositivos GPS.  

<a name="great-circle-distance"></a> **Distância de grande círculo**: distância mais curta entre dois pontos na superfície de uma esfera.

<a name="greenwich-mean-time-gmt"></a> **Horário de Greenwich (GMT)**: hora em que o primeiro meridiano percorre o Observatório Real de Greenwich, na Inglaterra.

<a name="guid"></a> **GUID**: identificador global exclusivo. Uma cadeia de caracteres usada para identificar exclusivamente uma interface, a classe, a biblioteca de tipos, a categoria do componente ou o registro.

## <a name="h"></a>H

<a name="haversine-formula"></a> **Fórmula de Haversine**: uma equação comum usada para calcular a distância de grande círculo entre dois pontos em uma esfera.

<a name="hd-maps"></a> **Mapas HD**: também conhecidos como mapas de alta definição, consistem em informações de rede de estradas de alta fidelidade, como marcações de faixas, sinalização e luzes de direção necessários para uma direção autônoma.

<a name="heading"></a> **Rumo**: direção em que algo está apontando ou direcionado. Consulte também [Influência](#heading).

<a name="heatmap"></a> **Mapa de calor**: visualização de dados em que um intervalo de cores representa a densidade de pontos em determinada área. Consulte também Mapa temático.

<a name="hybrid-imagery"></a> **Imagens híbridas**: imagens de satélite ou aéreas que têm dados de estradas e rótulos sobrepostos.

## <a name="i"></a>I

<a name="iana"></a> **IANA**: acrônimo de Internet Assigned Numbers Authority. Um grupo sem fins lucrativos que supervisiona alocação de endereços IP global.

<a name="isochrone"></a> **Isócrono**: um isócrono define a área em que alguém pode viajar em um tempo especificado para um modo de transporte em qualquer direção a partir de determinado local. Consulte também [Intervalo Acessível](#reachable-range).

<a name="isodistance"></a> **Isodistância**: dada uma localização, um isócrono define a área em que alguém pode viajar dentro de uma distância especificada para um modo de transporte em qualquer direção. Consulte também [Intervalo Acessível](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a> **KML**: também conhecido como Keyhole Markup Language, é um formato de arquivo XML comum para armazenar dados geográficos de vetor, como pontos, linhas e polígonos. 

## <a name="l"></a>L

<a name="landsat"></a> **Landsat**: satélites multiespectrais, que orbitam a Terra e foram desenvolvidos pela NASA, que coletam imagens de Terra usadas em vários setores, como agricultura, silvicultura e cartografia.

<a name="latitude"></a> **Latitude**: a distância angular medida em graus a partir da Linha do equador em uma direção norte ou sul.

<a name="level-of-detail"></a> **Nível de detalhe**: Veja Nível de zoom.

<a name="lidar"></a> **Lidar**: acrônimo em inglês para detecção e variação de luz. Uma técnica de detecção remota que usa lasers para medir a distância para superfícies refletivas.

<a name="linear-interpolation"></a> **Interpolação linear**: estimativa de um valor desconhecido usando a distância linear entre os valores conhecidos.

<a name="linestring"></a> **LineString**: uma geometria usada para representar uma linha. Também conhecida como uma polilinha. 

<a name="localization"></a> **Localização**: suporte para diferentes idiomas e culturas.

<a name="logistics"></a> **Logística**: processo de mover as pessoas, veículos, suprimentos ou ativos de maneira coordenada.

<a name="longitude"></a> **Longitude**: distância angular medida em graus a partir do Meridiano primário em uma direção leste ou oeste.

## <a name="m"></a>M

<a name="map-tile"></a> **Peça de mapa**: uma imagem retangular que representa uma partição de uma tela de mapa. Para obter mais informações, consulte [Níveis de Zoom e grade de peças](zoom-levels-and-tile-grid.md).

<a name="marker"></a> **Marcador**: também conhecido como pin ou pino, é um ícone que representa um local específico em um mapa.

<a name="mercator-projection"></a> **Projeção de Mercator**: uma projeção cilíndrica de mapa que se tornou a projeção de mapa padrão para fins náuticos por causa de sua capacidade de representar linhas de curso constante, conhecidas como linhas rhumb, como segmentos retos que conservam os ângulos com os meridianos. Todas as projeções de mapa simples distorcem as formas ou os tamanhos do mapa quando comparado com o layout verdadeiro da superfície da Terra. A projeção Mercator exagera áreas longe do Equador, de modo que as áreas pequenas pareçam maiores no mapa de como você aborda os polos. 

<a name="multilinestring"></a> **MultiLineString**: uma geometria que representa uma coleção de objetos de LineString. 

<a name="multipoint"></a> **MultiPoint**: uma geometria que representa uma coleção de objetos de ponto.

<a name="multipolygon"></a> **MultiPolygon**: uma geometria que representa uma coleção de objetos de polígono. Por exemplo, para mostrar a fronteira do Havaí, cada ilha poderia ser descrita com um polígono e o limite do Havaí, portanto, seria um MultiPolygon.

<a name="municipality"></a> **Município**: uma cidade ou município. 

<a name="municipality-subdivision"></a> **Subdivisão de município**: uma subdivisão de um Município, como um ambiente ou o nome de rede local, como "centro".

## <a name="n"></a>N

<a name="navigation-bar"></a> **Barra de navegação**: conjunto de controles em um mapa usado para ajustar o nível de zoom, inclinação, rotação e alternar a camada do mapa base.

<a name="nearby-search"></a> **Pesquisar por perto**: uma consulta espacial que procura uma distância linear fixa (em linha reta) a partir de um ponto.

<a name="neutral-ground-truth"></a> **Neutral Ground Truth**: mapa que renderiza os rótulos em idioma oficial da região em que ele representa em scripts locais, se disponível.

## <a name="o"></a>O

<a name="origin"></a> **Origem**: ponto de partida ou local em que um usuário está.

## <a name="p"></a>P

<a name="panning"></a> **Movimento panorâmico**: processo de mover o mapa em qualquer direção, mantendo um nível de zoom constante.

<a name="parcel"></a> **Lote**: um pedaço de terra ou limite de propriedade.

<a name="pitch"></a> **Inclinação**: quantidade de inclinação do mapa em relação à vertical, em que 0 apresenta a vista de cima do mapa.

<a name="point"></a> **Ponto**: geometria que representa uma única posição no mapa. 

<a name="points-of-interest-poi"></a> **Pontos de interesse (POI)**: uma empresa, ponto de referência ou local comum de interesse.

<a name="polygon"></a> **Polígono**: uma geometria sólida que representa uma área em um mapa. 

<a name="polyline"></a> **Linha poligonal**: uma geometria usada para representar uma linha. Também conhecida como um LineString. 

<a name="position"></a> **Posição**: longitude, latitude e altitude (x, y, z de coordenadas) de um ponto.

<a name="post-code"></a> **Código de postagem**: Veja [Código postal](#postal-code).

<a name="postal-code"></a> **Código postal**: uma série de letras, números ou ambos, em um formato específico, usado pelo serviço postal de um país para dividir as áreas geográficas em zonas para simplificar a entrega de correspondência.

<a name="prime-meridian"></a> **Meridiano primário**: linha de longitude que representa 0 graus de longitude. Em geral, os valores de longitude diminuem durante uma viagem em uma direção a oeste de até 180 graus e aumentam ao viajar em direções a leste para -180-graus. 

<a name="prj"></a> **PRJ**: conjunto de dados de um arquivo de texto que frequentemente acompanha um arquivo de forma que contém informações sobre o sistema de coordenadas projetadas.

<a name="projection"></a> **Projeção**: sistema de coordenadas projetado com base em uma projeção de mapa como área igual de Mercator, Albers e Robinson. Fornecem a capacidade de mapas de projeto de superfície esférica da Terra em um plano de coordenadas bidimensional de cartesianas. Sistemas de coordenadas projetados, às vezes, são chamados de projeções de mapa.

## <a name="q"></a>Q

<a name="quadkey"></a> **Quadkey**: índice de endereço de 4 bases para uma peça em um sistema de mosaico quadtree. Para obter mais informações, consulte [Níveis de Zoom e peça de blocos](zoom-levels-and-tile-grid.md).

<a name="quadtree"></a> **Quadtree**: estrutura de dados em que cada nó tem exatamente quatro filhos. O sistema de lado a lado usado em mapas do Azure Mapas usa uma estrutura de quadtree, de modo que, como um usuário aplica zoom em um nível, divide cada peça de mapa em quatro blocos inferiores.  Para obter mais informações, consulte [Níveis de Zoom e peça de blocos](zoom-levels-and-tile-grid.md).

<a name="queries-per-second-qps"></a> **Consultas por segundo (QPS)**: número de consultas ou solicitações que podem ser feitas a um serviço ou plataforma dentro de um segundo. 

## <a name="r"></a>R

<a name="radial-search"></a> **Pesquisa radial**: uma consulta espacial que procura uma distância linear fixa (em linha reta) a partir de um ponto. 

<a name="raster-data"></a> **Dados de varredura**: matriz de células (ou pixels) organizada em linhas e colunas (ou uma grade) em que cada célula contém um valor que representa informações, como temperatura. Varredura inclui fotografias aéreas digitais, imagens de satélites, imagens digitais e mapas digitalizados.

<a name="raster-layer"></a> **Camada de varredura**: uma camada de peça que consiste em imagens de varredura.

<a name="reachable-range"></a> **Intervalo pesquisável**: um intervalo pesquisável define a área em que alguém pode viajar em um tempo ou distância especificada para um modo de transporte em qualquer direção de um determinado local. Consulte também [Isócrono](#isochrone) e [Isodistância](#isodistance).

<a name="remote-sensing"></a> **Leitura remota**: processo de coletar e interpretar os dados do sensor de uma distância.

<a name="rest-service"></a> **Serviço REST**: o acrônimo em inglês REST significa Transferência de Estado Representacional. Um serviço REST é um serviço web baseado em URL que se baseia na tecnologia web básica para se comunicar, os métodos mais comuns que estão sendo solicitações HTTP GET e POST. Esses tipos de serviços costumam ser muito mais rápidos e menores do que os tradicionais serviços baseados em SOAP.

<a name="reverse-geocode"></a> **Inverter código geográfico**: processo de pegar uma coordenada e determinar o endereço em que ela está representada em um mapa.

<a name="reproject"></a> **Reprojetar**: Veja [Transformação](#transformation).

<a name="rest-service"></a> **Serviço REST**: acrônimo em inglês para Transferência de Estado Representacional. Uma arquitetura para a troca de informações entre pontos em um ambiente distribuído, descentralizado. REST permite que os programas em computadores diferentes se comuniquem independentemente de um sistema operacional ou plataforma, enviando uma solicitação de protocolo HTTP (Hypertext Transfer) para um localizador de recursos uniforme (URL) e obter dados de volta.

<a name="route"></a> **Rota**: um caminho entre dois ou mais locais, que também pode incluir informações adicionais, como instruções para marcos ao longo da rota.

<a name="requests-per-second-rps"></a> **RPS (solicitações por segundo)**: Veja [Consultas por segundo (QPS)](#queries-per-second-qps). 

<a name="rss"></a> **RSS**: acrônimo em inglês de Really Simple Syndication, resumo do Site do Framework de descrição de recurso (RDF) ou Rich Site Summary, dependendo da fonte. Formato XML estruturado simples para o compartilhamento de conteúdo entre diferentes sites da Web. Documentos do RSS incluem elementos de metadados de chave, como autor, data, título, uma breve descrição e um link de hipertexto. Essa informação ajuda um usuário (ou um serviço de editor RSS) a decidir quais materiais que valem a pena mais investigação.

## <a name="s"></a>S

<a name="satellite-imagery"></a> **Imagens de satélite**: imagens capturadas de cima por aviões e satélites.

<a name="software-development-kit-sdk"></a> **Kit de desenvolvimento de software (SDK)**: coleção de documentação, código de exemplo e aplicativos de exemplo para ajudar um desenvolvedor a usar uma API para criar aplicativos.

<a name="shapefile-shp"></a> **Shapefile (SHP)**: também conhecido como um Shapefile ESRI, é um formato de armazenamento de dados de vetor para armazenar o local, forma e atributos de recursos geográficos. Um shapefile é armazenado em um conjunto de arquivos relacionados.

<a name="spherical-mercator-projection"></a> **Projeção de mercator esférico**: Veja [Mercator Web](#web-mercator). 

<a name="spatial-query"></a> **Consulta espacial**: solicitação feita para um serviço que executa uma operação espacial. Como uma pesquisa radial ou ao longo de uma pesquisa de rota.

<a name="spatial-reference"></a> **Referência espacial**: baseada em coordenadas, regional, sistema local ou global usado para localizar entidades geográficas com precisão. Define o sistema de coordenadas usado para relacionar as coordenadas de mapa para locais no mundo real. Referências espaciais garantem que dados espaciais de diferentes camadas ou de fontes podem ser integrados a uma visualização precisa ou análise. O Azure Mapas usa o [EPSG:3857](https://epsg.io/3857) sistema de referência coordenada e o WGS 84 para entrada de dados geométricos. 

<a name="sql-spatial"></a> **SQL espacial**: refere-se à funcionalidade espacial incorporada ao SQL Azure e SQL Server 2008 e versões posteriores. Essa funcionalidade espacial também está disponível como uma biblioteca .NET que pode ser usada independentemente do SQL Server. Para obter mais informações, veja [Documentação do ExpressRoute (Microsoft SQL Server)](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) para obter mais informações.

<a name="synchronous-request"></a> **Solicitação síncrona**: uma solicitação HTTP que abre uma conexão e aguarda resposta. Os navegadores limitam o número de solicitações HTTP simultâneas que podem ser feitas a partir de uma página. Se várias solicitações síncronas de longa execução são feitas ao mesmo tempo, esse limite pode ser acessado e solicitações atrasadas até que uma das solicitações de tenha sido concluída.

## <a name="t"></a>T

<a name="telematics"></a> **Telemática**: enviar, receber e armazenar informações por meio de dispositivos de telecomunicações em conjunto com o controle efetivo sobre objetos remotos. 

<a name="temporal-data"></a> **Dados temporais**: dados que referem-se especificamente a horários ou datas. Dados temporais podem se referir a eventos discretos, como quedas de raios; mover objetos, como trens; ou observações repetidas, como contagens de sensores de tráfego.

<a name="terrain"></a> **Terreno**: uma área de terra que tem uma característica específica, como terreno arenoso ou montanhoso.

<a name="thematic-maps"></a> **Mapas temáticos**: um mapa temático é um mapa simples feito para refletir um tema sobre uma área geográfica. É um cenário comum para esse tipo de mapa colorir as regiões administrativas, como países com base em alguma métrica de dados.

<a name="tile-layer"></a> **Camada de peças**: camada exibida por meio da reunião de peças de mapa (seções retangulares) em uma camada contínua. As peças são qualquer peça de imagem ou peças de vetor. Camadas de peça de varredura normalmente são renderizadas antecipadamente e armazenadas como imagens em um servidor. Isso pode levar até muito espaço de armazenamento. Camadas de peça de vetor são renderizadas em tempo real dentro do aplicativo cliente, assim, os requisitos de armazenamento do lado de servidor são menores.

<a name="time-zone"></a> **Fuso horário**: uma região do mundo que tem um horário padrão uniforme para fins legais, comerciais e sociais. Fusos horários tendem a seguir os limites de países e suas subdivisões.

<a name="transaction"></a> **Transação**: o Azure Mapas usa um modelo de licenciamento transacional em que:

- Uma transação é criada para cada 15 peças de mapa ou tráfego solicitados.
- Uma transação é criada para cada chamada de API para um dos serviços de mapas do Azure Mapas, como roteamento ou pesquisa.

<a name="transformation"></a> **Transformação**: processo de conversão de dados entre diferentes sistemas de coordenadas geográficas. Por exemplo, você terá alguns dados que foram capturados no Reino Unido e com base no sistema de coordenadas geográficas OSGB 1936. O Azure Mapas usa o [EPSG:3857](https://epsg.io/3857) sistema de referência coordenada e o WGS 84 para entrada de dados geométricos. Como tal, para exibir os dados corretamente, precisará ter suas coordenadas transformadas de um sistema para outro.

<a name="traveling-salesmen-problem-tsp"></a> **Problema do Caixeiro Viajante (TSP)**:  um problema de circuito Hamiltoniano em que um vendedor deve localizar a maneira mais eficiente para visitar uma série de limites, em seguida, retornar para o local inicial.  

<a name="trilateration"></a> **Trilateração**: processo de determinar a posição de um ponto na superfície da Terra em relação a dois pontos, medindo as distâncias entre todos os três pontos.

<a name="turn-by-turn-navigation"></a> **Navegação curva a curva**: um aplicativo que fornece instruções de rota para cada etapa de uma rota, à medida que os usuários se aproximam da próxima manobra.

## <a name="v"></a>V

<a name="vector-data"></a> **Dados de vetor**: dados baseados em coordenadas representados como pontos, linhas ou polígonos.

<a name="vector-tile"></a> **Bloco de vetor**: especificação de dados abertos para armazenar dados de vetor geoespaciais usando o mesmo sistema lado a lado como o controle de mapa. Consulte também [camada de peça lado a lado](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a> **Problema de Roteamento de Veículo (VRP)**: uma classe de problemas em que um conjunto de rotas ordenadas para uma frota de veículos é calculado, levando em consideração um conjunto de restrições. Essas restrições podem incluem coisas como janelas de tempo de entrega, várias capacidades de rota e restrições de duração de viagem.

<a name="voronoi-diagram"></a> **Diagrama de Voronoi**: partição de espaço em áreas ou células, que circundam um conjunto de objetos geométricos (geralmente recursos de ponto). Essas células ou polígonos, devem satisfazer os critérios para os triângulos de Delaunay. Todos os locais dentro de uma área são mais próximos para o objeto que ele envolva que para qualquer outro objeto no conjunto. Diagramas de Voronoi geralmente são usados para delinear as áreas de influência em torno de características geográficas. 

## <a name="w"></a>W

<a name="waypoint"></a> **Ponto de passagem**: um ponto de passagem é um local geográfico especificado definido por longitude e latitude que é usado para fins de navegação. Geralmente usado para representar um ponto em que alguém navega por meio de uma rota.

<a name="waypoint-optimization"></a> **Otimização de ponto de passagem**: processo de reorganização de um conjunto de pontos de passagem para minimizar o tempo ou distância de viagem necessário para passar por todos os pontos fornecidos. Também conhecido como o [Problema do Caixeiro Viajante](#traveling-salesmen-problem-tsp) ou [problema de roteamento de veículo](#vehicle-routing-problem-vrp) dependendo da complexidade da otimização.

<a name="web-map-service-wms"></a> **Mapa de serviço (WMS) da Web**: WMS é um padrão de Consórcio Geográfico Aberto (OGC) que define serviços de mapa baseado em imagem. Serviços WMS fornecem imagens de mapa para áreas específicas dentro de um mapa sob demanda. As imagens incluem simbologia pré-renderizada e podem ser renderizadas em um dos vários estilos nomeados se definidas pelo serviço.

<a name="web-mercator"></a> **Mercator Web**: também conhecido como Projeção Esférica de Mercator, é uma pequena variante da projeção Mercator, usada principalmente em programas de mapeamento baseados na Web. Usa as mesmas fórmulas como a projeção Mercator padrão usados para mapas de pequena escala. No entanto, o Web Mercator usa fórmulas esféricas em todas as escalas enquanto o Mercator em larga escala mapeia normalmente o uso do formulário elipsoidal da projeção. A discrepância é imperceptível em escala global, mas faz com que os mapas de áreas de locais desviem um pouco dos verdadeiros mapas do Mercator na mesma escala.

<a name="wgs84"></a> **WGS84**: um conjunto de constantes usado para relacionar as coordenadas espaciais a locais na superfície do mapa. A referência de WGS84 é o padrão usado por mais provedores de mapeamento online e os dispositivos GPS. O Azure Mapas usa o [EPSG:3857](https://epsg.io/3857) sistema de referência coordenada e o WGS 84 para entrada de dados geométricos.

## <a name="z"></a>Z

<a name="z-coordinate"></a> **Coordenada Z**: Veja [Altitude](#altitude). 

<a name="zip-code"></a> **CEP**: Veja [Código postal](#postal-code).

<a name="Zoom level"></a> **Nível de zoom**: especifica o nível de detalhe e quanto do mapa é visível. Quando zoom for até um nível 0, o mapa mundial completo geralmente estará no modo de exibição, mas mostrará detalhes limitados, como nomes de países e bordas além de nomes do oceano. Depois de ampliar o próximo nível 17, o mapa exibirá uma área de alguns blocos da cidade com informações detalhadas da estrada. Para obter mais informações, consulte [Níveis de Zoom e grade de peças](zoom-levels-and-tile-grid.md).

