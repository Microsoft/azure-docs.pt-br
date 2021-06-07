---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4af8a150b062526f08c1d15581ec26e2fe12d8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95992929"
---
- Os instantâneos incrementais atualmente não podem ser movidos entre assinaturas.
- No momento, você pode gerar apenas URIs SAS de até cinco instantâneos de uma família de instantâneos específica em um determinado momento.
- Você não pode criar um instantâneo incremental para um disco específico fora da assinatura desse disco.
- Até sete instantâneos incrementais por disco podem ser criados a cada cinco minutos.
- Um total de 200 instantâneos incrementais podem ser criados para um único disco.
- Você não pode obter as alterações entre os instantâneos feitos antes e depois de alterar o tamanho do disco pai entre 4 TB de limite. Por exemplo, você tirou um instantâneo de instantâneo incremental-a quando o tamanho de um disco era 2 TB. Agora você aumentou o tamanho do disco para 6 TB e, em seguida, tirou outro instantâneo de instantâneo incremental-b. Você não pode obter as alterações entre o instantâneo-a e o instantâneo-b. Você precisa baixar novamente a cópia completa do instantâneo-b criado após o redimensionamento. Subsequentemente, você pode obter as alterações entre instantâneos-b e instantâneos criados após o instantâneo-b. 
