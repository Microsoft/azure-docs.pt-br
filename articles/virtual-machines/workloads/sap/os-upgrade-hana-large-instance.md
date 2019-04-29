---
title: Atualização de sistema operacional do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Executar a atualização de sistema operacional para o SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7d451f3831309b4755170915b35a23da8910510
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935629"
---
# <a name="operating-system-upgrade"></a>Atualização do Sistema Operacional
Este documento descreve os detalhes sobre as atualizações de sistema operacional em Instâncias Grandes de HANA.

>[!NOTE]
>A atualização do sistema operacional é de responsabilidade do cliente, o suporte a operações da Microsoft pode orientá-lo para as áreas principais a serem observadas durante a atualização. Antes de planejar uma atualização, você deve consultar o fornecedor do sistema operacional.

No momento do provisionamento da unidade HLI, a equipe de operações da Microsoft instalará o sistema operacional. Ao longo do tempo, você deverá manter o sistema operacional (exemplo: aplicação de patches, ajustes, atualizações, etc.) na unidade HLI.

Antes das principais mudanças do sistema operacional (por exemplo, Atualização SP1 a SP2), você deve contatar a equipe da Microsoft Operations ao abrir m tíquete de suporte para consultar.

Inclua em seu tíquete:

* A ID da assinatura da HLI.
* O nome do servidor.
* O nível de patch que você pretende aplicar.
* A data em que você está planejando essa alteração. 

Recomendamos que você abra esse tíquete pelo menos uma semana antes da data de conclusão da atualização desejável, para que a equipe de operações verifique se uma atualização de firmware é necessária na folha do seu servidor.


Para a matriz de suporte das diferentes versões do SAP HANA com as diferentes versões do Linux, consulte [Nota SAP nº 2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Problemas conhecidos

A seguir estão alguns problemas conhecidos comuns durante a atualização:
- Na SKU de classe Tipo II, o Software Foundation Server (SFS) é removido após a atualização do sistema operacional. Depois de atualizar o sistema operacional, você deverá reinstalar o SFS compatível.
- Drivers de placa Ethernet (ENIC e FNIC) revertidos para a versão mais antiga. Você deverá reinstalar a versão compatível dos drivers após a atualização.

## <a name="next-steps"></a>Próximas etapas
- Consulte [Backup e restauração](hana-overview-high-availability-disaster-recovery.md) para classe de SKU Tipo I de backup de sistema operacional.
- Consulte [Backup de Sistema Operacional para classe de SKU Tipo II](os-backup-type-ii-skus.md).
