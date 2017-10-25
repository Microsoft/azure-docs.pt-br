---
title: "Fazer backup de máquinas de virtuais do Azure implantadas com o modelo clássico em um cofre de backup | Microsoft Docs"
description: "Descubra suas máquinas virtuais, registre-as e faça backup delas com estes procedimentos para o backup de máquina virtual do Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "backup de máquinas virtuais; fazer backup de máquina virtual, backup e recuperação de desastre; backup de vm"
ms.assetid: c0ab5469-65fd-4af5-ae9b-f5d183f82ce8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: e1da8bce96078a43c656f84005cefc8bbe81c9e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-azure-virtual-machines-classic-portal"></a>Fazer backup de máquinas virtuais do Azure (portal clássico)
> [!div class="op_single_selector"]
> * [Fazer backup de VMs no cofre dos Serviços de Recuperação](backup-azure-arm-vms.md)
> * [Fazer backup de VMs no cofre de Backup](backup-azure-vms.md)
>
>

Este artigo mostra os procedimentos para fazer backup de uma VM (máquina virtual) do Azure com implantação clássica em um cofre de Backup. Há algumas tarefas que você precisa fazer antes de poder fazer backup de uma máquina virtual do Azure. Se ainda não tiver feito isto, conclua os [pré-requisitos](backup-azure-vms-prepare.md) para preparar o ambiente para o backup de VMs.

Para obter informações adicionais, confira os artigos em [planejamento da infraestrutura de backup de VM no Azure](backup-azure-vms-introduction.md) e em [máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).

> [!NOTE]
> O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../azure-resource-manager/resource-manager-deployment-model.md). Um cofre de Backup só pode proteger VMs com implantação clássica. Você não pode proteger VMs implantadas pelo Resource Manager com um cofre de Backup. Confira [Fazer backup de VMs no cofre dos Serviços de Recuperação](backup-azure-arm-vms.md) para obter detalhes sobre como trabalhar com cofres dos Serviços de Recuperação.
>
>

Fazer o backup de máquinas virtuais do Azure envolve três etapas principais:

![Três etapas para fazer o backup de uma VM de IaaS do Azure](./media/backup-azure-vms/3-steps-for-backup.png)

> [!NOTE]
> O backup de máquinas virtuais é um processo local. Não é possível fazer backup de máquinas virtuais de uma região em um cofre de backup em outra região. Portanto, você deve criar um cofre de backup em cada região do Azure na qual existam VMs das quais serão feitos backups.
>
> [!IMPORTANT]
> A partir de março de 2017, você não poderá mais usar o portal clássico para criar os cofres de Backup.
> Agora você pode atualizar os cofres de Backup para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Backup para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). A Microsoft incentiva você a atualizar os cofres de Backup para os cofres dos Serviços de Recuperação.<br/> Após 15 de outubro de 2017, você não poderá usar o PowerShell para criar os Cofres do Backup. **Em 1º de novembro de 2017**:
>- Todos os Cofres do Backup restantes serão atualizados automaticamente para os cofres dos Serviços de Recuperação.
>- Você não poderá acessar os dados de backup no portal clássico. Em vez disso, use o portal do Azure para acessar os dados de backup nos cofres dos Serviços de Recuperação.
>

## <a name="step-1---discover-azure-virtual-machines"></a>Etapa 1 - Descobrir máquinas virtuais do Azure
Para garantir que qualquer VM (máquina virtual) nova adicionada à assinatura seja identificada antes do registro, execute o processo de descoberta. O processo consulta o Azure quanto à lista de máquinas virtuais na assinatura, juntamente com informações adicionais, como o nome do serviço de nuvem e a região.

