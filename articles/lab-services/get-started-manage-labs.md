---
title: Introdução ao Azure Lab Services
description: Este artigo descreve como começar a usar o Azure Lab Services.
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: d260ace7d7819c3ca2db96d6c4984bf834e170da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98165035"
---
# <a name="get-started-with-lab-services"></a>Introdução aos serviços de laboratório 

A Azure Lab Services fornece aos alunos e professores acesso a laboratórios de computadores virtuais diretamente de seus próprios computadores.

Os professores precisam saber como ensinar os alunos/pais a utilizarem os serviços de laboratório em suas instruções por meio de um único hardware emitido por um aluno. Como resultado, os alunos seriam capazes de acessar o software padrão do setor necessário para seus programas de estudo por meio de VMs (máquinas virtuais). 

Uma VM é um ambiente virtual que atua como um computador virtual. As VMs têm seu próprio processador, memória e armazenamento. As VMs fornecem um substituto para uma máquina real e podem dar aos usuários acesso a sistemas operacionais e software sem a necessidade de tê-las em seu próprio dispositivo. O Azure Lab Services fornece uma ferramenta para que os alunos acessem e naveguem pelas VMs e para a equipe para gerenciar seus laboratórios de computador virtual. 

Este artigo fornece informações para ensinar a equipe sobre como acessar, gerenciar e ensinar alunos/pai a utilizar Azure Lab Services.

## <a name="key-concepts"></a>Principais conceitos

### <a name="quota-hours"></a>Horas de cota

Os alunos podem acessar suas VMs a qualquer momento durante a hora da aula agendada sem afetar as horas de cota. As horas de cota são definidas para o semestre inteiro e determinam o número de horas que um aluno pode usar sua VM fora da hora da classe agendada regularmente.

8 horas por semana, redefinidas em domingo – não cumulativa.

