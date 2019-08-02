---
title: Visão geral do disco gerenciado do Armazenamento em Disco do Azure para VMs Windows | Microsoft Docs
description: Visão geral dos discos gerenciados do Azure, que lidam com as contas de armazenamento para você ao usar as VMs do Windows no Azure
author: roygara
ms.service: virtual-machines-windows
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f86937feb7061950084a8b5bdb532075c7f93869
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693714"
---
# <a name="introduction-to-azure-managed-disks"></a>Introdução aos discos gerenciados do Azure

Um disco gerenciado do Azure é um disco rígido virtual (VHD). Você pode pensar nisso como um disco físico em um servidor local, mas virtualizado. Os discos gerenciados do Azure são armazenados como blobs de página, que são um objeto de armazenamento de E/S aleatório no Azure. Chamamos um disco gerenciado de “gerenciado” porque é uma abstração sobre blobs de páginas, contêineres de blob e contas de armazenamento do Azure. Com discos gerenciados, tudo o que você precisa fazer é provisionar o disco e o Azure cuidará do resto.

Ao escolher usar discos gerenciados do Azure com suas cargas de trabalho, o Azure cria e gerencia o disco para você. Os tipos de discos disponíveis são Ultra SSD (Unidades de Estado Sólido) (Versão prévia), SSD Premium, SSD Standard e Unidades de Disco Rígido (HDD) Padrão. Para obter mais informações sobre cada tipo de disco individual, consulte [Selecionar um tipo de disco para VMs de IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Selecionar um tipo de disco para VMs de IaaS](disks-types.md)