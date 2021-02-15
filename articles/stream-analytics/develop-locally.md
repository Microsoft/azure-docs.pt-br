---
title: Desenvolva e depure Azure Stream Analytics trabalhos localmente
description: Saiba como desenvolver e testar trabalhos de Azure Stream Analytics no computador local antes de executá-los no portal do Azure.
ms.author: sujie
author: su-jie
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 18df480dab90d9ab127bb96971fc19cdc5a361ce
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016466"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Desenvolva e depure Azure Stream Analytics trabalhos localmente

Embora você possa criar e testar trabalhos de Azure Stream Analytics no portal do Azure, muitos desenvolvedores preferem uma experiência de desenvolvimento local. Stream Analytics facilita o uso do editor de código favorito e das ferramentas de desenvolvimento para criar e testar trabalhos com fluxos de eventos ao vivo do hub de eventos do Azure, do Hub IoT e do armazenamento de BLOBs usando um tempo de execução local de nó único totalmente funcional. Você também pode enviar trabalhos para o Azure diretamente do seu ambiente de desenvolvimento local.

## <a name="local-development-environments"></a>Ambientes de desenvolvimento locais

A maneira pela qual você desenvolve Stream Analytics trabalhos no computador local depende das suas preferências de ferramentas e da disponibilidade dos recursos. Confira [Azure Stream Analytics comparação de recursos](feature-comparison.md) para ver quais recursos têm suporte para cada ambiente de desenvolvimento.

Os ambientes na tabela a seguir dão suporte a desenvolvimento local:

|Ambiente                              |Descrição    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| A [extensão de ferramentas de Azure Stream Analytics](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) para Visual Studio Code permite que você crie, gerencie, teste seu trabalho do Stream Analytics localmente e na nuvem com IntelliSense rico e controle de código-fonte nativo. Dá suporte ao desenvolvimento em Linux, MacOS e Windows. Para saber mais, confira [criar um trabalho de Azure Stream Analytics no Visual Studio Code](quick-create-visual-studio-code.md). A extensão também dá suporte ao [Visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/) , que é um ambiente de desenvolvimento hospedado na nuvem.|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Stream Analytics ferramentas faz parte do desenvolvimento do Azure e das cargas de trabalho de processamento e armazenamento de dados no Visual Studio. Você pode usar o Visual Studio para gravar funções personalizadas definidas pelo usuário e desserializadores do C#. Para saber mais, confira [criar um trabalho de Azure Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md).|
|[Prompt de comando ou terminal](stream-analytics-tools-for-visual-studio-cicd.md)|O pacote NuGet Azure Stream Analytics CI/CD fornece ferramentas para compilação de projeto do Visual Studio, teste local em um computador arbitrário. O pacote Azure Stream Analytics CI/CD NPM fornece ferramentas para Visual Studio Code compilações de projeto (que gera um modelo de Azure Resource Manager) em um computador arbitrário.|

## <a name="next-steps"></a>Próximas etapas

* [Testar consultas do Stream Analytics localmente com os dados de exemplo usando o Visual Studio Code](visual-studio-code-local-run.md)
* [Testar Stream Analytics consultas localmente contra entrada de fluxo ao vivo usando Visual Studio Code](visual-studio-code-local-run-live-input.md)
* [Testar as consultas do Stream Analytics localmente com o Microsoft Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testar dados dinâmicos localmente usando as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-live-data-local-testing.md)
