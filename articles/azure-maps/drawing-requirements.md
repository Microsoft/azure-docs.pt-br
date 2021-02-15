---
title: Desenhando requisitos de pacote no criador do Microsoft Azure Maps (versão prévia)
description: Saiba mais sobre os requisitos de pacote de desenho para converter seus arquivos de design de recursos para mapear dados
author: anastasia-ms
ms.author: v-stharr
ms.date: 1/08/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 2a37e716b7804b11ab396909f746af84294bb4e3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895264"
---
# <a name="drawing-package-requirements"></a>Requisitos do pacote de desenho


> [!IMPORTANT]
> Os serviços do Criador do Azure Mapas estão em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode converter pacotes de desenho carregados em dados de mapa usando o [serviço de conversão do Azure Maps](/rest/api/maps/conversion). Este artigo descreve os requisitos de pacote de desenho para a API de Conversão. Para exibir um pacote de exemplo, você pode baixar o [Pacote de desenho](https://github.com/Azure-Samples/am-creator-indoor-data-examples) de amostra.

## <a name="prerequisites"></a>Pré-requisitos

O pacote de desenho inclui desenhos salvos no formato DWG, que é o formato de arquivo nativo para o software AutoCAD® do Autodesk.

Você pode escolher qualquer software CAD para produzir os desenhos no pacote de desenho.  

O [Serviço de conversão do Azure Mapas](/rest/api/maps/conversion) converte o pacote de desenho em dados de mapa. O serviço de conversão funciona com o formato de arquivo do AutoCAD DWG. `AC1032` é a versão de formato interno para os arquivos DWG, e é uma boa ideia selecionar `AC1032` para a versão de formato de arquivo DWG interno.  

## <a name="glossary-of-terms"></a>Glossário de termos

Para referência fácil, aqui estão alguns termos e definições que são importantes à medida que você lê este artigo.

| Termo  | Definição |
|:-------|:------------|
| Camada | Uma camada de DWG do AutoCAD.|
| Nível | Uma área de uma construção com uma elevação definida. Por exemplo, o andar de uma construção. |
| Xref  |Um arquivo no formato de arquivo do AutoCAD DWG (. dwg), anexado ao desenho primário como uma referência externa.  |
| Recurso | Um objeto que combina uma geometria com mais informações de metadados. |
| Classes de recurso | Um blueprint comum dos recursos. Por exemplo, uma *unidade* é uma classe de recurso e um *escritório* é um recurso. |

## <a name="drawing-package-structure"></a>Estrutura de pacote de desenho

Um pacote de desenho é um arquivo .zip que contém os seguintes arquivos:

* Arquivos DWG com formato de arquivo DWG do AutoCAD.
* Um _manifest.jsno_ arquivo que descreve os arquivos DWG no pacote de desenho.

O pacote de desenho deve ser compactado em um único arquivo morto, com a extensão. zip. Os arquivos DWG podem ser organizados de qualquer forma dentro do pacote, mas o arquivo de manifesto deve residir no diretório raiz do pacote compactado. As seções a seguir detalham os requisitos para arquivos DWG, o arquivo de manifesto e o conteúdo desses arquivos.

## <a name="dwg-files-requirements"></a>Requisitos para arquivos DWG

Apenas um arquivo DWG é necessário para cada nível da instalação. Os dados do nível devem estar contidos em um único arquivo DWG. Todas as referências externas (_xrefs_) devem estar ligadas ao desenho pai. Além disso, cada arquivo DWG:

* Deve definir as camadas _Exterior_ e _Unidade_. Opcionalmente, ele pode definir as seguintes camadas opcionais: _mural_, _porta_, _UnitLabel_, _zona_ e _ZoneLabel_.
* Não deve conter recursos de vários níveis.
* Não deve conter recursos de várias instalações.
* Deve fazer referência ao mesmo sistema de medidas e unidade de medida que outros arquivos DWG no pacote de desenho.

O [Serviço de conversão do Azure Mapas](/rest/api/maps/conversion) pode extrair as seguintes classes de recurso de um arquivo DWG:

* Levels
* Unidades
* Zonas
* Aberturas
* Paredes
* Penetrações verticais

Todos os trabalhos de conversão resultam em um conjunto mínimo de categorias padrão: sala, estrutura.parede, abertura.porta, zona e instalação. Categorias adicionais são para cada nome de categoria referenciado por objetos.  

Uma camada do DWG deve conter recursos de uma única classe. As classes não devem compartilhar uma camada. Por exemplo, unidades e paredes não podem compartilhar uma camada.

As camadas do DWG também devem seguir os seguintes critérios:

* As origens de desenhos para todos os arquivos DWG devem se alinhar à mesma latitude e longitude.
* Cada nível deve estar na mesma orientação dos outros níveis.
* Polígonos com interseção automática são reparados automaticamente e o [serviço de conversão do Azure Maps](/rest/api/maps/conversion) gera um aviso. É aconselhável inspecionar manualmente os resultados reparados, pois eles podem não corresponder aos resultados esperados.

Todas as entidades de camada devem ser um dos seguintes tipos: linha, polilinha, polígono, arco circular, círculo, elipse (fechado) ou texto (linha única). Quaisquer outros tipos de entidade são ignorados.

A tabela a seguir descreve os tipos de entidade com suporte e os recursos de mapa convertidos para cada camada. Se uma camada contiver tipos de entidade sem suporte, o [serviço de conversão do Azure Maps](/rest/api/maps/conversion) ignorará essas entidades.  

| Camada | Tipos de entidade | Recursos convertidos |
| :----- | :-------------------| :-------
| [Exterior](#exterior-layer) | Polígono, polilinha (fechada), círculo, elipse (fechado) | Levels
| [Unidade](#unit-layer) |  Polígono, polilinha (fechada), círculo, elipse (fechado) | Invasões verticais, unidade
| [Parede](#wall-layer)  | Polígono, polilinha (fechada), círculo, elipse (fechado) | Não aplicável. Para obter mais informações, consulte [Camada Parede](#wall-layer).
| [Porta](#door-layer) | Polígono, polilinha, linha, CircularArc, círculo | Aberturas
| [Zona](#zone-layer) | Polígono, polilinha (fechada), círculo, elipse (fechado) | Zona
| [UnitLabel](#unitlabel-layer) | Texto (linha única) | Não aplicável. Essa camada só pode adicionar propriedades aos recursos da unidade a partir da camada Unidades. Para obter mais informações, consulte [Camada UnitLabel](#unitlabel-layer).
| [ZoneLabel](#zonelabel-layer) | Texto (linha única) | Não aplicável. Essa camada só pode adicionar propriedades a recursos de zona da ZonesLayer. Para obter mais informações, consulte a [camada ZoneLabel](#zonelabel-layer).

As seções a seguir detalham os requisitos de cada camada.

### <a name="exterior-layer"></a>Camada Exterior

O arquivo DWG de cada nível deve conter uma camada para definir o perímetro desse nível. Essa camada é conhecida como a camada *exterior* . Por exemplo, se uma instalação contiver dois níveis, ela precisará ter dois arquivos DWG, com uma camada Exterior para cada arquivo.

Não importa quantos desenhos de entidade estejam na camada exterior, o conjunto de recursos [resultante](tutorial-creator-indoor-maps.md#create-a-feature-stateset) conterá apenas um recurso de nível para cada arquivo DWG. Além disso:

* Os Exteriors devem ser desenhados como polígono, polilinha (fechada), Circle ou Ellipse (Closed).
* Os Exteriors podem se sobrepor, mas são desresolvidos em uma geometria.
* O recurso de nível resultante deve ter pelo menos 4 metros quadrados.
* O recurso de nível resultante não deve ser maior que 400.000 metros quadrados.

Se a camada contiver várias polilinhas sobrepostas, as polilinhas serão desresolvidas em um único recurso de nível. Como alternativa, se a camada contiver várias polilinhas não sobrepostas, o recurso de nível resultante terá uma representação de vários polígonos.

Você pode ver um exemplo da camada exterior como a camada de estrutura de tópicos no [pacote de desenho de exemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unit-layer"></a>Camada Unidade

O arquivo DWG para cada nível define uma camada que contém unidades. As unidades são espaços navegáveis da construção, como escritórios, corredores, escadas e elevadores. Se a `VerticalPenetrationCategory` propriedade for definida, as unidades navegáveis que abrangem vários níveis, como elevadors e escadas, são convertidas em recursos de penetração vertical. Os recursos de penetração vertical que se sobrepõem um ao outro são atribuídos `setid` .

A camada Unidades deve atender aos seguintes requisitos:

* As unidades devem ser desenhadas como polígono, polilinha (fechada), círculo ou elipse (fechado).
* As unidades devem estar dentro dos limites do perímetro externo da instalação.
* As unidades não devem se sobrepor parcialmente.
* As unidades não devem conter nenhuma geometria com autointerseção.

Nomeie uma unidade criando um objeto de texto na camada UnitLabel e coloque o objeto dentro dos limites da unidade. Para obter mais informações, consulte [Camada UnitLabel](#unitlabel-layer).

Você pode ver um exemplo da camada de unidades no [pacote de desenho de exemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="wall-layer"></a>Camada Parede

O arquivo DWG para cada nível pode conter uma camada que define as extensões físicas de paredes, colunas e outras estruturas de construção.

* As paredes devem ser desenhadas como polígono, polilinha (fechada), círculo ou elipse (fechado).
* A camada de parede ou as camadas só devem conter geometria interpretada como criação de estrutura.

Você pode ver um exemplo da camada de paredes no [pacote de desenho de exemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="door-layer"></a>Camada Porta

Você pode incluir uma camada do DWG que contém portas. Cada porta deve sobrepor a borda de uma unidade da camada de unidade.

As aberturas de porta em um conjunto de um DataSet do Azure são representadas como um segmento de linha única que sobrepõe vários limites de unidade. As imagens a seguir mostram como converter a geometria na camada de porta para abrir recursos em um conjunto de uma.

![Quatro gráficos que mostram as etapas para gerar aberturas](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Camada Zona

O arquivo DWG para cada nível pode conter uma camada de zona que define as extensões físicas de zonas. Uma zona é um espaço não navegável que pode ser nomeado e renderizado. As zonas podem abranger vários níveis e agrupadas usando a propriedade zoneSetId.

* As zonas devem ser desenhadas como polígono, polilinha (Closed) ou Ellipse (Closed).
* As zonas podem se sobrepor.
* As zonas podem ficar dentro ou fora do perímetro exterior do recurso.

Nomeie uma zona criando um objeto de texto na camada ZoneLabel e colocando o objeto de texto dentro dos limites da zona. Para obter mais informações, consulte [Camada ZoneLabel](#zonelabel-layer).

Você pode ver um exemplo da camada de zona no [pacote de desenho de exemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unitlabel-layer"></a>Camada UnitLabel

O arquivo DWG para cada nível pode conter uma camada UnitLabel. A camada UnitLabel adiciona uma propriedade Name às unidades extraídas da camada de unidade. Unidades com uma propriedade Name podem ter mais detalhes especificados no arquivo de manifesto.

* Os rótulos de unidade devem ser entidades de texto de linha única.
* Os rótulos de unidade devem estar dentro dos limites de sua unidade.
* As unidades não devem conter várias entidades de texto na camada UnitLabel.

Você pode ver um exemplo da camada UnitLabel no pacote de [desenho de exemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="zonelabel-layer"></a>Camada ZoneLabel

O arquivo DWG para cada nível pode conter uma camada ZoneLabel. Essa camada adiciona uma propriedade name às zonas extraídas da camada Zona. As zonas com uma propriedade Name podem ter mais detalhes especificados no arquivo de manifesto.

* Os rótulos de zonas devem ser entidades de texto de linha única.
* Os rótulos de zonas devem estar dentro dos limites de sua zona.
* As zonas não devem conter várias entidades de texto na camada ZoneLabel.

Você pode ver um exemplo da camada ZoneLabel no pacote de [desenho de exemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="manifest-file-requirements"></a>Requisitos do arquivo de manifesto

A pasta zip deve conter um arquivo de manifesto no nível raiz do diretório, e o arquivo deve ser nomeado como **manifest.json**. Ele descreve os arquivos DWG para permitir que o [Serviço de conversão do Azure Mapas](/rest/api/maps/conversion) analise seu conteúdo. Somente os arquivos identificados pelo manifesto são ingeridos. Os arquivos que estão na pasta zip, mas que não estão corretamente listados no manifesto, são ignorados.

Os caminhos de arquivo no `buildingLevels` objeto do arquivo de manifesto devem ser relativos à raiz da pasta zip. O nome do arquivo DWG deve corresponder exatamente ao nome do nível da instalação. Por exemplo, um arquivo DWG para o nível "porão" é "porão. dwg". Um arquivo DWG para o nível 2 é nomeado como "level_2. dwg". Use um sublinhado se o nome do seu nível tiver um espaço.

Embora haja requisitos ao usar os objetos de manifesto, nem todos os objetos são necessários. A tabela a seguir mostra os objetos obrigatórios e opcionais para a versão 1,1 do [serviço de conversão do Azure Maps](/rest/api/maps/conversion).

| Objeto | Obrigatório | Descrição |
| :----- | :------- | :------- |
| `version` | true |Versão do esquema de manifesto. Atualmente, há suporte apenas para a versão 1,1.|
| `directoryInfo` | true | Descreve as informações geográficas e de contato da instalação. Também pode ser usado para descrever informações geográficas de um ocupante ou de um contato. |
| `buildingLevels` | true | Especifica os níveis das construções e os arquivos que contêm o design dos níveis. |
| `georeference` | true | Contém informações geográficas numéricas do desenho da instalação. |
| `dwgLayers` | true | Lista os nomes das camadas, e cada camada lista os nomes de seus próprios recursos. |
| `unitProperties` | false | Pode ser usado para inserir mais metadados para os recursos de unidade. |
| `zoneProperties` | false | Pode ser usado para inserir mais metadados para os recursos de zona. |

As seções a seguir detalham os requisitos de cada objeto.

### `directoryInfo`

| Propriedade  | Tipo | Necessária | Descrição |
|-----------|------|----------|-------------|
| `name`      | string | true   |  Nome da construção. |
| `streetAddress`|    string |    false    | Endereço da construção. |
|`unit`     | string    |  false    |  Unidade na construção. |
| `locality` |    string |    false |    Nome de uma área, vizinhança ou região. Por exemplo, "Overlake" ou "Distrito Central". A localidade não faz parte do endereço de correspondência. |
| `adminDivisions` |    Matriz de cadeias de caracteres JSON |    false     | Uma matriz que contém designações de endereço (país, estado, cidade) ou (país, prefeitura, cidade, município). Use códigos de países ISO 3166 e códigos de estados/regiões ISO 3166-2. |
| `postalCode` |    string    | false    | O código de classificação de correspondência. |
| `hoursOfOperation` |    string |     false | Adere ao formato [horário de funcionamento OSM](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification). |
| `phone`    | string |    false |    Número de telefone associado à construção. Deve incluir o código do país. |
| `website`    | string |    false    | Site associado à construção. Deve começar com http ou HTTPS. |
| `nonPublic` |    bool    | false | Sinalizador especificando se a construção está aberta para o público. |
| `anchorLatitude` | numeric |    false | Latitude de uma âncora de instalação (pino). |
| `anchorLongitude` | numeric |    false | Longitude de uma âncora de instalação (pino). |
| `anchorHeightAboveSeaLevel`  | numeric | false | Altura em metros do térreo da instalação em relação ao nível do mar. |
| `defaultLevelVerticalExtent` | numeric | false | Altura padrão (espessura) de um nível dessa instalação e que deve ser usada quando o `verticalExtent` de um nível estiver indefinido. |

### `buildingLevels`

O objeto `buildingLevels` contém uma matriz JSON de níveis de construções.

| Propriedade  | Tipo | Necessária | Descrição |
|-----------|------|----------|-------------|
|`levelName`    |string    |true |    Nome do nível descritivo. Por exemplo: piso 1, lobby, estacionamento azul ou porão.|
|`ordinal` | inteiro |    true | Determina a ordem vertical dos níveis. Cada instalação deve ter um nível com o ordinal 0. |
|`heightAboveFacilityAnchor` | numeric | false |    Altura do nível acima da âncora em metros. |
| `verticalExtent` | numeric | false | Altura do piso para o teto (espessura) do nível em metros. |
|`filename` |    string |    true |    Caminho do sistema de arquivos do desenho do CAD para um nível da construção. Ele estar relacionado à raiz do arquivo zip da construção. |

### `georeference`

| Propriedade  | Tipo | Necessária | Descrição |
|-----------|------|----------|-------------|
|`lat`    | numeric |    true |    Representação decimal da latitude em graus na origem do desenho da instalação. As coordenadas de origem devem estar no WGS84 Web Mercator (`EPSG:3857`).|
|`lon`    |numeric|    true|    Representação decimal da longitude em graus na origem do desenho da instalação. As coordenadas de origem devem estar no WGS84 Web Mercator (`EPSG:3857`). |
|`angle`|    numeric|    true|   O ângulo no sentido horário, em graus, entre o norte verdadeiro e o eixo vertical (Y) do desenho.   |

### `dwgLayers`

| Propriedade  | Type | Obrigatório | Descrição |
|-----------|------|----------|-------------|
|`exterior`    |Matriz de cadeias de caracteres|    true|    Nomes de camadas que definem o perfil de construção exterior.|
|`unit`|    Matriz de cadeias de caracteres|    true|    Nomes de camadas que definem unidades.|
|`wall`|    Matriz de cadeias de caracteres    |false|    Nomes de camadas que definem paredes.|
|`door`    |Matriz de cadeias de caracteres|    false   | Nomes de camadas que definem portas.|
|`unitLabel`    |Matriz de cadeias de caracteres|    false    |Nomes de camadas que definem nomes de unidades.|
|`zone` | Matriz de cadeias de caracteres    | false    | Nomes de camadas que definem zonas.|
|`zoneLabel` | Matriz de cadeias de caracteres |     false |    Nomes de camadas que definem nomes de zonas.|

### `unitProperties`

O objeto `unitProperties` contém uma matriz JSON das propriedades da unidade.

| Propriedade  | Tipo | Necessária | Descrição |
|-----------|------|----------|-------------|
|`unitName`    |string    |true    |Nome da unidade a ser associada a esse registro `unitProperty`. Esse registro só é válido quando uma correspondência de rótulo `unitName` é encontrada nas `unitLabel` camadas. |
|`categoryName`|    string|    false    |Nome da categoria. Para obter uma lista completa das categorias, consulte [categorias](https://aka.ms/pa-indoor-spacecategories). |
|`navigableBy`| Matriz de cadeias de caracteres |    false    |Indica os tipos de agentes de navegação que podem atravessar a unidade. Essa propriedade informa os recursos de wayfinding. Os valores permitidos são: `pedestrian` , `wheelchair` , `machine` , `bicycle` , `automobile` , `hiredAuto` , `bus` , `railcar` , `emergency` , `ferry` , `boat` e `disallowed` .|
|`routeThroughBehavior`|    string|    false    |O comportamento da rota da unidade. Os valores permitidos são `disallowed`, `allowed` e `preferred`. O valor padrão é `allowed`.|
|`occupants`    |matriz de objetos directoryInfo |false    |Lista de ocupantes da unidade. |
|`nameAlt`|    string|    false|    Nome alternativo da unidade. |
|`nameSubtitle`|    string    |false|    Subtítulo da unidade. |
|`addressRoomNumber`|    string|    false|    Sala, unidade, apartamento ou número de pacote da unidade.|
|`verticalPenetrationCategory`|    string|    false| Quando essa propriedade é definida, o recurso resultante é uma penetração vertical (VRT) em vez de uma unidade. Você pode usar o VRTs para ir para outros recursos do VRT nos níveis acima ou abaixo dele. A penetração vertical é um nome de [categoria](https://aka.ms/pa-indoor-spacecategories) . Se essa propriedade for definida, a `categoryName` propriedade será substituída por `verticalPenetrationCategory` . |
|`verticalPenetrationDirection`|    string|    false    |Se `verticalPenetrationCategory` for definida, como opção, defina a direção válida da viagem. Os valores permitidos são: `lowToHigh` , `highToLow` , `both` e `closed` . O valor padrão é `both`.|
| `nonPublic` | bool | false | Indica se a unidade está aberta ao público. |
| `isRoutable` | bool | false | Quando essa propriedade é definida como `false` , não é possível ir para ou por meio da unidade. O valor padrão é `true`. |
| `isOpenArea` | bool | false | Permite que o agente de navegação Insira a unidade sem a necessidade de uma abertura anexada à unidade. Por padrão, esse valor é definido como `true` para unidades sem aberturas e `false` para unidades com aberturas. Definir manualmente `isOpenArea` como `false` em uma unidade sem aberturas resulta em um aviso, porque a unidade resultante não poderá ser acessada por um agente de navegação.|

### `zoneProperties`

O objeto `zoneProperties` contém uma matriz JSON das propriedades da zona.

| Propriedade  | Tipo | Necessária | Descrição |
|-----------|------|----------|-------------|
|zoneName        |string    |true    |Nome da zona a ser associada ao registro `zoneProperty`. Esse registro só é válido quando um rótulo correspondente a `zoneName` for encontrado na camada `zoneLabel` da zona.  |
|categoryName|    string|    false    |Nome da categoria. Para obter uma lista completa das categorias, consulte [categorias](https://aka.ms/pa-indoor-spacecategories). |
|zoneNameAlt|    string|    false    |Nome alternativo da zona.  |
|zoneNameSubtitle|    string |    false    |Subtítulo da zona. |
|zoneSetId|    string |    false    | Defina ID para estabelecer uma relação entre várias zonas para que elas possam ser consultadas ou selecionadas como um grupo. Por exemplo, zonas que abrangem vários níveis. |

### <a name="sample-drawing-package-manifest"></a>Manifesto do pacote de desenho de amostra

Abaixo está o arquivo de manifesto do pacote de desenho de exemplo. Para baixar o pacote inteiro, consulte [pacote de desenho de exemplo](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

#### <a name="manifest-file"></a>Arquivo de manifesto

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

Quando o pacote de desenho atende aos requisitos, você pode usar o [serviço de conversão do Azure Maps](/rest/api/maps/conversion) para converter o pacote em um conjunto de um DataSet. Em seguida, você pode usar o conjunto de um para gerar um mapa interno usando o módulo de mapas em interno.

> [!div class="nextstepaction"]
>[Criador (visualização) para mapas de interno](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Tutorial: Criando um mapa interno de criador (visualização)](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Estilos dinâmicos de mapas de interno](indoor-map-dynamic-styling.md)