---
title: Noções básicas sobre modelos de objeto e grafos de inteligência espacial dos Gêmeos Digitais | Microsoft Docs
description: Use o serviço Gêmeos Digitais do Azure para modelar as relações entre pessoas, locais e dispositivos
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: e7efe1a8632643e2a299b6c9a1b1407414deee4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60925758"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Noções básicas sobre modelos de objeto e grafos de inteligência espacial dos Gêmeos Digitais

O recurso Gêmeos Digitais do Azure é um serviço de IoT do Azure que possibilita representações virtuais abrangentes de ambientes físicos e dispositivos, sensores e pessoas associados. Melhora o desenvolvimento ao organizar conceitos específicos de domínio em modelos úteis. Os modelos são situados dentro de um gráfico de inteligência espacial. Esses conceitos modelam fielmente os relacionamentos e as interações entre pessoas, espaços e dispositivos.

Os modelos de objeto de Gêmeos Digitais descrevem conceitos, categorias e propriedades específicos do domínio. Os modelos são predefinidos pelos usuários que desejam adaptar a solução às suas necessidades específicas. Juntos, esses modelos de objeto de Gêmeos Digitais predefinidos formam uma _ontologia_. A ontologia de construção inteligente descreve regiões, locais, andares, escritórios, zonas, salas de conferência e salas de foco. Uma ontologia da rede de energia descreve várias estações de energia, subestações, recursos energéticos e clientes. Com os modelos de objeto de Gêmeos Digitais e ontologias, diversos cenários e necessidades podem ser personalizados.

Com os modelos de objeto de Gêmeos Digitais e uma ontologia implantada, é possível preencher um _grafo espacial_. Gráficos espaciais são representações virtuais das muitas relações entre espaços, dispositivos e pessoas que são relevantes para uma solução de IoT. Este diagrama mostra um exemplo de um gráfico espacial que usa a ontologia de construção inteligente.

![Construção de grafo espacial de Gêmeos Digitais][1]

<a id="model"></a>

O grafo espacial reúne usuários, dispositivos, sensores e espaços. Cada um está interligado de forma que modela o mundo real. Nesse exemplo, o local 43 tem quatro andares, cada um com muitas áreas diferentes. Os usuários estão associados às estações de trabalho e recebem acesso a partes do grafo. Um administrador tem os direitos para fazer alterações no grafo espacial, enquanto um visitante tem direitos para exibir apenas determinados dados de construção.

## <a name="digital-twins-object-models"></a>Modelos de objeto de Gêmeos Digitais

Modelos de objeto de Gêmeos Digitais dão suporte a estas categorias principais de objetos:

- **Espaços** são locais virtuais ou físicos, por exemplo, `Tenant`, `Customer`, `Region` e `Venue`.
- **Dispositivos** são equipamentos virtuais ou físicos, por exemplo, `AwesomeCompany Device` exemplo `Raspberry Pi 3`.
- **Sensores** ão objetos que detectam eventos, por exemplo, `AwesomeCompany Temperature Sensor` e `AwesomeCompany Presence Sensor`.
- **Usuários** identificam ocupantes e suas características.

Outras categorias de objetos são:

- **Recursos** são anexados a um espaço e normalmente representam recursos do Azure a serem usados por objetos no grafo espacial, por exemplo, `IoTHub`.
- **Blobs** são anexados a objetos (como espaços, dispositivos, sensores e usuários). Eles são usados como arquivos com tipo mime e metadados, por exemplo, `maps`, `pictures` e `manuals`.
- **Tipos estendidos** são enumerações extensíveis que aumentam as entidades com características específicas, por exemplo `SpaceType` e `SpaceSubtype`.
- **Ontologias** representam um conjunto de tipos estendidos, por exemplo `Default`, `Building`, `BACnet` e `EnergyGrid`.
- **Chaves e valores de propriedade** são características personalizadas de usuários, dispositivos, sensores e espaços. Eles podem ser usados junto com características internas, por exemplo, `DeltaProcessingRefreshTime` como chave e `10` como valor.
- **Funções** são conjuntos de permissões atribuídas a usuários e dispositivos no grafo espacial, por exemplo `Space Administrator`, `User Administrator` e `Device Administrator`.
- **Atribuições de funções** são a associação entre uma função e um objeto no grafo espacial. Por exemplo, um usuário ou uma entidade de serviço pode receber permissão para gerenciar um espaço no grafo espacial.
- **Armazenamentos de chaves de seguranças** fornecem as chaves de segurança para todos os dispositivos na hierarquia sob um determinado objeto de espaço para permitir que o dispositivo comunique-se de maneira segura com Gêmeos Digitais.
- **UDFs** (Funções Definidas pelo Usuário) permitem processamento de telemetria do sensor personalizável no grafo espacial. Por exemplo, uma UDF pode:
  - Definir um valor de sensor.
  - Realizar uma lógica personalizada com base nas leituras do sensor e definir a saída para um espaço.
  - Anexar metadados a um espaço.
  - Enviar notificações quando condições predefinidas forem atendidas. Atualmente, as UDFs podem ser escritas em JavaScript.
