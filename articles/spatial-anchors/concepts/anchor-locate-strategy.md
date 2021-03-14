---
title: Estratégia de localização de âncora
description: Saiba mais sobre as diferentes estratégias ao chamar a API localizar
author: pamistel
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: pamistel
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 43273ccd7c882bbac6cbc68d359db4ecb100800e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617396"
---
# <a name="understanding-the-anchorlocatecriteria-class"></a>Compreendendo a classe AnchorLocateCriteria
Neste artigo, você aprenderá as diferentes opções que você pode usar ao consultar uma âncora. Vamos examinar a classe AnchorLocateCriteria, suas opções e combinações de opções válidas.

## <a name="anchor-locate-criteria"></a>Critérios de localização de âncora
A [classe AnchorLocateCriteria](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.anchorlocatecriteria) ajuda a consultar o serviço para âncoras criadas anteriormente. Um objeto AnchorLocateCriteria pode ser usado por Inspetor a qualquer momento. Cada objeto AnchorLocateCriteria deve incluir **exatamente uma** das seguintes propriedades: [identificadores](#identifiers), [NearAnchor](#nearanchor)ou [NearDevice](#neardevice). Propriedades adicionais, como [Strategy](#strategy), [BypassCache](#bypasscache)e [RequestedCategories](#requestedcategories) , podem ser definidas, se desejado. 

### <a name="properties"></a>Propriedades
Defina **exatamente uma** das seguintes propriedades no Inspetor:
#### <a name="identifiers"></a>Identificadores
*Valor padrão: matriz de cadeia de caracteres vazia*

Usando identificadores, você pode definir uma lista de IDs de âncora para âncoras que deseja localizar. As IDs de âncora são retornadas inicialmente após a criação de âncora com êxito. Com os identificadores especificados, AnchorLocateCriteria restringe o conjunto de âncoras solicitados a âncoras com as IDs de âncora correspondentes. Essa propriedade é especificada usando uma matriz de cadeia de caracteres. 

#### <a name="nearanchor"></a>NearAnchor
*Valor padrão: não definido*

Usando NearAnchor, você pode especificar que o AnchorLocateCriteria restringe o conjunto de âncoras solicitados a âncoras dentro de uma distância desejada de uma âncora escolhida. Você deve fornecer essa âncora escolhida como a âncora de origem. Você também pode definir a distância desejada fora da âncora de origem e o número máximo de âncoras retornado, para limitar ainda mais a pesquisa.
Essa propriedade é especificada usando um objeto NearAnchorCriteria.

#### <a name="neardevice"></a>NearDevice
*Valor padrão: não definido*

Usando NearDevice, você pode especificar que o AnchorLocateCriteria restringe o conjunto de âncoras solicitados àqueles próximos à localização física do dispositivo. Todos os sensores habilitados serão usados para ajudar a descobrir âncoras em seu dispositivo. Para ter a melhor chance de localizar âncoras, você deve configurar o SensorCapabilities para dar à sessão acesso a todos os sensores apropriados. Para obter mais informações sobre como configurar e usar essa propriedade, consulte [relocalização de alta segurança – âncoras espaciais do Azure | Microsoft docs](https://docs.microsoft.com/azure/spatial-anchors/concepts/coarse-reloc) e *como criar e localizar âncoras usando a relocalação* de alta utilização em [C#](https://docs.microsoft.com/azure/spatial-anchors/how-tos/set-up-coarse-reloc-unity), [Objective-C](https://docs.microsoft.com/azure/spatial-anchors/how-tos/set-up-coarse-reloc-unity), [Swift](https://docs.microsoft.com/azure/spatial-anchors/how-tos/set-up-coarse-reloc-swift), [Java](https://docs.microsoft.com/azure/spatial-anchors/how-tos/set-up-coarse-reloc-java), [c++/NDK](https://docs.microsoft.com/azure/spatial-anchors/how-tos/set-up-coarse-reloc-cpp-ndk), [c++/WinRT](https://docs.microsoft.com/azure/spatial-anchors/how-tos/set-up-coarse-reloc-cpp-winrt).
Essa propriedade é especificada usando um objeto NearDeviceCriteria.

### <a name="additional-properties"></a>Propriedades adicionais
#### <a name="bypasscache"></a>BypassCache
*Valor padrão: false*

Quando uma âncora é criada ou encontrada em uma sessão, ela também é armazenada no cache.  Com essa propriedade definida como false, qualquer consulta subsequente na mesma sessão retornará o valor armazenado em cache. Nenhuma solicitação ao serviço ASA é feita.

#### <a name="requestedcategories"></a>RequestedCategories
*Valor padrão: Propriedades | Espacial*

Essa propriedade é usada para determinar quais dados são retornados de uma consulta usando AnchorLocateCriteria. O valor padrão retornará as propriedades e os dados espaciais, isso não deverá ser alterado se as propriedades e os dados espaciais forem ambos desejados. Essa propriedade pode ser especificada usando a enumeração AnchorDataCategory.

Valor de enumeração AnchorDataCategory | Dados Retornados
-----|------------
Nenhum | Nenhum dado é retornado
Propriedades| Propriedades de âncora, incluindo AppProperties, são retornadas.
Espacial| Informações espaciais sobre uma âncora são retornadas.

#### <a name="strategy"></a>Estratégia
*Valor padrão: AnyStrategy*

A estratégia também define como as âncoras devem ser localizadas. A propriedade Strategy pode ser especificada usando uma enumeração LocateStrategy.

Valor de enumeração LocateStrategy | Descrição
---------------|------------
AnyStrategy | Essa estratégia permite que o sistema use combinações de VisualInformation e estratégias de relacionamento para localizar âncoras. 
VisualInformation|Essa estratégia tenta encontrar âncoras combinando informações visuais do ambiente atual para os da superfície visual da âncora. A superfície visual de uma âncora refere-se às informações visuais atualmente associadas à âncora. Normalmente, essas informações visuais não são reunidas exclusivamente durante a criação da âncora. Atualmente, essa estratégia só é permitida em conjunto com as propriedades NearDevice ou identificadores.
Relationship|Essa estratégia tenta encontrar âncoras fazendo uso de [âncoras conectadas](https://docs.microsoft.com/azure/spatial-anchors/concepts/anchor-relationships-way-finding#connect-anchors)existentes. Atualmente, essa estratégia só é permitida em conjunto com as propriedades NearAnchor ou identificadores. Quando usado com a propriedade identificadores, é necessário que, na mesma sessão, o usuário tenha localizado anteriormente uma âncora com relações de conexão já estabelecidas com as âncoras cujas IDs são especificadas na matriz de identificadores. 


### <a name="valid-combinations-of-locatestrategy-and-anchorlocatecriteria-properties"></a>Combinações válidas de propriedades LocateStrategy e AnchorLocateCriteria 

Nem todas as combinações de propriedades Strategy e AnchorLocateCriteria são permitidas no momento pelo sistema. A tabela a seguir mostra as combinações permitidas:



Propriedade | AnyStrategy | Relationship | VisualInformation
-------- | ------------|--------------|-------------------
Identificadores | &check;    | &check;     | &check;
NearAnchor  | &check;   (o padrão será a relação) | &check;    | 
NearDevice  | &check;    |   | &check;




## <a name="next-steps"></a>Próximas etapas

Consulte [como criar e localizar âncoras usando âncoras espaciais do Azure](https://docs.microsoft.com/azure/spatial-anchors/create-locate-anchors-overview) para obter mais exemplos usando a classe AnchorLocateCriteria.