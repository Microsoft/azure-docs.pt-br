---
title: Integração e implantação contínuas para Azure Stream Analytics
description: Este artigo fornece uma visão geral de um pipeline de CI/CD (integração e implantação contínuas) para o Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 940813f12d542715db47781731144a75e854a98e
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019560"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>CI/CD (integração e implantação contínuas) para Azure Stream Analytics

Você pode implantar seu trabalho de Azure Stream Analytics continuamente usando a integração de controle do código-fonte. A integração de controle de origem permite que um fluxo de trabalho no qual uma atualização de código dispare uma implantação de recurso no Azure. Este artigo descreve as etapas básicas para criar um pipeline de CI/CD (integração e implantação contínuas).

Se você for novo no Azure Stream Analytics, comece a usar o guia de [início rápido do Azure Stream Analytics](stream-analytics-quick-create-portal.md).

## <a name="create-a-cicd-pipeline"></a>Crie um pipeline CI/CD

Siga as etapas neste guia para criar um pipeline de CI/CD para Stream Analytics.

1. Desenvolver uma consulta Azure Stream Analytics.

   Use as ferramentas de Azure Stream Analytics para [Visual Studio Code](./quick-create-visual-studio-code.md) ou o [Visual Studio](stream-analytics-quick-create-vs.md) para [desenvolver e testar consultas localmente](develop-locally.md). Você também pode [exportar um trabalho existente](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project) para um projeto local.

2. Confirme seus projetos de Azure Stream Analytics para o sistema de controle do código-fonte, como um repositório git.

3. Use [Azure Stream Analytics ferramentas de CI/CD](cicd-tools.md) para compilar os projetos e gerar modelos de gerenciamento de recursos do Azure para a implantação.

4. Execute [testes de script automatizados](cicd-tools.md#automated-test) para regressão de qualidade.

5. [Implante o trabalho](cicd-tools.md#deploy-to-azure) no Azure automaticamente.

## <a name="auto-build-test-and-deploy"></a>Criação, teste e implantação automáticos

Você pode usar a linha de comando e as [ferramentas Azure Stream Analytics CI/CD](cicd-tools.md) para criar, testar e implantar automaticamente. Você também pode configurar um pipeline de CI/CD no [Azure pipelines](set-up-cicd-pipeline.md). Azure Pipelines para habilitar recursos mais avançados, como gerenciamento de pipeline, visualização e gatilhos.

## <a name="next-steps"></a>Próximas etapas

* [Automatizar compilações, testes e implantações de um trabalho de Azure Stream Analytics usando as ferramentas de CI/CD](cicd-tools.md)
* [Configurar um pipeline de CI/CD para Stream Analytics trabalho usando Azure Pipelines](set-up-cicd-pipeline.md)