---
title: Como configurar a retenção em seu ambiente-Azure Time Series Insights | Microsoft Docs
description: Saiba como configurar a retenção em seu ambiente de Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 468b4f7ca7b0af4abc32df5d9ef64a74154d3de1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569409"
---
# <a name="configuring-retention-in-azure-time-series-insights-gen1"></a>Configurando a retenção no Azure Time Series Insights Gen1

> [!CAUTION]
> Esse é um artigo do Gen1.

Este artigo descreve como configurar o **Tempo de retenção de dados** e o **Comportamento em caso de limite de armazenamento excedido** no Azure Time Series Insights.

## <a name="summary"></a>Resumo

Cada ambiente de Azure Time Series Insights tem uma configuração para configurar o **tempo de retenção de dados**. O valor varia de 1 a 400 dias. Os dados são excluídos com base na capacidade de armazenamento do ambiente ou na duração da retenção (1-400), o que vier primeiro.

Cada ambiente de Azure Time Series Insights tem um comportamento de limite de armazenamento de configuração adicional **excedido**. Essa configuração controla o comportamento de ingresso e de limpeza quando a capacidade máxima de um ambiente é atingida. Há dois comportamentos dentre os quais escolher:

- **Limpar dados antigos** (padrão)
- **Pausar a entrada**

Para obter informações detalhadas para entender melhor essas configurações, consulte [noções básicas sobre retenção em Azure Time Series insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Configurar retenção de dados

1. Entre no [portal do Azure](https://portal.azure.com).

1. Localize seu ambiente de Azure Time Series Insights existente. Selecione **Todos os recursos** no menu à esquerda do portal do Azure. Selecione seu ambiente de Azure Time Series Insights.

1. No título **configurações** , selecione **configuração de armazenamento**.

    [![Em configurações, selecione configuração de armazenamento](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. Selecione o **tempo de retenção de dados (em dias)** para configurar a retenção usando a barra deslizante ou digite um número na caixa de texto.

1. Observe a configuração **Capacidade**, já que essa configuração afeta a quantidade máxima de eventos de dados e a capacidade de armazenamento total para armazenamento de dados.

1. Mude a configuração de **Comportamento em caso de limite de armazenamento excedido**. Selecione o comportamento **Limpar dados antigos** ou **Colocar a entrada em pausa**.

    [![Pausar entrada-aceitar e salvar.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. Examine a documentação para entender os possíveis riscos de perda de dados. Selecione **Salvar** para configurar as alterações.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações, consulte [noções básicas sobre retenção em Azure Time Series insights](time-series-insights-concepts-retention.md).

- Saiba [como dimensionar seu ambiente de Azure Time Series insights](time-series-insights-how-to-scale-your-environment.md).

- Saiba mais sobre como [planejar o seu ambiente](time-series-insights-environment-planning.md).
