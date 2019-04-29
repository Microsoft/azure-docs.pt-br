---
title: Monitorar e gerenciar trabalhos do Azure Stream Analytics usando o Microsoft Visual Studio
description: Este artigo descreve como usar Microsoft Visual Studio para monitorar e gerenciar os trabalhos do Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: e673d4f1c2d58a61c541cca4f8448dffc003f9fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479384"
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


## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) 

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Criar um trabalho do Azure Stream Analytics com o Microsoft Visual Studio](stream-analytics-quick-create-vs.md)
* [Instalar o Azure Stream Analytics Tools para o Microsoft Visual Studio](stream-analytics-tools-for-visual-studio-install.md) 


