---
title: Integrar modelos padrão do setor
titleSuffix: Azure Digital Twins
description: Entenda como integrar modelos padrão do setor ao DTDL para o gêmeos digital do Azure usando o DTDL ontologies especial ou convertendo os ontologies existentes
author: baanders
ms.author: baanders
ms.date: 11/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3376f5d5e207a33ad39cd7506998e2ee90e084ad
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051540"
---
# <a name="integrate-industry-standard-models-with-dtdl-for-azure-digital-twins"></a>Integre modelos padrão do setor com o DTDL para o gêmeos digital do Azure

Usar modelos baseados em padrões do setor ou usar a representação ontologia padrão, como RDF ou OWL, fornece um ponto de partida rico ao projetar seus modelos do Azure digital gêmeos. O uso de modelos do setor também ajuda na padronização e no compartilhamento de informações.

Para ser usado com o gêmeos digital do Azure, um modelo deve ser representado na [**DTDL (digital gêmeos Definition Language)**](concepts-models.md)baseada em JSON-ld. Portanto, este artigo descreve como representar os modelos padrão do setor no DTDL, integrando os conceitos existentes do setor com a semântica DTDL para que o Azure digital gêmeos possa usá-los. O modelo DTDL, em seguida, serve como a fonte de verdade para o modelo dentro do gêmeos digital do Azure.

Há três caminhos possíveis para integrar modelos padrão do setor com o DTDL:
* **Adote**: você pode iniciar sua solução com um ontologia de DTDL de software livre criado com base em padrões de indústria amplamente adotados. 
* **Converter**: se você já tiver modelos existentes, precisará convertê-los em DTDL.
* **Autor**: você sempre pode desenvolver seus próprios modelos de DTDL personalizados do zero, conforme descrito em [*como gerenciar modelos personalizados*](how-to-manage-model.md).

## <a name="adopt-an-open-source-dtdl-ontology"></a>Adote um ontologia de DTDL de software livre

Geralmente, é mais fácil começar com um ontologia DTDL de software livre do que a partir de uma página em branco. 

Por exemplo, as soluções de prédios inteligentes podem aproveitar o [**RealEstateCore ontologia**](https://github.com/Azure/opendigitaltwins-building)de software livre baseado em DTDL, que fornece uma base comum para a modelagem de prédios inteligentes, aproveitando os padrões do setor para evitar a reinvenção. 

Essas DTDL ontologies de software livre também fornecem práticas recomendadas sobre como consumir e estender adequadamente os modelos. 

## <a name="convert-existing-models-to-dtdl"></a>Converter modelos existentes em DTDL

A maioria dos modelos do setor (também conhecidos como **ontologies**) baseia-se em padrões da Web semânticos, como [Owl](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)e [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Para usar um modelo com o gêmeos digital do Azure, ele deve estar no formato DTDL. Esta seção descreve as diretrizes de design gerais na forma de um **padrão de conversão** para converter modelos baseados em RDF para DTDL para que eles possam ser usados com o gêmeos digital do Azure. 

Ele também contém [o **código do conversor de exemplo** para um conversor RDF](#sample-converter-application), que foi validado para o esquema de [Brick](https://brickschema.org/ontology/) e pode ser estendido para outros esquemas no setor de construção.

### <a name="conversion-pattern"></a>Padrão de conversão

Há várias bibliotecas de terceiros que podem ser usadas ao converter modelos baseados em RDF em DTDL. Algumas dessas bibliotecas permitem que você carregue seu arquivo de modelo em um grafo. Você pode executar um loop no grafo procurando construções RDFS e OWL específicas e convertê-las em DTDL.   

A tabela a seguir é um exemplo de como as construções RDFS e OWL podem ser mapeadas para DTDL. 

| Conceito de RDFS/OWL | Construção RDFS/OWL | Conceito de DTDL | Construção DTDL |
| --- | --- | --- | --- |
| Classes | `owl:Class`<br>Sufixo IRI<br>``rdfs:label``<br>``rdfs:comment`` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Subclasses | `owl:Class`<br>Sufixo IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Propriedades de DataType | `owl:DatatypeProperty`<br>`rdfs:label` ou `INode`<br>`rdfs:label`<br>`rdfs:range` | Propriedades de interface | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Propriedades de objeto | `owl:ObjectProperty`<br>`rdfs:label` ou `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relationship | `type:Relationship`<br>`name`<br>`target` (ou omitido se não `rdfs:range` )<br>`comment`<br>`displayName`<br>

O trecho de código C# a seguir mostra como um arquivo de modelo RDF é carregado em um grafo e convertido em DTDL, usando a biblioteca [**dotNetRDF**](https://www.dotnetrdf.org/) . 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

### <a name="sample-converter-application"></a>Aplicativo de conversor de exemplo 

Há um aplicativo de exemplo disponível que converte um arquivo de modelo baseado em RDF para [DTDL (versão 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) para uso pelo serviço gêmeos do Azure digital. Ele foi validado para o esquema de [Brick](https://brickschema.org/ontology/) e pode ser estendido para outros esquemas no setor de construção (como a [criação de ontologia de topologia (bot)](https://w3c-lbd-cg.github.io/bot/), [rede de sensor semântico](https://www.w3.org/TR/vocab-ssn/)ou [IFC (buildingSmart Industry Foundation classes)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

O exemplo é um aplicativo de linha de comando do .NET Core chamado **RdfToDtdlConverter**.

Você pode obter o exemplo aqui: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Para baixar o código em seu computador, clique no botão *baixar zip* abaixo do título na página de aterrissagem de exemplo. Isso fará o download de um arquivo *zip* sob o nome *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*, que você pode descompactar e explorar.

Você pode usar este exemplo para ver os padrões de conversão no contexto e para ter como um bloco de construção para seus próprios aplicativos executando conversões de modelo de acordo com suas necessidades específicas.

## <a name="validate-and-upload-dtdl-models"></a>Validar e carregar modelos DTDL

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

Depois que um modelo é convertido e validado, você pode **carregá-lo em sua instância de gêmeos digital do Azure**. Para obter mais informações sobre esse processo, consulte a seção [*carregar modelos*](how-to-manage-model.md#upload-models) de *como: gerenciar modelos personalizados*.

## <a name="next-steps"></a>Próximas etapas 

Leia mais sobre como projetar e gerenciar modelos de entrelaçamento digital:
 
* [*Conceitos: modelos personalizados*](concepts-models.md)
* [*Como gerenciar modelos personalizados*](how-to-manage-model.md)
* [*Como: analisar e validar modelos*](how-to-parse-models.md)