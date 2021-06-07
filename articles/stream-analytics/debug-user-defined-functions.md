---
title: Depurar funções definidas pelo usuário no Azure Stream Analytics
description: Este artigo descreve como depurar funções definidas pelo usuário no Azure Stream Analytics.
author: jenssuessmeyer
ms.author: jenss
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/24/2020
ms.custom: devx-track-js
ms.openlocfilehash: 7f5a24e4d351d7c133251900a80c70094b84cc4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019424"
---
# <a name="debug-user-defined-functions-in-azure-stream-analytics"></a>Depurar funções definidas pelo usuário no Azure Stream Analytics 

Quando as funções definidas pelo usuário (UDF) não funcionam conforme o esperado, você precisa depurá-las para encontrar o problema. Você pode depurar UDFs para seus trabalhos de Stream Analytics ao executar seus trabalhos localmente usando o [Visual Studio Code](visual-studio-code-local-run-live-input.md) ou o [Visual Studio](stream-analytics-vs-tools-local-run.md).

Quando você executa o trabalho em um fluxo de entrada ao vivo localmente, ele imita apenas a execução do mecanismo de Azure Stream Analytics de nuvem em um nó. Os testes locais de dados dinâmicos não podem substituir o desempenho e os testes de escalabilidade que você faz na nuvem, mas economiza tempo durante os testes funcionais, sem a possibilidade de enviar seu trabalho para a nuvem toda vez que você quiser testar. Além disso, as políticas de tempo são desabilitadas para execução local com dados locais ou de exemplo, mas as políticas de tempo têm suporte para testes de dados dinâmicos.

## <a name="pick-your-language"></a>Escolha seu idioma

