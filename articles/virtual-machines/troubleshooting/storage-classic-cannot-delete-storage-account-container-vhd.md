---
title: Solução de problemas de erros ao excluir contas de armazenamento clássicas do Azure, contêineres ou VHDs | Microsoft Docs
description: Como solucionar problemas ao excluir os recursos de armazenamento que contém o VHDs anexados.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 673101ad7f55969c216adf7e970402a2109f8254
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60864170"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Solução de problemas de erros de exclusão de recursos de armazenamento clássicos
Este artigo fornece diretrizes para solução de problemas quando ocorre um dos erros a seguir ao tentar excluir uma conta de armazenamento clássica do Azure, um contêiner ou um arquivo *.vhd do blob de páginas. 


Este artigo apenas aborda problemas com recursos de armazenamento clássicos. Se um usuário exclui uma máquina virtual clássica usando o portal do Azure, o PowerShell ou a CLI, os Discos não são excluídos automaticamente. O usuário recebe a opção de excluir o recurso de "Disco". Caso a opção não esteja selecionada, o recurso de "Disco" impedirá a exclusão da conta de armazenamento, do contêiner e do arquivo *.vhd real do blob de páginas.

Mais informações sobre discos do Azure podem ser encontradas [aqui](../../virtual-machines/windows/managed-disks-overview.md). O Azure evita a exclusão de um disco que está anexado a uma VM para evitar a corrupção. Isso também impedirá a exclusão de contêineres e contas de armazenamento que têm um blob de páginas anexado a uma VM. 

## <a name="what-is-a-disk"></a>O que é um "Disco"?
Um recurso de "Disco" é usado para montar um arquivo *.vhd do blob de páginas em uma máquina virtual, como um disco do sistema operacional ou um disco de Dados. Um recurso de disco do sistema operacional ou de disco de Dados, até que seja excluído, continuará mantendo uma concessão no arquivo *.vhd. Qualquer recurso de armazenamento no caminho mostrado na imagem abaixo não poderá ser excluído se um recurso de “Disco” apontar para ele.

