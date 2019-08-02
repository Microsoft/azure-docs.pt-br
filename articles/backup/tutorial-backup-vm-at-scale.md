---
title: Fazer backup de máquinas virtuais do Azure em escala
description: Faça backup de várias máquinas virtuais do Azure simultaneamente
keywords: backup de máquina virtual; backup da máquina virtual, fazer backup de vm, fazer backup da vm, fazer backup de vm do Azure, backup e recuperação de desastre
author: dcurwin
manager: carmonm
ms.author: dacurwin
ms.date: 01/31/2019
ms.topic: tutorial
ms.service: backup
ms.custom: mvc
ms.openlocfilehash: fa9f13bf4f4e06973f7b9125897366ad53d06857
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688443"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Use o portal do Azure para fazer backup de várias máquinas virtuais

Ao fazer backup dos dados no Azure, você os armazena em um recurso do Azure chamado Cofre dos Serviços de Recuperação. O recurso de Cofre dos Serviços de Recuperação está disponível no menu Configurações da maioria dos serviços do Azure. O benefício de ter o cofre dos Serviços de Recuperação integrado ao menu Configurações da maioria dos serviços do Azure facilita muito o backup de dados. No entanto, trabalhar individualmente com cada banco de dados ou com cada máquina virtual em sua empresa é entediante. E se você quiser fazer backup dos dados de todas as máquinas virtuais de um departamento ou de um local? É fácil fazer backup de várias máquinas virtuais ao criar uma política de backup e aplicá-la às máquinas virtuais desejadas. Este tutorial explica como:

> [!div class="checklist"]
> * Criar um cofre dos Serviços de Recuperação
> * Definir uma política de backup
> * Aplicar a política de backup para proteger várias máquinas virtuais
> * Disparar um trabalho de backup sob demanda nas máquinas virtuais protegidas

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

O cofre dos Serviços de Recuperação contém os dados de backup e a política de backup aplicada às máquinas virtuais protegidas. O backup de máquinas virtuais é um processo local. Não é possível fazer backup de uma máquina virtual de um local para um Cofre dos Serviços de Recuperação em outro local. Portanto, para cada local do Azure que tenha máquinas virtuais que devam passar por backup, pelo menos um Cofre dos Serviços de Recuperação deverá existir nesse local.

1. No menu à esquerda, selecione **Todos os serviços** e na lista de serviços, digite *Serviços de Recuperação*. A lista de recursos é filtrada enquanto você digita. Quando você vir os Cofres dos Serviços de Recuperação na lista, selecione-os para abrir o menu dos Cofres dos Serviços de Recuperação.

    ![Abrir menu do Cofre dos Serviços de Recuperação](./media/tutorial-backup-vm-at-scale/full-browser-open-rs-vault.png) <br/>

2. No menu **Cofres dos Serviços de Recuperação**, clique em **Adicionar** para abrir o menu dos Cofres dos Serviços de Recuperação.

    ![Menu Abrir Cofre](./media/tutorial-backup-vm-at-scale/provide-vault-detail-2.png)

3. No menu Cofre dos Serviços de Recuperação,

    - Digite *myRecoveryServicesVault* em **Nome**.
    - A ID da assinatura atual aparecerá em **Assinatura**. Se tiver assinaturas adicionais, você poderá escolher outra assinatura para o novo cofre.
    - Em **Grupo de Recursos**, selecione **Usar Existente** e selecione *myResourceGroup*. Se *myResourceGroup* não existir, selecione **Criar Novo** e digite *myResourceGroup*.
    - No menu suspenso **Localização**, escolha *Europa Ocidental*.
    - Clique em **Criar** para criar seu Cofre dos Serviços de Recuperação.

Um Cofre dos Serviços de Recuperação deverá estar no mesmo local que as máquinas virtuais que estão sendo protegidas. Se você tiver máquinas virtuais em várias regiões, crie um cofre de Serviços de Recuperação em cada região. Este tutorial criará um Cofre dos Serviços de Recuperação na *Europa Ocidental* porque este é local em que a *myVM* (a máquina virtual criada com o guia de início rápido) foi criada.

