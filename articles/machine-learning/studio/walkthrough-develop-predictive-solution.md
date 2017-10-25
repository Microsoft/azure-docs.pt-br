---
title: "Uma solução preditiva para o risco de crédito com o Machine Learning | Microsoft Docs"
description: "Um passo a passo detalhado mostrando como criar uma solução de análise preditiva para avaliação de risco de crédito no Azure Machine Learning Studio."
keywords: "risco de crédito, solução de análise preditiva, avaliação de riscos"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: fe504d826b6c40099f1f8706ef7e8780eed5cf9a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Passo a passo: Desenvolver uma solução de análise preditiva para avaliação de risco de crédito no Azure Machine Learning

Neste passo a passo, vamos conferir de forma aprofundada o processo de desenvolvimento de uma solução de análise preditiva no Machine Learning Studio. Vamos desenvolver um modelo simples no Machine Learning Studio e implantá-lo como um serviço Web do Machine Learning do Azure, em que o modelo pode fazer previsões usando novos dados. 

Este passo a passo pressupõe que você tenha usado o Machine Learning Studio pelo menos uma vez e tenha noções básicas sobre conceitos de aprendizado de máquina. Mas não pressupõe que você seja um especialista em qualquer um deles.

Se você nunca usou **Azure Machine Learning Studio** antes, convém iniciar pelo tutorial [Criar seu primeiro experimento de ciência de dados no Azure Machine Learning Studio](create-experiment.md). Esse tutorial leva você a explorar o Machine Learning Studio pela primeira vez. Ele mostra os conceitos básicos de como arrastar e soltar módulos no seu experimento, conectá-los, executar o experimento e examinar os resultados. Outra ferramenta que pode ser útil para introdução é um diagrama que fornece uma visão geral dos recursos do Machine Learning Studio. Você pode baixá-lo e imprimi-lo aqui: [Diagrama de visão geral dos recursos do Azure Machine Learning Studio](studio-overview-diagram.md).
 
Se você é novo no campo de aprendizado de máquina em geral, há uma série de vídeos que pode ser útil para você. Ela é chamada de [Ciência de dados para iniciantes](data-science-for-beginners-the-5-questions-data-science-answers.md) e pode fornecer uma excelente introdução ao aprendizado de máquina usando linguagem e conceitos do dia a dia.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
 

## <a name="the-problem"></a>O problema

Suponha que você precisa prever o risco de crédito de uma pessoa com base nas informações dadas em um aplicativo de crédito.  

A avaliação de risco de crédito é um problema complexo, mas podemos simplificá-la um pouco para este passo a passo. Usaremos isso como um exemplo de como você pode criar uma solução de análise preditiva usando o Microsoft Azure Machine Learning. Para fazer isso, usamos o Azure Machine Learning Studio e um serviço Web do Machine Learning.  

## <a name="the-solution"></a>A solução

Neste passo a passo detalhado, vamos começar falando sobre os dados de risco de crédito disponíveis publicamente e desenvolver e treinar um modelo de previsão com base nesses dados. Então podemos implantar o modelo como um serviço Web para que ele pode ser usado por outras pessoas para avaliação de risco de crédito.

Para criar essa solução de avaliação de risco de crédito, vamos seguir estas etapas:  

1. [Criar um espaço de trabalho do Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Carregar dados existentes](walkthrough-2-upload-data.md)
3. [Criar um experimento](walkthrough-3-create-new-experiment.md)
4. [Treinar e avaliar os modelos](walkthrough-4-train-and-evaluate-models.md)
5. [Implantar o serviço Web](walkthrough-5-publish-web-service.md)
6. [Acessar o serviço Web](walkthrough-6-access-web-service.md)

> [!TIP] 
> Você pode encontrar uma cópia funcional do experimento que desenvolvemos neste passo a passo na [Galeria do Cortana Intelligence](https://gallery.cortanaintelligence.com). Vá até **[Passo a passo – Previsão de risco de crédito](https://gallery.cortanaintelligence.com/Experiment/Walkthrough-Credit-risk-prediction-1)** e clique em **Abrir no Studio** para baixar uma cópia do experimento no seu espaço de trabalho do Machine Learning Studio.
> 
> Este passo a passo baseia-se em uma versão simplificada do experimento de exemplo, [Classificação binária: previsão de risco de crédito](http://go.microsoft.com/fwlink/?LinkID=525270) disponível também na [Galeria](http://gallery.cortanaintelligence.com/).
