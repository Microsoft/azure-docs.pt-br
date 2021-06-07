---
title: Criar relatórios de vetor de ataque
description: Os relatórios de vetor de ataque fornecem uma representação gráfica de uma cadeia de vulnerabilidade de dispositivos exploráveis.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 6c178f379561ca4a572a7ae8f556ae6753d6e2be
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784366"
---
# <a name="attack-vector-reporting"></a>Relatório de vetor de ataque

## <a name="about-attack-vector-reports"></a>Sobre relatórios de vetor de ataque

Os relatórios de vetor de ataque fornecem uma representação gráfica de uma cadeia de vulnerabilidade de dispositivos exploráveis. Essas vulnerabilidades podem dar a um invasor acesso aos principais dispositivos de rede. O simulador de vetor de ataque calcula os vetores de ataque em tempo real e analisa todos os vetores de ataque para um destino específico.

Trabalhar com o vetor de ataque permite avaliar o efeito das atividades de mitigação na sequência de ataque. Em seguida, você pode determinar, por exemplo, se uma atualização do sistema interrompe o caminho do invasor, dividindo a cadeia de ataques ou se um caminho de ataque alternativo permanecer. Essas informações ajudam a priorizar as atividades de correção e mitigação.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Exiba seus alertas no centro de controle.":::

> [!NOTE]
> Os administradores e analistas de segurança podem executar os procedimentos descritos nesta seção.

## <a name="create-an-attack-vector-report"></a>Criar um relatório de vetor de ataque

Para criar uma simulação de vetor de ataque:

1. Selecione o :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="sinal de adição":::no menu lateral para adicionar uma simulação.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="A simulação do vetor de ataque.":::

2. Insira as propriedades de simulação:

   - **Nome**: nome da simulação.

   - **Vetores máximos**: o número máximo de vetores em uma única simulação.

   - **Mostrar no mapa do dispositivo**: mostra o vetor de ataque como um filtro no mapa do dispositivo.

   - **Todos os dispositivos de origem**: o vetor de ataque considerará todos os dispositivos como uma fonte de ataque.

   - **Origem do ataque**: o vetor de ataque considerará apenas os dispositivos especificados como uma fonte de ataque.

   - **Todos os dispositivos de destino**: o vetor de ataque considerará todos os dispositivos como um alvo de ataque.

   - **Alvo do ataque**: o vetor de ataque considerará apenas os dispositivos especificados como um alvo de ataque.

   - **Excluir dispositivos**: os dispositivos especificados serão excluídos da simulação de vetor de ataque.

   - **Excluir sub-redes**: as sub-redes especificadas serão excluídas da simulação de vetor de ataque.

3. Selecione **Adicionar simulação**. A simulação será adicionada à lista simulações.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Adicione uma nova simulação.":::

4. Selecione :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: se você deseja editar a simulação.

   Selecione :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: se você deseja excluir a simulação.

   Selecione :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: se você deseja marcar a simulação como um favorito.

5. Uma lista de vetores de ataque é exibida e inclui A pontuação vetorial (de 100), dispositivo de origem de ataque e dispositivo de destino de ataque. Selecione um ataque específico para a representação gráfica dos vetores de ataque.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Vetores de ataque.":::

## <a name="see-also"></a>Veja também

[Relatório de vetor de ataque](how-to-create-attack-vector-reports.md)