Talvez demore alguns minutos para o cofre de Serviços de Recuperação ser criado. Monitore as notificações de status na área superior direita do portal. Depois que o cofre é criado, ele aparece na lista de cofres dos Serviços de Recuperação.

Quando você cria um Cofre dos Serviços de Recuperação, por padrão, o cofre tem o armazenamento com redundância geográfica. Para fornecer resiliência de dados, o armazenamento com redundância geográfica replica os dados várias vezes em duas regiões do Azure.

## <a name="set-backup-policy-to-protect-vms"></a>Definir política de backup para proteger VMs

Após criar o Cofre dos Serviços de Recuperação, a próxima etapa é configurar o cofre para o tipo de dados e definir a política de backup. A política de backup é o agendamento de quando e com que frequência os pontos de recuperação serão feitos. A Política também inclui o período de retenção dos pontos de recuperação. Para este tutorial, vamos supor que sua empresa é um complexo esportivo com um hotel, um estádio, restaurantes e concessões e você está protegendo os dados nas máquinas virtuais. As etapas a seguir criam uma política de backup para os dados financeiros.

1. Na lista de Cofres dos Serviços de Recuperação, selecione **myRecoveryServicesVault** para abrir seu painel.

   ![Abrir o menu Cenário](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

2. No menu do painel do cofre, clique em **Backup** para abrir o menu do Backup.

3. No menu Meta de Backup do menu suspenso **Onde sua carga de trabalho é executada**, escolha *Azure*. No menu suspenso **Do que você deseja fazer backup**, escolha *Máquina virtual* e clique em **Backup**.

    Essas ações preparam o Cofre dos Serviços de Recuperação para interagir com uma máquina virtual. Os Cofres dos Serviços de Recuperação têm uma política padrão que cria um ponto de restauração por dia e retém os pontos de restauração por 30 dias.

    ![Abrir o menu Cenário](./media/tutorial-backup-vm-at-scale/backup-goal.png)

4. Para criar uma nova política, no menu Política de Backup, no menu suspenso **Escolher política de backup**, selecione *Criar Nova*.

    ![Selecionar carga de trabalho](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

5. No menu **Política de backup**, para **Nome da política**, digite *Finanças*. Insira as seguintes alterações na Política de Backup:
   - Para a **Frequência de backup**, defina o fuso horário para *Hora Central*. Como o complexo esportivo está localizado no Texas, o proprietário deseja que o horário definido seja o local. Deixe a frequência de backup definida como Diariamente às 03h30.
   - Para a **Retenção do ponto de backup diário**, defina o período para 90 dias.
   - Para a **Retenção do ponto de backup semanal**, use o ponto de restauração *Segunda-feira* e mantenha-o por 52 semanas.
   - Para a **Retenção do ponto de backup mensal**, use o ponto de restauração do Primeiro Domingo do mês e mantenha-o por 36 meses.
   - Desmarque a opção **Retenção do ponto de backup anual**. O líder de finanças não deseja manter dados por mais de 36 meses.
   - Clique em **OK** para criar a política de backup.

     ![Selecionar carga de trabalho](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

     Depois de criar a política de backup, associe-a às máquinas virtuais.

6. Na caixa de diálogo **Selecionar máquinas virtuais**, selecione *myVM* e clique em **OK** para implantar a política de backup nas máquinas virtuais.

    Todas as máquinas virtuais que estão no mesmo local e ainda não estão associadas a uma política de backup, aparecerão. *myVMH1* e *myVMR1* são selecionados para serem associados à política de *Finanças*.

    ![Selecionar carga de trabalho](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

    Ao término da implantação, você receberá uma notificação de que ela foi concluída com êxito.

## <a name="initial-backup"></a>Backup inicial

Você habilitou o backup para os Cofres dos Serviços de Recuperação, mas um backup inicial não foi criado. Uma melhor prática para a recuperação de desastre é disparar o primeiro backup para que seus dados sejam protegidos.

Para executar um trabalho de backup sob demanda:

1. No painel do cofre, clique no número **3** em **Itens de Backup** para abrir o menu Itens de Backup.

    ![Ícone Configurações](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    O menu **Itens de Backup** será aberto.

2. No menu **Itens de Backup**, clique em **Máquina Virtual do Azure** para abrir a lista das máquinas virtuais associadas ao cofre.

    ![Ícone Configurações](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    A lista **Itens de Backup** será aberta.

    ![Trabalho de backup iniciado](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

3. Na lista **Itens de Backup**, clique nas reticências **...** para abrir o menu de contexto.

4. No Menu de contexto, selecione **Fazer Backup Agora**.

    ![Menu de contexto](./media/tutorial-backup-vm-at-scale/context-menu.png)

    O menu Fazer Backup Agora será aberto.

5. No menu Fazer Backup Agora, insira o último dia para reter o ponto de recuperação e, em seguida, clique em **Backup**.

    ![definir o último dia em que o ponto de recuperação de Fazer Backup Agora será retido](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    As notificações de implantação informam que o trabalho de backup foi disparado, e que você pode monitorar o andamento do trabalho na página de Trabalhos de backup. Dependendo do tamanho da sua máquina virtual, a criação do backup inicial poderá demorar um pouco.

    Quando o trabalho de backup inicial for concluído, você poderá ver seu status no menu Trabalho de backup. O trabalho de backup sob demanda criou o ponto de restauração inicial para *myVM*. Se você quiser fazer backup de outras máquinas virtuais, repita essas etapas em cada máquina virtual.

    ![Bloco dos Trabalhos de Backup](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar trabalhando com os tutoriais subsequentes, não limpe os recursos criados neste tutorial. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse tutorial no Portal do Azure.

1. No painel **myRecoveryServicesVault**, clique no número **3** em **Itens de Backup** para abrir o menu Itens de Backup.

    ![Ícone Configurações](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)


2. No menu **Itens de Backup**, clique em **Máquina Virtual do Azure** para abrir a lista das máquinas virtuais associadas ao cofre.

    ![Ícone Configurações](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    A lista **Itens de Backup** será aberta.

3. Na lista **Itens de Backup**, clique nas reticências para abrir o Menu de contexto.

    ![Ícone Configurações](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

4. No menu de contexto, selecione **Interromper backup** para abrir o menu Interromper backup.

    ![Ícone Configurações](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

5. No menu **Interromper backup**, selecione o menu suspenso superior e escolha **Excluir Dados de Backup**.

6. Na caixa de diálogo **Digite o nome do item de Backup**, digite *myVM*.

7. Depois que o item de backup for verificado (uma marca de seleção aparecerá), o botão **Interromper Backup** será habilitado. Clique em **Interromper Backup** para interromper a política e excluir os pontos de restauração.

    ![Clique em Interromper Backup para excluir o cofre](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

8. No menu **myRecoveryServicesVault**, clique em **Excluir**.

    ![Clique em Interromper Backup para excluir o cofre](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    Depois que o cofre for excluído, você retornará à lista de Cofres dos Serviços de Recuperação.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou o portal do Azure para:

> [!div class="checklist"]
> * Criar um cofre dos Serviços de Recuperação
> * Definir o cofre para proteger as máquinas virtuais
> * Criar uma política personalizada de backup e retenção
> * Atribuir a política para proteger várias máquinas virtuais
> * Disparar um backup sob demanda para máquinas virtuais

Continue no próximo tutorial para restaurar uma máquina virtual do Azure de um disco.

> [!div class="nextstepaction"]
> [Restaurar VMs usando a CLI](./tutorial-restore-disk.md)
