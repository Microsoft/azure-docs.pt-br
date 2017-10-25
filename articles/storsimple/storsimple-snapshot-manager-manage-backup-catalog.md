---
title: "Catálogo de backup do StorSimple Snapshot Manager | Microsoft Docs"
description: "Descreve como usar o snap-in StorSimple Snapshot Manager MMC para exibir e gerenciar o catálogo de backup."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: b97753e6f1b67e3c8d247281c5e5208033a56eca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Usar o StorSimple Snapshot Manager para gerenciar o catálogo de backup

## <a name="overview"></a>Visão geral
A principal função do StorSimple Snapshot Manager é permitir a criação de cópias de backup consistentes com o aplicativo de volumes do StorSimple na forma de instantâneos. Os instantâneos são listados em um arquivo XML chamado de *catálogo de backups*. O catálogo de backups organiza os instantâneos por grupo de volumes e, depois, por instantâneo local ou instantâneo de nuvem.

Este tutorial descreve como você pode usar o nó **Catálogo de Backups** para realizar as seguintes tarefas:

* Restaurar um volume
* Clonar um volume ou grupo de volumes
* Excluir um conjunto de backups
* Recuperar um arquivo
* Restaurar o banco de dados do StorSimple Snapshot Manager

Você pode exibir o catálogo de backup expandindo o nó **Catálogo de Backup** no painel **Escopo** e expandindo o grupo de volumes.

