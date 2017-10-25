---
title: Anexar um disco de dados a uma VM do Linux | Microsoft Docs
description: Utilize o portal para anexar disco de dados novo ou existente a uma VM Linux.
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 787f729732accd74c212b3be9520af50a2f04261
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Utilize o portal para anexar um disco de dados a uma VM Linux 
Este artigo mostra como anexar discos novos e existentes a uma máquina virtual Linux por meio do portal do Azure. Você também pode [anexar um disco de dados a uma VM do Windows no Portal do Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Antes de anexar discos à sua VM, veja estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para usar o Armazenamento Premium, você precisa de uma máquina virtual da série DS ou GS. Você pode usar discos Premium e Standard com essas máquinas virtuais. O armazenamento Premium está disponível em determinadas regiões. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../../storage/common/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Discos anexados às máquinas virtuais são, na verdade, os arquivos. vhd armazenados no Azure. Para obter detalhes, confira [Sobre discos e VHDs para máquinas virtuais](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="find-the-virtual-machine"></a>Localizar a máquina virtual
1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda, clique em **Máquinas Virtuais**.
3. Selecione a máquina virtual na lista.
4. Na página Máquinas Virtuais, em **Essentials**, clique em **Discos**.
   
    ![Abrir configurações de disco](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Anexar um novo disco

1. No painel **Discos**, clique em **+ Adicionar disco de dados**.
2. Clique no menu suspenso para **Nome** e selecione **Criar disco**:

    ![Criar disco gerenciado do Azure](./media/attach-disk-portal/create-new-md.png)

3. Insira um nome para o disco gerenciado. Examine as configurações padrão, atualize conforme necessário e clique em **Criar**.
   
   ![Analisar configurações de disco](./media/attach-disk-portal/create-new-md-settings.png)

4. Clique em **Salvar** para criar o disco gerenciado e atualizar a configuração da VM:

   ![Salvar novo Azure Managed Disk](./media/attach-disk-portal/confirm-create-new-md.png)

5. Depois que o Azure cria o disco e o anexa à máquina virtual, o novo disco é listado nas configurações de disco da máquina virtual em **Discos de Dados**. Como os discos gerenciados são um recurso de nível superior, o disco aparece na raiz do grupo de recursos:

   ![Azure Managed Disk no grupo de recursos](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Anexar um disco existente
1. No painel **Discos**, clique em **+ Adicionar disco de dados**.
2. Clique no menu suspenso para **Nome** para exibir uma lista de discos gerenciados existentes acessíveis à sua assinatura do Azure. Selecione o disco gerenciado a ser anexado:

   ![Anexar um Azure Managed Disk existente](./media/attach-disk-portal/select-existing-md.png)

3. Clique em **Salvar** para anexar o disco gerenciado existente e atualizar a configuração da VM:
   
   ![Salvar atualizações do Azure Managed Disk](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Depois que o Azure anexa o disco à máquina virtual, ele é listado nas configurações de disco da máquina virtual em **Discos de Dados**.



## <a name="next-steps"></a>Próximas etapas
Você também pode [anexar um disco de dados](add-disk.md) utilizando a CLI do Azure.
