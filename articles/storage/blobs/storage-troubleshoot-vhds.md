---
title: Solucionar problemas de discos anexados às VMs do Azure | Microsoft Docs
description: Fornece links para os recursos de solução de problemas dos VHDs (discos rígidos virtuais) da máquina virtual do Azure.
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 10/31/2018
ms.date: 12/10/2018
ms.author: v-jay
ms.openlocfilehash: bf1aa75399f28b8dd1732bc9ec09c15e46132939
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098092"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Solucionar problemas de discos anexados às VMs do Azure 

As Máquinas Virtuais (VMs) do Azure dependem de Discos Rígidos virtuais (VHDs) para o disco do sistema operacional e quaisquer discos de dados anexados. Os VHDs são armazenados como blobs de páginas em uma ou mais contas de Armazenamento do Microsoft Azure. Este artigo descreve como solucionar problemas de conteúdo para problemas comuns que podem surgir com VHDs. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Solucionar problemas de erros de exclusão de armazenamento para uma VM

Em certos casos, você pode encontrar um erro enquanto a exclusão de um recurso de armazenamento quando uma VM em uma implantação do Gerenciador de Recursos contém VHDs anexados. Para obter ajuda na resolução desse problema, consulte um dos seguintes artigos: 

  * Em VMs do Linux: [Erros de exclusão de armazenamento na implantação do Gerenciador de Recursos](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Em VMs do Windows: [Erros de exclusão de armazenamento na implantação do Gerenciador de Recursos](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Solucionar problemas de reinicializações inesperadas de VMs com VHDs anexados

Se você encontrar reinicializações inesperadas de uma VM com um grande número de VHDs anexados, consulte um dos seguintes artigos:

  * Em VMs do Linux: [Reinicializações inesperadas de VMs com VHDs anexados](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Em VMs do Windows: [Reinicializações inesperadas de VMs com VHDs anexados](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
