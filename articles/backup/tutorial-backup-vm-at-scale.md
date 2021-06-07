---
title: Tutorial – Fazer backup de várias máquinas virtuais do Azure
description: Neste tutorial, saiba como criar um cofre dos Serviços de Recuperação, definir uma política de backup e fazer backup de várias máquinas virtuais simultaneamente.
ms.date: 07/26/2020
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a9517ffc1e37d50f7c0e57b9ed53fb8bcf55fd70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89180568"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Use o portal do Azure para fazer backup de várias máquinas virtuais

Ao fazer backup dos dados no Azure, você os armazena em um recurso do Azure chamado Cofre dos Serviços de Recuperação. O recurso de Cofre dos Serviços de Recuperação está disponível no menu Configurações da maioria dos serviços do Azure. O benefício de ter o cofre dos Serviços de Recuperação integrado ao menu Configurações da maioria dos serviços do Azure facilita o backup de dados. No entanto, trabalhar individualmente com cada banco de dados ou com cada máquina virtual na sua empresa é entediante. E se você quiser fazer backup dos dados de todas as máquinas virtuais de um departamento ou de um local? É fácil fazer backup de várias máquinas virtuais ao criar uma política de backup e aplicá-la às máquinas virtuais desejadas. Este tutorial explica como:

> [!div class="checklist"]
>
> * Criar um cofre dos Serviços de Recuperação
> * Definir uma política de backup
> * Aplicar a política de backup para proteger várias máquinas virtuais
> * Disparar um trabalho de backup sob demanda nas máquinas virtuais protegidas

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

O cofre dos Serviços de Recuperação contém os dados de backup e a política de backup aplicada às máquinas virtuais protegidas. O backup de máquinas virtuais é um processo local. Não é possível fazer backup de uma máquina virtual de uma localização para um cofre dos Serviços de Recuperação em outra localização. Portanto, para cada local do Azure que tenha máquinas virtuais que devam passar por backup, pelo menos um Cofre dos Serviços de Recuperação deverá existir nesse local.

1. No menu esquerdo, selecione **Todos os serviços**.

    ![Selecionar Todos os serviços](./media/tutorial-backup-vm-at-scale/click-all-services.png)

1. Na caixa de diálogo **Todos os serviços**, insira *Serviços de Recuperação*. A lista de recurso filtra de acordo com sua entrada. Na lista de recursos, selecione **Cofres dos Serviços de Recuperação**.

    ![Insira e escolha os cofres dos Serviços de Recuperação](./media/tutorial-backup-vm-at-scale/all-services.png)

    A lista de cofres de Serviços de Recuperação na assinatura aparecerá.

1. No painel **Cofres dos Serviços de Recuperação**, selecione **Adicionar**.

    ![Adicionar um cofre dos Serviços de Recuperação](./media/tutorial-backup-vm-at-scale/add-button-create-vault.png)

1. No menu Cofre dos Serviços de Recuperação,

    * Digite *myRecoveryServicesVault* em **Nome**.
    * A ID da assinatura atual aparecerá em **Assinatura**. Se você tiver assinaturas adicionais, poderá escolher outra assinatura para o novo cofre.
    * Em **Grupo de recursos**, selecione **Usar existente** e selecione *myResourceGroup*. Se *myResourceGroup* não existir, selecione **Criar Novo** e digite *myResourceGroup*.
    * No menu suspenso **Localização**, escolha *Europa Ocidental*.

    ![Valores do cofre dos Serviços de Recuperação](./media/tutorial-backup-vm-at-scale/review-and-create.png)

    Um Cofre dos Serviços de Recuperação deverá estar no mesmo local que as máquinas virtuais que estão sendo protegidas. Se você tiver máquinas virtuais em várias regiões, crie um cofre de Serviços de Recuperação em cada região. Este tutorial cria um cofre dos Serviços de Recuperação no *Oeste da Europa* porque este é local em que a *myVM* (a máquina virtual criada com o guia de início rápido) foi criada.

1. Quando você estiver pronto para criar o cofre dos Serviços de Recuperação, clique em **Criar**.

    ![Criar o cofre dos Serviços de Recuperação](./media/tutorial-backup-vm-at-scale/click-create-button.png)

