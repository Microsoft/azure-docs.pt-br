---
title: Use o Armazenamento de Backup Moderno com o Servidor de Backup do Azure
description: Conheça os novos recursos do Servidor de Backup do Azure. Este artigo descreve como atualizar sua instalação do servidor de Backup.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: 1a265579593c8eb39f0df4037f8ec39c788ddbce
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465117"
---
# <a name="add-storage-to-azure-backup-server"></a>Adicionar armazenamento ao Servidor de Backup do Azure

O Servidor de Backup do Azure V2 (ou posterior) é compatível com o Armazenamento de Backup Moderno que oferece 50% de economia em armazenamento, backups três vezes mais rápidos e armazenamento mais eficiente. Ele também oferece o armazenamento com reconhecimento de carga de trabalho.

> [!NOTE]
> Para usar o Armazenamento de Backup Moderno, você deve executar o Servidor de Backup V2 ou V3 no Windows Server 2016 ou V3 no Windows Server 2019.
> Se você executar o Backup Server V2 em uma versão anterior do Windows Server, o Servidor de Backup do Azure não poderá aproveitar o Armazenamento de Backup Moderno. Em vez disso, ele protege as cargas de trabalho como acontece com o Backup Server V1. Para obter mais informações, consulte a versão do servidor de Backup [matriz proteção](backup-mabs-protection-matrix.md).

## <a name="volumes-in-backup-server"></a>Volumes no servidor de Backup

O servidor de Backup do Microsoft Azure V2 ou posterior aceita volumes de armazenamento. Quando você adiciona um volume, o servidor de Backup formata o volume para o sistema de arquivos resiliente (ReFS), que exige o armazenamento de Backup modernos. Para adicionar um volume e expandi-lo mais tarde, se você precisar, sugerimos que você use esse fluxo de trabalho:

1.  Configure o servidor de Backup em uma máquina virtual.
2.  Crie um volume em um disco virtual em um pool de armazenamento:
    1.  Adicione um disco para um pool de armazenamento e crie um disco virtual com o layout simples.
    2.  Adicione quaisquer discos adicionais e estenda o disco virtual.
    3.  Crie volumes no disco virtual.
3.  Adicione volumes ao servidor de Backup.
4.  Configure o armazenamento com reconhecimento de carga de trabalho.

## <a name="create-a-volume-for-modern-backup-storage"></a>Crie um volume para armazenamento de Backup moderno

Usar o Backup Server V2 ou posterior com volumes como armazenamento em disco pode ajudá-lo a manter o controle sobre o armazenamento. Um volume pode ser um único disco. No entanto, se você quiser estender o armazenamento no futuro, crie um volume fora de um disco criado usando espaços de armazenamento. Isso pode ajudar se você quiser expandir o volume de armazenamento de backup. Esta seção oferece as práticas recomendadas para a criação de um volume com esta instalação.

