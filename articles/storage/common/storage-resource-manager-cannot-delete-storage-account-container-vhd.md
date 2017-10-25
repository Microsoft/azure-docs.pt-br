---
title: "Solucione erros ao excluir contas de Armazenamento do Azure, contêineres ou VHDs | Microsoft Docs"
description: "Solucione erros ao excluir contas de Armazenamento do Azure, contêineres ou VHDs"
services: storage
documentationcenter: 
author: passaree
manager: cshepard
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/13/2017
ms.author: genli
ms.openlocfilehash: 174ab97ac14f4c05690306691df5ee4b6d33dd93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-storage-delete-errors-in-resource-manager-deployment"></a>Solucionar problemas de erros de exclusão de armazenamento na implantação do Resource Manager
Este artigo fornece orientação para solução de problemas quando um dos seguintes erros ocorrem ao tentar excluir uma conta de Armazenamento do Azure, um contêiner ou um blob na implantação do Azure Resource Manager.

>**Falha ao excluir a conta de armazenamento 'StorageAccountName'. Erro: A conta de armazenamento não pode ser excluída, pois seus artefatos estão em uso.**

>**Falha ao excluir # de # contêineres:<br>vhds: Atualmente, há uma concessão no contêiner e nenhuma ID de concessão foi especificada na solicitação.**

>**Falha ao excluir # de # blobs:<br>BlobName.vhds: Atualmente, há uma concessão no blob e nenhuma ID de concessão foi especificada na solicitação.**

Os discos rígidos virtuais usados em VMs do Azure são arquivos .vhd armazenados como blobs de páginas em uma conta de armazenamento padrão ou premium no Azure.  Mais informações sobre discos do Azure podem ser encontradas [aqui](../../virtual-machines/windows/about-disks-and-vhds.md). O Azure evita a exclusão de um disco que está anexado a uma VM para evitar a corrupção. Isso também impedirá a exclusão de contêineres e contas de armazenamento que têm um blob de páginas que está anexado a uma VM. 

