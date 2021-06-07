---
title: Formato de redução-QnA Maker
description: A seguir está a lista de formatos de redução que você pode usar no texto de resposta de QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 03/19/2020
ms.openlocfilehash: f49e05578f55a38845acbd4010f928fb17c51606
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96352262"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>Formato de redução com suporte no texto de resposta QnA Maker

QnA Maker armazena o texto de resposta como uma redução. Há muitos tipos de redução. Para certificar-se de que o texto de resposta seja retornado e exibido corretamente, use essa referência.

Use o tutorial **[CommonMark](https://commonmark.org/help/tutorial/index.html)** para validar seu Markdown. O tutorial tem um recurso **Experimentar** para validação rápida de copiar/colar.

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Quando usar edição de Rich Text versus redução

A edição de respostas em [Rich Text](How-To/edit-knowledge-base.md#add-an-editorial-qna-set) permite que você, como autor, use uma barra de ferramentas de formatação para selecionar e formatar rapidamente o texto.

A redução é uma ferramenta melhor quando você precisa gerar o conteúdo automaticamente para criar bases de dados de conhecimento a serem importadas como parte de um pipeline de CI/CD ou para [testes em lotes](./index.yml).

## <a name="supported-markdown-format"></a>Formato de redução com suporte

A seguir está a lista de formatos de redução que você pode usar no texto de resposta de QnA Maker.

|Finalidade|Formatar|Exemplo de markdown|Renderização<br>conforme exibido no bot de chat|
|--|--|--|--|
Uma nova linha entre duas frases.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![Formatar nova linha entre duas frases](./media/qnamaker-concepts-datasources/format-newline.png)|
|Cabeçalhos de H1 a H6, o número de `#` denota o cabeçalho. 1 `#` é o H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![Formatar com cabeçalhos de redução](./media/qnamaker-concepts-datasources/format-headers.png)<br>![Formatar com cabeçalhos de redução H1 a H5](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Itálico |`*text*`|`How do I create a bot with *QnA Maker*?`|![Formatar com itálico](./media/qnamaker-concepts-datasources/format-italics.png)|
|Forte (negrito)|`**text**`|`How do I create a bot with **QnA Maker**?`|![Formatar com marcação forte para negrito](./media/qnamaker-concepts-datasources/format-strong.png)|
|URL para o link|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![formato da URL (hiperlink)](./media/qnamaker-concepts-datasources/format-url.png)|
|* URL para imagem pública|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![formato da URL da imagem pública ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|Tachado|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![formato de tachado](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Negrito e itálico|`***text**_`|`How can I create a _*_QnA Maker_** bot?`|![formato para negrito e itálico](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|URL em negrito para o link|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![formato para URL em negrito](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|URL de itálico para o link|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![formato para URL de itálico](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Símbolos de redução de escape|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![Formato para símbolos de redução de escape.](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Lista ordenada|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>O exemplo anterior usa a numeração automática incorporada à redução.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>O exemplo anterior usa a numeração explícita.|![formato da lista ordenada](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Lista não ordenada|`\n * item1 \n * item2`<br>ou<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![formato da lista não ordenada](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Listas aninhadas|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Você pode aninhar listas ordenadas e não ordenadas juntas. A guia, `\t` , indica o nível de recuo do elemento filho.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![formato da lista não ordenada aninhada](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![formato da lista ordenada aninhada](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

* QnA Maker não processa a imagem de forma alguma. É a função do aplicativo cliente para renderizar a imagem.

Se você quiser adicionar conteúdo usando atualizar/substituir APIs da base de dados de conhecimento e o conteúdo/arquivo contiver marcas HTML, você poderá preservar o HTML em seu arquivo, garantindo que a abertura e o fechamento das marcas sejam convertidos no formato codificado.

| Preservar HTML  | Representação na solicitação de API  | Representação em KB |
|-----------|---------|-------------------------|
| Sim | \&lt; br \& gt; | &lt;br&gt; |
| Sim | \&lt; H3 \& gt; header \& lt;/H3 \& gt; | &lt;&gt;/H3 de cabeçalho H3 &lt;&gt; |

Além disso, CR LF (\r\n) são convertidas em \n no KB. A LF (\n) é mantida como está. Se você quiser escapar qualquer sequência de escape como um \t ou \n, poderá usar a barra invertida, por exemplo: ' \\ \\ r \\ \\ n ' e ' \\ \\ T'

## <a name="next-steps"></a>Próximas etapas

Examine os [formatos de arquivo](reference-tsv-format-batch-testing.md)de teste de lote.