---
title: Use um ouvinte DNN (nome de rede distribuída) em vez de um ouvinte VNN para grupos de disponibilidade em VMs do SQL Server.
description: Mensagem para redirecionar os clientes para usar o ouvinte DNN em vez do ouvinte VNN.
ms.topic: include
author: MashaMSFT
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b4ea8428ae23537cde584bf6944732f65ebd2f26
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167978"
---
> [!NOTE]
> Os clientes no SQL Server 2019 CU8 e posterior no Windows 2016 e posterior podem substituir o ouvinte VNN tradicional e o Azure Load Balancer por um [ouvinte DNN (nome de rede distribuída)](../virtual-machines/windows/availability-group-distributed-network-name-dnn-listener-configure.md). Ignore o restante das etapas neste artigo que criam o ouvinte e o balanceador de carga.
