---
title: Navegar nas APIs dos Gêmeos Digitais do Azure | Microsoft Docs
description: Aprenda os padrões comuns para consultar as APIs de gerenciamento dos Gêmeos Digitais do Azure.
author: kingdomofends
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: v-adgera
ms.openlocfilehash: da1493d2d52f2c8a964df3b72c1622a9c6b66abf
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719858"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Como usar as APIs de gerenciamento de Gêmeos Digitais do Azure

As APIs de gerenciamento dos Gêmeos Digitais do Azure fornecem funcionalidades eficientes para seus aplicativos de IoT. Este artigo mostra como navegar pela estrutura de API.  

## <a name="api-summary"></a>Resumo da API

A lista a seguir mostra os componentes das APIs de Gêmeos Digitais.

* [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): essas APIs interagem com os locais físicos em sua configuração. Elas o ajudam a criar, excluir e gerenciar os mapeamentos digitais de seus locais físicos na forma de um [grafo espacial](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): essas APIs interagem com os dispositivos em sua configuração. Esses dispositivos podem gerenciar um ou mais sensores. Por exemplo, um dispositivo pode ser seu telefone, um pod do sensor Raspberry Pi, um gateway Lora e assim por diante.

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): essas APIs ajudam você a se comunicar com os sensores associados com seus dispositivos e seus locais físicos. os sensores gravam e enviam valores de ambiente que, em seguida, podem ser usados para manipular o seu ambiente espacial.  

* [/resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): essas APIs ajudam a configurar recursos, como um Hub IoT, para sua instância dos Gêmeos Digitais.

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): essas APIs permitem que você associe tipos estendidos de seus objetos dos Gêmeos Digitais para adicionar características específicas a esses objetos. esses tipos permitem filtrar e agrupar facilmente objetos na interface do usuário e as funções personalizadas que processam os dados de telemetria. Exemplos de tipos estendidos são *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType* e assim por diante.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): essas APIs ajudam você a gerenciar ontologias, que são coleções de tipos estendidos. As ontologias fornecem nomes para tipos de objeto conforme o espaço físico que eles representam. Por exemplo, a ontologia *BACnet* fornece nomes específicos para *sensor types*, *datatypes*, *datasubtypes* e *dataunittypes*. As ontologias são gerenciadas e criadas pelo serviço. Os usuários podem carregar e descarregar ontologias. Quando uma ontologia é carregada, todos os seus nomes de tipo associados estão habilitados e prontos para serem provisionados no grafo espacial. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): você pode usar essas APIs para criar propriedades personalizadas para seus *espaços*, *dispositivos*, *usuários* e *sensores*. Essas propriedades são criadas como pares chave/valor. Você pode definir o tipo de dados para essas propriedades definindo o *PrimitiveDataType*. Por exemplo, você pode definir uma propriedade chamada *BasicTemperatureDeltaProcessingRefreshTime* do tipo *uint* de sensores e, em seguida, atribuir um valor para essa propriedade para cada um dos seus sensores. Você também pode adicionar restrições para esses valores ao criar a propriedade, como os intervalos *Mín* e *Máx*, bem como os valores permitidos como *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): essas APIs permitem que você especifique as condições que deseja avaliar de seus dados de dispositivo de entrada. Para saber mais, confira [este artigo](concepts-user-defined-functions.md#matchers). 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): essas APIs permitem que você crie, exclua ou atualize uma função personalizada que será executada quando as condições definidas pelos *elementos de correspondência* ocorrerem, para processar dados provenientes de sua instalação. Veja [neste artigo](concepts-user-defined-functions.md#user-defined-functions) para obter mais informações sobre essas funções personalizadas, também chamadas de *funções definidas pelo usuário*. 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): essas APIs permitem que você crie pontos de extremidade para que sua solução de Gêmeos Digitais possa se comunicar com outros serviços do Azure para armazenamento de dados e análise. Leia [este artigo](concepts-events-routing.md) para obter mais informações. 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): essas APIs permitem que você gerencie repositórios de chaves de segurança para seus espaços. Esses armazenamentos podem manter uma coleção de chaves de segurança e permitem que você recupere facilmente as chaves válidas mais recentes.

* [/users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): essas APIs permitem que você associe usuários aos seus espaços para localizar esses indivíduos quando necessário. 

* [/system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): essas APIs permitem que você gerencie as configurações de todo o sistema, como os tipos padrão de sensores e espaços. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): essas APIs permitem que você associe funções a entidades, como ID de usuário, ID de função definida pelo usuário etc. Cada atribuição de função inclui a ID da entidade a ser associada, o tipo de entidade, a ID da função a ser associada, a ID do locatário e um caminho que define o limite superior do recurso que a entidade pode acessar com essa associação. Leia [este artigo](security-role-based-access-control.md) para obter mais informações.


