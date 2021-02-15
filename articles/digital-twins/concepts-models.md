---
title: Modelos personalizados
titleSuffix: Azure Digital Twins
description: Entenda como o Azure digital gêmeos usa modelos definidos pelo usuário para descrever as entidades em seu ambiente.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 599bb93e747acf504a4ebf43aaea771ed5064886
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131382"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Entender os modelos de entrelaçamento no Azure digital gêmeos

Uma característica importante do Azure digital gêmeos é a capacidade de definir seu próprio vocabulário e criar seu grafo de entrelaçamento nos termos autodefinidos de sua empresa. Esse recurso é fornecido por meio de **modelos** definidos pelo usuário. Você pode considerar os modelos como os substantivos em uma descrição do seu mundo. 

Um modelo é semelhante a uma **classe** em uma linguagem de programação orientada a objeto, definindo uma forma de dados para um determinado conceito em seu ambiente de trabalho real. Os modelos têm nomes (como *sala* ou *sensor*) e contêm elementos como propriedades, telemetria/eventos e comandos que descrevem o que esse tipo de entidade em seu ambiente pode fazer. Posteriormente, você usará esses modelos para criar [**gêmeos digitais**](concepts-twins-graph.md) que representam entidades específicas que atendem a essa descrição de tipo.

Os modelos de gêmeos digitais do Azure são representados na **DTDL (digital Mydefinition Language)** baseada em JSON-ld.  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>DTDL (digital mydefinition Language) para modelos

Os modelos para o gêmeos digital do Azure são definidos usando o DTDL (digital gêmeos Definition Language). O DTDL é baseado em JSON-LD e é independente da linguagem de programação. O DTDL não é exclusivo do Azure digital gêmeos, mas também é usado para representar dados de dispositivo em outros serviços de IoT, como o [IoT plug and Play](../iot-pnp/overview-iot-plug-and-play.md). 

O Azure digital gêmeos usa a **_versão 2_ do DTDL**. Para obter mais informações sobre esta versão do DTDL, consulte sua documentação de especificações no GitHub: [*digital gêmeos Definition Language (DTDL)-versão 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). O uso do DTDL _versão 1_ com o Azure digital gêmeos agora foi preterido.

