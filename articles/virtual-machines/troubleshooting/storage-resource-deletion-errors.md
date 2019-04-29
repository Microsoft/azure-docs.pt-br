---
title: Solução de problemas de erros de exclusão de recursos de armazenamento em VMs do Linux no Azure | Microsoft Docs
description: Como solucionar problemas ao excluir os recursos de armazenamento que contém o VHDs anexados.
keywords: ''
services: virtual-machines
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: a1eb946d3f1b18aaa86735dedcfbaa1fd6a89621
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60922566"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Solucionar problemas de erros de exclusão de recursos de armazenamento

Em determinados cenários, você pode encontrar um dos seguintes erros ocorre quando se tenta excluir uma conta de armazenamento do Azure, um contêiner ou um blob na implantação do Azure Resource Manager:

> **Falha ao excluir a conta de armazenamento 'StorageAccountName'. Erro: Não é possível excluir a conta de armazenamento porque seus artefatos estão em uso.**
> 
> **Falha ao excluir # de # contêiner(es):<br>vhds: Atualmente, há uma concessão no contêiner e nenhuma ID de concessão foi especificada na solicitação.**
> 
> **Falha ao excluir # de # blobs:<br>NomeDoBlob.vhd: Atualmente, há uma concessão no blob e nenhuma ID de concessão foi especificada na solicitação.**

Os VHDs usados em VMs do Azure são arquivos .vhd armazenados como blobs de página em uma conta de armazenamento padrão ou premium no Azure. Para saber mais sobre os discos do Azure, confira [Introdução aos discos gerenciados](../linux/managed-disks-overview.md).

O Azure evita a exclusão de um disco que está anexado a uma VM para evitar a corrupção. Isso também impedirá a exclusão de contêineres e contas de armazenamento que têm um blob de páginas que está anexado a uma VM. 

