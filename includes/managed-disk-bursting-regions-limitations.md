---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 01/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 5658b68081fae8dab528030f7fc1cd6fe4935730
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "102178300"
---
- A intermitência sob demanda não pode ser habilitada em um SSD Premium com menos de 512 GiB. O SSDs Premium com menos de 512 GiB sempre usará a intermitência baseada em crédito.
- A intermitência sob demanda só tem suporte no SSDs Premium. Se um SSD Premium com intermitência sob demanda habilitado for alternado para outro tipo de disco, a intermitência de disco será desabilitada.
- A intermitência sob demanda não é automaticamente desativada quando o nível de desempenho é alterado. Se você quiser alterar o nível de desempenho, mas não quiser manter a intermitência de disco, deverá desabilitá-lo.
- A intermitência sob demanda só pode ser habilitada quando o disco é desanexado de uma VM ou quando a VM é interrompida. A intermitência sob demanda pode ser desabilitada 12 horas após ter sido habilitada.