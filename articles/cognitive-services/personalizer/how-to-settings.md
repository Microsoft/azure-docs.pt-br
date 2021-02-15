---
title: Configurar o Personalizador
description: A configuração do serviço inclui como o serviço trata as recompensas, com que frequência o serviço faz explorações, com que frequência o modelo é treinado novamente e quantos dados são armazenados.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 9b7347cb98bcbf2e1d92f115d404197083acef4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91292601"
---
# <a name="configure-personalizer-learning-loop"></a>Configurar o loop de aprendizagem do personalizador

A configuração do serviço inclui como o serviço trata as recompensas, com que frequência o serviço faz explorações, com que frequência o modelo é treinado novamente e quantos dados são armazenados.

Configure o loop de aprendizagem na página **configuração** , na portal do Azure para esse recurso personalizado.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="planning-configuration-changes"></a>Planejando alterações de configuração

Como algumas alterações [de configuração redefinem seu modelo](#settings-that-include-resetting-the-model), você deve planejar as alterações de configuração.

Se você planeja usar o [modo aprendiz](concept-apprentice-mode.md), certifique-se de revisar a configuração do personalizador antes de alternar para o modo aprendiz.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Configurações que incluem a redefinição do modelo

As ações a seguir disparam um novo treinamento do modelo usando dados disponíveis até os últimos 2 dias.

* Recompensa
* Exploração

Para [limpar](how-to-manage-model.md) todos os seus dados, use a página de **configurações modelo e aprendizado** .

## <a name="configure-rewards-for-the-feedback-loop"></a>Configurar recompensas para o loop de comentários

Configure o serviço para o uso de recompensas do seu loop de aprendizagem. As alterações nos valores a seguir redefinirão o modelo personalizado atual e o treinarão novamente com os últimos 2 dias de dados.

> [!div class="mx-imgBorder"]
> ![Configurar os valores de recompensa para o loop de comentários](media/settings/configure-model-reward-settings.png)

|Valor|Finalidade|
|--|--|
|Tempo de espera de recompensa|Define o período de tempo durante o qual Personalizador coletará valores de recompensa para uma chamada de Classificação, a partir do momento em que ocorre a chamada de Classificação. Esse valor é definido perguntando: "quanto tempo o personalizador deve esperar por chamadas de recompensas?" Qualquer recompensa recebida após essa janela será registrada em log, mas não será usada para o aprendizado.|
|Recompensa padrão|Se nenhuma chamada de recompensa for recebida pelo Personalizador durante a janela de Tempo de Espera de Recompensa associada a uma Chamada de classificação, o Personalizador atribuirá a Recompensa Padrão. Por padrão, e na maioria dos cenários, a recompensa padrão é zero (0).|
|Agregação de recompensas|Se várias recompensas forem recebidas para a mesma chamada à API de Classificação, este método de agregação será usado: **soma** ou **mais anterior**. A opção "mais anterior" escolhe a pontuação mais anterior recebida e descarta o restante. Isso será útil se você quiser um recompensa exclusivo entre chamadas possivelmente duplicadas. |

Depois de alterar esses valores, certifique-se de selecionar **salvar**.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Configurar a exploração para permitir que o loop de aprendizagem se adapte

A personalização é capaz de descobrir novos padrões e adaptar-se às alterações de comportamento do usuário ao longo do tempo explorando alternativas em vez de usar a previsão do modelo treinado. O valor de **exploração** determina qual porcentagem de chamadas de classificação são respondidas com a exploração.

As alterações nesse valor redefinirão o modelo personalizado atual e o treinarão novamente com os últimos 2 dias de dados.

![O valor de exploração determina qual porcentagem de chamadas de classificação são respondidas com a exploração](media/settings/configure-exploration-setting.png)

Depois de alterar esse valor, certifique-se de selecionar **salvar**.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Configurar a frequência de atualização do modelo para treinamento de modelo

A **frequência de atualização do modelo** define a frequência com que o modelo é treinado.

|Configuração de frequência|Finalidade|
|--|--|
|1 minuto|As frequências de atualização de um minuto são úteis ao **depurar** o código de um aplicativo usando o personalizador, fazer demonstrações ou testar interativamente aspectos de aprendizado de máquina.|
|15 minutos|As frequências de atualização de modelo alto são úteis para situações em que você deseja controlar com mais **precisão as alterações** nos comportamentos do usuário. Os exemplos incluem sites executados em notícias em tempo real, conteúdo viral ou licitação de produtos ao vivo. Você pode usar uma frequência de 15 minutos nesses cenários. |
|1 hora|Na maioria dos casos de uso, uma frequência de atualização mais baixa é eficaz.|

![A frequência de atualização do modelo define a frequência com que um novo modelo do Personalizador é treinado novamente.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Depois de alterar esse valor, certifique-se de selecionar **salvar**.

## <a name="data-retention"></a>Retenção de dados

O **Período de retenção de dados** define por quantos dias o Personalizador mantém os logs de dados. Os logs de dados passados são necessários para realizar [avaliações offline](concepts-offline-evaluation.md), que são usados para medir a eficácia do Personalizador e otimizar a Política de Aprendizado.

Depois de alterar esse valor, certifique-se de selecionar **salvar**.



## <a name="next-steps"></a>Próximas etapas

[Saiba como gerenciar seu modelo](how-to-manage-model.md)