> [!NOTE] 
> Nem todos os serviços que usam DTDL implementam exatamente os mesmos recursos de DTDL. Por exemplo, a Plug and Play de IoT não usa os recursos de DTDL que são para grafos, enquanto o Azure digital gêmeos atualmente não implementa comandos DTDL.
>
> Para obter mais informações sobre os recursos do DTDL que são específicos do Azure digital gêmeos, consulte a seção mais adiante neste artigo sobre [especificações de implementação do Azure digital gêmeos DTDL](#azure-digital-twins-dtdl-implementation-specifics).

## <a name="elements-of-a-model"></a>Elementos de um modelo

Dentro de uma definição de modelo, o item de código de nível superior é uma **interface**. Isso encapsula todo o modelo e o restante do modelo é definido dentro da interface. 

Uma interface de modelo DTDL pode conter zero, um ou muitos dos seguintes campos:
* Propriedades de **Propriedade** são campos de dados que representam o estado de uma entidade (como as propriedades em muitas linguagens de programação orientada a objeto). As propriedades têm armazenamento de backup e podem ser lidas a qualquer momento.
* **Telemetria** -os campos de telemetria representam medições ou eventos e geralmente são usados para descrever as leituras do sensor de dispositivo. Ao contrário das propriedades, a telemetria não é armazenada em uma teledigital. é uma série de eventos de dados associados ao tempo que precisam ser manipulados conforme eles ocorrem. Para obter mais informações sobre as diferenças entre a propriedade e a telemetria, consulte a seção [*Properties vs. telemetria*](#properties-vs-telemetry) abaixo.
* **Componente** -componentes permitem que você crie sua interface de modelo como um assembly de outras interfaces, se desejar. Um exemplo de componente é uma interface *frontCamera* (e outra *backcamera* da interface de componente) que são usados na definição de um modelo para um *telefone*. Primeiro, você deve definir uma interface para *frontCamera* como se fosse seu próprio modelo e, em seguida, pode fazer referência a ela ao definir o *telefone*.

    Use um componente para descrever algo que é parte integrante da sua solução, mas que não precisa de uma identidade separada, e não precisa ser criado, excluído ou reorganizado no grafo de entrelaçamento de forma independente. Se você quiser que as entidades tenham existência independentes no grafo de entrelaçamento, represente-as como gêmeos digitais separadas de modelos diferentes, conectadas por *relações* (consulte o próximo marcador).
    
    >[!TIP] 
    >Os componentes também podem ser usados para a organização, para agrupar conjuntos de propriedades relacionadas em uma interface de modelo. Nessa situação, você pode considerar cada componente como um namespace ou "pasta" dentro da interface.
* As relações de **relacionamento** permitem que você represente como uma teledigital pode ser envolvida com outros gêmeos digitais. As relações podem representar significados de semântica diferentes, como *Contains* ("piso contém sala"), *frios* ("sala de frios do HVAC"), *isBilledTo* ("o compresso é cobrado pelo usuário") etc. As relações permitem que a solução forneça um grafo de entidades inter-relacionadas.

> [!NOTE]
> A [especificação para DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) também define **comandos**, que são métodos que podem ser executados em uma troca digital (como um comando de redefinição ou um comando para ativar ou desativar um ventilador). No entanto, os *comandos não têm suporte no momento no Azure digital gêmeos.*

### <a name="properties-vs-telemetry"></a>Propriedades versus telemetria

Aqui estão algumas orientações adicionais sobre como distinguir entre campos de **Propriedade** e **telemetria** DTDL no Azure digital gêmeos.

A diferença entre as propriedades e a telemetria para os modelos de gêmeos digital do Azure é a seguinte:
* Espera-se que **as propriedades** tenham armazenamento de backup. Isso significa que você pode ler uma propriedade a qualquer momento e recuperar seu valor. Se a propriedade for gravável, você também poderá armazenar um valor na propriedade.  
* A **telemetria** é mais semelhante a um fluxo de eventos; é um conjunto de mensagens de dados que têm breves providas. Se você não configurar a escuta para o evento e as ações a serem executadas quando isso acontecer, não haverá nenhum rastreamento do evento posteriormente. Não é possível voltar a ele e lê-lo mais tarde. 
  - Em termos do C#, a telemetria é como um evento em C#. 
  - Em termos de IoT, a telemetria normalmente é uma única medida enviada por um dispositivo.

A **telemetria** é geralmente usada com dispositivos IOT, porque muitos dispositivos não são capazes de, ou interesse, de armazenar os valores de medida que eles geram. Eles apenas os enviam como um fluxo de eventos de "telemetria". Nesse caso, não é possível consultar o dispositivo a qualquer momento para obter o valor mais recente do campo telemetria. Em vez disso, você precisará escutar as mensagens do dispositivo e executar ações à medida que as mensagens chegarem. 

Como resultado, ao criar um modelo no Azure digital gêmeos, você provavelmente usará **Propriedades** na maioria dos casos para modelar seu gêmeos. Isso permite que você tenha o armazenamento de backup e a capacidade de ler e consultar os campos de dados.

A telemetria e as propriedades geralmente funcionam juntas para lidar com a entrada de dados de dispositivos. Como todas as ingressos no Azure digital gêmeos são por meio de [APIs](how-to-use-apis-sdks.md), normalmente você usará sua função de entrada para ler eventos de telemetria ou propriedade de dispositivos e definirá uma propriedade em ADT em resposta. 

Você também pode publicar um evento de telemetria da API gêmeos do Azure digital. Assim como acontece com outras telemetrias, isso é um evento de curta duração que requer um ouvinte para manipular.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Especificações de implementação do Azure digital gêmeos DTDL

Para que um modelo DTDL seja compatível com o gêmeos digital do Azure, ele deve atender a esses requisitos.

* Todos os elementos DTDL de nível superior em um modelo devem ser do tipo *interface*. Isso ocorre porque as APIs do modelo do gêmeos digital do Azure podem receber objetos JSON que representam uma interface ou uma matriz de interfaces. Como resultado, nenhum outro tipo de elemento DTDL é permitido no nível superior.
* O DTDL para o gêmeos digital do Azure não deve definir nenhum *comando*.
* O Azure digital gêmeos permite apenas um único nível de aninhamento de componentes. Isso significa que uma interface que está sendo usada como um componente não pode ter nenhum componente. 
* Interfaces não podem ser definidas embutidas em outras interfaces DTDL; Eles devem ser definidos como entidades de nível superior separadas com suas próprias IDs. Em seguida, quando outra interface deseja incluir essa interface como um componente ou por herança, ela pode referenciar sua ID.

O gêmeos digital do Azure também não observa o `writable` atributo em Propriedades ou relações. Embora isso possa ser definido de acordo com as especificações de DTDL, o valor não é usado pelo gêmeos digital do Azure. Em vez disso, eles são sempre tratados como graváveis por clientes externos que têm permissões de gravação gerais para o serviço de gêmeos digital do Azure.

## <a name="example-model-code"></a>Exemplo de código de modelo

Os modelos de tipo de entrelaçamento podem ser escritos em qualquer editor de texto. A linguagem DTDL segue a sintaxe JSON, portanto, você deve armazenar modelos com a extensão *. JSON*. Usar a extensão JSON permitirá que muitos editores de texto de programação forneçam verificação e realce de sintaxe básica para seus documentos do DTDL. Também há uma [extensão DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) disponível para [Visual Studio Code](https://code.visualstudio.com/).

Esta seção contém um exemplo de um modelo típico, escrito como uma interface DTDL. O modelo descreve os **planetas**, cada um com um nome, uma massa e uma temperatura.
 
Considere que os planetas também podem interagir com **luas** que são seus satélites e **podem conter** enjuntores. No exemplo a seguir, o `Planet` modelo expressa conexões com essas outras entidades fazendo referência a dois modelos externos — `Moon` e `Crater` . Esses modelos também são definidos no código de exemplo abaixo, mas são mantidos muito simples para não detrair o `Planet` exemplo primário.

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

Os campos do modelo são:

| Campo | Descrição |
| --- | --- |
| `@id` | Um identificador para o modelo. Deve estar no formato `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | Identifica o tipo de informações que estão sendo descritas. Para uma interface, o tipo é *interface*. |
| `@context` | Define o [contexto](https://niem.github.io/json/reference/json-ld/context/) para o documento JSON. Os modelos devem usar o `dtmi:dtdl:context;2` . |
| `displayName` | adicional Permite que você dê um nome amigável ao modelo, se desejado. |
| `contents` | Todos os dados de interface restantes são colocados aqui, como uma matriz de definições de atributo. Cada atributo deve fornecer um `@type` (*Propriedade*, *telemetria*, *comando*, *relação* ou *componente*) para identificar o tipo de informações de interface que ele descreve e, em seguida, um conjunto de propriedades que definem o atributo real (por exemplo, `name` e `schema` para definir uma *Propriedade*). |

> [!NOTE]
> Observe que a interface de componente (*Crater* neste exemplo) é definida na mesma matriz que a interface que a usa (*planeta*). Os componentes devem ser definidos dessa forma em chamadas à API para que a interface seja encontrada.

### <a name="possible-schemas"></a>Esquemas possíveis

De acordo com o DTDL, o esquema para atributos de *Propriedade* e *telemetria* pode ser de tipos primitivos padrão — `integer` ,, `double` `string` e `Boolean` — e outros tipos, como `DateTime` e `Duration` . 

Além dos tipos primitivos, os campos de *Propriedade* e *telemetria* podem ter esses tipos complexos:
* `Object`
* `Map`
* `Enum`

Os campos de *telemetria* também dão suporte a `Array` .

### <a name="model-inheritance"></a>Herança de modelo

Às vezes, talvez você queira especializar ainda mais um modelo. Por exemplo, pode ser útil ter uma *sala* de modelo genérico e variantes especializadas *conferenceroom* e *Gym*. Para expressar especialização, o DTDL dá suporte à herança: as interfaces podem herdar de uma ou mais interfaces. 

O exemplo a seguir reimagina o modelo *planeta* do exemplo de DTDL anterior como um subtipo de um modelo de *CelestialBody* maior. O modelo "pai" é definido primeiro e, em seguida, o modelo "filho" é criado com base nele usando o campo `extends` .

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

Neste exemplo, *CelestialBody* contribui com um nome, uma massa e uma temperatura para o *planeta*. A `extends` seção é um nome de interface ou uma matriz de nomes de interface (permitindo que a interface de extensão herde de vários modelos pai, se desejado).

Depois que a herança é aplicada, a interface de extensão expõe todas as propriedades de toda a cadeia de herança.

A interface de extensão não pode alterar nenhuma das definições das interfaces pai; Ele só pode adicionar a eles. Ele também não pode redefinir um recurso já definido em qualquer uma de suas interfaces pai (mesmo que os recursos estejam definidos para serem os mesmos). Por exemplo, se uma interface pai define uma `double` propriedade de *massa*, a interface de extensão não pode conter uma declaração de *massa*, mesmo que também seja um `double` .

## <a name="best-practices-for-designing-models"></a>Práticas recomendadas para a criação de modelos

Ao criar modelos para refletir as entidades em seu ambiente, pode ser útil olhar em frente e considerar as implicações de [consulta](concepts-query-language.md) do seu design. Talvez você queira criar propriedades de uma maneira que evite grandes conjuntos de resultados de percurso de grafo. Você também pode querer modelar relações que serão respondidas em uma única consulta como relações de nível único.

### <a name="validating-models"></a>Validando modelos

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="integrating-with-industry-standard-models"></a>Integração com modelos padrão do setor

Usar modelos baseados em padrões do setor ou usar a representação ontologia padrão, como RDF ou OWL, fornece um ponto de partida rico ao projetar seus modelos do Azure digital gêmeos. O uso de modelos do setor também ajuda na padronização e no compartilhamento de informações.

Para ser usado com o gêmeos digital do Azure, um modelo deve ser representado na [**DTDL (digital gêmeos Definition Language)**](concepts-models.md)baseada em JSON-ld. Portanto, para usar um modelo padrão do setor, você deve primeiro convertê-lo em DTDL para que o gêmeos digital do Azure possa usá-lo. O modelo DTDL, em seguida, serve como a fonte de verdade para o modelo dentro do gêmeos digital do Azure.

Há dois caminhos principais para integrar modelos padrão do setor com o DTDL, dependendo da sua situação:
* Se você ainda tiver criado seus modelos, poderá criá-los em um **ontologies inicial DTDL existente** que contenha idioma específico do seu setor.
* Se você já tiver modelos existentes baseados em um padrão do setor, precisará **convertê-los em DTDL para** colocá-los no gêmeos digital do Azure.

Para obter mais informações sobre esses dois processos, consulte [*como: integrar modelos padrão do setor*](how-to-integrate-models.md).

## <a name="next-steps"></a>Próximas etapas

Consulte como gerenciar modelos com as APIs do DigitalTwinModels:
* [*Como gerenciar modelos personalizados*](how-to-manage-model.md)

Ou então, saiba como os gêmeos digitais são criados com base em modelos:
* [*Conceitos: digital gêmeos e o gráfico de entrelaçamento*](concepts-twins-graph.md)

