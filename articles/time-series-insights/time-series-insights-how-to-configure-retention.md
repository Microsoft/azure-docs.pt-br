---
title: Como configurar a retenção no ambiente do Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como configurar a retenção no ambiente do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 3235844c8750003d08e996f5065fcef256c2d244
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165655"
---
# <a name="configuring-retention-in-time-series-insights"></a>Configurando a retenção no Azure Time Series Insights

Este artigo descreve como configurar o **Tempo de retenção de dados** e o **Comportamento em caso de limite de armazenamento excedido** no Azure Time Series Insights.

## <a name="summary"></a>Resumo

Cada ambiente do TSI (Time Series Insights) tem uma configuração para configurar o **Tempo de retenção de dados**. O valor varia de 1 a 400 dias. Os dados são excluídos com base na capacidade de armazenamento do ambiente ou na duração da retenção (1-400), o que vier primeiro.

Cada ambiente TSI tem uma configuração adicional **Comportamento em caso de limite de armazenamento excedido**. Essa configuração controla o comportamento de ingresso e de limpeza quando a capacidade máxima de um ambiente é atingida. Há dois comportamentos dentre os quais escolher:

- **Limpar dados antigos** (padrão)
- **Pausar a entrada**

Para mais informações detalhadas a fim de compreender melhor essas configurações, veja [Noções básicas sobre retenção no Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Configurar retenção de dados

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Localize seu ambiente Time Series Insights existente. Selecione **Todos os recursos** no menu à esquerda do portal do Azure. Selecione o seu ambiente de Análise de Séries Temporais.

1. No cabeçalho **CONFIGURAÇÕES**, selecione **Configurar**.

1. Selecione o **Tempo de retenção de dados** para configurar a retenção usando a barra do controle deslizante ou digite um número na caixa de texto.

1. Observe a configuração **Capacidade**, já que essa configuração afeta a quantidade máxima de eventos de dados e a capacidade de armazenamento total para armazenamento de dados.

1. Mude a configuração de **Comportamento em caso de limite de armazenamento excedido**. Selecione o comportamento **Limpar dados antigos** ou **Colocar a entrada em pausa**.

1. Selecione **Salvar** para configurar as alterações.

## <a name="next-steps"></a>Próximas etapas

- Para mais informações, veja [Noções básicas sobre retenção no Time Series Insights](time-series-insights-concepts-retention.md).