---
title: Como aplicar DevOps com o GitHub-LUIS
titleSuffix: Azure Cognitive Services
description: Aplique DevOps com Reconhecimento vocal (LUIS) e GitHub.
services: cognitive-services
author: andycw
manager: cmayo
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.author: anwigley
ms.openlocfilehash: 448b3d93ed58e4cfc73da576f0c5871600400ac6
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019832"
---
# <a name="apply-devops-to-luis-app-development-using-github-actions"></a>Aplicar DevOps ao desenvolvimento de aplicativo do LUIS usando GitHub Actions

Acesse o [repositório de modelos Luis DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) para obter uma solução completa que implementa DevOps e práticas recomendadas de engenharia de software para Luis. Você pode usar este repositório de modelos para criar seu próprio Repository com suporte interno para fluxos de trabalho e práticas de CI/CD que habilitam o [controle do código-fonte](luis-concept-devops-sourcecontrol.md), [compilações automatizadas](luis-concept-devops-automation.md), [testes](luis-concept-devops-testing.md)e [Gerenciamento de liberações](luis-concept-devops-automation.md#release-management) com o Luis para seu próprio projeto.

## <a name="the-luis-devops-template-repo"></a>O repositório de modelos LUIS DevOps

O [repositório de modelos Luis DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) percorre como:

* **Clonar o repositório de modelos** – Copie o modelo para seu próprio repositório do github.
* **Configurar recursos do Luis** – crie os [recursos de criação e previsão do Luis no Azure](./luis-how-to-azure-subscription.md) que serão usados pelos fluxos de trabalho de integração contínua.
* **Configurar os fluxos de trabalho de CI/CD** -configurar parâmetros para os fluxos de trabalho de CI/CD e armazená-los em [segredos do GitHub](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).
* **Percorre o ["loop interno de desenvolvimento"](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow)** – o desenvolvedor faz atualizações em um aplicativo Luis de exemplo enquanto trabalha em uma ramificação de desenvolvimento, testa as atualizações e, em seguida, gera uma solicitação de pull para propor alterações e para buscar aprovação de revisão.
* **Executar fluxos de trabalho de CI/CD** -executar [fluxos de trabalho de integração contínua para compilar e testar um aplicativo Luis](luis-concept-devops-automation.md) usando ações do github.
* **Executar testes automatizados** -executar [testes de lote automatizados para um aplicativo Luis](luis-concept-devops-testing.md) para avaliar a qualidade do aplicativo.
* **Implantar o aplicativo Luis** – executar um [trabalho de entrega contínua (CD)](luis-concept-devops-automation.md#continuous-delivery-cd) para publicar o aplicativo Luis.
* **Use o repositório com seu próprio projeto** -explica como usar o repositório com seu próprio aplicativo Luis.

## <a name="next-steps"></a>Próximas etapas

* Use o [repositório de modelos Luis DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) para aplicar o DevOps com seu próprio projeto.
* [Controle do código-fonte e estratégias de branch do LUIS](luis-concept-devops-sourcecontrol.md)
* [Teste para DevOps do LUIS](luis-concept-devops-testing.md)
* [Fluxos de trabalho de automação para LUIS DevOps](luis-concept-devops-automation.md)