## <a name="solution"></a>Solução
O processo para excluir uma conta de armazenamento, contêiner ou blob ao receber um dos erros acima é: 
1. [Identificar os blobs anexados a uma VM](#step-1-identify-blobs-attached-to-a-vm)
2. [Excluir VMs com **disco de SO** anexado](#step-2-delete-vm-to-detach-os-disk)
3. [Desanexar todos os **discos de dados** das VMs restantes](#step-3-detach-data-disk-from-the-vm)

Tente novamente excluir a conta de armazenamento, o contêiner ou o blob depois de concluir as etapas acima.

### <a name="step-1-identify-blob-attached-to-a-vm"></a>Etapa 1: identificar blob anexado a uma VM

#### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Cenário 1: excluir um blob – identificar VM anexada
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Todos os recursos**. Vá para a conta de armazenamento e, em **Serviço Blob**, selecione **Contêineres** e navegue até o blob a ser excluído.
3. Se o blob **Estado da Concessão** está **Concedido**, clique com o botão direito do mouse e selecione **Editar Metadados** para abrir o painel de metadados de Blob. 

    ![Captura de tela do portal, com os blobs de conta de armazenamento e a opção > "Editar Metadados" destacada após um clique com o botão direito do mouse](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-edit-metadata-sm.png)

4. No painel de metadados de Blob, verifique e registre o valor para **MicrosoftAzureCompute_VMName**. Esse valor é o nome da VM à qual o VHD está anexado. (Consulte **importante** se esse campo não existir)
5. No painel de metadados de Blob, verifique e registre o valor de **MicrosoftAzureCompute_DiskType**. Isso identifica se o disco anexado é de SO ou de dados (consulte **importante** se esse campo não existir). 

     ![Captura de tela do portal, com o painel "Metadados de Blob" aberto](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-blob-metadata-sm.png)

6. Se o tipo de disco de blob for **OSDisk**, siga a [Etapa 2: excluir a VM para desanexar o disco de SO](#step-2-delete-vm-to-detach-os-disk). Caso contrário, se o tipo de disco de blob é **DataDisk**, siga as etapas em [Etapa 3: desanexar o disco de dados da VM](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Se **MicrosoftAzureCompute_VMName** e **MicrosoftAzureCompute_DiskType** não aparecem nos metadados de blob, isso indica que o blob é concedido explicitamente e não está anexado a uma VM. Blobs concedidos não podem ser excluídos sem a prévia interrupção da concessão. Para interromper a concessão, clique com o botão direito do mouse no blob e selecione **Interromper concessão**. Blobs concedidos que não estão conectados a uma VM impedem a exclusão do blob, mas não impedem a exclusão de um contêiner ou conta de armazenamento.

#### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Cenário 2: excluir um contêiner – identificar todos os blobs no contêiner que estão anexados a VMs
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Todos os recursos**. Vá para a conta de armazenamento e, em **Serviço Blob**, selecione **Contêineres** para localizar o contêiner a ser excluído.
3. Clique para abrir o contêiner e a lista de blobs dentro dele será exibida. Identifique todos os blobs com o Tipo de Blob = **Blob de páginas** e Estado da Concessão = **Concedido** dessa lista. Siga o [Cenário 1](#step-1-identify-blobs-attached-to-a-vm) para identificar a VM associada a cada um desses blobs.

    ![Captura de tela do portal, com os blobs da conta de armazenamento e o "Estado da Concessão" com "Concedido" realçado](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-disks-sm.png)

4. Siga a [Etapa 2](#step-2-delete-vm-to-detach-os-disk) e a [Etapa 3](#step-3-detach-data-disk-from-the-vm) para excluir VMs com **OSDisk** e desanexar **DataDisk**. 

#### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Cenário 3: excluir a conta de armazenamento – identificar todos os blobs na conta de armazenamento que estão anexados às VMs
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Todos os recursos**. Vá para a conta de armazenamento e, em **Serviço Blob**, selecione **Contêineres**.

    ![Captura de tela do portal, com os contêineres da conta de armazenamento e o "Estado da Concessão" com "Concedido" realçado](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-containers-sm.png)

3. Na folha **Contêineres**, identifique todos os contêineres em que **Estado da Concessão** é **Concedido** e siga o [Cenário 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) para cada contêiner **Concedido**.
4. Siga a [Etapa 2](#step-2-delete-vm-to-detach-os-disk) e a [Etapa 3](#step-3-detach-data-disk-from-the-vm) para excluir VMs com **OSDisk** e desanexar **DataDisk**. 

### <a name="step-2-delete-vm-to-detach-os-disk"></a>Etapa 2: excluir a VM para desanexar o disco do SO
Se o VHD é um disco do sistema operacional, você deve excluir a VM antes de poder excluir o VHD anexado. Nenhuma ação adicional será necessária para discos de dados anexados à mesma VM após a conclusão dessas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu do Hub, selecione **Máquinas Virtuais**.
3. Selecione a VM à qual o VHD está anexado.
4. Certifique-se de que nada esteja usando ativamente a máquina virtual, e que você não precise mais da máquina virtual.
5. Na parte superior da folha **Detalhes da Máquina Virtual**, selecione **Excluir** e clique em **Sim** para confirmar.
6. A VM deve ser excluída, mas o VHD pode ser mantido. No entanto, o VHD não deve ter mais estar anexado a uma VM nem ter uma concessão. Talvez demore alguns minutos para o serviço ser liberado. Para verificar se a concessão está liberada, navegue até o local do blob e, no painel **Propriedades do blob**, o **Status de concessão** deve ser **Disponível**.

### <a name="step-3-detach-data-disk-from-the-vm"></a>Etapa 3: desanexar o disco de dados da VM
Se o VHD for um disco de dados, desanexe o VHD da VM para remover a concessão:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu do Hub, selecione **Máquinas Virtuais**.
3. Selecione a VM à qual o VHD está anexado.
4. Selecione **Discos** na folha **Detalhes da máquina virtual**.
5. Selecione o disco de dados a ser excluído ao qual o VHD está associado. Você pode determinar qual blob está anexado ao disco verificando a URL do VHD.
6. Você pode verificar o local do blob clicando no disco para verificar o caminho no campo **URI do VHD**.
7. Selecione **Editar** na parte superior da folha **Discos**.
8. Clique no **ícone desanexar** do disco de dados a ser excluído.

     ![Captura de tela do portal, com o painel "Metadados de Blob" aberto](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-vm-disks-edit.png)

9. Selecione **Salvar**. Agora, o disco deve ser desanexado da VM, e o VHD não deve ter mais uma concessão. Talvez demore alguns minutos para o serviço ser liberado. Para verificar se a concessão foi liberada, navegue até o local do blob e, no painel **Propriedades do blob**, o valor **Status de concessão** deve ser **Desbloqueado** ou **Disponível**.

## <a name="next-steps"></a>Próximas etapas
Tente novamente realizar a exclusão do objeto de armazenamento que falhou anteriormente.

