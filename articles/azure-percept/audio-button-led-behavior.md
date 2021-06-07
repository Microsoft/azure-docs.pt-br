---
title: Botão de áudio do Azure Percept e comportamento do LED
description: Saiba mais sobre os Estados de botão e LED do áudio do Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: fa919acb527084d19ab88b2e7895d4e6ab0b72d3
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609064"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Botão de áudio do Azure Percept e comportamento do LED

Consulte as diretrizes a seguir para obter informações sobre os Estados de botão e LED do dispositivo de áudio do Azure Percept.

## <a name="button-behavior"></a>Comportamento do botão

Use os botões para controlar o comportamento do dispositivo.

|Estado do botão|Comportamento|
|------------|----------|
|Mute|Pressione para ativar/desativar mudo da matriz do MIC. O evento de botão é disparado por liberação quando pressionado.|
|PTT/PTS|Pressione PTT para ignorar o estado de detecção de palavra-chave e ativar o estado de escuta do comando. Pressione novamente para interromper a caixa de diálogo ativa do agente e reverter para o estado de parada da palavra-chave. O evento de botão é disparado por liberação quando pressionado. O PTS só funciona quando o botão é pressionado enquanto o agente está falando, não quando o agente está ouvindo ou pensando.|

## <a name="led-behavior"></a>Comportamento do LED

Use os indicadores de LED para entender em qual estado seu dispositivo está.

|LED|Estado do LED|Status de SoM Ear|
|---|------------|----------------|
|L02|1x branco, estático em|Ligue |
|L02|1x branco, 0,5 Hz piscando|Autenticação em andamento |
|L01 & L02 & L03|azul-3x, estático ativado|Aguardando palavra-chave|
|L01 & L02 & L03|Matriz de LED piscando, 20fps |Ouvindo ou falando|
|L01 & L02 & L03|Corrida de matriz LED, 20fps|Pensando|
|L01 & L02 & L03|triplo vermelho, estático em |Mute|

## <a name="next-steps"></a>Próximas etapas

Para obter dicas de solução de problemas para o dispositivo de áudio do Azure Percept, consulte este [guia](./troubleshoot-audio-accessory-speech-module.md).