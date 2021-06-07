---
title: Conversão de ontologias
titleSuffix: Azure Digital Twins
description: Entenda o processo de conversão de modelos padrão do setor em DTDL para o gêmeos digital do Azure
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: aa4dde51c077152dd5c8a938ad64ad0a051f89ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100561312"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Converter ontologies padrão do setor em DTDL para o Azure digital gêmeos

A maioria dos [ontologiess](concepts-ontologies.md) baseia-se em padrões da Web semânticos, como [Owl](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)e [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Para usar um modelo com o gêmeos digital do Azure, ele deve estar no formato DTDL. Este artigo descreve as diretrizes de design gerais na forma de um **padrão de conversão** para converter modelos baseados em RDF para DTDL para que eles possam ser usados com o gêmeos digital do Azure. 

O artigo também contém o [**código de exemplo de conversor**](#converter-samples) para conversores RDF e Owl, que podem ser estendidos para outros esquemas no setor de construção.

## <a name="conversion-pattern"></a>Padrão de conversão

Há várias bibliotecas de terceiros que podem ser usadas ao converter modelos baseados em RDF em DTDL. Algumas dessas bibliotecas permitem que você carregue seu arquivo de modelo em um grafo. Você pode executar um loop no grafo procurando construções RDFS e OWL específicas e convertê-las em DTDL.   

A tabela a seguir é um exemplo de como as construções RDFS e OWL podem ser mapeadas para DTDL. 

| Conceito de RDFS/OWL | Construção RDFS/OWL | Conceito de DTDL | Construção DTDL |
| --- | --- | --- | --- |
| Classes | `owl:Class`<br>Sufixo IRI<br>``rdfs:label``<br>``rdfs:comment`` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Subclasses | `owl:Class`<br>Sufixo IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Propriedades de DataType | `owl:DatatypeProperty`<br>`rdfs:label` ou `INode`<br>`rdfs:label`<br>`rdfs:range` | Propriedades de interface | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Propriedades de objeto | `owl:ObjectProperty`<br>`rdfs:label` ou `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relação | `type:Relationship`<br>`name`<br>`target` (ou omitido se não `rdfs:range` )<br>`comment`<br>`displayName`<br>

O trecho de código C# a seguir mostra como um arquivo de modelo RDF é carregado em um grafo e convertido em DTDL, usando a biblioteca [**dotNetRDF**](https://www.dotnetrdf.org/) . 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Exemplos de conversor

### <a name="rdf-converter-application"></a>Aplicativo de conversor RDF 

Há um aplicativo de exemplo disponível que converte um arquivo de modelo baseado em RDF para [DTDL (versão 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) para uso pelo serviço gêmeos do Azure digital. Ele foi validado para o esquema de [Brick](https://brickschema.org/ontology/) e pode ser estendido para outros esquemas no setor de construção (como a [criação de ontologia de topologia (bot)](https://w3c-lbd-cg.github.io/bot/), [rede de sensor semântico](https://www.w3.org/TR/vocab-ssn/)ou [IFC (buildingSmart Industry Foundation classes)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

O exemplo é um aplicativo de linha de comando do .NET Core chamado **RdfToDtdlConverter**.

Você pode obter o exemplo aqui: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Para baixar o código em seu computador, clique no botão *baixar zip* abaixo do título na página de aterrissagem de exemplo. Isso fará o download de um arquivo *zip* sob o nome *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*, que você pode descompactar e explorar.

Você pode usar este exemplo para ver os padrões de conversão no contexto e para ter como um bloco de construção para seus próprios aplicativos executando conversões de modelo de acordo com suas necessidades específicas.

### <a name="owl2dtdl-converter"></a>Conversor de OWL2DTDL 

O [**conversor de OWL2DTDL**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) é um exemplo que traduz um ontologia de Owl em um conjunto de declarações de interface DTDL, que pode ser usado com o serviço de gêmeos do Azure digital. Ele também funciona para redes ontologia, feitas de uma ontologia raiz reutilizando outras ontologies por meio de `owl:imports` declarações.

Esse conversor foi usado para converter o [ontologia principal de imóveis](https://doc.realestatecore.io/3.1/full.html) em DTDL e pode ser usado para qualquer ontologia baseado em Owl.

## <a name="next-steps"></a>Próximas etapas 

* Saiba mais sobre como estender ontologies padrão do setor para atender às suas especificações: [*conceitos: estendendo ontologies do setor*](concepts-ontologies-extend.md).

* Ou, continue no caminho para desenvolver modelos com base em ontologies: [*usando estratégias de ontologia em um caminho de desenvolvimento de modelo*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).