1. Pode levar um tempo para criar o cofre dos Serviços de Recuperação. Monitore as notificações de status na área **Notificações** no canto superior direito do portal. Depois que o cofre tiver sido criado, ele aparecerá na lista de cofres dos Serviços de Recuperação. Se você não encontrar seu cofre, selecione **Atualizar**.

     ![Atualizar a lista de cofres de backup](./media/tutorial-backup-vm-at-scale/refresh-button.png)

Quando você cria um Cofre dos Serviços de Recuperação, por padrão, o cofre tem o armazenamento com redundância geográfica. Para fornecer resiliência de dados, o armazenamento com redundância geográfica replica os dados várias vezes em duas regiões do Azure.

## <a name="set-backup-policy-to-protect-vms"></a>Definir política de backup para proteger VMs

Após criar o Cofre dos Serviços de Recuperação, a próxima etapa é configurar o cofre para o tipo de dados e definir a política de backup. A política de backup é o agendamento de quando e com que frequência os pontos de recuperação serão feitos. A Política também inclui o período de retenção dos pontos de recuperação. Para este tutorial, vamos supor que a sua empresa é um complexo esportivo com um hotel, um estádio, restaurantes e concessões e você está protegendo os dados nas máquinas virtuais. As etapas a seguir criam uma política de backup para os dados financeiros.

