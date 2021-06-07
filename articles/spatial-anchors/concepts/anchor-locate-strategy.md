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
ms.openlocfilehash: 13aa12be5a336363bbe3bcbf3e3fb354a8fa3074
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048469"
---
# <a name="understanding-the-anchorlocatecriteria-class"></a>Compreendendo a classe AnchorLocateCriteria
Neste artigo, você aprenderá as diferentes opções que você pode usar ao consultar uma âncora. Vamos examinar a classe AnchorLocateCriteria, suas opções e combinações de opções válidas.

## <a name="anchor-locate-criteria"></a>Critérios de localização de âncora
A [classe AnchorLocateCriteria](/dotnet/api/microsoft.azure.spatialanchors.anchorlocatecriteria) ajuda a consultar âncoras criadas anteriormente no serviço. Um objeto AnchorLocateCriteria pode ser usado por observador a qualquer momento. Cada objeto AnchorLocateCriteria deve incluir **exatamente uma** das seguintes propriedades: [Identificadores](#identifiers), [NearAnchor](#nearanchor) ou [NearDevice](#neardevice). Propriedades adicionais, como [Strategy](#strategy), [BypassCache](#bypasscache) e [RequestedCategories](#requestedcategories) podem ser definidas, se desejado. 

### <a name="properties"></a>Propriedades
Defina **exatamente uma** das seguintes propriedades no observador:
#### <a name="identifiers"></a>Identificadores
*Valor padrão: matriz de cadeia de caracteres vazia*

Usando a propriedade Identificadores, você pode definir uma lista de IDs de âncora para âncoras que deseja localizar. As IDs de âncora são retornadas inicialmente após a criação de âncora com êxito. Com a propriedade Identificadores especificada, AnchorLocateCriteria restringe o conjunto de âncoras solicitados a âncoras com as IDs de âncora correspondentes. Essa propriedade é especificada usando uma matriz de cadeia de caracteres. 

#### <a name="nearanchor"></a>NearAnchor
*Valor padrão: não definido*

Ao usar NearAnchor, você pode especificar que o AnchorLocateCriteria restringe o conjunto de âncoras solicitadas a âncoras dentro de uma distância desejada relativa a uma âncora escolhida. Você deve fornecer essa âncora escolhida como a âncora de origem. Você também pode definir a distância desejada em relação à âncora de origem e o número máximo de âncoras retornado, para limitar ainda mais a pesquisa.
Essa propriedade é especificada usando um objeto NearAnchorCriteria.

#### <a name="neardevice"></a>NearDevice
*Valor padrão: não definido*

Ao usar NearDevice, você pode especificar que o AnchorLocateCriteria restrinja o conjunto de âncoras solicitados àquelas próximas à localização física do dispositivo. Todos os sensores habilitados serão usados para ajudar a descobrir âncoras em seu dispositivo. Para ter a melhor chance de localizar âncoras, você deve configurar o SensorCapabilities para dar à sessão acesso a todos os sensores apropriados. Para obter mais informações sobre como configurar e usar essa propriedade, consulte [Relocalização aproximada – Âncoras Espaciais do Azure | Microsoft Docs](./coarse-reloc.md) e *Como criar e localizar âncoras usando a relocalização aproximada* em [C#](../how-tos/set-up-coarse-reloc-unity.md), [Objective-C](../how-tos/set-up-coarse-reloc-unity.md), [Swift](../how-tos/set-up-coarse-reloc-swift.md), [Java](../how-tos/set-up-coarse-reloc-java.md), [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md), [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md).
Essa propriedade é especificada usando um objeto NearDeviceCriteria.

### <a name="additional-properties"></a>Propriedades adicionais
#### <a name="bypasscache"></a>BypassCache
*Valor Padrão: false*

Quando uma âncora é criada ou encontrada em uma sessão, ela também é armazenada no cache.  Com essa propriedade definida como false, qualquer consulta subsequente na mesma sessão retornará o valor armazenado em cache. Nenhuma solicitação ao serviço ASA é feita.

#### <a name="requestedcategories"></a>RequestedCategories
*Valor padrão: Propriedades | Espacial*

Essa propriedade é usada para determinar quais dados são retornados de uma consulta usando AnchorLocateCriteria. O valor padrão retornará as propriedades e os dados espaciais; isso não deverá ser alterado se tanto as propriedades quanto os dados espaciais forem desejados. Essa propriedade pode ser especificada usando a enumeração AnchorDataCategory.

Valor da enumeração AnchorDataCategory | Dados Retornados
-----|------------
Nenhum | Nenhum dado é retornado
Propriedades| Propriedades de âncora, incluindo AppProperties, são retornadas.
Espacial| Informações espaciais sobre uma âncora são retornadas.

#### <a name="strategy"></a>Estratégia
*Valor padrão: AnyStrategy*

A estratégia também define como as âncoras devem ser localizadas. A propriedade Strategy pode ser especificada usando uma enumeração LocateStrategy.

Valor da enumeração LocateStrategy | Descrição
---------------|------------
AnyStrategy | Essa estratégia permite que o sistema use combinações de VisualInformation e estratégias de Relacionamento para localizar âncoras. 
VisualInformation|Essa estratégia tenta encontrar âncoras combinando informações visuais do ambiente atual com as informações da pegada visual da âncora. A pegada visual de uma âncora refere-se às informações visuais atualmente associadas à âncora. Normalmente, essas informações visuais não são reunidas exclusivamente durante a criação da âncora. Atualmente, essa estratégia só é permitida em conjunto com as propriedades NearDevice ou Identificadores.
Relação|Essa estratégia tenta encontrar âncoras fazendo uso de [âncoras conectadas](./anchor-relationships-way-finding.md#connect-anchors) existentes. Atualmente, essa estratégia só é permitida em conjunto com as propriedades NearAnchor ou Identificadores. Quando usada com a propriedade Identificadores, é necessário que, na mesma sessão, o usuário tenha localizado anteriormente uma âncora com relações de conexão já estabelecidas com as âncoras cujas IDs são especificadas na matriz da propriedade Identificadores. 


### <a name="valid-combinations-of-locatestrategy-and-anchorlocatecriteria-properties"></a>Combinações válidas das propriedades LocateStrategy e AnchorLocateCriteria 

Nem todas as combinações das propriedades Strategy e AnchorLocateCriteria são permitidas pelo sistema no momento. A seguinte tabela mostra as combinações permitidas:



Propriedade | AnyStrategy | Relação | VisualInformation
-------- | ------------|--------------|-------------------
Identificadores | &check;    | &check;     | &check;
NearAnchor  | &check; (o padrão será Relação) | &check;    | 
NearDevice  | &check;    |   | &check;




## <a name="next-steps"></a>Próximas etapas

Consulte [Como criar e localizar âncoras usando Âncoras Espaciais do Azure](../create-locate-anchors-overview.md) para obter mais exemplos usando a classe AnchorLocateCriteria.