Você pode escrever UDFs para Azure Stream Analytics usando .NET (C#) ou JavaScript. 

### <a name="functions-in-c"></a>Funções em C # 

Ao [escrever UDFs .NET usando o Visual Studio](stream-analytics-edge-csharp-udf-methods.md), você obtém o mesmo nível de suporte que faria para qualquer projeto de classe .net. Esse suporte inclui:

* Suporte à compilação, como verificação de sintaxe e suporte do compilador.

* A capacidade de adicionar, criar e fazer referência a um projeto C# e a artefatos em sua solução Stream Analytics. 

* Fácil reutilização de código encapsulado em um projeto compartilhável. 

* Suporte de depuração no Visual Studio diretamente. Defina o projeto Stream Analytics como iniciar projeto e defina pontos de interrupção no código C#. Em seguida, pressione **F5** para depurar o código c# como você faria para qualquer outro projeto c#. 

### <a name="functions-in-javascript"></a>Funções em JavaScript

O JavaScript é outra opção para criar funções no Stream Analytics. O código JavaScript é colocado diretamente na área de funções do projeto Stream Analytics, o que torna o compartilhamento entre projetos mais difícil.

A compilação acontece quando o projeto de Stream Analytics é compilado ou executado. A possibilidade de encontrar um problema somente no tempo de execução é maior. Não há suporte para depuração para funções JavaScript no Stream Analytics diretamente.

## <a name="debug-options-for-javascript"></a>Opções de depuração para JavaScript

Como não há suporte para depuração para funções JavaScript no Stream Analytics diretamente, você pode depurar encapsulando a função em um site HTML e obtendo a saída de lá.

O exemplo a seguir mostra como depurar UDFs JavaScript com algumas limitações em um ambiente de tempo de execução integrado no [Visual Studio Code](quick-create-visual-studio-code.md).

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que seu projeto Azure Stream Analytics tem os seguintes itens:

* Uma entrada 
* Uma saída 
* Uma consulta Stream Analytics (. asaql) 
* Uma configuração de trabalho Stream Analytics (JobConfig.jsem)
* Um UDF do JavaScript

### <a name="prepare-files"></a>Preparar arquivos

Na imagem a seguir, o arquivo de consulta *. asaql* inclui apenas a chamada para o UDF, *fxcharCount*. Essa alteração garante que você ainda poderá compilar o projeto depois que as alterações forem feitas.

> [!div class="mx-imgBorder"]
> ![Stream Analytics arquivo de consulta de teste](./media/debug-user-defined-functions/asaql-file.png)

Crie uma pasta adicional em **testes** para hospedar o arquivo de teste, que é chamado para executar o teste com a função JavaScript. Neste exemplo, o nome da pasta é *fxcharCount* e o nome do teste é *Test_UDF.js*. 

A imagem abaixo mostra o código no arquivo de teste, que carrega o arquivo de função e executa a função. Este exemplo é simples, mas você pode carregar arquivos de dados de teste adicionais e executar loops por meio de testes adicionais para obter a saída. A notação da chamada de função é pouco diferente das chamadas comuns porque o arquivo é referenciado e não carregado no tempo de execução, o que possibilita a depuração. 

> [!div class="mx-imgBorder"]
> ![Stream Analytics arquivo de teste](./media/debug-user-defined-functions/test-file.png)

Na função, adicione as seguintes linhas de código ao arquivo para expor os métodos. Eles não afetam a capacidade de compilar o código em Visual Studio Code.

```javascript
var methods = {};
methods.fxchartCount = fxchartCount;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![UDF Stream Analytics JavaScript](./media/debug-user-defined-functions/udf-file.png)
  
## <a name="install-debug-support"></a>Instalar o suporte de depuração

Para depurar, você deve [baixar](https://nodejs.org/en/download/) e instalar o **node.js**. Instale a versão correta de acordo com a plataforma que você está usando. Depois de instalar o node.js Runtime, reinicie o Visual Studio Code para implementar as alterações. 

Selecione **executar e depurar** ou pressione **Ctrl + Shift + D** para iniciar a depuração. É exibida uma caixa de combinação onde você pode selecionar **node.js** como o tempo de execução. Se você tiver apenas node.js instalado, ele será usado por padrão. Você deve ser capaz de percorrer o código e o arquivo satélite, se necessário, com F11. 

> [!div class="mx-imgBorder"]
> ![Stream Analytics executar e depurar UDF](./media/debug-user-defined-functions/run-debug-udf.png)

### <a name="debug-user-defined-aggregates"></a>Depurar agregações definidas pelo usuário 

Você pode usar o método Debug para UDFs do JavaScript para depurar agregações definidas pelo usuário (UDA). Neste exemplo, um UDA é adicionado ao arquivo de consulta *. asaql* e ao arquivo de teste.

Assim como no UDF, você inclui uma chamada para o UDA para garantir que o projeto será compilado depois que as alterações forem feitas. 

> [!div class="mx-imgBorder"]
> ![Adicionar UDA a asaql](./media/debug-user-defined-functions/asaql-uda.png)

No arquivo de *Test_UDA.js* , você faz referência ao arquivo UDA como fez com o UDF. Além disso, você chama `main()` , `init()` e `accumulate()` . O `accumulate()` método é chamado em um loop para colocar os valores na pilha de estado. O `computeresult()` método é chamado para compor a consulta final. 

> [!div class="mx-imgBorder"]
> ![Arquivo de teste do UDA](./media/debug-user-defined-functions/uda-test.png)

Como no exemplo de UDF, alguns códigos precisam ser adicionados ao próprio UDA para expor os métodos relevantes.

```javascript
var methods = {};
methods.main = main;
methods.init = main.init;
methods.accumulate = main.accumulate;
methods.computeResult = main.computeResult;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Código adicionado ao UDA](./media/debug-user-defined-functions/uda-expose-methods.png)

Selecione **executar e depurar** ou pressione **Ctrl + Shift + D** para iniciar a depuração. É exibida uma caixa de combinação onde você pode selecionar **node.js** como o tempo de execução. Se você tiver apenas node.js instalado, ele será usado por padrão. Você deve ser capaz de percorrer o código e o arquivo satélite, se necessário, com F11.

> [!div class="mx-imgBorder"]
> ![Stream Analytics executar e depurar UDA](./media/debug-user-defined-functions/run-debug-uda.png)


## <a name="next-steps"></a>Próximas etapas

* [Desenvolva e depure Azure Stream Analytics trabalhos localmente](develop-locally.md)
* [Depurar Azure Stream Analytics consultas localmente usando o diagrama de trabalho no Visual Studio](debug-locally-using-job-diagram.md)
* [Funções definidas pelo usuário no Azure Stream Analytics](functions-overview.md)
 
