---
title: IntelliSense no Azure Stream Analytics Tools para Visual Studio Code
description: Este artigo descreve como usar os recursos do IntelliSense no Azure Stream Analytics Tools para Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: how-to
ms.openlocfilehash: 988b9d364f09a4c88bcaab1fe81ae80c8de5d4d9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491563"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense no Azure Stream Analytics Tools para Visual Studio Code

O IntelliSense está disponível para [Stream Analytics linguagem de consulta](/stream-analytics-query/stream-analytics-query-language-reference?bc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fbread%2ftoc.json&toc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fstream-analytics%2ftoc.json) no [Azure Stream Analytics Tools para vs Code](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview). O IntelliSense é uma ajuda de preenchimento de código que inclui inúmeras funcionalidades: Listar Membros, Informações do Parâmetro, Informações Rápidas e Completar Palavra. Os recursos do IntelliSense às vezes são chamados por outros nomes, como "auto-completar de código", "assistência a conteúdo" e "dicas de código".

![Demonstração do IntelliSense](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>Recursos do IntelliSense

Os recursos do IntelliSense no Stream Analytics Tools for VS Code são alimentados por um serviço de linguagem. Um serviço de linguagem analisa seu código-fonte e fornece preenchimentos de código inteligentes com base na semântica da linguagem. Se um serviço de linguagem souber possíveis conclusões, as sugestões do IntelliSense pop-up conforme você digita. Se você continuar digitando, uma lista de membros, como variáveis e métodos, será filtrada para incluir somente membros que contenham os caracteres digitados. Quando você pressiona as `Tab` `Enter` teclas ou, o IntelliSense insere o membro que você selecionou.

Você pode disparar o IntelliSense em qualquer janela do Editor digitando um caractere de gatilho, como o caractere de ponto `.` .

![preenchimento automático do IntelliSense](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> O widget de sugestões dá suporte à filtragem de CamelCase. Você pode digitar as letras que estão em letras maiúsculas em um nome de método para limitar as sugestões. Por exemplo, "CRA" abrirá rapidamente "createapplication".

### <a name="types-of-completions"></a>Tipos de conclusões

As ferramentas de Stream Analytics para o VS Code IntelliSense oferecem diferentes tipos de conclusões, incluindo sugestões de servidor de linguagem, trechos de código e conclusões textuais baseadas em palavras simples.

|Completion     |  Tipo       |
| ----- | ------- |
| Palavras-chave | `keyword`
| Funções | `build-in function`, `user defined function`  |
| Nome do conjunto de dados| `input`, `output`, `intermediate result set`|
| Nome da coluna do conjunto de dados|`input`, `intermediate result set`|

#### <a name="name-completion"></a>Conclusão do nome

Além da conclusão automática de palavras-chave, Stream Analytics ferramentas para VS Code lê a lista de nomes de entrada e saída do trabalho, bem como os nomes das colunas em suas fontes de dados quando eles são configurados. A extensão lembra essas informações para fornecer recursos de conclusão de nome que são úteis para inserir instruções com poucos pressionamentos de tecla:

Durante a codificação, você não precisa deixar o editor para executar pesquisas em nomes de entrada de trabalho, nome de saída e nomes de coluna. Você pode manter seu contexto, encontrar as informações necessárias, inserir elementos diretamente em seu código e fazer com que o IntelliSense conclua sua digitação para você.

Observe que você precisa configurar a entrada local ou a entrada ao vivo e salvar o arquivo de configuração para poder usar a conclusão de nome.

![conclusão do nome](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Informações de Parâmetro

A opção **informações do parâmetro** do IntelliSense abre uma lista de parâmetros que fornece informações sobre o número, os nomes e os tipos dos parâmetros exigidos por uma função. O parâmetro em negrito indica o próximo parâmetro necessário à medida que você digita uma função.

A lista de parâmetros também é exibida para funções aninhadas. Se você digitar uma função como um parâmetro para outra função, a lista de parâmetros exibirá os parâmetros da função interna. Em seguida, quando a lista de parâmetros da função interna estiver completa, a lista de parâmetros é revertida para exibir os parâmetros da função externa.

![informações do parâmetro](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Informação Rápida

Conforme fornecido pelo serviço de linguagem, você pode ver **informações rápidas** para cada identificador em seu código. Alguns exemplos de identificadores são entrada, saída, um conjunto de resultados intermediário ou função. Quando você move o ponteiro do mouse sobre um identificador, sua declaração é exibida em uma janela pop-up. As propriedades e esquemas de dados para entradas, se configuradas, e conjuntos de dados intermediários são mostrados.

![informações rápidas](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Solução de problemas do IntelliSense

Esse problema é causado pela configuração de entrada ausente que fornece dados. Você pode verificar se uma [entrada local](visual-studio-code-local-run.md#define-a-local-input) ou uma [entrada ao vivo](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) foi configurada corretamente.

## <a name="next-steps"></a>Próximas etapas

* [Início rápido: criar um trabalho de Azure Stream Analytics no Visual Studio Code](quick-create-visual-studio-code.md)
* [Testar consultas do Stream Analytics localmente com os dados de exemplo usando o Visual Studio Code](visual-studio-code-local-run.md)
* [Testar Stream Analytics consultas localmente contra entrada de fluxo ao vivo usando Visual Studio Code](visual-studio-code-local-run-live-input.md)