* Se você clicar no nome do grupo de volumes, o painel **Resultados** mostra o número de instantâneos locais e instantâneos de nuvem disponíveis para o grupo de volumes. 
* Se você clicar em **Instantâneo Local** ou **Instantâneo de Nuvem**, o painel **Resultados** mostra as seguintes informações sobre cada instantâneo de backup (dependendo de suas configurações de **Exibição**):
  
  * **Nome** – a hora em que o instantâneo foi tirado.
  * **Tipo** – se se trata de um instantâneo local ou de nuvem.
  * **Proprietário** – o proprietário do conteúdo. 
  * **Disponível** – se o instantâneo está disponível no momento. **Verdadeiro** indica que o instantâneo está disponível e pode ser restaurado. **Falso** indica que o ele não está mais disponível. 
  * **Importado** – se o backup foi importado. **Verdadeiro** indica que o backup foi importado do serviço Gerenciador de Dispositivos StorSimple no momento em que o dispositivo foi configurado no StorSimple Snapshot Manager. **Falso** indica que não foi importado, mas foi criado pelo StorSimple Snapshot Manager. (Você pode facilmente identificar um grupo de volumes importado porque é adicionado um sufixo que identifica o dispositivo do qual ele foi importado.)
    
    ![Catálogo de backup](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Se você expandir **Instantâneo Local** ou **Instantâneo de Nuvem** e clicar no nome de um instantâneo individual, o painel **Resultados** mostra as seguintes informações sobre o instantâneo selecionado:
  
  * **Nome** – o volume identificado pela letra da unidade. 
  * **Nome do Local** – o nome do local da unidade (se disponível). 
  * **Dispositivo** – o nome do dispositivo no qual reside o volume. 
  * **Disponível** – se o instantâneo está disponível no momento. **Verdadeiro** indica que o instantâneo está disponível e pode ser restaurado. **Falso** indica que o ele não está mais disponível. 

## <a name="restore-a-volume"></a>Restaurar um volume
Use o procedimento a seguir para restaurar um volume do backup.

#### <a name="prerequisites"></a>Pré-requisitos
Se ainda não tiver feito isso, crie um volume e grupo de volumes e, em seguida, exclua o volume. Por padrão, o StorSimple Snapshot Manager faz o backup de um volume antes de permitir que ele seja eliminado. Essa precaução pode evitar a perda de dados se o volume for excluído acidentalmente ou se os dados precisam ser recuperados por qualquer motivo. 

O StorSimple Snapshot Manager exibe a mensagem a seguir enquanto cria o backup de precaução.

![Mensagem de instantâneo automático](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Você não pode excluir um volume que faz parte de um grupo de volumes. A opção excluir não está disponível. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Para restaurar um volume
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager. 
2. No painel **Escopo**, expanda o nó **Catálogo de Backup**, expanda um grupo de volumes e clique em **Instantâneos Locais** ou **Instantâneos de Nuvem**. Uma lista de instantâneos de backup aparece no painel **Resultados** .
3. Localize o backup que você deseja restaurar, clique com botão direito e clique em **Restaurar**.
   
    ![Restaurar o catálogo de backups](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Na página de confirmação, examine os detalhes, digite **Confirmar** e clique em **OK**. O StorSimple Snapshot Manager usa o backup para restaurar o volume.
   
    ![Mensagem de confirmação de restauração](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Você pode monitorar a ação de restauração enquanto ela é executada. No painel **Escopo**, expanda o nó **Trabalhos** e clique em **Executando**. Os detalhes do trabalho aparecem no painel **Resultados** . Quando o trabalho de restauração é concluído, os detalhes do trabalho são transferidos para a lista **Últimas 24 horas** .

## <a name="clone-a-volume-or-volume-group"></a>Clonar um volume ou grupo de volumes
Use o procedimento a seguir para criar uma duplicata (clone) de um volume ou grupo de volumes.

#### <a name="to-clone-a-volume-or-volume-group"></a>Para clonar um volume ou grupo de volumes
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, expanda o nó **Catálogo de Backup**, expanda um grupo de volumes e clique em **Instantâneos de Nuvem**. Uma lista de backups aparece no painel **Resultados** .
3. Encontre o volume ou grupo de volumes que você deseja clonar, clique com o botão direito no nome do volume ou grupo de volume e clique em **Clonar**. A caixa de diálogo **Clonar Instantâneo em Nuvem** é exibida.
   
    ![Clonar um instantâneo de nuvem](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Preencha a caixa de diálogo **Clonar Instantâneo em Nuvem** da seguinte maneira: 
   
   1. Na caixa de texto **Nome** , digite um nome para o volume clonado. Esse nome aparecerá no nó **Volumes** . 
   2. (Opcional) Selecione a **Unidade**e selecione uma letra da unidade na lista suspensa.
   3. (Opcional) Selecione **Pasta (NTFS)**e digite um caminho de pasta ou clique em Procurar e selecione um local para a pasta. 
   4. Clique em **Criar**.
5. Quando o processo de clonagem for concluído, você precisa inicializar o volume clonado. Inicie o Gerenciador do Servidor e inicie o gerenciamento de disco. Para obter instruções detalhadas, consulte [Montar volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Depois que é inicializado, o volume é listado no nó **Volumes** no painel **Escopo**. Se você não vir o volume listado, atualize a lista de volumes (clique com o botão direito do mouse no nó **Volumes** e clique em **Atualizar**).

## <a name="delete-a-backup"></a>Excluir um conjunto de backups
Use o procedimento a seguir para excluir um instantâneo do catálogo de backups. 

> [!NOTE]
> Excluir um instantâneo exclui os dados dos backups associados a ele. No entanto, o processo de limpeza de dados da nuvem pode levar algum tempo.<br>


#### <a name="to-delete-a-backup"></a>Para excluir um backup
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, expanda o nó **Catálogo de Backup**, expanda um grupo de volumes e clique em **Instantâneos Locais** ou **Instantâneos de Nuvem**. Uma lista de instantâneos aparece no painel **Resultados** .
3. Clique no instantâneo que deseja excluir e clique em **Excluir**.
4. Quando a mensagem de confirmação aparecer, clique em **OK**.

## <a name="recover-a-file"></a>Recuperar um arquivo
Se um arquivo for excluído acidentalmente de um volume, você poderá recuperá-lo obtendo um instantâneo que antecipa a exclusão, usando o instantâneo para criar um clone do volume e copiando o arquivo do volume clonado para o volume original.

#### <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de ter um backup atual do grupo de volumes. Em seguida, exclua um arquivo armazenado em um dos volumes no grupo de volumes. Por fim, use as etapas a seguir para restaurar o arquivo excluído do backup. 

#### <a name="to-recover-a-deleted-file"></a>Para recuperar um arquivo excluído
1. Clique no ícone do StorSimple Snapshot Manager na área de trabalho. A janela de console do StorSimple Snapshot Manager aparece. 
2. No painel **Escopo**, expanda o nó **Catálogo de Backup** e navegue para um instantâneo que contém o arquivo excluído. Normalmente, você deve selecionar um instantâneo que foi criado logo antes da exclusão.
3. Localize o volume que você deseja clonar, clique com botão direito e clique em **Clonar**. A caixa de diálogo **Clonar Instantâneo em Nuvem** é exibida.
   
    ![Clonar um instantâneo de nuvem](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Preencha a caixa de diálogo **Clonar Instantâneo em Nuvem** da seguinte maneira: 
   
   1. Na caixa de texto **Nome** , digite um nome para o volume clonado. Esse nome aparecerá no nó **Volumes** . 
   2. (Opcional) Selecione a **Unidade**e selecione uma letra da unidade na lista suspensa. 
   3. (Opcional) Selecione **Pasta (NTFS)** e digite um caminho de pasta ou clique em **Procurar** e selecione um local para a pasta. 
   4. Clique em **Criar**. 
5. Quando o processo de clonagem for concluído, você precisa inicializar o volume clonado. Inicie o Gerenciador do Servidor e inicie o gerenciamento de disco. Para obter instruções detalhadas, consulte [Montar volumes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Depois que é inicializado, o volume é listado no nó **Volumes** no painel **Escopo**. 
   
    Se você não vir o volume listado, atualize a lista de volumes (clique com o botão direito do mouse no nó **Volumes** e clique em **Atualizar**).
6. Abra a pasta NTFS que contém o volume clonado, expanda o nó **Volumes** e abra o volume clonado. Localize o arquivo que deseja recuperar e copie-o para o volume primário.
7. Depois de restaurar o arquivo, você pode excluir a pasta NTFS que contém o volume clonado.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Restaurar o banco de dados do StorSimple Snapshot Manager
Você deve fazer backup regularmente do banco de dados do StorSimple Snapshot Manager no computador host. Se ocorrer um desastre ou o computador host falhar por algum motivo, você poderá restaurá-lo do backup. Criar o backup do banco de dados é um processo manual.

#### <a name="to-back-up-and-restore-the-database"></a>Para fazer backup e restaurar o banco de dados
1. Parar o Serviço de Gerenciamento do Microsoft StorSimple:
   
   1. Inicie o Gerenciador do Servidor.
   2. No Painel do Gerenciador do Servidor, no menu **Ferramentas**, selecione **Serviços**.
   3. Na janela **Serviços**, selecione o **Serviço de Gerenciamento Microsoft StorSimple**.
   4. No painel direito, em **Serviço de Gerenciamento Microsoft StorSimple**, clique em **Parar o serviço**.
2. No computador host, vá para C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData é uma pasta oculta.
   > 
   > 
3. Localize o arquivo XML do catálogo, copie o arquivo e armazene a cópia em um local seguro ou na nuvem. Se o host falhar, você pode usar esse arquivo de backup para ajudar a recuperar as políticas de backup que você criou no StorSimple Snapshot Manager.
   
    ![Arquivo de catálogo de backups do Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Reinicie o Serviço de Gerenciamento do Microsoft StorSimple: 
   
   1. No Painel do Gerenciador do Servidor, no menu **Ferramentas**, selecione **Serviços**.
   2. Na janela **Serviços**, selecione o **Serviço de Gerenciamento Microsoft StorSimple**.
   3. No painel direito, em **Serviço de Gerenciamento Microsoft StorSimple**, clique em **Reiniciar o serviço**.
5. No computador host, vá para C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Exclua o arquivo XML do catálogo e substitua pela versão de backup que você criou. 
7. Clique no ícone do StorSimple Snapshot Manager na área de trabalho para iniciar o StorSimple Snapshot Manager. 

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [como usar o StorSimple Snapshot Manager para administrar sua solução do StorSimple](storsimple-snapshot-manager-admin.md).
* [Saiba mais sobre fluxos de trabalho e tarefas do StorSimple Snapshot Manager](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

