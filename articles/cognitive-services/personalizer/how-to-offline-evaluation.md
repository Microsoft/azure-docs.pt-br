---
title: Como executar a avaliação offline – personalizador
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como usar a avaliação offline para medir a eficácia de seu aplicativo e analisar seu loop de aprendizado.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: a473085f9c94ca42a75d01b342d60cc33836b096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88244832"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analise seu loop de aprendizagem com uma avaliação offline

Saiba como concluir uma avaliação offline e entender os resultados.

As avaliações offline permitem que você meça a eficiência do personalizador em comparação com o comportamento padrão do aplicativo, saiba quais recursos estão contribuindo com a personalização e descubra automaticamente novos valores de aprendizado de máquina.

Leia sobre [avaliações offline](concepts-offline-evaluation.md) para saber mais.

## <a name="prerequisites"></a>Pré-requisitos

* Um loop personalizado configurado
* O loop personalizador deve ter uma quantidade representativa de dados – como um aproximada, é recomendável pelo menos 50.000 eventos em seus logs para obter resultados significativos de avaliação. Opcionalmente, talvez você também tenha exportado anteriormente arquivos da _política de aprendizado_, que podem ser comparados e testados na mesma avaliação.

## <a name="run-an-offline-evaluation"></a>Executar uma avaliação offline

1. Na [portal do Azure](https://azure.microsoft.com/free/cognitive-services), localize seu recurso personalizador.
1. Na portal do Azure, vá para a seção **avaliações** e selecione **criar avaliação**.
    ![Na portal do Azure, vá para a seção * * avaliações * * e selecione * * criar avaliação * *.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Configure os seguintes valores:

    * Um nome de avaliação.
    * Data de início e de término – são datas que especificam o intervalo de dados a ser usado na avaliação. Esses dados devem estar presentes nos logs, conforme especificado no valor de [retenção de dados](how-to-settings.md) .
    * Descoberta de otimização definida como **Sim**.

    > [!div class="mx-imgBorder"]
    > ![Escolher configurações de avaliação offline](./media/offline-evaluation/create-an-evaluation-form.png)

1. Inicie a avaliação selecionando **OK**.

## <a name="review-the-evaluation-results"></a>Examinar os resultados da avaliação

As avaliações podem levar muito tempo para serem executadas, dependendo da quantidade de dados a serem processados, do número de políticas de aprendizado para comparação e se uma otimização foi solicitada.

Depois de concluído, você pode selecionar a avaliação na lista de avaliações e, em seguida, selecionar **comparar a pontuação de seu aplicativo com outras configurações de aprendizado potenciais**. Selecione esse recurso quando desejar ver como sua política de aprendizado atual é executada em comparação com uma nova política.

1. Examine o desempenho das [políticas de aprendizado](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy).

    > [!div class="mx-imgBorder"]
    > [![Examinar resultados da avaliação](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. Selecione **aplicar** para aplicar a política que melhora o modelo melhor para seus dados.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [como funcionam as avaliações offline](concepts-offline-evaluation.md).
