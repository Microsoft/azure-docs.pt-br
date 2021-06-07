---
title: Monitorar e gerenciar o Azure Stream Analytics com o Visual Studio
description: Este artigo descreve como usar Microsoft Visual Studio para monitorar e gerenciar os trabalhos do Azure Stream Analytics.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: e0db101e47a5ec8eb88d3b999058e7c8521f0ff9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020274"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>Monitorar e gerenciar os trabalhos do Stream Analytics com o Microsoft Visual Studio

Este artigo demonstra como monitorar o trabalho do Stream Analytics no Microsoft Visual Studio. As ferramentas de Stream Analytics do Azure para o Microsoft Visual Studio fornecem uma experiência de monitoramento semelhante ao portal do Azure sem precisar sair do IDE. Você pode começar a monitorar um trabalho, assim que você **Enviar para o Azure** de seu **Script.asaql**, ou você pode monitorar os trabalhos existentes, independentemente de como eles foram criados. 

## <a name="job-summary"></a>Resumo do trabalho

O **Resumo do Trabalho** e **Métricas do Trabalho** dão a um instantâneo rápido do seu trabalho. Em uma visão rápida, você pode determinar um status de trabalho e informações do evento.]

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>Métricas do trabalho

Você pode recolher o **Resumo do Trabalho** e clicar na guia **Métricas do Trabalho** para exibir um gráfico com métricas importantes. Marque e desmarque os tipos de métricos para adicionar e remover do gráfico.

![Métricas do Stream Analytics no Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>Monitoramento de erro

Você também pode monitorar erros, clicando na guia **Erros**.

![Erros do Stream Analytics no Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>Obtenha suporte
Para obter mais assistência, veja nossa [página de perguntas e respostas da Microsoft do Azure Stream Analytics](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Criar um trabalho do Azure Stream Analytics com o Microsoft Visual Studio](stream-analytics-quick-create-vs.md)
* [Instalar o Azure Stream Analytics Tools para o Microsoft Visual Studio](stream-analytics-tools-for-visual-studio-install.md)