## <a name="api-navigation"></a>Navegação de API

As APIs de Gêmeos Digitais oferecem suporte para filtragem e navegação em todo o grafo espacial usando os seguintes parâmetros:

- **spaceId**: a API filtrará os resultados pela ID do espaço determinado. Além disso, o sinalizador booliano **useParentSpace** é aplicável às APIs [/espaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces), o que indica que a ID do espaço determinada se refere ao espaço pai, em vez de ao espaço atual. 

- **minLevel** e **maxLevel**: espaços raiz são considerados como estando no nível 1. Espaço com espaço pai no nível *n* estão no nível *n+1*. Com esses valores definidos, você pode filtrar os resultados em níveis específicos. Esses são os valores inclusivos quando definidos. Dispositivos, sensores e outros objetos são considerados como estando no mesmo nível que seu espaço de mais próximo. Para obter todos os objetos em um determinado nível, defina **minLevel** e **maxLevel** para o mesmo valor.

- **minRelative** e **maxRelative**: quando esses filtros são fornecidos, o nível correspondente é relativo ao nível da ID do espaço determinada:
   - O nível relativo *0* é do mesmo nível que a ID do espaço determinada.
   - O nível relativo *1* representa espaços no mesmo nível que os filhos da ID do espaço determinada. O nível relativo *n* representa espaços menores do que o espaço especificado por níveis *n*.
   - O nível relativo *-1* representa espaços no mesmo nível que o espaço do pai do espaço especificado.

- **percorrer**: permite que você percorra em qualquer direção de uma ID de espaço determinada, conforme especificado pelos valores a seguir.
   - **Nenhum**: Esse valor padrão de filtros para a ID do espaço determinada.
   - **Para baixo**: filtra pela ID do espaço determinada e seus descendentes. 
   - **Para cima**: filtra pela ID do espaço determinada e seus ancestrais. 
   - **Alcance**: filtra uma parte horizontal do grafo espacial no mesmo nível que a ID do espaço determinada. Precisa que s **minRelative** ou **maxRelative** esteja definido como true. 


### <a name="examples"></a>Exemplos

A lista a seguir mostra alguns exemplos de navegação pelas APIs [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices). Observe que o espaço reservado `YOUR_MANAGEMENT_API_URL` refere-se ao URI das APIs de Gêmeos Digitais no formato `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`, em que `YOUR_INSTANCE_NAME` é o nome da sua instância de Gêmeos Digitais do Azure e `YOUR_LOCATION` é a região em que a instância está hospedada.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` retorna todos os dispositivos conectados aos espaços raiz.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` retorna todos os dispositivos conectados aos espaços de níveis 2, 3 ou 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` retorna todos os dispositivos diretamente anexados ao mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` retorna todos os dispositivos anexados ao mySpaceId ou um de seus descendentes.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` retorna todos os dispositivos anexados a descendentes do mySpaceId, excluindo mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` retorna todos os dispositivos anexados a filhos imediatos do mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` retorna todos os dispositivos anexados a um dos ancestrais mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` retorna todos os dispositivos anexados a descendentes do mySpaceId que estão no nível menor ou igual a 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` retorna todos os dispositivos anexados a espaços que estão no mesmo nível como mySpaceId.


## <a name="odata-support"></a>Suporte a OData
A maioria das APIs que retorna coleções, como uma chamada GET em /spaces, dá suporte ao seguinte subconjunto dos genéricos das opções de consulta do sistema [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData):  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** – se você pretende exibir toda a coleção, deve solicitá-la como um conjunto inteiro em uma única chamada e, em seguida, executar a paginação em seu aplicativo. 

Observe que não há suporte para outras opções de consulta, como $count, $expand, $search.

### <a name="examples"></a>Exemplos

A lista a seguir mostra alguns exemplos de consultas usando opções de consulta do sistema do OData:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,’space’)`
- `YOUR_MANAGEMENT_API_URL/propertykeys?$filter=Scope ne ‘Spaces’`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=Size gt ‘M’`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,’k’)&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor’,Name)`
 

## <a name="next-steps"></a>Próximas etapas

Para aprender sobre alguns padrões comuns de consulta de API, leia [Como consultar as APIs de Gêmeos Digitais do Azure para tarefas comuns](how-to-query-common-apis.md).

Para saber mais sobre seus pontos de extremidade de API, leia [como usar o Swagger de gêmeos Digital](./how-to-use-swagger.md).
