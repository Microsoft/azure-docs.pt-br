---
title: Grupos de volume do StorSimple Snapshot Manager | Microsoft Docs
description: Descreve como usar o snap-in StorSimple Snapshot Manager MMC para criar e gerenciar grupos de volumes.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 6067a88cd42d29c3d2f4b74580095424de77561e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Usar o StorSimple Snapshot Manager para criar e gerenciar grupos de volumes
## <a name="overview"></a>Visão geral
Você pode usar o nó **Grupos de Volume** no painel **Escopo** para atribuir volumes a grupos de volumes, exibir informações sobre um grupo de volumes, agendar backups e editar grupos de volumes.

Grupos de volumes são conjuntos de volumes relacionados usados para garantir que os backups sejam consistentes com o aplicativo. Para saber mais, consulte [Volumes e grupos de volumes](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) e [Integração com o Serviço de Cópias de Sombra de Volume do Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Todos os volumes em um grupo de volumes devem vir de um único provedor de serviço de nuvem.
> * Quando configurar grupos de volumes, não misture volumes compartilhados de cluster (CSVs) e volumes não CSV no mesmo grupo de volumes. O StorSimple Snapshot Manager não dá suporte a uma combinação de CSVs e não CSVs no mesmo instantâneo.

![Nó Grupos de volumes](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Figura 1: Nó Grupos de Volumes do StorSimple Snapshot Manager** 

Este tutorial explica como você pode usar o StorSimple Snapshot Manager para:

* Exibir informações sobre os grupos de volumes
* Criar um grupo de volumes
* Fazer backup de um grupo de volumes
* Editar um grupo de volumes
* Excluir um grupo de volumes

Todas essas ações também estão disponíveis no painel **Ações** .

## <a name="view-volume-groups"></a>Exibir grupos de volumes
Se você clicar no nó **Grupos de Volumes**, o painel **Resultados** mostrará as informações a seguir sobre cada grupo de volumes, dependendo das colunas selecionadas. (As colunas no painel **Resultados** são configuráveis. Clique com o botão direito do mouse no nó **Volumes**, selecione **Exibir** e selecione **Adicionar/Remover Colunas**.)

| Coluna de resultados | Descrição |
|:--- |:--- |
| Nome |A coluna **Nome** contém o nome do grupo de volumes. |
| Aplicativo |A coluna **Aplicativos** mostra o número de gravadores VSS atualmente instalados e em execução no host do Windows. |
| Selecionado |A coluna **Selecionados** mostra o número de volumes contidos no grupo de volumes. Um zero (0) indica que nenhum aplicativo está associado aos volumes no grupo de volumes. |
| Importado |A coluna **Importados** mostra o número de volumes importados. Quando definida como **True**, essa coluna indica que um grupo de volumes foi importado do portal do Azure e não foi criado no StorSimple Snapshot Manager. |

> [!NOTE]
> Os grupos de volumes do StorSimple Snapshot Manager também são exibidos na guia **Políticas de Backup** no portal do Azure.
> 
> 

## <a name="create-a-volume-group"></a>Criar um grupo de volumes
Use o procedimento a seguir para criar um grupo de volumes.

#### <a name="to-create-a-volume-group"></a>Para criar um grupo de volumes
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, clique com o botão direito do mouse em **Grupos de Volume** e clique em **Criar Grupo de Volumes**.
   
    ![Criar Grupo de Volumes](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    A caixa de diálogo **Criar um grupo de volumes** é exibida.
   
    ![Criar uma caixa de diálogo de grupo de volumes](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Insira as seguintes informações:
   
   1. Na caixa **Nome** , digite um nome exclusivo para o novo grupo de volumes.
   2. Na caixa **Aplicativos** , selecione os aplicativos associados aos volumes que você adicionará ao grupo de volumes.
      
       A caixa **Aplicativos** lista apenas os aplicativos que usam volumes do StorSimple e que contêm gravadores VSS habilitados. Um gravador VSS será habilitado apenas se todos os volumes de que ele estiver ciente forem volumes do StorSimple. Se a caixa Aplicativos estiver vazia, nenhum aplicativo que usa volumes do Azure StorSimple e oferece suporte a gravadores VSS está instalado. (Atualmente, o Azure StorSimple dá suporte ao Microsoft Exchange e ao SQL Server.) Para obter mais informações sobre os gravadores VSS, veja [Integração com o Serviço de Cópias de Sombra de Volume do Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Se você selecionar um aplicativo, todos os volumes associados a ele serão selecionados automaticamente. Por outro lado, se você selecionar volumes associados a um aplicativo específico, o aplicativo será selecionado automaticamente na caixa **Aplicativos** . 
   3. Na caixa **Volumes** , selecione os volumes do StorSimple a serem adicionados ao grupo de volumes. 
      
      * É possível incluir volumes com partições simples ou múltiplas. (Volumes com partições múltiplas podem ser discos dinâmicos ou básicos com várias partições.) Um volume que contém partições múltiplas é tratado como uma única unidade. Consequentemente, se você adicionar apenas uma das partições a um grupo de volumes, todas as outras partições serão adicionadas automaticamente ao grupo de volumes ao mesmo tempo. Após você adicionar um volume com partições múltiplas a um grupo de volumes, o volume com partições múltiplas continuará sendo tratado como uma única unidade.
      * Você pode criar grupos de volumes vazios não atribuindo nenhum volume a eles. 
      * Não misture volumes compartilhados de cluster (CSVs) e volumes não CSV no mesmo grupo de volumes. O StorSimple Snapshot Manager não dá suporte a uma combinação de volumes CSV e não CSV no mesmo instantâneo.
4. Clique em **OK** para salvar o grupo de volumes.

## <a name="back-up-a-volume-group"></a>Fazer backup de um grupo de volumes
Use o procedimento a seguir para fazer backup de um grupo de volumes.

#### <a name="to-back-up-a-volume-group"></a>Para fazer backup de um grupo de volumes
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, expanda o nó **Grupos de Volumes**, clique com o botão direito do mouse no nome de um grupo de volumes e clique em **Fazer Backup**.
   
    ![Fazer imediatamente o backup do grupo de volumes](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. Na caixa de diálogo **Fazer Backup**, selecione **Instantâneo Local** ou **Instantâneo em Nuvem** e clique em **Criar**.
   
    ![Caixa de diálogo Fazer backup](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Para confirmar que o backup está em execução, expanda o nó **Trabalhos** e clique em **Em execução**. O backup deve estar listado.
5. Para exibir o instantâneo concluído, expanda o nó **Catálogo de Backup**, expanda o nome do grupo de volumes e clique em **Instantâneo Local** ou em **Instantâneo em Nuvem**. O backup será listado se tiver sido concluído com êxito.

## <a name="edit-a-volume-group"></a>Editar um grupo de volumes
Use o procedimento a seguir para editar um grupo de volumes.

#### <a name="to-edit-a-volume-group"></a>Para editar um grupo de volumes
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, expanda o nó **Grupos de Volumes**, clique com o botão direito do mouse no nome de um grupo de volumes e clique em **Editar**.
3. A caixa de diálogo **Criar um grupo de volumes** é exibida. Você pode alterar as entradas **Nome**, **Aplicativos** e **Volumes**.
4. Clique em **OK** para salvar as alterações.

## <a name="delete-a-volume-group"></a>Excluir um grupo de volumes
Use o procedimento a seguir para excluir um grupo de volumes. 

> [!WARNING]
> Isso também exclui todos os backups associados ao grupo de volumes.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Para excluir um grupo de volumes
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, expanda o nó **Grupos de Volumes**, clique com o botão direito do mouse no nome de um grupo de volumes e clique em **Excluir**.
3. A caixa de diálogo **Excluir Grupo de Volumes** é exibida. Digite **Confirmar** na caixa de texto e clique em **OK**.
   
    O grupo de volumes excluído desaparece da lista no painel **Resultados** e todos os backups associados ao grupo de volumes são excluídos do catálogo de backups.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [usar o StorSimple Snapshot Manager para administrar sua solução do StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [usar o StorSimple Snapshot Manager para criar e gerenciar políticas de backup](storsimple-snapshot-manager-manage-backup-policies.md).

