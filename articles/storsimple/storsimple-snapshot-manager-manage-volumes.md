---
title: StorSimple Snapshot Manager e volumes | Microsoft Docs
description: Descreve como usar o snap-in do MMC do StorSimple Snapshot Manager para exibir e gerenciar volumes, bem como configurar backups.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: alkohli
ms.openlocfilehash: 309fa85d0a4d877522a89dd8f1e6e71fb2074744
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96022931"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Usar o StorSimple Snapshot Manager para exibir e gerenciar volumes
## <a name="overview"></a>Visão geral
Você pode usar o nó **Volumes** do StorSimple Snapshot Manager (no painel **Escopo**) para selecionar volumes e exibir informações sobre eles. Os volumes são apresentados como unidades que correspondem aos volumes montados pelo host. O nó **Volumes** mostra os volumes locais e os tipos de volumes que têm suporte do StorSimple, incluindo os volumes descobertos por meio do uso do iSCSI e de um dispositivo. 

Para saber mais sobre os volumes compatíveis, vá até [Suporte para vários tipos de volume](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Lista de Volumes no painel de Resultados](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

O nó **Volumes** também permite examinar novamente ou excluir volumes após o StorSimple Snapshot Manager descobri-los. 

Este tutorial explica como montar, inicializar e formatar volumes e usar o StorSimple Snapshot Manager para:

* Exibir informações sobre volumes 
* Excluir volumes
* Examinar volumes novamente 
* Configurar um volume básico e fazer seu backup
* Configurar um volume dinâmico espelhado e fazer seu backup

> [!NOTE]
> Todas as ações de nó **Volume** também estão disponíveis no painel **Ações**.
> 
> 

## <a name="mount-volumes"></a>Montar volumes
Use o procedimento a seguir para montar, inicializar e formatar volumes do StorSimple. Este procedimento usa o Gerenciamento de Disco, um utilitário do sistema para gerenciar discos rígidos e os volumes ou as partições correspondentes. Para saber mais sobre o Gerenciamento de Disco, vá até [Gerenciamento de Disco](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) no site do Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Para montar volumes
1. No computador host, inicie o Microsoft iSCSI.
2. Forneça um dos endereços IP da interface como o portal de destino ou endereço IP de descoberta e conecte-se ao dispositivo. Após o dispositivo ser conectado, os volumes ficarão acessíveis para o seu sistema Windows. Para saber mais sobre o uso do iniciador iSCSI da Microsoft, acesse a seção “Conectando a um dispositivo de destino iSCSI”, em [Instalando e configurando o iniciador iSCSI da Microsoft][1].
3. Use qualquer uma das opções a seguir para iniciar o Gerenciamento de Disco:
   
   * Digite Diskmgmt.msc na caixa **Executar** .
   * Inicie o Gerenciador de Servidores, expanda o nó **Armazenamento** e, em seguida, selecione **Gerenciamento de Disco**.
   * Inicie as **Ferramentas Administrativas**, expanda o nó **Gerenciamento do Computador** e selecione **Gerenciamento de Disco**. 
     
     > [!NOTE]
     > Você precisa usar privilégios de administrador para executar o Gerenciamento de Disco.
     > 
     > 
4. Coloque os volumes online:
   
   1. No Gerenciamento de Disco, clique com o botão direito do mouse em qualquer volume marcado **Offline**.
   2. Clique em **Reativar Disco**. O disco deve ser marcado **Online** depois que o disco for reativado.
5. Inicializar os volumes:
   
   1. Clique com botão direito nos volumes descobertos.
   2. No menu, selecione **Inicializar Disco**.
   3. Na caixa de diálogo **Inicializar Disco**, selecione os discos que você gostaria de inicializar e, em seguida, clique em **OK**.
6. Formate os volumes simples:
   
   1. Clique com o botão direito em um volume que você deseja formatar.
   2. No menu, selecione **Novo Volume Simples**.
   3. Use o assistente de Novo Volume Simples para formatar o volume:
      
      * Especifique o tamanho do volume.
      * Forneça uma letra da unidade.
      * Selecione o sistema de arquivos NTFS.
      * Especifique um tamanho de unidade de alocação com 64 KB.
      * Realize uma formatação rápida.
7. Formate os volumes com várias partições. Para obter instruções, vá até a seção "Partições e Volumes" em [Implementando o Gerenciamento de Disco](/previous-versions/tn-archive/dd163556(v=technet.10)).

## <a name="view-information-about-your-volumes"></a>Exibir informações sobre os volumes
Use o procedimento a seguir para exibir informações sobre volumes locais e do Azure StorSimple.

#### <a name="to-view-volume-information"></a>Para exibir informações de volume
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager. 
2. No painel **Escopo**, clique no nó **Volumes**. Uma lista de volumes locais e montados, incluindo todos os volumes do Azure StorSimple, aparece no painel **Resultados** . As colunas no painel de **resultados** são configuráveis. (Clique com o botão direito do mouse no nó **volumes** , selecione **Exibir** e, em seguida, selecione **Adicionar/remover colunas**.)
   
    ![Configurar as colunas](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Coluna de resultados | Descrição |
   |:--- |:--- |
   |  Nome |A coluna **Nome** contém a letra da unidade atribuída a cada volume descoberto. |
   |  Dispositivo |A coluna **Dispositivo** contém o endereço IP do dispositivo conectado ao computador host. |
   |  Nome do Volume do Dispositivo |A coluna **Nome do Volume do Dispositivo** contém o nome do volume do dispositivo ao qual o volume selecionado pertence. Esse é o nome do volume definido no portal do Azure para o volume específico. |
   |  Caminhos de acesso |A coluna **Caminhos de Acesso** exibe o caminho de acesso ao volume. Esta é a letra da unidade ou ponto de montagem no qual o volume está acessível para o computador host. |

## <a name="delete-a-volume"></a>Excluir um volume
Use o procedimento a seguir para excluir um volume do StorSimple Snapshot Manager.

> [!NOTE]
> Você não poderá excluir um volume se ele fizer parte de um grupo de volumes. (A opção Excluir não está disponível para volumes que são membros de um grupo de volumes.) Você deve excluir o grupo de volumes inteiro para excluir o volume.

#### <a name="to-delete-a-volume"></a>Para excluir um volume
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, clique no nó **Volumes**. 
3. No painel **Resultados**, clique com o botão direito do mouse no volume que você deseja excluir.
4. No menu, clique em **Excluir**. 
   
    ![Excluir um volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. A caixa de diálogo **Excluir Volume** aparece. Digite **Confirmar** na caixa de texto e, em seguida, clique em **OK**.
6. Por padrão, o StorSimple Snapshot Manager faz backup de um volume antes de excluí-lo. Essa precaução pode proteger você contra perda de dados se a exclusão tiver sido involuntária. O StorSimple Snapshot Manager exibe uma mensagem de andamento do **Instantâneo Automático** enquanto faz backup do volume. 
   
    ![Mensagem de instantâneo automático](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Examinar volumes novamente
Use o procedimento a seguir para examinar novamente os volumes conectados ao StorSimple Snapshot Manager.

#### <a name="to-rescan-the-volumes"></a>Para examinar novamente os volumes
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, clique com o botão direito do mouse em **Volumes** e, em seguida, clique em **Examinar novamente volumes**.
   
    ![Examinar volumes novamente](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Este procedimento sincroniza a lista de volumes com o StorSimple Snapshot Manager. Quaisquer alterações, como novos volumes ou volumes excluídos, serão refletidas nos resultados.

## <a name="configure-and-back-up-a-basic-volume"></a>Configurar e fazer backup de um volume básico
Use o procedimento a seguir para configurar um backup de um volume básico e, em seguida, inicie um backup imediatamente ou crie uma política para backups agendados.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

* Certifique-se de que o computador host e o dispositivo StorSimple estejam configurados corretamente. Para saber mais, vá até [Implantar seu dispositivo StorSimple local](./storsimple-8000-deployment-walkthrough-u2.md).
* Instale e configure o StorSimple Snapshot Manager. Para obter mais informações, acesse [implantar o StorSimple snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Para configurar o backup de um volume básico
1. Crie um volume básico no dispositivo StorSimple.
2. Monte, inicialize e formate o volume, como descrito em [Montar volumes](#mount-volumes). 
3. Clique no ícone do StorSimple Snapshot Manager na área de trabalho. A janela do StorSimple Snapshot Manager aparece. 
4. No painel de escopo , clique com o botão direito do mouse no nó **Volumes** e, em seguida, selecione **Verificar os volumes novamente**. Quando o exame for concluído, uma lista de volumes deve aparecer no painel **Resultados**. 
5. No painel **Resultados**, clique com o botão direito do mouse no volume e, em seguida, selecione **Criar Grupo de Volume**. 
   
    ![Criar Grupo de Volumes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. Na caixa de diálogo **Criar Grupo de Volume**, digite um nome para o grupo de volumes, atribua o volume dinâmico espelhado para este grupo e, em seguida, clique em **OK**.
7. No painel **Escopo**, expanda o nó **Grupos de volume**. O novo grupo de volumes deve aparecer no nó **grupos de volumes** . 
8. Clique com o botão direito no nome do grupo de volumes.
   
   * Para iniciar um trabalho de backup interativo (sob demanda), clique em **Fazer Backup**. 
   * Para programar um backup automático, clique em **Criar política de backup**. Na página Geral , selecione um grupo de volumes na lista. Na página **Agenda**, insira os detalhes da agenda. Quando terminar, clique em **OK**. 
9. Para confirmar que a tarefa de backup já começou, expanda o nó em **Trabalhos** no painel de **Escopo** e, em seguida, clique no nó **Executar**. A lista de tarefas em execução no momento aparece no painel de **Resultados**. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Configurar e fazer backup de um volume espelhado dinâmico
Conclua as seguintes etapas para configurar o backup de um volume espelhado dinâmico:

* Etapa 1: usar o Gerenciamento de Disco para criar um volume espelhado dinâmico. 
* Etapa 2: usar o StorSimple Snapshot Manager para configurar o backup.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

* Certifique-se de que o computador host e o dispositivo StorSimple estejam configurados corretamente. Para saber mais, vá até [Implantar seu dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md).
* Instale e configure o StorSimple Snapshot Manager. Para obter mais informações, acesse [implantar o StorSimple snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Configure dois volumes no dispositivo StorSimple. (Nos exemplos, os volumes disponíveis são **Disco 1** e **Disco 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Etapa 1: Usar o Gerenciamento de Disco para criar um volume espelhado dinâmico
O Gerenciamento de Disco é um utilitário do sistema para gerenciar discos rígidos e os volumes ou partições que eles contêm. Para saber mais sobre o Gerenciamento de Disco, vá até [Gerenciamento de Disco](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) no site do Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Para criar um volume espelhado dinâmico
1. Use qualquer uma das opções a seguir para iniciar o Gerenciamento de Disco: 
   
   * Abra a caixa **Executar**, digite **Diskmgmt.msc** e pressione Enter.
   * Inicie o Gerenciador de Servidores, expanda o nó **Armazenamento** e, em seguida, selecione **Gerenciamento de Disco**. 
   * Inicie as **Ferramentas Administrativas**, expanda o nó **Gerenciamento do Computador** e selecione **Gerenciamento de Disco**. 
2. Certifique-se de que você tenha dois volumes disponíveis no dispositivo StorSimple. (No exemplo, os volumes disponíveis são **Disco 1** e **Disco 2**.) 
3. Na janela Gerenciamento de Disco, na coluna direita do painel inferior, clique com o botão direito do mouse em **Disco 1** e selecione **Novo volume espelhado**. 
   
    ![Novo Volume Espelhado](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Na página do assistente **Novo volume espelhado**, clique em **Avançar**.
5. Na página **Selecionar Discos**, selecione **Disco 2** no painel **Selecionado**, clique em **Adicionar** e, em seguida, clique em **Avançar**. 
6. Na página **Atribuir uma Letra da Unidade ou Caminho**, aceite os padrões e clique em **Avançar**. 
7. Na página **Formatar volume**, na caixa **Tamanho da unidade de alocação**, selecione **64 K**. Marque a caixa de seleção **Executar uma formatação rápida** e, em seguida, clique em **Avançar**. 
8. Na página **Concluindo o novo volume espelhado**, revise as suas configurações e, em seguida, clique em **Concluir**. 
9. Será exibida uma mensagem indicando que o disco básico será convertido em um disco dinâmico. Clique em **Sim**.
   
    ![Mensagem de conversão de disco dinâmico](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. No gerenciamento de disco, verifique se os discos 1 e 2 são mostrados como volumes dinâmicos espelhados. (**Dinâmico** aparece na coluna de status e a cor da barra de capacidade muda para vermelho, indicando um volume espelhado.) 
    
    ![Discos dinâmicos espelhados do Gerenciamento de Disco](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Etapa 2: Usar o StorSimple Snapshot Manager para configurar o backup
Use o procedimento a seguir para configurar um volume espelhado dinâmico, em seguida, inicie um backup imediatamente ou crie uma política para backups agendados.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Para configurar o backup de um volume espelhado dinâmico
1. Clique no ícone do StorSimple Snapshot Manager na área de trabalho. A janela do StorSimple Snapshot Manager aparece. 
2. No painel **Escopo**, clique com o botão direito do mouse no nó **Volumes** e selecione **Examinar volumes novamente**. Quando o exame for concluído, uma lista de volumes deve aparecer no painel **Resultados**. O volume dinâmico espelhado é listado como um único volume. 
3. No painel **Resultados**, clique com o botão direito do mouse no volume dinâmico espelhado e, em seguida, clique em **Criar grupo de volume**. 
4. Na caixa de diálogo **Criar grupo de volume**, digite um nome para o grupo de volumes, atribua o volume dinâmico espelhado para este grupo e, em seguida, clique em **OK**. 
5. No painel **Escopo**, expanda o nó **Grupos de volume**. O novo grupo de volumes deve aparecer no nó **Grupos de Volumes**. 
6. Clique com o botão direito no nome do grupo de volumes. 
   
   * Para iniciar um trabalho de backup interativo (sob demanda), clique em **Fazer Backup**. 
   * Para programar um backup automático, clique em **Criar política de backup**. Na página **Geral**, selecione o grupo de volume na lista. Na página **Agenda**, insira os detalhes da agenda. Quando terminar, clique em **OK**. 
7. Você pode monitorar o trabalho de backup enquanto ele é executado. No painel **Escopo**, expanda o nó **Trabalhos** e, em seguida, clique em **Em execução**. Os detalhes do trabalho aparecem no painel **Resultados**. Quando o trabalho de backup é concluído, os detalhes são transferidos para a lista de trabalhos **Últimas 24 horas**. 

## <a name="next-steps"></a>Próximas etapas
* Saiba como [usar o StorSimple Snapshot Manager para administrar sua solução do StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [usar o StorSimple Snapshot Manager para criar e gerenciar grupos de volumes](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)