1. No Gerenciador do Servidor, selecione **Arquivo e Serviços de Armazenamento** > **Volumes** > **Pools de armazenamento**. Em **DISCOS FÍSICOS**, selecione **novo Pool de armazenamento**.

    ![Crie um novo pool de armazenamento](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. Em **TAREFAS** caixa suspensa, selecione **novo Disco Virtual**.

    ![Adicione um disco virtual](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Selecione o pool de armazenamento e, em seguida, selecione **Adicionar Disco Físico**.

    ![Adicione um disco físico](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Selecione o disco físico e, em seguida, selecione **Expandir Disco Virtual**.

    ![Estender o disco virtual](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Selecione o disco virtual e, em seguida, selecione **Novo Volume**.

    ![Criar um novo volume](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. No diálogo **Selecione o servidor e disco**, selecione o servidor e o novo disco. Em seguida, selecione **Avançar**.

    ![Selecione o servidor e disco](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Adicione volumes de armazenamento do servidor de Backup em disco

Para adicionar um volume ao servidor de Backup, no painel **Gerenciamento**, examinar novamente o armazenamento e, em seguida, selecione **Adicionar**. É exibida uma lista de todos os volumes disponíveis para serem adicionados para o armazenamento de servidor de Backup. Depois dos volumes disponíveis serem adicionados à lista de volumes selecionados, você pode dar-lhes um nome amigável para te ajudar a gerenciá-los. Para formatar esses volumes para ReFS para que o servidor de Backup possa usar os benefícios do armazenamento de Backup moderna, selecione **Ok**.

![Adicione Volumes disponíveis](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Configure o armazenamento com reconhecimento de carga de trabalho

Com o armazenamento com reconhecimento de carga de trabalho, você pode selecionar os volumes que armazenam preferencialmente determinados tipos de cargas de trabalho. Por exemplo, você pode definir volumes caros que dão suporte a um grande número de operações de entrada/saída por segundo (IOPS) para armazenar apenas as cargas de trabalho que exigem backups frequentes de alto volume. Um exemplo é o SQL Server com logs de transação. Outras cargas de trabalho de backup com menos frequência, como VMs, podem ser feitas o backup de volumes de baixo custo.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Você pode configurar o armazenamento com reconhecimento de carga de trabalho usando o cmdlet do PowerShell Update-DPMDiskStorage, que atualiza as propriedades de um volume no pool de armazenamento em um Servidor de Backup do Azure. 

Sintaxe:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
A captura de tela a seguir mostra o cmdlet Update-DPMDiskStorage na janela do PowerShell.

![O comando Update-DPMDiskStorage na janela do PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

As alterações feitas por meio do PowerShell são refletidas no Console do administrador do servidor de Backup.

![Discos e volumes no Console do administrador](./media/backup-mabs-add-storage/mabs-add-storage-9.png)


## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrar o armazenamento herdado para o Armazenamento de Backup Moderno
Depois de atualizar ou instalar o Backup Server V2 e atualizar o sistema operacional para o Windows Server 2016, atualize seus grupos de proteção para usar o Armazenamento de Backup Moderno. Por padrão, os grupos de proteção não são alterados. Eles continuam a funcionar como foram configurados inicialmente.

Atualizar grupos de proteção para usar o Armazenamento de Backup Moderno é opcional. Para atualizar o grupo de proteção, interrompa a proteção de todas as fontes de dados usando a opção manter dados. Em seguida, adicione as fontes de dados para um novo grupo de proteção.

1. No Console do Administrador, selecione a funcionalidade **Proteção**. Na lista **Membro do Grupo de Proteção**, clique com o botão direito no membro e, em seguida, selecione **Interromper proteção do membro**.

   ![Interromper a proteção de membro](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. Na caixa de diálogo **Remover do grupo**, examine o espaço em disco usado e o espaço livre disponível no pool de armazenamento. O padrão é deixar os pontos de recuperação no disco e permitir a expiração por sua política de retenção associada. Clique em **OK**.

   Se você quiser retornar imediatamente o espaço em disco usado para o pool de armazenamento livre, selecione a caixa de seleção **Excluir réplica no disco** para excluir os dados de backup (e pontos de recuperação) associado a esse membro.

   ![Remover da caixa de diálogo Grupo](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Crie um grupo de proteção que usa o Armazenamento de Backup Moderno. Inclua as fontes de dados desprotegidos.

## <a name="add-disks-to-increase-legacy-storage"></a>Adicionar discos para aumentar o armazenamento herdado

Se você quiser usar armazenamento herdado com o Servidor de Backup, talvez seja preciso adicionar discos para aumentar o armazenamento legado.

Para adicionar armazenamento em disco:

1. No Console do Administrador, selecione **Gerenciamento** > **Armazenamento em Disco** > **Adicionar**.

    ![Adicionar caixa de diálogo de Armazenamento em Disco](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. Na caixa de diálogo **Adicionar Armazenamento em Disco**, selecione **Adicionar discos**.

5. Na lista de discos disponíveis, selecione os discos que você deseja adicionar, selecione **Adicionar**e, em seguida, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas
Depois de instalar o Servidor de Backup, saiba como preparar seu servidor, ou começar a proteger uma carga de trabalho.

- [Preparar as cargas de trabalho do Servidor de Backup](backup-azure-microsoft-azure-backup.md)
- [Usar o Servidor de Backup para fazer backup de um Servidor do VMware](backup-azure-backup-server-vmware.md)
- [Usar o Servidor de Backup para fazer backup de um SQL Server](backup-azure-sql-mabs.md)
