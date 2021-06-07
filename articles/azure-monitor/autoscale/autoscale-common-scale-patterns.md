---
title: Visão geral dos padrões comuns de dimensionamento automático
description: Conheça alguns dos padrões comuns para fazer o dimensionamento automático de seu recurso no Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: adbfecd05bfd4330e7c7c972cfdb9fe7021eec31
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100605787"
---
# <a name="overview-of-common-autoscale-patterns"></a>Visão geral dos padrões comuns de dimensionamento automático
Este artigo descreve alguns dos padrões comuns para dimensionar seu recurso no Azure.

O dimensionamento automático do Azure Monitor aplica-se somente aos [Conjuntos de Dimensionamento de Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), aos [Serviços de Nuvem](https://azure.microsoft.com/services/cloud-services/), ao [Serviço de Aplicativo – Aplicativos Web](https://azure.microsoft.com/services/app-service/web/) e aos [Serviços de Gerenciamento de API](../../api-management/api-management-key-concepts.md).

## <a name="lets-get-started"></a>Vamos começar

Este artigo pressupõe que você esteja familiarizado com o dimensionamento automático. Você pode [começar a dimensionar seu recurso aqui][1]. A seguir, temos alguns dos padrões de dimensionamento comuns.

## <a name="scale-based-on-cpu"></a>Dimensionamento com base na CPU

Você tem um aplicativo Web (/VMSS/função de serviço de nuvem) e

- Você deseja escalar/reduzir horizontalmente com base na CPU.
- Você quer garantir também que haja um número mínimo de instâncias.
- Além disso, você deseja garantir que haja um limite máximo para o número de instâncias para as quais o dimensionamento pode ser feito.

![Dimensionamento com base na CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Dimensionamento diferente em finais de semana e dias da semana

Você tem um aplicativo Web (/VMSS/função de serviço de nuvem) e

- Você deseja três instâncias por padrão (em dias da semana)
- Você não espera o tráfego nos fins de semana e, portanto, deseja reduzir verticalmente para uma instância nos fins de semana.

![Dimensionamento diferente em finais de semana e dias da semana][3]

## <a name="scale-differently-during-holidays"></a>Dimensionamento diferente durante feriados

Você tem um aplicativo Web (/VMSS/função de serviço de nuvem) e

- Por padrão, você deseja reduzir/escalar verticalmente com base no uso da CPU
- No entanto, durante o período de férias (ou em dias específicos que são importantes para sua empresa), você deseja substituir os padrões e ter mais capacidade à sua disposição.

![Dimensionamento diferente em feriados][4]

## <a name="scale-based-on-custom-metric"></a>Dimensionamento baseado em métrica personalizada

Você tem um front-end da Web e uma camada de API que se comunica com o back-end.

- Você deseja dimensionar a camada de API com base em eventos personalizados no front-end (exemplo: você quer dimensionar seu processo de check-out com base no número de itens no carrinho de compras)

![Dimensionamento baseado em métrica personalizada][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png
