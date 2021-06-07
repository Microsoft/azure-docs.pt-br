---
title: Extensão de ontologias
titleSuffix: Azure Digital Twins
description: Saiba mais sobre os motivos e as estratégias por trás da extensão de um ontologia
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e5973f58887b212919ad739232faafddcf9e735c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100561258"
---
# <a name="extending-ontologies"></a>Extensão de ontologias 

Um [ontologia](concepts-ontologies.md)padrão do setor, como o [RealEstateCore ontologia baseado em DTDL para prédios inteligentes](https://github.com/Azure/opendigitaltwins-building), é uma ótima maneira de começar a criar sua solução de IOT. A ontologies do setor fornece um conjunto avançado de interfaces base projetadas para seu domínio e projetada para trabalhar prontamente nos serviços de IoT do Azure, como o gêmeos digital do Azure. 

No entanto, é possível que sua solução possa ter necessidades específicas que não são cobertas pela ontologia do setor. Por exemplo, talvez você queira vincular seu gêmeos digital a modelos 3D armazenados em um sistema separado. Nesse caso, você pode estender um desses ontologies para adicionar seus próprios recursos e, ao mesmo tempo, manter todos os benefícios do ontologia original.

Este artigo usa o ontologia [RealEstateCore](https://www.realestatecore.io/) baseado em DTDL como base para exemplos de extensão de ontologies com novas propriedades DTDL. As técnicas descritas aqui são gerais, no entanto, e podem ser aplicadas a qualquer parte de um ontologia baseado em DTDL com qualquer recurso de DTDL (telemetria, propriedade, relação, componente ou comando). 

## <a name="realestatecore-space-hierarchy"></a>Hierarquia de espaço RealEstateCore 

No ontologia RealEstateCore baseado em DTDL, a hierarquia de espaço é usada para definir vários tipos de espaços: salas, prédios, zona, etc. A hierarquia se estende de cada um desses modelos para definir vários tipos de salas, prédios e zonas. 

Uma parte da hierarquia é semelhante ao diagrama abaixo. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-original.png" alt-text="Diagrama de fluxo que ilustra parte da hierarquia de espaço RealEstateCore. No nível superior, há um elemento chamado Space; Ele está conectado por uma seta ' extends ' abaixo de um nível à sala; A sala está conectada por duas setas ' extends ' um nível para ConferenceRoom e para o Office."::: 

Para obter mais informações sobre o RealEstateCore ontologia, consulte [*conceitos: adotando ontologies padrão do setor*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology).

## <a name="extending-the-realestatecore-space-hierarchy"></a>Estendendo a hierarquia de espaço RealEstateCore 

Às vezes, sua solução tem necessidades específicas que não são cobertas pela ontologia do setor. Nesse caso, estender a hierarquia permite que você continue a usar o ontologia do setor ao personalizá-lo para suas necessidades. 

Neste artigo, discutiremos dois casos diferentes em que estender a hierarquia do ontologia é útil: 

* Adicionar novas interfaces para conceitos que não estão no setor ontologia. 
* Adicionar propriedades adicionais (ou relações, componentes, telemetria ou comandos) a interfaces existentes.

### <a name="add-new-interfaces-for-new-concepts"></a>Adicionar novas interfaces para novos conceitos 

Nesse caso, você deseja adicionar interfaces para conceitos necessários para sua solução, mas que não estão presentes no ontologia do setor. Por exemplo, se sua solução tiver outros tipos de salas que não estão representados no ontologia RealEstateCore baseado em DTDL, você poderá adicioná-los estendendo diretamente das interfaces RealEstateCore. 

O exemplo a seguir apresenta uma solução que precisa representar "salas de foco", que não estão presentes no RealEstateCore ontologia. Uma sala de foco é um pequeno espaço projetado para que as pessoas se concentrem em uma tarefa por algumas horas por vez. 

Para estender o ontologia do setor com esse novo conceito, crie uma nova interface que se [estenda das](concepts-models.md#model-inheritance) interfaces no ontologia do setor. 

Depois de adicionar a interface de sala de foco, a hierarquia estendida mostra o novo tipo de sala. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-1.png" alt-text="Diagrama de fluxo ilustrando a hierarquia de espaço RealEstateCore acima, com uma nova adição. No nível inferior com ConferenceRoom e Office, há um novo elemento chamado FocusRoom (também conectado por meio de uma seta ' extends ' do espaço)"::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>Adicionar recursos adicionais a interfaces existentes 

Nesse caso, você deseja adicionar mais Propriedades (ou relações, componentes, telemetria ou comandos) a interfaces que estão no ontologia do setor.

Nesta seção, você verá dois exemplos: 
* Se você estiver criando uma solução que exibe desenhos 3D de espaços que você já tem em um sistema existente, talvez queira associar cada "digital" ao seu desenho 3D (por ID) para que, quando a solução exibir informações sobre o espaço, ele também possa recuperar o desenho 3D do sistema existente. 
* Se sua solução precisar acompanhar o status online/offline de salas de conferência, talvez você queira acompanhar o status da sala de conferência para uso em exibições ou consultas. 

Ambos os exemplos podem ser implementados com novas propriedades: uma `drawingId` propriedade que associa o desenho 3D com a teledigital e uma propriedade "online" que indica se a sala da conferência está online ou não. 

Normalmente, você não quer modificar o ontologia do setor diretamente porque gostaria de poder incorporar atualizações a ele em sua solução no futuro (o que substituiria suas adições). Em vez disso, esses tipos de adições podem ser feitos em sua própria hierarquia de interface que se estende do ontologia RealEstateCore baseado em DTDL. Cada interface que você cria usa várias heranças de interface para estender sua interface pai RealEstateCore e sua interface pai da sua hierarquia de interface estendida. Essa abordagem permite que você use a ontologia do setor e suas adições. 

Para estender o ontologia do setor, você cria suas próprias interfaces que se estendem das interfaces da ontologia do setor e adicionam os novos recursos às suas interfaces estendidas. Para cada interface que você deseja estender, crie uma nova interface. As interfaces estendidas são escritas em DTDL (consulte a seção DTDL para interfaces estendidas mais adiante neste documento). 

Depois de estender a parte da hierarquia mostrada acima, a hierarquia estendida é semelhante ao diagrama abaixo. Aqui, a interface de espaço estendido adiciona a `drawingId` propriedade que conterá uma ID que associa o teledigital com o desenho 3D. Além disso, a interface ConferenceRoom adiciona uma propriedade "online" que conterá o status online da sala de conferência. Por meio da herança, a interface ConferenceRoom contém todos os recursos da interface RealEstateCore ConferenceRoom, bem como todos os recursos da interface de espaço estendido. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-2.png" alt-text="Diagrama de fluxo ilustrando a hierarquia de espaço RealEstateCore estendida acima, com mais novas adições. A sala agora compartilha seu nível com um elemento Space, que se conecta com uma seta ' extends ' abaixo de um nível para um novo elemento Room ao lado de ConferenceRoom e Office.  Os novos elementos estão conectados ao ontologia existente com mais relações ' extends '."::: 

## <a name="using-the-extended-space-hierarchy"></a>Usando a hierarquia de espaço estendida 

Quando você cria gêmeos digital usando a hierarquia de espaço estendido, cada modelo de teledigital será um da hierarquia de espaço estendida (não a ontologia do setor original) e incluirá todos os recursos do setor ontologia e as interfaces estendidas, embora a herança de interface.

Cada modelo de placa digital será uma interface da hierarquia estendida, mostrada no diagrama a seguir. 
 
:::image type="content" source="media/concepts-extending-ontologies/ontology-with-models.png" alt-text="Um trecho da hierarquia de espaço RealEstateCore estendida, incluindo espaço (nível superior), uma sala (nível médio) e ConferenceRoom, Office e FocusRoom (nível inferior). Os nomes de modelos são conectados a cada elemento (por exemplo, a sala é conectada a um modelo chamado Room101)."::: 

Ao consultar o gêmeos digital usando a ID do modelo (o `IS_OF_MODEL` operador), as IDs de modelo da hierarquia estendida devem ser usadas. Por exemplo, `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## <a name="contributing-back-to-the-original-ontology"></a>Contribuindo de volta para o ontologia original 

Em alguns casos, você estenderá a ontologia do setor de uma maneira que seja amplamente útil para a maioria dos usuários do ontologia. Nesse caso, você deve considerar contribuir com suas extensões de volta para o ontologia original. Cada ontologia tem um processo diferente para contribuir, portanto, verifique o repositório GitHub do ontologia para obter detalhes de contribuição. 

## <a name="dtdl-for-new-interfaces"></a>DTDL para novas interfaces 

O DTDL para novas interfaces que se estendem diretamente da ontologia do setor ficaria assim. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>DTDL para interfaces estendidas 

O DTDL para as interfaces estendidas, limitado à parte discutida acima, ficaria assim. 

```json
[
  {
    "@id": "dtmi:com:example:Space;1",
    "@type": "Interface",
    "extends": "dtmi:digitaltwins:rec_3_3:core:Space;1",
    "contents": [
      {
        "@type": "Property",
        "name": "drawingid",
        "schema": "string"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Room;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:core:Room;1",
      "dtmi:com:example:Space;1"
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:ConferenceRoom;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:ConferenceRoom;1",
      "dtmi:com:example:Room;1"
    ],
    "contents": [
      {
        "@type": "Property",
        "name": "online",
        "schema": "boolean"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Office;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:Office;1", 
      "dtmi:com:example:Room;1" 
    ],
    "@context": "dtmi:dtdl:context;2" 
  }, 
  {
    "@id": "dtmi:com:example:FocusRoom;1", 
    "@type": "Interface", 
    "extends": "dtmi:com:example:Office;1", 
    "@context": "dtmi:dtdl:context;2" 
  }
]
``` 

## <a name="next-steps"></a>Próximas etapas

Continue no caminho para desenvolver modelos com base em ontologies: [*usando estratégias de ontologia em um caminho de desenvolvimento de modelo*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).