![Captura de tela do portal, com o painel "Propriedade" (clássico) aberto do disco](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Etapas ao excluir uma máquina virtual clássica 
1. Exclua a máquina virtual clássica.
2. Se a caixa de seleção “Discos” estiver marcada, a **concessão de disco** (mostrada na imagem acima) associada ao *.vhd do blob de páginas será desfeita. O arquivo *.vhd real do blob de páginas ainda existirá na conta de armazenamento.
![Captura de tela do portal, com o painel de erro "Excluir" (clássico) aberto da máquina virtual](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. Depois que as concessões de disco forem desfeitas, os blobs de páginas em si poderão ser excluídos. Uma conta de armazenamento ou um contêiner pode ser excluído depois que todos os recursos de "Disco" presentes neles são excluídos.

>[!NOTE] 
>Se o usuário excluir a VM, mas não o VHD, os encargos de armazenamento continuarão sendo acumulados no arquivo *.vhd do blob de páginas. Os encargos serão alinhados ao tipo de conta de armazenamento; verifique a [página de preços](https://azure.microsoft.com/en-us/pricing/details/storage/) para obter mais detalhes. Se o usuário não pretende mais usar os VHDs, exclua-os para evitar encargos futuros. 

## <a name="unable-to-delete-storage-account"></a>Não é possível excluir a conta de armazenamento 

Quando o usuário tentar excluir uma conta de armazenamento clássica que não é mais necessária, ele poderá ver o comportamento a seguir.

#### <a name="azure-portal"></a>Portal do Azure 
O usuário navega para a conta de armazenamento clássica no [portal do Azure](https://portal.azure.com) e clica em **Excluir**. Ele verá a seguinte mensagem: 

Com os discos “anexados” a uma máquina virtual

![Captura de tela do portal, com o painel de erro "Excluir" (clássico) aberto da máquina virtual](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Com os discos “desanexados” de uma máquina virtual

![Captura de tela do portal, com o painel sem erro "Excluir" (clássico) aberto da máquina virtual](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
O usuário tenta excluir uma conta de armazenamento, que não está sendo mais usada, por meio de cmdlets clássicos do PowerShell. O usuário verá a seguinte mensagem:

> <span style="color:cyan">**Remove-AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount: BadRequest: A conta de armazenamento myclassicaccount tem algumas imagens e/ou alguns discos ativos, por exemplo,  
> myclassicaccount. Garanta que esses discos e/ou essas imagens sejam removidos antes de excluir essa conta de armazenamento.</span>

## <a name="unable-to-delete-storage-container"></a>Não é possível excluir o contêiner de armazenamento

Quando o usuário tentar excluir um contêiner de blobs de armazenamento clássico que não é mais necessário, ele poderá ver o comportamento a seguir.

#### <a name="azure-portal"></a>Portal do Azure 
O portal do Azure não permitirá ao usuário excluir um contêiner se houver uma concessão de "Discos" apontando para um arquivo *.vhd do blob de páginas no contêiner. Por design, isso serve para impedir a exclusão acidental de um arquivo VHD com concessão de Discos nele. 

![Captura de tela do portal, com o painel "Lista" aberto do contêiner de armazenamento](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Se o usuário optar pela exclusão usando o PowerShell, isso resultará no erro a seguir. 

> <span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Name vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer: O servidor remoto retornou um erro: (412) Atualmente, há uma concessão no contêiner e nenhuma ID de concessão foi especificada na solicitação. Código de Status HTTP: 412 – Mensagem de Erro HTTP: Atualmente, há uma concessão no contêiner e nenhuma ID de concessão foi especificada na solicitação.</span>

## <a name="unable-to-delete-a-vhd"></a>Não é possível excluir um VHD 

Depois de excluir a máquina virtual do Azure, o usuário tenta excluir o arquivo VHD (blob de páginas) e recebe a mensagem abaixo:

#### <a name="azure-portal"></a>Portal do Azure 
No portal, pode haver duas experiências, dependendo da lista de blobs selecionados para exclusão.

1. Se apenas blobs “Concedidos” estiverem selecionados, o botão Excluir não será exibido.
![Captura de tela do portal, com o painel "Lista" aberto do blob do contêiner](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Se uma combinação de blobs “Concedidos” e “Disponíveis” estiver selecionada, o botão “Excluir” será exibido. No entanto, a operação “Excluir” deixará para trás os blobs de páginas que têm uma concessão de Disco. 
![Captura de tela do portal, com o painel "Lista" aberto do blob do contêiner](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![Captura de tela do portal, com o painel "Excluir" aberto do blob selecionado](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Se o usuário optar pela exclusão usando o PowerShell, isso resultará no erro a seguir. 

> <span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob: O servidor remoto retornou um erro: (412) Atualmente, há uma concessão no blob e nenhuma ID de concessão foi especificada na solicitação. Código de Status HTTP: 412 – Mensagem de Erro HTTP: Atualmente, há uma concessão no blob e nenhuma ID de concessão foi especificada na solicitação.</span>


## <a name="resolution-steps"></a>Etapas de resolução

### <a name="to-remove-classic-disks"></a>Para remover Discos clássicos
Siga estas etapas no portal do Azure:
1.  Navegue até o [Portal do Azure](https://portal.azure.com).
2.  Navegue para Discos (clássicos). 
3.  Clique na guia Discos. ![Captura de tela do portal, com o painel "Lista" aberto do blob do contêiner](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Selecione o disco de dados e clique em Excluir o Disco.
 ![Captura de tela do portal, com o painel "Lista" aberto do blob do contêiner](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Repita a operação de Exclusão que falhou anteriormente.
6.  Uma conta de armazenamento ou um contêiner não poderá ser excluído enquanto tiver um único Disco.

### <a name="to-remove-classic-images"></a>Para remover Imagens clássicas   
Siga estas etapas no portal do Azure:
1.  Navegue até o [Portal do Azure](https://portal.azure.com).
2.  Navegue para as imagens do sistema operacional (clássicas).
3.  Exclua a imagem.
4.  Repita a operação de Exclusão que falhou anteriormente.
5.  Uma conta de armazenamento ou um contêiner não poderá ser excluído enquanto tiver uma única Imagem.
