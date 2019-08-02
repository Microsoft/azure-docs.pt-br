---
title: Atualização de sistema operacional do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Executar a atualização de sistema operacional para o SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa88e45f2523dd65c4f714bfeab1c0eda401d720
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869146"
---
# <a name="operating-system-upgrade"></a>Atualização do Sistema Operacional
Este documento descreve os detalhes sobre as atualizações de sistema operacional em Instâncias Grandes de HANA.

>[!NOTE]
>A atualização do sistema operacional é de responsabilidade do cliente, o suporte a operações da Microsoft pode orientá-lo para as áreas principais a serem observadas durante a atualização. Antes de planejar uma atualização, você deve consultar o fornecedor do sistema operacional.

No momento, do provisionamento da unidade HLI, a equipe de operações da Microsoft instala o sistema operacional. Ao longo do tempo, você deverá manter o sistema operacional (exemplo: aplicação de patches, ajustes, atualizações, etc.) na unidade HLI.

Antes de fazer alterações importantes no sistema operacional (por exemplo, atualizar o SP1 para o SP2), você precisa entrar em contato com a equipe de operações da Microsoft abrindo um tíquete de suporte para consultar.

Incluir no seu tíquete:

* A ID da assinatura da HLI.
* O nome do servidor.
* O nível de patch que você pretende aplicar.
* A data em que você está planejando essa alteração. 

Recomendamos que você abra esse tíquete pelo menos uma semana antes da data de conclusão da atualização desejável, para que a equipe de operações verifique se uma atualização de firmware é necessária na folha do seu servidor.


Para a matriz de suporte das diferentes versões do SAP HANA com as diferentes versões do Linux, consulte [Nota SAP nº 2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Problemas conhecidos

A seguir estão alguns problemas conhecidos comuns durante a atualização:
- Na SKU de classe Tipo II, o Software Foundation Server (SFS) é removido após a atualização do sistema operacional. Você precisa reinstalar o SFS compatível após a atualização do sistema operacional.
- Drivers de placa Ethernet (ENIC e FNIC) revertidos para a versão mais antiga. Você precisa reinstalar a versão compatível dos drivers após a atualização.

## <a name="next-steps"></a>Próximas etapas
- Consulte [Backup e restauração](hana-overview-high-availability-disaster-recovery.md) para classe de SKU Tipo I de backup de sistema operacional.
- Consulte [backup do sistema operacional para SKUs do tipo II de carimbos de revisão 3](os-backup-type-ii-skus.md) para a classe SKU do tipo II.