- **Correspondentes** são objetos que determinam quais UDFs são executadas para uma determinada mensagem de telemetria.
- **Pontos de extremidade** são os locais onde as mensagens de telemetria e os eventos de Gêmeos Digitais podem ser roteados, por exemplo, `Event Hub`, `Service Bus` e `Event Grid`.

<a id="graph"></a>

## <a name="spatial-intelligence-graph"></a>Grafo de inteligência espacial

Grafo espacial é o grafo hierárquico de espaços, dispositivos e pessoas definido no modelo de objeto de Gêmeos Digitais. O grafo espacial dá suporte para herança, filtragem, travessia, escalabilidade e extensibilidade. É possível gerenciar e interagir com o grafo espacial com uma coleção de APIs REST.

Se você implantar um serviço de Gêmeos Digitais na assinatura, você se tornará o administrador global do nó raiz. Você receberá automaticamente o acesso completo a toda a estrutura. Provisione espaços no grafo usando a API de Espaço. Provisione serviços usando a API de Dispositivo e os sensores usando a API do Sensor. [Ferramentas de software livre](https://github.com/Azure-Samples/digital-twins-samples-csharp) também estão disponíveis para provisionar o grafo em massa.

**Herança de grafo**. A herança aplica-se às permissões e propriedades que descendem de um nó pai para todos os nós abaixo dele. Por exemplo, quando uma função é atribuída a um usuário em um determinado nó, o usuário tem as permissões dessa função para o nó fornecido e para cada nó abaixo dele. Cada chave de propriedade e tipo estendido definido para um determinado nó é herdado por todos os nós abaixo desse nó.

**Filtragem de grafo**. A filtragem é usada para restringir os resultados da solicitação. É possível filtrar por IDs, nome, tipos, subtipos, espaço pai e espaços associados. Além disso, é possível filtrar por tipos de dados de sensores, chaves de propriedades e valores, *traverse*, *minLevel*, *maxLevel* e outros parâmetros do filtro OData.

**Travessia de grafo**. É possível atravessar o grafo espacial através de sua profundidade e largura. Para profundidade, atravesse o grafo de cima para baixo ou de baixo para cima usando os parâmetros *traverse*, *minLevel* e *maxLevel*. Atravesse o grafo para obter os nós irmãos diretamente anexados a um espaço pai ou a um de seus descendentes por largura. Ao consultar um objeto é possível obter todos os objetos relacionados que possuem relacionamentos com esse objeto, usando o parâmetro *includes* das APIs GET.

**Escalabilidade de grafo**. O recurso Gêmeos Digitais garante escalabilidade de grafo, portanto, pode atender as cargas de trabalho do mundo real. O recurso Gêmeos Digitais pode ser usado para representar grandes portfólios de imóveis, infraestrutura, dispositivos, sensores, telemetria e muito mais.

**Extensibilidade de grafo**. Use extensibilidade para personalizar os modelos de objetos subjacentes de Gêmeos Digitais com novos tipos e ontologias. Os dados dos Gêmeos Digitais também podem ser aprimorados com propriedades e valores extensíveis.

### <a name="spatial-intelligence-graph-management-apis"></a>APIs de Gerenciamento de grafo de inteligência espacial

Após implantar os Gêmeos Digitais pelo [portal do Azure](https://portal.azure.com), a URL de [Swagger](https://swagger.io/tools/swagger-ui/) das APIs de Gerenciamento será gerada automaticamente. Ela é exibida no portal do Azure na seção **Visão geral** com o seguinte formato.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| NOME | Substitua por |
| --- | --- |
| NOME_DA_SUA_INSTÂNCIA | O nome da instância dos Gêmeos Digitais |
| SUA_LOCALIZAÇÃO | Em qual região do servidor de sua instância está hospedada |

 O formato completo da URL aparece nesta imagem.

![API de Gerenciamento do portal de Gêmeos Digitais][2]

Para obter mais detalhes sobre como usar grafos de inteligência espacial, visite a versão prévia das APIs de Gerenciamento de Gêmeos Digitais do Azure.

> [!TIP]
> Uma versão prévia do Swagger é fornecida para demonstrar o conjunto de recursos da API.
> Ele está hospedado no [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Saiba mais sobre [como usar o Swagger](how-to-use-swagger.md).

Todas as chamadas à API devem ser autenticadas usando [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). As APIs seguem as [convenções de Diretrizes da API REST da Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). A maioria das APIs que retorna coleções é compatível com opções de consulta do sistema [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre conectividade do dispositivo e como enviar mensagens de telemetria para Gêmeos Digitais, leia [Entrada de telemetria e conectividade do dispositivo de Gêmeos Digitais do Azure](concepts-device-ingress.md).

- Para saber mais sobre restrições e limitações da API de Gerenciamento, leia [Limitações e gerenciamento de API de Gêmeos Digitais do Azure](concepts-service-limits.md).

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
