---
title: Usar a mudança de tempo para criar a reprodução de vídeo sob demanda
description: Este artigo descreve como usar as saídas dinâmicas e de mudança de tempo para registrar fluxos ao vivo e criar a reprodução sob demanda.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: a2bb876c164f0df56a8b7f3c4a3666ff306e9416
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955929"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Usar mudança de tempo e saídas ao vivo para criar reprodução de vídeo sob demanda

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Nos serviços de mídia do Azure, um objeto de [saída ao vivo](/rest/api/media/liveoutputs) é como um gravador de vídeo digital que detectará e registrará sua transmissão ao vivo em um ativo em sua conta de serviços de mídia. O conteúdo gravado é mantido no contêiner definido pelo recurso de [ativo](/rest/api/media/assets) (o contêiner está na conta de armazenamento do Azure anexada à sua conta). A saída ao vivo também permite que você controle algumas propriedades da transmissão ao vivo de saída, como quanto do fluxo é mantido na gravação de arquivo morto (por exemplo, a capacidade do DVR de nuvem) ou quando os visualizadores podem começar a assistir à transmissão ao vivo. O arquivo morto em disco é uma "janela" de arquivo circular que mantém apenas a quantidade de conteúdo especificada na propriedade **archiveWindowLength** da saída em tempo real. O conteúdo que está fora desta janela é descartado automaticamente do contêiner de armazenamento e não é recuperável. O valor archiveWindowLength representa uma duração ISO-8601 TimeSpan (por exemplo, PTHH: MM: SS), que especifica a capacidade do DVR. O valor pode ser definido de um mínimo de um minuto para um máximo de 25 horas.

A relação entre um evento ao vivo e suas saídas ao vivo é semelhante à transmissão de TV tradicional, pois um canal (evento ao vivo) representa um fluxo constante de vídeo e uma gravação (saída ao vivo) tem como escopo um segmento de tempo específico (por exemplo, notícias da noite de 6: às 16h30 para 7:13h). Depois que o fluxo fluir para o evento ao vivo, você poderá iniciar o evento de streaming criando um ativo, uma saída ao vivo e um localizador de streaming. A Saída ao vivo arquiva o fluxo e o disponibiliza para usuários por meio do [Ponto de extremidade de streaming](/rest/api/media/streamingendpoints). Você pode criar várias Saídas ao vivo (até três no máximo) em um Evento ao vivo com diferentes comprimentos e configurações de arquivamento. Para obter informações sobre o fluxo de trabalho de transmissão ao vivo, consulte a seção [etapas gerais](live-streaming-overview.md#general-steps) .

## <a name="using-a-dvr-during-an-event"></a>Usando um DVR durante um evento

Esta seção discute como usar um DVR durante um evento para controlar quais partes do fluxo estão disponíveis para ' retrocesso '.

O `archiveWindowLength` valor determina quanto tempo um visualizador pode ir da posição atual ao vivo. O `archiveWindowLength` valor também determina por quanto tempo os manifestos do cliente podem crescer.

Suponha que você esteja transmitindo um jogo de futebol e que ele tenha `ArchiveWindowLength` apenas 30 minutos. Um espectador que começa a assistir ao seu evento 45 minutos após o início do jogo pode buscar de volta no máximo a marca de 15 minutos. Suas saídas ao vivo para o jogo continuarão até que o evento ao vivo seja interrompido. O conteúdo que está fora do archiveWindowLength é continuamente descartado do armazenamento e não é recuperável. Neste exemplo, o vídeo entre o início do evento e a marca de 15 minutos teria sido limpo do DVR e do contêiner no armazenamento de BLOBs para o ativo. O arquivo morto não é recuperável e é removido do contêiner no armazenamento de BLOBs do Azure.

Um evento ao vivo dá suporte a até três saídas dinâmicas em execução simultânea (é possível criar no máximo três gravações/arquivos mortos de uma transmissão ao vivo ao mesmo tempo). Esse suporte permite que você publique e arquive diferentes partes de um evento, conforme necessário. Suponha que você precise transmitir um feed linear ao vivo 24x7 e criar "gravações" dos diferentes programas ao longo do dia para oferecer aos clientes como conteúdo sob demanda para visualização aproximada. Para esse cenário, primeiro você cria uma saída dinâmica principal com uma janela de arquivo curto de 1 hora ou menos – essa é a transmissão ao vivo principal que seus visualizadores ajustarão. Você deve criar um localizador de streaming para essa saída ao vivo e publicá-lo em seu aplicativo ou site como o feed "ao vivo". Enquanto o Evento ao Vivo estiver em execução, você poderá criar programaticamente uma segunda Saída ao Vivo simultânea no início de um programa (ou 5 minutos antes para fornecer algumas alças para aparar mais tarde). Essa segunda Saída ao Vivo pode ser excluída 5 minutos depois que o programa é finalizado. Com esse segundo ativo, você pode criar um novo localizador de streaming para publicar este programa como um ativo sob demanda no catálogo do aplicativo. Você pode repetir esse processo várias vezes para outros limites do programa ou destaques que queira compartilhar como vídeos sob demanda, enquanto o feed "Ao vivo" da primeira Saída ao Vivo continua difundindo o feed linear.

## <a name="creating-an-archive-for-on-demand-playback"></a>Criando um arquivo para reprodução sob demanda

O ativo ao qual a saída dinâmica está arquivando se torna automaticamente um ativo sob demanda quando a saída dinâmica é excluída. Você deve excluir todas as saídas ao vivo antes que um evento ao vivo possa ser interrompido. Você pode usar um sinalizador opcional [removeOutputsOnStop](/rest/api/media/liveevents/stop#request-body) para remover automaticamente as saídas dinâmicas ao parar.

Mesmo depois de parar e excluir o evento, os usuários podem transmitir o conteúdo arquivado como um vídeo sob demanda, desde que você não exclua o ativo. Um ativo não deve ser excluído se for usado por um evento; o evento deve ser excluído primeiro.

Se você publicou o ativo da sua saída ao vivo usando um localizador de streaming, o evento ao vivo (até o comprimento da janela DVR) continuará a ser exibido até a expiração ou a exclusão do localizador de streaming, o que ocorrer primeiro.

Para obter mais informações, consulte:

- [Visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Tutorial de live streaming](stream-live-tutorial-with-api.md)

> [!NOTE]
> Quando você exclui a saída dinâmica, não está excluindo o ativo subjacente e o conteúdo no ativo.

## <a name="next-steps"></a>Próximas etapas

* [Subclipe seus vídeos](subclip-video-rest-howto.md).
* [Defina filtros para seus ativos](filters-dynamic-manifest-rest-howto.md).
