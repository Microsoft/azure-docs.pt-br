---
title: Azure Monitor visualizações de texto da pasta de trabalho
description: Saiba mais sobre as visualizações de texto da pasta de trabalho do Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: e8f2d9495484b781b26962c2946b5bada6c38b4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100605237"
---
# <a name="text-visualizations"></a>Visualizações de texto

As pastas de trabalho permitem que os autores incluam blocos de texto em suas pastas de trabalho. O texto pode ser uma análise humana da telemetria, informações para ajudar os usuários a interpretar seus dados, títulos de seção, etc.

[![Captura de tela da tabela Apdex de texto](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

O texto é adicionado por meio de um controle de redução, que fornece controle de formatação completo. Isso inclui diferentes estilos de título e fonte, hiperlinks, tabelas, etc.

Modo de edição:

![Captura de tela de uma etapa de texto no modo de edição.](./media/workbooks-text-visualizations/text-edit-mode.png)

Modo de visualização:

![Captura de tela de uma etapa de texto no modo de edição na guia Visualização.](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>Adicionar um controle de texto

1. Alterne a pasta de trabalho para o modo de edição clicando no item **Editar** barra de ferramentas.
2. Use o link **Adicionar texto** para adicionar um controle de texto à pasta de trabalho.
3. Adicione redução no campo do editor.
4. Use a opção *estilo de texto* para alternar entre redução e redução simples encapsuladas com o estilo padrão de portal do Azure/aviso/êxito/erro.
5. Use a guia **Visualização** para ver como o conteúdo será exibido. Durante a edição, a visualização mostrará o conteúdo dentro de uma área de barra de rolagem para limitar seu tamanho; no entanto, em tempo de execução, o conteúdo de redução será expandido para preencher qualquer espaço necessário, sem barras de rolagem.
6. Selecione o botão de **edição concluído** para concluir a edição da etapa.

> [!TIP]
> Use esta [página](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) de dicas de redução para saber mais sobre as diferentes opções de formatação.

## <a name="text-styles"></a>Estilos de texto

Os seguintes estilos de texto estão disponíveis para a etapa de texto:

| Estilo     | Explicação                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | Nenhuma formatação adicional é aplicada.                                                      |
| `info`    | O estilo de "informações" do portal, com um  `ℹ` ou mais ícones semelhantes e geralmente um plano de fundo azul.      |
| `error`   | O estilo de "erro" do portal, com um `❌` ou mais ícones semelhantes e geralmente em vermelho.     |
| `success` | O estilo de "sucesso" do portal, com um `✔` ou mais ícones semelhantes e geralmente um plano de fundo verde.  |
| `upsell`  | O estilo de "venda" do portal, com um `🚀` ou mais ícones semelhantes e geralmente um plano de fundo roxo. |
| `warning` | O estilo de "aviso" do portal, com um `⚠` ou mais ícones semelhantes e geralmente um plano de fundo azul.   |

Em vez de selecionar um estilo específico, você também pode escolher um parâmetro de texto como a origem do estilo. O valor do parâmetro deve ser um dos valores de texto acima. A ausência de um valor ou de qualquer valor não reconhecido será tratada como `plain` estilo.

Exemplo de estilo de informação:

![Captura de tela de como o estilo de informações se parece.](./media/workbooks-text-visualizations/text-preview-info-style.png)

Exemplo de estilo de aviso:

![Captura de tela da aparência do estilo de aviso.](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba como criar um [gráfico em pastas de trabalho](workbooks-chart-visualizations.md).
* Saiba como criar uma [grade em pastas de trabalho](workbooks-grid-visualizations.md).
