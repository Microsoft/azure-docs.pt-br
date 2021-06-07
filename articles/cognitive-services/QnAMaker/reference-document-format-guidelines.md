---
title: Diretrizes de importação de formato de documento-QnA Maker
description: Use estas diretrizes para importar documentos para obter os melhores resultados para seu conteúdo.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 15ff2ec296cedc37b086a9ca2d0825fb20b4f05a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99549534"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Diretrizes de formato para documentos e URLs importadas

Examine essas diretrizes de formatação para obter os melhores resultados para seu conteúdo.

## <a name="formatting-considerations"></a>Considerações de formatação

Depois de importar um arquivo ou URL, QnA Maker converte e armazena seu conteúdo no [formato de redução](https://en.wikipedia.org/wiki/Markdown). O processo de conversão adiciona novas linhas no texto, como `\n\n` . Um conhecimento do formato de redução ajuda você a entender o conteúdo convertido e gerenciar seu conteúdo da base de dados de conhecimento.

Se você adicionar ou editar seu conteúdo diretamente na sua base de dados de conhecimento, use a **formatação de redução** para criar conteúdo de Rich Text ou alterar o conteúdo do formato de redução que já está na resposta. QnA Maker dá suporte a grande parte do formato de redução para trazer recursos de Rich Text para seu conteúdo. No entanto, o aplicativo cliente, como um bot de chat, pode não dar suporte ao mesmo conjunto de formatos de redução. É importante testar a exibição de respostas do aplicativo cliente.

Consulte uma lista completa de [tipos de conteúdo e exemplos](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base).

## <a name="basic-document-formatting"></a>Formatação básica de documentos

QnA Maker identifica seções e subseções e relações no arquivo com base em pistas visuais como:

* tamanho da fonte
* estilo da fonte
* numeração
* cores

> [!NOTE]
> Não há suporte para extração de imagens de documentos carregados no momento.

### <a name="product-manuals"></a>Manuais de produtos

Normalmente, um manual é o material de diretrizes que acompanha um produto. Ele ajuda o usuário a configurar, usar, manter e solucionar problemas do produto. Quando o QnA Maker processa um manual, ele extrai os títulos e subtítulos como perguntas e o conteúdo subsequente como respostas. Veja um exemplo [aqui](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Abaixo está um exemplo de um manual com uma página de índice e conteúdo hierárquico

 ![Exemplo de manual de produto para uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> A extração funciona melhor em manuais com uma tabela de conteúdo e/ou uma página de índice e uma estrutura clara com cabeçalhos hierárquicos.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochuras, diretrizes, documentos e outros arquivos

Muitos outros tipos de documentos também podem ser processados para gerar pares de QA, contanto que tenham uma estrutura e um layout claros. Eles incluem: folhetos, diretrizes, relatórios, White papers, documentos científicos, políticas, livros, etc. Veja um exemplo [aqui](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Abaixo está um exemplo de um documento semiestruturado sem um índice:

 ![Documento semiestruturado de armazenamento de Blobs do Azure](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>Documento de QnA Estruturado

O formato para Pergunta-Respostas estruturas em arquivos DOC é na forma de Perguntas e Respostas alternadas por linha, uma pergunta por linha seguida pela respectiva resposta na linha seguinte, conforme mostrado abaixo:

```text
Question1

Answer1

Question2

Answer2
```

Abaixo está um exemplo de um documento do word de QnA estruturado:

 ![Exemplo de documento do QnA estruturado em uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

### <a name="structured-txt-tsv-and-xls-files"></a>Arquivos *TXT*, *TSV* e *XLS* Estruturados

QnAs na forma de arquivos *.txt*, *.tsv* ou *.xls* estruturados também podem ser carregadas para o QnA Maker para criar ou ampliar uma base de conhecimento.  Podem ser texto sem formatação ou ter conteúdo em RTF ou HTML.

| Pergunta  | Resposta  | Metadados (1 chave: 1 valor) |
|-----------|---------|-------------------------|
| Pergunta1 | Resposta1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 |      `Key:Value`           |

As colunas adicionais no arquivo de origem são ignoradas.

#### <a name="example-of-structured-excel-file"></a>Exemplo de arquivo do Excel estruturado

Abaixo está um exemplo de arquivo *.xls* de QnA estruturada, com conteúdo HTML:

 ![Exemplo do QnA estruturado em uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

#### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exemplo de perguntas alternativas para uma única resposta no arquivo do Excel

Veja abaixo um exemplo de um arquivo QnA *. xls* estruturado, com várias perguntas alternativas para uma única resposta:

 ![Exemplo de perguntas alternativas para uma única resposta no arquivo do Excel](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Depois que o arquivo for importado, o par de perguntas e respostas estará na base de dados de conhecimento, conforme mostrado abaixo:

 ![Captura de tela de perguntas alternativas para uma única resposta importada na base de dados de conhecimento](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

### <a name="structured-data-format-through-import"></a>Formato de dados estruturados por meio de importação

Importar uma base de dados de conhecimento substitui o conteúdo da base de dados de conhecimento existente. A importação requer um arquivo .tsv estruturado que contenha informações de fonte de dados. Essas informações ajudam o QnA Maker a agrupar os pares de resposta de pergunta e atribuí-los a uma fonte de dados específico.

| Pergunta  | Resposta  | Fonte| Metadados (1 chave: 1 valor) |
|-----------|---------|----|---------------------|
| Pergunta1 | Resposta1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pergunta2 | Resposta2 | Editorial|    `Key:Value`       |

<a href="#formatting-considerations"></a>

### <a name="multi-turn-document-formatting"></a>Formatação de documento de várias transformações

* Use títulos e subtítulos para denotar hierarquia. Por exemplo, você pode indicar que o pai QnA e H2 denotam o QnA que deve ser levado como prompt. Use pequeno tamanho de título para denotar a hierarquia subsequente. Não use estilo, cor ou algum outro mecanismo para implicar a estrutura em seu documento, QnA Maker não extrairá as solicitações de múltipla ativação.
* O primeiro caractere de título deve estar em letras maiúsculas.
* Não termine um cabeçalho com um ponto de interrogação, `?` .

**Documentos de exemplo**:<br>[Surface pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Benefícios da Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Benefícios da Contoso (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="faq-urls"></a>URLs de perguntas frequentes

O QnA Maker pode dar suporte a páginas da Web de perguntas frequentes em três diferentes formas:

* Páginas de perguntas frequentes simples
* Páginas de perguntas frequentes com links
* Página de perguntas frequentes com uma home page de tópicos

### <a name="plain-faq-pages"></a>Páginas de perguntas frequentes simples

Este é o tipo mais comum de página de perguntas frequentes em que as respostas a seguem imediatamente às perguntas na mesma página.

Abaixo está um exemplo de uma página de perguntas frequentes simples:

![Exemplo de página de perguntas frequentes simples para uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Páginas de perguntas frequentes com links

Nesse tipo de página de perguntas frequentes, as perguntas são agregadas e vinculadas a respostas em seções diferentes da mesma página ou em páginas diferentes.

Abaixo está um exemplo de uma página de perguntas frequentes com links nas seções que estão na mesma página:

 ![Exemplo de página de perguntas frequentes com link de seção para uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Links de página de tópicos pai para páginas de respostas filho

Esse tipo de perguntas frequentes tem uma página de tópicos em que cada tópico está vinculado a um conjunto correspondente de perguntas e respostas em uma página diferente. QnA Maker rastreia todas as páginas vinculadas para extrair as perguntas correspondentes & respostas.

Abaixo está um exemplo de uma página de tópicos com links para seções de perguntas frequentes em páginas diferentes.

 ![Exemplo de página de perguntas frequentes com link profundo para uma base de dados de conhecimento](./media/qnamaker-concepts-datasources/topics-faq.png)

### <a name="support-urls"></a>URLs de suporte

O QnA Maker pode processar páginas da web de suporte semiestruturadas, como artigos da web que descrevem como executar uma tarefa específica, como diagnosticar e resolver um problema específico e quais são as práticas recomendadas para um determinado processo. A extração funciona melhor em documentos que têm uma estrutura clara com cabeçalhos hierárquicos.

> [!NOTE]
> Extração para artigos de suporte é um recurso novo e está nos estágios iniciais. Funciona melhor para páginas simples, que também são estruturadas e não contêm cabeçalhos/rodapés complexos.

![O QnA Maker dá suporte à extração de páginas web semi-estruturadas em que a estrutura limpa é apresentada com cabeçalhos hierárquicos](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)

## <a name="import-and-export-knowledge-base"></a>Importar e exportar base de dados de conhecimento

Os **arquivos TSV e XLS**, das bases de dados de conhecimento exportadas, só podem ser usados pela importação dos arquivos da página **configurações** no portal de QnA Maker. Eles não podem ser usados como fontes de dados durante a criação da base de conhecimento ou no recurso **+ Adicionar arquivo** ou **+ Adicionar URL** na página **configurações** . 

Quando você importa a base de dados de conhecimento por meio desses **arquivos TSV e XLS**, os pares de QnA são adicionados à fonte editorial e não às fontes das quais os QnAs foram extraídos na base de dados de conhecimento exportada. 


## <a name="next-steps"></a>Próximas etapas

Veja uma lista completa de [tipos de conteúdo e exemplos](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base)
