---
title: Fontes de dados e tipos de conteúdo-QnA Maker
description: Saiba como importar pares de perguntas e respostas de fontes de dados e tipos de conteúdo com suporte, que incluem muitos documentos estruturados padrão, como PDF, DOCX e TXT-QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 0d4d32aba34a97c6a060c999694f66d79933d011
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99556044"
---
# <a name="importing-from-data-sources"></a>Importar de fontes de dados

Uma base de dados de conhecimento consiste em pares de perguntas e respostas trazidos por arquivos e URLs públicos.

## <a name="data-source-locations"></a>Locais de origem de dados

O conteúdo é trazido para uma base de conhecimento de uma fonte de dados. Os locais de fonte de dados são **URLs ou arquivos públicos**, que não exigem autenticação.

Os [arquivos do SharePoint](../how-to/add-sharepoint-datasources.md), protegidos com autenticação, são a exceção. Os recursos do SharePoint devem ser arquivos, não páginas da Web. Se a URL terminar com uma extensão da Web, como .ASPX, ela não será importada do SharePoint para o QnA Maker.

## <a name="chit-chat-content"></a>Chit-conteúdo de chat

O conjunto de conteúdo Chit-Chat é oferecido como uma fonte de dados de conteúdo completa em vários idiomas e estilos de conversação. Esse pode ser um ponto de partida para a personalidade do seu bot, o que economizará o tempo e o custo de escrevê-la do zero. Saiba [como adicionar conteúdo do Chit-Chat](../how-to/chit-chat-knowledge-base.md) à sua base de dados de conhecimento.

## <a name="structured-data-format-through-import"></a>Formato de dados estruturados por meio de importação

Importar uma base de dados de conhecimento substitui o conteúdo da base de dados de conhecimento existente. A importação requer um `.tsv` arquivo estruturado que contenha perguntas e respostas. Essas informações ajudam o QnA Maker a agrupar os pares de resposta de pergunta e atribuí-los a uma fonte de dados específico.

| Pergunta  | Resposta  | Fonte| Metadados (1 chave: 1 valor) |
|-----------|---------|----|---------------------|
| Pergunta1 | Resposta1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 | Editorial|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Formato de várias transformações estruturado por meio de importação

Você pode criar conversas com vários folheios em um `.tsv` formato de arquivo. O formato fornece a capacidade de criar as conversas de passagem múltipla analisando os logs de chat anteriores (com outros processos, não usando QnA Maker) e, em seguida, criar o `.tsv` arquivo por meio da automação. Importe o arquivo para substituir a base de dados de conhecimento existente.

> [!div class="mx-imgBorder"]
> ![Modelo conceitual de 3 níveis de pergunta de vários desligamentos](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

A coluna para uma passagem múltipla `.tsv` , específica para a múltipla ativação, é **solicitada**. Um exemplo `.tsv` , mostrado no Excel, mostra as informações a serem incluídas para definir os filhos de troca múltipla:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

O **displayOrder** é numérico e o **exibirtexto** é texto que não deve incluir redução.

> [!div class="mx-imgBorder"]
> ![Exemplo de pergunta de várias pontas, como mostrado no Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportar como exemplo

Se você não tiver certeza de como representar seu par de QnA no `.tsv` arquivo:
* Use este [exemplo para download do GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Ou crie o par no portal de QnA Maker, salve e, em seguida, exporte a base de dados de conhecimento para obter um exemplo de como representar o par.

## <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Tipos de conteúdo de documentos que você pode adicionar a uma base de dados de conhecimento
Os tipos de conteúdo incluem muitos documentos estruturados padrão, como PDF, DOC e TXT.

### <a name="file-and-url-data-types"></a>Tipos de dados de arquivo e URL

A tabela a seguir resume os tipos de conteúdo e formatos de arquivo com suporte no QnA Maker.

|Tipo de Fonte|Tipo de conteúdo| Exemplos|
|--|--|--|
|URL|Perguntas frequentes<br> (simples, com seções ou com uma página inicial de tópicos)<br>Páginas de suporte <br> (artigos de instrução de uma página, artigos de solução de problemas etc.)|[Perguntas frequentes simples](../troubleshooting.md), <br>[Perguntas frequentes com links](https://www.microsoft.com/en-us/software-download/faq),<br> [Perguntas frequentes com home page de tópicos](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artigo de suporte](./best-practices.md)|
|PDF/DOC|Perguntas frequentes,<br> Manual do Produto,<br> Folhetos,<br> Papel,<br> Política de folheto,<br> Guia de suporte,<br> QnA estruturado,<br> etc.|**Sem troca múltipla**<br>[QnA.docxestruturado ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Exemplo produto Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [semi-structured.docxde exemplo ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [paper.pdfbranco de exemplo ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Troca múltipla**:<br>[Surface pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Benefícios da Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Benefícios da Contoso (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|Arquivo QnA estruturado<br> (incluindo suporte RTF, HTML)|**Sem troca múltipla**:<br>[Exemplo de QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Troca múltipla**:<br>[FAQ.xlssimples estruturados ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[FAQ.xlsde laptop Surface ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|Arquivo QnA estruturado|[Exemplo de chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Se você precisar de autenticação para sua fonte de dados, considere os seguintes métodos para obter esse conteúdo em QnA Maker:

* Baixar o arquivo manualmente e importar para o QnA Maker
* Adicionar o arquivo do local do [SharePoint](../how-to/add-sharepoint-datasources.md) autenticado

### <a name="url-content"></a>Conteúdo da URL

Dois tipos de documentos podem ser importados por meio de **URL** no QnA Maker:

* URLs de perguntas frequentes
* URLs de suporte

Cada tipo indica um formato esperado.

### <a name="file-based-content"></a>Conteúdo baseado em arquivo

Você pode adicionar arquivos a uma base de dados de conhecimento de uma fonte pública ou seu sistema de arquivos local, no [portal de QnA Maker](https://www.qnamaker.ai).

### <a name="content-format-guidelines"></a>Diretrizes para formato de conteúdo

Saiba mais sobre as [diretrizes de formato](../reference-document-format-guidelines.md) para os diferentes arquivos.

## <a name="next-steps"></a>Próximas etapas

Saiba como [Editar QnAs](../how-to/edit-knowledge-base.md).
