---
title: Configurar o comportamento de aprendizado
description: O modo aprendiz dá a você confiança no serviço personalizador e em seus recursos de aprendizado de máquina, além de fornecer métricas para as quais o serviço recebe informações que podem ser aprendidas – sem arriscar o tráfego online.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: 57a03b107678f83200b11f408784f6455cbceffd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94579284"
---
# <a name="configure-the-personalizer-learning-behavior"></a>Configurar o comportamento de aprendizagem do personalizador

O [modo aprendiz](concept-apprentice-mode.md) fornece confiança e confiança no serviço personalizador e em seus recursos de aprendizado de máquina e fornece garantia de que o serviço recebe informações que podem ser aprendidas do – sem risco de tráfego online.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>Configurar o modo aprendiz

1. Entre no [portal do Azure](https://portal.azure.com), para o recurso personalizador.

1. Na página **configuração** , na guia **comportamento de aprendizado** , selecione **ação retornar linha de base, saiba como um aprendiz** e, em seguida, selecione **salvar**.

> [!div class="mx-imgBorder"]
> ![Captura de tela da configuração do comportamento de aprendizado do modo aprendiz no portal do Azure](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>Alterações no aplicativo existente

Seu aplicativo existente não deve alterar a forma como ele seleciona ações para exibir ou como o aplicativo determina o valor, **recompensando** essa ação. A única alteração no aplicativo pode ser a ordem das ações enviadas para a API de classificação do personalizador. A ação que seu aplicativo exibe atualmente é enviada como a _primeira ação_ na lista de ações. A [API de classificação](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) usa essa primeira ação para treinar o modelo personalizado.

### <a name="configure-your-application-to-call-the-rank-api"></a>Configurar seu aplicativo para chamar a API de classificação

Para adicionar o personalizador ao seu aplicativo, você precisa chamar as APIs de classificação e recompensa.

1. Adicione a chamada à [API de classificação](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) após o ponto em sua lógica de aplicativo existente onde você determina a lista de ações e seus recursos. A primeira ação na lista de ações precisa ser a ação selecionada pela lógica existente.

1. Configure seu código para exibir a ação associada à **ID da ação de recompensa** da resposta da API de classificação.

### <a name="configure-your-application-to-call-reward-api"></a>Configurar seu aplicativo para chamar a API de recompensa

1. Use a lógica de negócios existente para calcular a **recompensa** da ação exibida. O valor precisa estar no intervalo de 0 a 1. Envie este prêmio ao personalizador usando a [API de recompensa](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward). O valor de recompensa não é esperado imediatamente e pode ser atrasado em um período de tempo, dependendo de sua lógica de negócios.

1. Se você não retornar a recompensa dentro do **tempo de espera de recompensa** configurado, a recompensa padrão será usada em seu lugar.

## <a name="evaluate-apprentice-mode"></a>Avaliar o modo aprendiz

Na portal do Azure, na página **avaliações** do recurso personalizado, examine o desempenho do comportamento de **aprendizado atual**.

> [!div class="mx-imgBorder"]
> ![Captura de tela da revisão da avaliação do comportamento de aprendizado do modo aprendiz no portal do Azure](media/settings/evaluate-apprentice-mode.png)

O modo aprendiz fornece as seguintes **métricas de avaliação**:
* **Linha de base – recompensa média**: recompensas médias do padrão do aplicativo (linha de base).
* **Personalizador – recompensa média**: a média do personalizador de recompensas pode ter atingido potencialmente.
* **Recompensa a taxa de realização por meio dos eventos 1000 mais recentes**: taxa de linha de base e recompensa de personalizador – normalizado sobre os eventos 1000 mais recentes.

## <a name="switch-behavior-to-online-mode"></a>Alternar comportamento para o modo online

Quando você determina que o personalizador é treinado com uma média de 75-85% de média móvel, o modelo está pronto para alternar para o modo online.

Na portal do Azure para o recurso personalizador, na página **configuração** , na guia comportamento de **aprendizado** , selecione **retornar a melhor ação** e selecione **salvar**.

Você não precisa fazer nenhuma alteração na classificação e recompensar as chamadas à API.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar configurações de modelo e de aprendizado](how-to-manage-model.md)