1. Na lista de Cofres dos Serviços de Recuperação, selecione **myRecoveryServicesVault** para abrir seu painel.

   ![Abrir o menu Cenário](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

1. No menu do painel do cofre, selecione **Backup** para abrir o menu do Backup.

1. No menu Meta de Backup do menu suspenso **Onde sua carga de trabalho é executada**, escolha *Azure*. No menu suspenso **Do que você deseja fazer backup**, escolha *Máquina virtual* e selecione **Backup**.

    Essas ações preparam o Cofre dos Serviços de Recuperação para interagir com uma máquina virtual. Os Cofres dos Serviços de Recuperação têm uma política padrão que cria um ponto de restauração por dia e retém os pontos de restauração por 30 dias.

    ![Meta de backup](./media/tutorial-backup-vm-at-scale/backup-goal.png)

1. Para criar uma política, no menu Política de backup, no menu suspenso **Escolher política de backup**, selecione *Criar uma política*.

    ![Criar política](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

1. O painel **Política de backup** será aberto. Preencha os seguintes detalhes:
   * Para **Nome da Política**, digite *Finanças*. Insira as seguintes alterações na Política de Backup:
   * Para a **Frequência de backup**, defina o fuso horário para *Hora Central*. Como o complexo esportivo está localizado no Texas, o proprietário deseja que o horário definido seja o local. Deixe a frequência de backup definida como Diariamente às 03h30.
   * Para a **Retenção do ponto de backup diário**, defina o período para 90 dias.
   * Para a **Retenção do ponto de backup semanal**, use o ponto de restauração *Segunda-feira* e mantenha-o por 52 semanas.
   * Para a **Retenção do ponto de backup mensal**, use o ponto de restauração do Primeiro Domingo do mês e mantenha-o por 36 meses.
   * Desmarque a opção **Retenção do ponto de backup anual**. O líder de finanças não deseja manter dados por mais de 36 meses.
   * Selecione **OK** para criar a política de backup.

     ![Configurações de política de backup](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

     Depois de criar a política de backup, associe-a às máquinas virtuais.

1. Em **Máquinas virtuais**, selecione **Adicionar**.

     ![Adicionar máquinas virtuais](./media/tutorial-backup-vm-at-scale/add-virtual-machines.png)

1. O painel **Selecionar máquinas virtuais** será aberto. Selecione *myVM* e clique em **OK** para implantar a política de backup nas máquinas virtuais.

    Todas as máquinas virtuais que estão na mesma localização e ainda não estão associadas a uma política de backup, aparecerão. *myVMH1* e *myVMR1* são selecionados para serem associados à política de *Finanças*.

    ![Escolher as VMs a serem protegidas](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

1. Depois que as máquinas virtuais tiverem sido escolhidas, selecione **Habilitar Backup**.

    Quando terminar implantação, você receberá uma notificação de que ela foi concluída com êxito.

## <a name="initial-backup"></a>Backup inicial

Você habilitou o backup para os cofres dos Serviços de Recuperação, mas um backup inicial não foi criado. Uma prática recomendada para a recuperação de desastre é disparar o primeiro backup para que os seus dados sejam protegidos.

Para executar um trabalho de backup sob demanda:

1. No painel do cofre, selecione o número **3** em **Itens de Backup** para abrir o menu Itens de Backup.

    ![Itens de Backup](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    O menu **Itens de Backup** será aberto.

1. No menu **Itens de Backup**, selecione **Máquina Virtual do Azure** para abrir a lista das máquinas virtuais associadas ao cofre.

    ![Lista de máquinas virtuais](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

1. A lista **Itens de Backup** será aberta.

    ![Trabalho de backup iniciado](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

1. Na lista **Itens de Backup**, selecione reticências **...** para abrir o Menu de contexto.

1. No Menu de contexto, selecione **Fazer Backup Agora**.

    ![Menu de contexto – selecionar Fazer backup agora](./media/tutorial-backup-vm-at-scale/context-menu.png)

    O menu Fazer Backup Agora será aberto.

1. No menu Fazer Backup Agora, insira o último dia para reter o ponto de recuperação e selecione **OK**.

    ![Definir o último dia em que o ponto de recuperação Fazer Backup Agora será retido](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    As notificações de implantação informam que o trabalho de backup foi disparado, e que você pode monitorar o andamento do trabalho na página de Trabalhos de Backup. Dependendo do tamanho da sua máquina virtual, a criação do backup inicial poderá demorar um pouco.

    Quando o trabalho de backup inicial for concluído, você poderá ver seu status no menu Trabalho de backup. O trabalho de backup sob demanda criou o ponto de restauração inicial para *myVM*. Se você quiser fazer backup de outras máquinas virtuais, repita essas etapas em cada máquina virtual.

    ![Bloco dos Trabalhos de Backup](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se planejar continuar trabalhando com os tutoriais subsequentes, não limpe os recursos criados neste tutorial. Caso contrário, use as etapas a seguir para excluir todos os recursos criados por este tutorial no portal do Azure.

1. No painel **myRecoveryServicesVault**, selecione o número **3** em **Itens de Backup** para abrir o menu Itens de Backup.

    ![Abrir menu Itens de backup](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

1. No menu **Itens de Backup**, selecione **Máquina Virtual do Azure** para abrir a lista das máquinas virtuais associadas ao cofre.

    ![Lista de máquinas virtuais](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    A lista **Itens de Backup** será aberta.

1. Na lista **Itens de Backup**, selecione as reticências para abrir o Menu de contexto.

    ![No menu Itens de Backup, abra o Menu de contexto](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

1. No menu de contexto, selecione **Interromper backup** para abrir o menu Interromper Backup.

    ![Interromper o menu de backup](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

1. No menu **Interromper backup**, selecione o menu suspenso superior e escolha **Excluir Dados de Backup**.

1. Na caixa de diálogo **Digite o nome do item de Backup**, digite *myVM*.

1. Depois que o item de backup for verificado (uma marca de seleção será exibida), o botão **Interromper backup** será habilitado. Selecione **Interromper Backup** para interromper a política e excluir os pontos de restauração.

    ![Selecione Interromper backup para excluir o cofre](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

    >[!NOTE]
    >Os itens excluídos são retidos no estado de exclusão reversível por 14 dias. Somente depois desse período, o cofre poderá ser excluído. Para obter mais informações, confira [Excluir um cofre dos Serviços de Recuperação do Backup do Azure](backup-azure-delete-vault.md).

1. Quando não houver nenhum outro item no cofre, selecione **Excluir**.

    ![Selecione excluir](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    Depois que o cofre for excluído, você retornará para a lista de cofres dos Serviços de Recuperação.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou o portal do Azure para:

> [!div class="checklist"]
>
> * Criar um cofre dos Serviços de Recuperação
> * Definir o cofre para proteger as máquinas virtuais
> * Criar uma política personalizada de backup e retenção
> * Atribuir a política para proteger várias máquinas virtuais
> * Disparar um backup sob demanda para máquinas virtuais

Continue no próximo tutorial para restaurar uma máquina virtual do Azure de um disco.

> [!div class="nextstepaction"]
> [Restaurar VMs usando a CLI](./tutorial-restore-disk.md)
