---
title: Operações de e/s básica | Mapas do Microsoft Azure
description: Saiba como ler e gravar com eficiência dados XML e delimitados usando bibliotecas principais do módulo de e/s espacial.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 08966549d2c4d2bf701ee0dbfc5ed24f202396fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891369"
---
# <a name="core-io-operations"></a>Operações de e/s básica

Além de fornecer ferramentas para ler arquivos de dados espaciais, o módulo de e/s espacial expõe as bibliotecas principais subjacentes para ler e gravar dados XML e delimitados de forma rápida e eficiente.

O `atlas.io.core` namespace contém duas classes de nível baixo que podem ler e gravar dados CSV e XML rapidamente. Essas classes base capacitam os leitores de dados espaciais e os gravadores no módulo de e/s espacial. Sinta-se à vontade para usá-los para adicionar suporte adicional de leitura e gravação para arquivos CSV ou XML.
 
## <a name="read-delimited-files"></a>Ler arquivos delimitados

A `atlas.io.core.CsvReader` classe lê cadeias de caracteres que contêm conjuntos de dados delimitados. Essa classe fornece dois métodos para ler dados:

- A `read` função lerá o conjunto de dados completo e retornará uma matriz bidimensional de cadeias de caracteres representando todas as células do conjunto de dados delimitado.
- A `getNextRow` função lê cada linha de texto em um conjunto de dados delimitado e retorna uma matriz de cadeia de caracteres que representa todas as células nessa linha de conjunto de dados. O usuário pode processar a linha e descartar a memória desnecessária dessa linha antes de processar a próxima linha. Portanto, a função é mais eficiente na memória.

Por padrão, o leitor usará o caractere de vírgula como o delimitador. No entanto, o delimitador pode ser alterado para qualquer caractere único ou definido como `'auto'` . Quando definido como `'auto'` , o leitor analisará a primeira linha de texto na cadeia de caracteres. Em seguida, ele selecionará o caractere mais comum da tabela abaixo para usar como o delimitador.

| Delimitador | Caractere |
| :-- | :-- |
| Vírgula | `,` |
| Tab | `\t` |
| Pipe | `|` |

Esse leitor também dá suporte a qualificadores de texto que são usados para manipular células que contêm o caractere delimitador. O caractere quote ( `'"'` ) é o qualificador de texto padrão, mas pode ser alterado para qualquer caractere único.

## <a name="write-delimited-files"></a>Gravar arquivos delimitados

O `atlas.io.core.CsvWriter` grava uma matriz de objetos como uma cadeia de caracteres delimitada. Qualquer caractere único pode ser usado como um delimitador ou um qualificador de texto. O delimitador padrão é vírgula ( `','` ) e o qualificador de texto padrão é o caractere de aspas ( `'"'` ).

Para usar essa classe, siga as etapas abaixo:

- Crie uma instância da classe e, opcionalmente, defina um delimitador personalizado ou um qualificador de texto.
- Grave dados na classe usando a `write` função ou a `writeRow` função. Para a `write` função, passe uma matriz bidimensional de objetos representando várias linhas e células. Para usar a `writeRow` função, passe uma matriz de objetos representando uma linha de dados com várias colunas.
- Chame a `toString` função para recuperar a cadeia de caracteres delimitada. 
- Opcionalmente, chame o `clear` método para tornar o gravador reutilizável e reduzir sua alocação de recursos ou chame o `delete` método para descartar a instância do gravador.

> [!Note]
> O número de colunas gravadas será restrito ao número de células na primeira linha dos dados passados para o gravador.

## <a name="read-xml-files"></a>Ler arquivos XML

A `atlas.io.core.SimpleXmlReader` classe é mais rápida na análise de arquivos XML do que o `DOMParser` . No entanto, a `atlas.io.core.SimpleXmlReader` classe requer que os arquivos XML sejam bem formatados. Os arquivos XML que não estão bem formatados, por exemplo, marcas de fechamento ausentes, provavelmente resultarão em um erro.

O código a seguir demonstra como usar a `SimpleXmlReader` classe para analisar uma cadeia de caracteres XML em um objeto JSON e serializá-la em um formato desejado.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>Gravar arquivos XML

A `atlas.io.core.SimpleXmlWriter` classe grava XML bem formatado de forma eficiente da memória.

O código a seguir demonstra como usar a `SimpleXmlWriter` classe para gerar uma cadeia de caracteres XML bem formatada.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

O XML gerado do código acima seria semelhante ao seguinte.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

[CsvReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

[CsvWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

[SimpleXmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

[SimpleXmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

[Detalhes do formato de dados com suporte](spatial-io-supported-data-format-details.md)