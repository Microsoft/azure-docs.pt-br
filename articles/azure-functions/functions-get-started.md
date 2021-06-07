---
title: Introdução ao Azure Functions
description: Execute as primeiras etapas para trabalhar com o Azure Functions.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 77d370b895c777278d3136c7d2c511e7f9e23b36
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179248"
---
# <a name="getting-started-with-azure-functions"></a>Introdução ao Azure Functions

## <a name="introduction"></a>Introdução

O [Azure Functions](./functions-overview.md) permite que você implemente a lógica do seu sistema em blocos de código prontamente disponíveis. Esses blocos de código são denominados "funções".

Use os recursos a seguir para começar.

::: zone pivot="programming-language-csharp"

| Ação | Recursos |
| --- | --- |
| **Criar sua primeira função** | Use uma das seguintes ferramentas:<br><br><li>[Visual Studio](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio Code](./create-first-function-vs-code-csharp.md)<li>[Linha de comando](./create-first-function-cli-csharp.md) |
| **Ver uma função em execução** | <li>[Navegador de Exemplos do Azure](/samples/browse/?expanded=azure&languages=csharp&products=azure-functions)<li>[Biblioteca da Comunidade do Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **Explorar um tutorial interativo**| <li>[Escolher a melhor tecnologia sem servidor do Azure para seu cenário empresarial](/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework​ – Eficiência de desempenho](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Executar uma função do Azure com gatilhos](/learn/modules/execute-azure-function-with-triggers/) <br><br>Confira o Microsoft Learn para obter uma [listagem completa de tutoriais interativos](/learn/browse/?expanded=azure&products=azure-functions).|
| **Examinar práticas recomendadas** |<li>[Desempenho e confiabilidade](./functions-best-practices.md)<li>[Gerenciar conexões](./manage-connections.md)<li>[Tratamento de erro e novas tentativas de função](./functions-bindings-error-pages.md?tabs=csharp)<li>[Segurança](./security-concepts.md)|
| **Saiba mais detalhadamente** | <li>Saiba como as funções [aumentam ou diminuem automaticamente](./functions-scale.md) as instâncias para corresponder à demanda<li>Explore os diferentes [métodos de implantação](./functions-deployment-technologies.md) disponíveis<li>Use [ferramentas de monitoramento](./functions-monitoring.md) internas para ajudar a analisar suas funções<li>Leia a [Referência da linguagem C#](./functions-dotnet-class-library.md)|

::: zone-end

::: zone pivot="programming-language-java"
| Ação | Recursos |
| --- | --- |
| **Criar sua primeira função** | Use uma das seguintes ferramentas:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[Função Java/Maven com terminal/prompt de comando](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ IDEA](./functions-create-maven-intellij.md) |
| **Ver uma função em execução** | <li>[Navegador de Exemplos do Azure](/samples/browse/?expanded=azure&languages=java&products=azure-functions)<li>[Biblioteca da Comunidade do Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **Explorar um tutorial interativo**| <li>[Escolher a melhor tecnologia sem servidor do Azure para seu cenário empresarial](/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework​ – Eficiência de desempenho](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Desenvolver um aplicativo usando o plug-in do Maven para Azure Functions](/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>Confira o Microsoft Learn para obter uma [listagem completa de tutoriais interativos](/learn/browse/?expanded=azure&products=azure-functions).|
| **Examinar práticas recomendadas** |<li>[Desempenho e confiabilidade](./functions-best-practices.md)<li>[Gerenciar conexões](./manage-connections.md)<li>[Tratamento de erro e novas tentativas de função](./functions-bindings-error-pages.md?tabs=java)<li>[Segurança](./security-concepts.md)|
| **Saiba mais detalhadamente** | <li>Saiba como as funções [aumentam ou diminuem automaticamente](./functions-scale.md) as instâncias para corresponder à demanda<li>Explore os diferentes [métodos de implantação](./functions-deployment-technologies.md) disponíveis<li>Use [ferramentas de monitoramento](./functions-monitoring.md) internas para ajudar a analisar suas funções<li>Leia a [Referência da linguagem Java](./functions-reference-java.md)|
::: zone-end

::: zone pivot="programming-language-javascript"
| Ação | Recursos |
| --- | --- |
| **Criar sua primeira função** | Use uma das seguintes ferramentas:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Terminal/prompt de comando do Node.js](./create-first-function-cli-node.md) |
| **Ver uma função em execução** | <li>[Navegador de Exemplos do Azure](/samples/browse/?expanded=azure&languages=javascript%2ctypescript&products=azure-functions)<li>[Biblioteca da Comunidade do Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **Explorar um tutorial interativo** | <li>[Escolher a melhor tecnologia sem servidor do Azure para seu cenário empresarial](/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework​ – Eficiência de desempenho](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Criar APIs sem servidor com o Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Criar uma lógica sem servidor com o Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Refatore as APIs do Node.js e Express para APIs sem servidor com o Azure Functions](/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>Confira o Microsoft Learn para obter uma [listagem completa de tutoriais interativos](/learn/browse/?expanded=azure&products=azure-functions).|
| **Examinar práticas recomendadas** |<li>[Desempenho e confiabilidade](./functions-best-practices.md)<li>[Gerenciar conexões](./manage-connections.md)<li>[Tratamento de erro e novas tentativas de função](./functions-bindings-error-pages.md?tabs=javascript)<li>[Segurança](./security-concepts.md)|
| **Saiba mais detalhadamente** | <li>Saiba como as funções [aumentam ou diminuem automaticamente](./functions-scale.md) as instâncias para corresponder à demanda<li>Explore os diferentes [métodos de implantação](./functions-deployment-technologies.md) disponíveis<li>Use [ferramentas de monitoramento](./functions-monitoring.md) internas para ajudar a analisar suas funções<li>Leia a referência da linguagem [JavaScript](./functions-reference-node.md) ou [TypeScript](./functions-reference-node.md#typescript)|
::: zone-end

::: zone pivot="programming-language-powershell"
| Ação | Recursos |
| --- | --- |
| **Criar sua primeira função** | <li>Usar o [Visual Studio Code](./create-first-function-vs-code-powershell.md) |
| **Ver uma função em execução** | <li>[Navegador de Exemplos do Azure](/samples/browse/?expanded=azure&languages=powershell&products=azure-functions)<li>[Biblioteca da Comunidade do Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **Explorar um tutorial interativo** | <li>[Escolher a melhor tecnologia sem servidor do Azure para seu cenário empresarial](/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework​ – Eficiência de desempenho](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Criar APIs sem servidor com o Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Criar uma lógica sem servidor com o Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Executar uma função do Azure com gatilhos](/learn/modules/execute-azure-function-with-triggers/) <br><br>Confira o Microsoft Learn para obter uma [listagem completa de tutoriais interativos](/learn/browse/?expanded=azure&products=azure-functions).|
| **Examinar práticas recomendadas** |<li>[Desempenho e confiabilidade](./functions-best-practices.md)<li>[Gerenciar conexões](./manage-connections.md)<li>[Tratamento de erro e novas tentativas de função](./functions-bindings-error-pages.md?tabs=powershell)<li>[Segurança](./security-concepts.md)|
| **Saiba mais detalhadamente** | <li>Saiba como as funções [aumentam ou diminuem automaticamente](./functions-scale.md) as instâncias para corresponder à demanda<li>Explore os diferentes [métodos de implantação](./functions-deployment-technologies.md) disponíveis<li>Use [ferramentas de monitoramento](./functions-monitoring.md) internas para ajudar a analisar suas funções<li>Leia a [Referência da linguagem PowerShell](./functions-reference-powershell.md)|
::: zone-end

::: zone pivot="programming-language-python"
| Ação | Recursos |
| --- | --- |
| **Criar sua primeira função** | Use uma das seguintes ferramentas:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)<li>[Terminal/prompt de comando](./create-first-function-cli-csharp.md?pivots=programming-language-python) |
| **Ver uma função em execução** | <li>[Navegador de Exemplos do Azure](/samples/browse/?expanded=azure&languages=python&products=azure-functions)<li>[Biblioteca da Comunidade do Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **Explorar um tutorial interativo** | <li>[Escolher a melhor tecnologia sem servidor do Azure para seu cenário empresarial](/learn/modules/serverless-fundamentals/)<li>[Well-Architected Framework​ – Eficiência de desempenho](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Criar APIs sem servidor com o Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Criar uma lógica sem servidor com o Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>Confira o Microsoft Learn para obter uma [listagem completa de tutoriais interativos](/learn/browse/?expanded=azure&products=azure-functions).|
| **Examinar práticas recomendadas** |<li>[Desempenho e confiabilidade](./functions-best-practices.md)<li>[Gerenciar conexões](./manage-connections.md)<li>[Tratamento de erro e novas tentativas de função](./functions-bindings-error-pages.md?tabs=python)<li>[Segurança](./security-concepts.md)<li>[Aprimorar o desempenho da taxa de transferência](./python-scale-performance-reference.md)|
| **Saiba mais detalhadamente** | <li>Saiba como as funções [aumentam ou diminuem automaticamente](./functions-scale.md) as instâncias para corresponder à demanda<li>Explore os diferentes [métodos de implantação](./functions-deployment-technologies.md) disponíveis<li>Use [ferramentas de monitoramento](./functions-monitoring.md) internas para ajudar a analisar suas funções<li>Leia a [Referência da linguagem Python](./functions-reference-python.md)|
::: zone-end

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre a anatomia de um aplicativo Azure Functions](./functions-reference.md)
