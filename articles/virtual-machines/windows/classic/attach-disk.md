---
title: "Anexar disco a uma VM clássica do Azure | Microsoft Docs"
description: "Anexe um disco de dados a uma máquina virtual do Windows criada com o modelo de implantação clássica e inicialize-a."
services: virtual-machines-windows, storage
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: be4e3e74-05bc-4527-969f-84f10a1d66a7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cynthn
ms.openlocfilehash: 087d5cda354f6e1780bddd3725859444177abd16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Anexe um disco de dados a uma máquina virtual do Windows criada com o modelo de implantação clássico
<!--
Refernce article:
    If you want to use the new portal, see [How to attach a data disk to a Windows VM in the Azure portal](../../virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

Este artigo mostra como anexar discos novos e existentes criados com o modelo de implantação Clássico a uma máquina virtual Windows usando o portal do Azure.

Você também pode [anexar um disco de dados a uma VM Linux no portal do Azure](../../linux/attach-disk-portal.md).

Antes de anexar um disco, leia estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](../../virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Para usar o Armazenamento Premium, você precisa de uma máquina virtual da série DS ou GS. Você pode usar discos de contas de armazenamento Premium e Standard com essas máquinas virtuais. O armazenamento Premium está disponível em determinadas regiões. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../../../storage/common/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Para um novo disco, você não precisa criá-lo primeiro porque o Azure cria quando você anexa o mesmo.

Você também pode [anexar um disco de dados usando o Powershell](../../virtual-machines-windows-attach-disk-ps.md).

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md).

## <a name="find-the-virtual-machine"></a>Localizar a máquina virtual
1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Escolha a máquina virtual nos recursos listados no painel.
3. No painel esquerdo, em **Configurações**, clique em **Discos**.

    ![Abrir configurações de disco](./media/attach-disk/virtualmachinedisks.png)

Continue seguindo as instruções para anexar um [novo disco](#option-1-attach-a-new-disk) ou um [disco existente](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-and-initialize-a-new-disk"></a>Opção 1: Anexar e inicializar um novo disco

1. Na folha **Discos**, clique em **Anexar novo**.
2. Examine as configurações padrão, atualize conforme necessário e clique em **OK**.

   ![Analisar configurações de disco](./media/attach-disk/attach-new.png)

3. Depois que o Azure cria o disco e o anexa à máquina virtual, o novo disco é listado nas configurações de disco da máquina virtual em **Discos de Dados**.

### <a name="initialize-a-new-data-disk"></a>Inicializar um novo disco de dados

1. Conectar-se à máquina virtual. Para obter instruções, consulte [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](../../virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Depois de entrar na máquina virtual, abra o **Gerenciador de Servidores**. No painel esquerdo, selecione **Arquivos e serviços de armazenamento**.

    ![Abra o gerenciador de servidor.](../media/attach-disk-portal/fileandstorageservices.png)

3. Escolha **Discos**.
4. A seção **Discos** lista os discos. Na maioria das vezes, uma máquina virtual tem disco 0, disco 1 e disco 2. Disco 0 é o disco do sistema operacional, disco 1 é o disco temporário e disco 2 é o disco de dados que você acabou de conectar à máquina virtual. O disco de dados lista a Partição como **Desconhecido**.

 Clique com o botão direito do mouse no disco e selecione **Inicializar**.

5. Você é notificado de que todos os dados serão apagados quando o disco for inicializado. Clique em **Sim** para confirmar o aviso e inicializar o disco. Depois de concluir, a partição será listada como **GPT**. Clique com o botão direito do mouse no disco novamente e selecione **Novo Volume**.

6. Conclua o assistente usando os valores padrão. Quando o assistente for concluído, a seção **Volumes** listará o novo volume. Agora, o disco está online e pronto para armazenar dados.

    ![Volume inicializado com êxito](./media/attach-disk/newdiskafterinitialization.png)

## <a name="option-2-attach-an-existing-disk"></a>Opção 2: Anexar um disco existente
1. Na folha **Discos**, clique em **Anexar existente**.
2. Em **Anexar disco existente**, clique em **Local**.

   ![Anexar disco existente](./media/attach-disk/attachexistingdisksettings.png)
3. Em **Contas de armazenamento**, selecione a conta e o contêiner que mantém o arquivo .vhd.

   ![Localização do VHD](./media/attach-disk/existdiskstorageaccountandcontainer.png)

4. Selecionar o arquivo .vhd
5. Em **Anexar disco existente**, o arquivo que você selecionou é listado em **Arquivo VHD**. Clique em **OK**.
6. Depois que o Azure anexa o disco à máquina virtual, ele é listado nas configurações de disco da máquina virtual em **Discos de Dados**.

## <a name="use-trim-with-standard-storage"></a>Usar TRIM com o armazenamento padrão

Se você usar o armazenamento padrão (HDD), será necessário habilitar o TRIM. O TRIM descarta os blocos não usados do disco para que você seja cobrado apenas pelo armazenamento que está efetivamente sendo usado. Usar o TRIM pode poupar dinheiro, incluindo blocos não utilizados que resultam da exclusão de arquivos grandes.

Você pode executar esse comando para verificar a configuração de TRIM. Abra um prompt de comando na sua VM do Windows e digite:

```
fsutil behavior query DisableDeleteNotify
```

Se o comando retornar 0, o TRIM estará habilitado corretamente. Se ele retornar 1, execute o seguinte comando para habilitar o TRIM:
```
fsutil behavior set DisableDeleteNotify 0
```

## <a name="next-steps"></a>Próximas etapas
Caso seu aplicativo precise usar a unidade D: para armazenar dados, é possível [alterar a letra da unidade do disco temporário do Windows](../../virtual-machines-windows-change-drive-letter.md).

## <a name="additional-resources"></a>Recursos adicionais
[Sobre discos e VHDs para máquinas virtuais](../../virtual-machines-linux-about-disks-vhds.md)