O processo para excluir uma conta de armazenamento, contêiner ou blob ao receber um destes erros é: 
1. Identificar os blobs anexados a uma VM
2. [Excluir VMs com **disco de SO** anexado](#step-2-delete-vm-to-detach-os-disk)
3. [Desanexar todos os **discos de dados** das VMs restantes](#step-3-detach-data-disk-from-the-vm)

Tente novamente excluir a conta de armazenamento, o contêiner ou o blob depois de concluir estas etapas.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Etapa 1: Identificar o blob anexado a uma VM

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Cenário 1: Excluir um blob – identificar VM anexada
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Todos os recursos**. Vá para a conta de armazenamento e, em **Serviço Blob**, selecione **Contêineres** e navegue até o blob a ser excluído.
3. Se o blob **Estado da Concessão** estiver **Concedido**, clique no botão direito do mouse e selecione **Editar Metadados** para abrir o painel de metadados de Blob. 

    ![Captura de tela do portal, com os blobs de conta de Armazenamento e a opção > "Editar Metadados" destacada após um clique com o botão direito do mouse](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. No painel de metadados de Blob, verifique e registre o valor para **MicrosoftAzureCompute_VMName**. Esse valor é o nome da VM à qual o VHD está anexado. (Consulte **importante** se esse campo não existir)
5. No painel de metadados de Blob, verifique e registre o valor de **MicrosoftAzureCompute_DiskType**. Esse valor identifica se o disco anexado é de SO ou de dados (consulte **importante** se esse campo não existir). 

     ![Captura de tela do portal, com o painel "Metadados de Blob" aberto](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Se o tipo de disco de blob for **OSDisk**, siga a [Etapa 2: excluir a VM para desanexar o disco do SO](#step-2-delete-vm-to-detach-os-disk). Caso contrário, se o tipo de disco de blob for **DataDisk**, siga os passos da [Etapa 3: desanexar o disco de dados da VM](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Se **MicrosoftAzureCompute_VMName** e **MicrosoftAzureCompute_DiskType** não aparecem nos metadados de blob, isso indica que o blob é concedido explicitamente e não está anexado a uma VM. Blobs concedidos não podem ser excluídos sem a prévia interrupção da concessão. Para interromper a concessão, clique com o botão direito do mouse no blob e selecione **Interromper concessão**. Blobs concedidos que não estão conectados a uma VM impedem a exclusão do blob, mas não impedem a exclusão de um contêiner ou uma conta de armazenamento.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Cenário 2: excluir um contêiner – identificar todos os blobs no contêiner que estão anexados às VMs
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Todos os recursos**. Vá para a conta de armazenamento e, em **Serviço Blob**, selecione **Contêineres** para localizar o contêiner a ser excluído.
3. Clique para abrir o contêiner e a lista de blobs dentro dele será exibida. Identifique todos os blobs com o Tipo de Blob = **Blob de páginas** e Estado da Concessão = **Concedido** dessa lista. Siga o Cenário 1 para identificar a VM associada a cada um desses blobs.

    ![Captura de tela do portal, com os blobs da conta de armazenamento e o "Estado da Concessão" com "Concedido" realçado](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Siga a [Etapa 2](#step-2-delete-vm-to-detach-os-disk) e a [Etapa 3](#step-3-detach-data-disk-from-the-vm) para excluir VMs com **OSDisk** e desanexar **DataDisk**. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Cenário 3: excluir a conta de armazenamento – identificar todos os blobs na conta de armazenamento que estão anexados às VMs
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Todos os recursos**. Vá para a conta de armazenamento, em **Serviço de Blob**, selecione **Blobs**.
3. No painel **Contêineres**, identifique todos os contêineres em que **Estado da Concessão** é **Concedido** e siga o [Cenário 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) para cada contêiner **Concedido**.
4. Siga a [Etapa 2](#step-2-delete-vm-to-detach-os-disk) e a [Etapa 3](#step-3-detach-data-disk-from-the-vm) para excluir VMs com **OSDisk** e desanexar **DataDisk**. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Etapa 2: excluir a VM para desanexar o disco do SO
Se o VHD é um disco do sistema operacional, você deve excluir a VM antes de poder excluir o VHD anexado. Nenhuma ação adicional será necessária para discos de dados anexados à mesma VM após a conclusão dessas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu do Hub, selecione **Máquinas Virtuais**.
3. Selecione a VM à qual o VHD está anexado.
4. Certifique-se de que nada esteja usando ativamente a máquina virtual, e que você não precise mais da máquina virtual.
5. Na parte superior do painel **Detalhes da Máquina Virtual**, selecione **Excluir** e clique em **Sim** para confirmar.
6. A VM deve ser excluída, mas o VHD pode ser mantido. No entanto, o VHD não deve ter mais estar anexado a uma VM nem ter uma concessão. Talvez demore alguns minutos para o serviço ser liberado. Para verificar se a concessão está liberada, navegue até o local do blob e, no painel **Propriedades do blob**, o **Status de concessão** deve ser **Disponível**.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Etapa 3: desanexar o disco de dados da VM
Se o VHD for um disco de dados, desanexe o VHD da VM para remover a concessão:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu do Hub, selecione **Máquinas Virtuais**.
3. Selecione a VM à qual o VHD está anexado.
4. Selecione **Discos** no painel **Detalhes da máquina virtual**.
5. Selecione o disco de dados a ser excluído ao qual o VHD está associado. Você pode determinar qual blob está anexado ao disco verificando a URL do VHD.
6. Você pode verificar o local do blob clicando no disco para verificar o caminho no campo **URI do VHD**.
7. Selecione **Editar** na parte superior do painel **Discos**.
8. Clique no **ícone desanexar** do disco de dados a ser excluído.

     ![Captura de tela do portal, com o painel "Metadados de Blob" aberto](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Clique em **Salvar**. Agora, o disco está desconectado da VM, e o VHD não é mais concedido. Talvez demore alguns minutos para o serviço ser liberado. Para verificar se a concessão foi liberada, navegue até o local do blob e, no painel **Propriedades do blob**, o valor **Status de concessão** deve ser **Desbloqueado** ou **Disponível**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