Para obter mais informações, consulte [set quota](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="automatic-shut-down"></a>Desligamento automático

Para ajudar a manter os custos e poupar horas de cota dos alunos, os desligamentos automáticos são habilitados para os laboratórios. Os desligamentos automáticos desativarão as VMs após um período de inatividade (sem entradas de mouse ou teclado). Os desligamentos automáticos funcionam em dois estágios, primeiro um aluno será desconectado da VM após um período de inatividade. Neste ponto, a VM ainda está **em execução** e os alunos podem se conectar. Após outro período de inatividade depois de desconectado, a VM será desligada.

Os desligamentos automáticos são uma ferramenta de economia de custos importante, no entanto, eles apresentam um desafio para alunos em relação ao salvamento de seu trabalho e à renderização de arquivos de projetos grandes. Se os alunos estiverem sendo desconectados com frequência ou as VMs estiverem desligadas muito rapidamente, entre em contato com o administrador da CTE. 

Para obter mais informações, consulte [Configurar o desligamento automático de VMs para uma conta de laboratório](how-to-configure-lab-accounts.md).

### <a name="managing-virtual-machines"></a>Gerenciando máquinas virtuais

O gerenciamento do laboratório permite que os professores controlem itens como a capacidade do laboratório (o número de VMs disponíveis para alunos) e iniciem, interrompam ou redefinam as VMs manualmente. os professores também podem se conectar a VMs para experimentar a interface do aluno, acessar arquivos e solucionar problemas com software ou a própria VM.

A coisa mais importante a ser lembrada ao gerenciar as VMs é que, sempre que um computador estiver **em execução**, estamos incorrendo em custos, mesmo que ninguém esteja conectado à VM.

## <a name="lab-dashboards"></a>Painéis de laboratório

### <a name="overview"></a>Visão geral

Painéis para laboratórios no Azure Lab Services fornecem um instantâneo de diferentes aspectos de um laboratório específico, incluindo informações da VM, número de VMs atribuídas e não atribuídas, número de usuários registrados e não registrados e informações sobre agendamentos de laboratório. 

> [!NOTE]
> Embora a maioria dos aspectos administrativos do painel e do [Azure Lab Services site](https://labs.azure.com/) fique visível para os professores, as permissões específicas para sua função podem afetar sua capacidade de modificar determinados critérios no painel. Se você encontrar um problema com sua configuração de laboratório específica, entre em contato com o administrador da CTE.

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="o portal de Azure Lab Services":::

### <a name="examine-a-dashboard"></a>Examinar um painel

1. Navegue e entre no site do [Azure Lab Services](https://labs.azure.com/).
1. Selecione seu laboratório.
1. Você verá um **painel** no lado esquerdo da janela. Clique no **painel** e você verá vários blocos em seu painel.
1. Abaixo dos **custos &** bloco de cobrança, também há blocos para modelos, pools de máquinas virtuais, usuários e agendas, que permitem modificar aspectos e exibir mais detalhes sobre o laboratório da sala de aula.

    * Modelo – descreve a data em que o modelo foi criado e a última publicação. 
    * Pool de máquinas virtuais-número de VMs atribuídas e não atribuídas.
    * Usuários-número de usuários registrados e usuários que foram adicionados ao laboratório, mas não registrados.
    * Agendas – exibe eventos agendados futuros para o laboratório e um link para exibir mais eventos.

Para obter mais informações, consulte [usar o painel](use-dashboard.md).

### <a name="manually-starting-vms"></a>Iniciando VMs manualmente

1. Na página **pool de máquinas virtuais** , você pode iniciar todas as VMs em um laboratório clicando no botão **Iniciar tudo** na parte superior da página.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="Inicie suas VMs":::
1. VMs individuais podem ser iniciadas clicando-se na alternância de estado. 

    A alternância será lida a **partir do início** da VM e, em seguida, **executada** quando a VM for iniciada.
1. Você também pode selecionar um número de VMs usando as verificações à esquerda da coluna **nome** . 

    Depois de selecionar as VMs desejadas, clique no botão **Iniciar** na parte superior da tela.
1. Depois de iniciado, você pode clicar no botão **parar tudo** para interromper todas as VMs.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="Pare suas VMs":::

### <a name="stopping-and-resetting-vms"></a>Interrompendo e redefinindo VMs

* Você pode parar VMs individuais clicando na alternância de estado.
* Você também pode interromper várias VMs usando as verificações e clicando no botão "parar" na parte superior da tela.

Se um aluno estiver enfrentando dificuldades para se conectar à VM ou se a VM precisar ser redefinida por qualquer outro motivo, você poderá usar a função redefinir.
1. Para redefinir uma ou mais VMs, selecione-as usando as verificações e, em seguida, clique no botão **Redefinir** na parte superior da página.
1. Na janela pop-up, clique em **Redefinir**.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="Redefinir sua VM":::

    > [!NOTE]
    > Ativar uma VM de aluno não afetará a cota do aluno. Cotas para usuários especifica o número de horas de laboratório disponíveis para o usuário fora da hora da classe agendada.

### <a name="connect-to-vms"></a>Conectar-se às VMs

Os professores podem se conectar a uma VM de aluno desde que ela esteja ativada e o aluno não esteja conectado à VM. Conectando-se à VM, você poderá acessar arquivos locais na VM e ajudar os alunos a solucionar problemas.

1. Para se conectar à VM do aluno, passe o mouse sobre a VM na lista e clique no botão **conectar** . 
1. Em seguida, siga o guia de introdução para estudantes para Chromebooks, Macs ou PCs

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="Botão Conectar-se à VM do aluno":::

## <a name="manage-users-in-a-lab"></a>Gerenciar usuários em um laboratório

Os professores podem adicionar usuários de aluno a um laboratório e monitorar suas cotas de hora. Para obter detalhes sobre como adicionar usuários por endereço de email ou usando uma lista de planilhas e registrar usuários, consulte [Adicionar e gerenciar usuários de laboratório](how-to-configure-student-usage.md).

Depois de ter convidado usuários ou compartilhado o link, você poderá monitorar quais usuários registraram com êxito na página **usuários** na coluna **status** . 

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não pretende usar os recursos que criou neste guia de início rápido, exclua os recursos.

## <a name="next-steps"></a>Próximas etapas

[Configurar uma conta de laboratório](tutorial-setup-lab-account.md)