1. Entrar no [portal clássico](http://manage.windowsazure.com/)
2. Na lista de serviços do Azure, clique em **Serviços de Recuperação** para abrir a lista de cofres de Backup e Recuperação de Site.
    ![Abrir listas de cofres](./media/backup-azure-vms/choose-vault-list.png)
3. Na lista de cofres de Backup, escolha o cofre para fazer backup de uma VM.

    Se for um novo cofre, o portal abrirá na página **Início Rápido** .

    ![Abrir o menu Itens registrados](./media/backup-azure-vms/vault-quick-start.png)

    Se o cofre tiver sido configurado anteriormente, o portal abrirá no menu usado mais recentemente.
4. No menu do cofre (na parte superior da página), clique em **Itens Registrados**.

    ![Abrir o menu Itens registrados](./media/backup-azure-vms/vault-menu.png)
5. No menu **Tipo**, selecione **Máquina Virtual do Azure**.

    ![Selecionar carga de trabalho](./media/backup-azure-vms/discovery-select-workload.png)
6. Clique em **DESCOBRIR** na parte inferior da página.
    ![Botão Descobrir](./media/backup-azure-vms/discover-button-only.png)

    O processo de descoberta pode ser executado por alguns minutos, enquanto as máquinas virtuais estão sendo tabuladas. Há uma notificação na parte inferior da tela que informa você de que o processo está sendo executado.

    ![Descobrir VMs](./media/backup-azure-vms/discovering-vms.png)

    As alterações de notificação quando o processo é concluído. Se o processo de descoberta não tiver localizado as máquinas virtuais, primeiro verifique se as VMs existem mesmo. Se existirem, garanta que as VMs estejam na mesma região que o cofre de backup. Se existirem e estiverem na mesma região, verifique se as VMs já não estão registradas em um cofre de backup. Se uma VM estiver atribuída a um cofre de backup, ela não estará disponível para ser atribuída a outros cofres de backup.

    ![Descoberta concluída](./media/backup-azure-vms/discovery-complete.png)

    Depois de descobrir os novos itens, vá para a Etapa 2 e registre suas VMs.

## <a name="step-2---register-azure-virtual-machines"></a>Etapa 2 - Registrar as máquinas virtuais do Azure
Você registra uma máquina virtual do Azure para associá-la ao serviço Backup do Azure. Normalmente, é uma atividade realizada uma única vez.

1. Navegue até o cofre de backup em **Serviços de Recuperação** no portal do Azure e clique em **Itens Registrados**.
2. Selecione **Máquina Virtual do Azure** no menu suspenso.

    ![Selecionar carga de trabalho](./media/backup-azure-vms/discovery-select-workload.png)
3. Clique em **REGISTRAR** na parte inferior da página.
    ![Botão Registrar](./media/backup-azure-vms/register-button-only.png)
4. No menu de atalho **Registrar Itens** , selecione as máquinas virtuais que você deseja registrar. Se houver duas ou mais máquinas virtuais com o mesmo nome, use o serviço de nuvem para fazer a distinção entre elas.

   > [!TIP]
   > Várias máquinas virtuais podem ser registradas ao mesmo tempo.
   >
   >

    Um trabalho é criado para cada máquina virtual selecionada.
5. Clique em **Exibir Trabalho** na notificação para ir para a página **Trabalhos**.

    ![Registrar trabalho](./media/backup-azure-vms/register-create-job.png)

    A máquina virtual também aparece na lista de itens registrados junto com o status da operação de registro.

    ![Status de registro 1](./media/backup-azure-vms/register-status01.png)

    Quando a operação for concluída, o status será alterado para refletir o estado *registrado* .

    ![Status de registro 2](./media/backup-azure-vms/register-status02.png)

## <a name="step-3---protect-azure-virtual-machines"></a>Etapa 3 - Proteger máquinas virtuais do Azure
Agora você pode configurar uma política de backup e de retenção para a máquina virtual. Várias máquinas virtuais podem ser protegidas usando uma única ação de proteção.

Os cofres do Backup do Azure criados depois de maio de 2015 poderão vir com uma política padrão interna ao cofre. Essa política padrão é fornecida com uma retenção padrão de 30 dias e agendamento de backup de uma vez por dia.

1. Navegue até o cofre de backup em **Serviços de Recuperação** no portal do Azure e clique em **Itens Registrados**.
2. Selecione **Máquina Virtual do Azure** no menu suspenso.

    ![Selecionar a carga de trabalho no portal](./media/backup-azure-vms/select-workload.png)
3. Na parte inferior da página, clique em **PROTEGER** .

    O **assistente Proteger Itens** é exibido. Esse assistente lista apenas as máquinas virtuais registradas e não protegidas. Selecione as máquinas virtuais que deseja proteger.

    Se houver duas ou mais máquinas virtuais com o mesmo nome, use o serviço de nuvem para distinguir entre elas.

   > [!TIP]
   > Você pode proteger várias máquinas virtuais de uma só vez.
   >
   >

    ![Configurar proteção em escala](./media/backup-azure-vms/protect-at-scale.png)

4. Escolha um **agendamento de backup** para fazer o backup das máquinas virtuais que você selecionou. Escolha um conjunto existente de políticas ou defina um novo.

    Cada política de backup pode ter várias máquinas virtuais associadas a ela. No entanto, a máquina virtual só pode estar associada a apenas uma política em um determinado ponto no tempo.

    ![Proteger com nova política](./media/backup-azure-vms/policy-schedule.png)

   > [!NOTE]
   > Uma política de backup também inclui um esquema de retenção para os backups agendados. Se você selecionar uma política de backup existente, não será possível modificar as opções de retenção na próxima etapa.
   >
   >

5. Escolha um **intervalo de retenção** a ser associado aos backups.

    ![Proteger com retenção flexível](./media/backup-azure-vms/policy-retention.png)

    A política de retenção especifica o período de armazenamento de um backup. Você pode especificar políticas de retenção diferentes com base em quando o backup é feito. Por exemplo, um ponto de backup feito diariamente (que funciona como um ponto de recuperação operacional) pode ser preservado durante 90 dias. Em comparação, um ponto de backup feito no final de cada trimestre (para fins de auditoria) precisará ser preservado por muitos meses ou anos.

    ![O backup da máquina virtual é realizado com ponto de recuperação](./media/backup-azure-vms/long-term-retention.png)

    Nesta imagem de exemplo:

   * **Política de retenção diária**: os backups diários são armazenados por 30 dias.
   * **Política de retenção semanal**: os backups feitos todas as semanas aos domingos serão preservados por 104 semanas.
   * **Política de retenção mensal**: os backups feitos no último domingo de cada mês serão preservados por 120 meses.
   * **Política de retenção anual**: os backups feitos no primeiro domingo de cada janeiro serão preservados por 99 anos.

     Um trabalho é criado para configurar a política de proteção e associar as máquinas virtuais a essa política para cada máquina virtual selecionada.
6. Para exibir a lista de trabalhos de **Configurar Proteção**, no menu dos cofres, clique em **Trabalhos** e selecione **Configurar Proteção** no filtro **Operação**.

    ![Configurar o trabalho de proteção](./media/backup-azure-vms/protect-configureprotection.png)

## <a name="initial-backup"></a>Backup inicial
Uma vez protegida com uma política, a máquina virtual será exibida na guia **Itens Protegidos** com o status *Protegida (pendente de backup inicial)*. Por padrão, o primeiro backup agendado é o *backup inicial*.

Para disparar o backup inicial imediatamente após a configuração de proteção:

1. Na parte inferior da página **Itens Protegidos**, clique em **Fazer Backup Agora**.

    O serviço de Backup do Azure cria um trabalho de backup para a operação de backup inicial.
2. Clique na guia **Trabalhos** para exibir a lista de trabalhos.

    ![Backup em andamento](./media/backup-azure-vms/protect-inprogress.png)

> [!NOTE]
> Durante a operação de backup, o serviço de Backup do Azure emite um comando para a extensão de backup em cada máquina virtual a fim de limpar todos os trabalhos de gravação e capturar um instantâneo consistente.
>
>

Quando o backup inicial terminar, o status da máquina virtual na guia **Itens Protegidos** será *Protegido*.

![O backup da máquina virtual é realizado com ponto de recuperação](./media/backup-azure-vms/protect-backedupvm.png)

## <a name="viewing-backup-status-and-details"></a>Exibindo detalhes e status do backup
Depois de protegido, a contagem de máquina virtual também aumenta no resumo da página **Painel** . A página **Painel** também mostra o número de trabalhos das últimas 24 horas que foram *bem-sucedidos*, que *falharam* e que estão *em andamento*. Na página **Trabalhos**, use os menus **Status**, **Operação** ou **De** e **Para** a fim de filtrar os trabalhos.

![Status do backup na página Painel](./media/backup-azure-vms/dashboard-protectedvms.png)

Os valores no painel são atualizados a cada 24 horas.

## <a name="troubleshooting-errors"></a>Solucionar erros
Se você enfrentar problemas durante o backup da sua máquina virtual, examine o [artigo sobre solução de problemas de VM](backup-azure-vms-troubleshoot.md) para obter ajuda.

## <a name="next-steps"></a>Próximas etapas
* [Gerenciar e monitorar suas máquinas virtuais](backup-azure-manage-vms.md)
* [Restaurar máquinas virtuais](backup-azure-restore-vms.md)
