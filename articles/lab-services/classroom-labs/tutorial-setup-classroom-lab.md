---
title: Configurar um laboratório de sala de aula usando o Azure Lab Services | Microsoft Docs
description: Neste tutorial, você configura um laboratório para usar em uma sala de aula.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 0c50a321cbeb0d07a5039038ff796df00463ac8a
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385673"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutorial: Configurar um laboratório de sala de aula 
Neste tutorial, você configura um laboratório de sala de aula com máquinas virtuais que são usadas por alunos na sala de aula.  

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Criar um laboratório de sala de aula
> * Adicionar usuários ao laboratório
> * Enviar link de registro para estudantes

## <a name="prerequisites"></a>Pré-requisitos
Para configurar um laboratório de sala de aula em uma conta de laboratório, é necessário ser membro de uma destas funções na conta de laboratório: Proprietário, Criador de Laboratório ou Colaborador. A conta que você usou para criar uma conta de laboratório é adicionada automaticamente à função de proprietário.

Um proprietário de laboratório pode adicionar outros usuários à função **Criador de Laboratório**. Por exemplo, um proprietário de laboratório adiciona professores à função Criador de Laboratório. Em seguida, os professores criam laboratórios com VMs para suas aulas. Os alunos usam o link de registro que recebem dos professores para registrarem-se para o laboratório. Depois de registrados, eles podem usar VMs nos laboratórios para fazerem os trabalhos de aula e os deveres de casa. Para obter etapas detalhadas para adicionar usuários à função Criador de Laboratório, confira [Adicionar um usuário à função Criador de Laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue até [Site do Azure Lab Services](https://labs.azure.com). Observe que ainda não há suporte para o Internet Explorer 11. 
2. Selecione **Iniciar sessão** e insira suas credenciais. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft. 
3. Na janela **Novo laboratório**, execute as seguintes ações: 
    1. Especifique um **nome** para o laboratório. 
    2. Especifique o **número máximo de máquinas virtuais** no laboratório. É possível aumentar ou diminuir o número de VMs após criar o laboratório ou em um laboratório existente. Para saber mais, confira [Update number of VMs in a lab](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab) (Atualizar número de VMs em um laboratório)
    6. Clique em **Salvar**.

        ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na página **Selecionar especificações da máquina virtual**, siga estas etapas:
    1. Selecione um **tamanho** para as VMs (máquinas virtuais) criadas no laboratório. No momento, os tamanhos **pequeno**, **médio**, **médio (virtualização)** , **grande** e **GPU** são permitidos.
    3. Selecione a **imagem da VM** a ser usada para criar as VMs no laboratório. Se você selecionar uma imagem do Linux, verá uma opção para habilitar a conexão de área de trabalho remota para ela. Para obter detalhes, veja [Habilitar conexão de área de trabalho remota para Linux](how-to-enable-remote-desktop-linux.md).
    4. Selecione **Avançar**.

        ![Definir as especificações da VM](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Na página **Definir credenciais**, especifique as credenciais padrão para todas as VMs no laboratório. 
    1. Especifique o **nome do usuário** para todas as VMs no laboratório.
    2. Especifique a **senha** do usuário. 

        > [!IMPORTANT]
        > Anote o nome de usuário e a senha. Eles não serão mostrados novamente.
    3. Selecione **Criar**. 

        ![Definir as credenciais](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na página **Configurar modelo**, você vê o status do processo de criação do laboratório. A criação do modelo no laboratório leva até 20 minutos. 

    ![Configurar o modelo](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Após a configuração do modelo ser concluída, você verá a seguinte página: 

    ![Página de configuração do modelo após a conclusão](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Na página **Configurar modelo**, execute as seguintes etapas: Essas etapas são **opcionais** para o tutorial.
    2. Conecte-se à VM modelo selecionando **Conectar**. Se for uma VM de modelo do Linux, você escolherá se deseja se conectar usando SSH ou RDP (se RDP estiver habilitado).
    1. Selecione **Redefinição de senha** para redefinir a senha da VM. 
    1. Instale e configure software em sua VM modelo. 
    1. **Pare** a VM.  
    1. Insira uma **descrição** do modelo
9. Selecione **Avançar** na página do modelo. 
10. Na página **Publicar o modelo**, execute as seguintes ações. 
    1. Para publicar o modelo imediatamente, selecione **Publicar**.  

        > [!WARNING]
        > Depois de publicar, você não pode cancelar a publicação. 
    2. Para publicar mais tarde, selecione **Salvar para mais tarde**. É possível publicar a VM modelo após a conclusão do assistente. Para obter detalhes de como configurar e publicar após a conclusão do assistente, confira a seção [Publicar o modelo](how-to-create-manage-template.md#publish-the-template-vm) no artigo [Como gerenciar laboratórios de sala de aula](how-to-manage-classroom-labs.md).

        ![Publicar modelo](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Você vê o **andamento da publicação** do modelo. Esse processo pode levar até uma hora. 

    ![Publicar modelo – andamento](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Você verá a página a seguir quando o modelo for publicado com êxito. Selecione **Concluído**.

    ![Publicar modelo – êxito](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Você verá o **painel** do laboratório. 
    
    ![Painel de laboratório de sala de aula](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Alterne para a página **Máquinas virtuais** selecionando Máquinas virtuais no menu esquerdo ou selecionando o bloco Máquinas virtuais. Confirme que você vê cinco máquinas virtuais no estado **Não atribuído**. Essas máquinas virtuais ainda não foram atribuídas aos alunos. Elas devem estar no estado **Parado**. Você pode iniciar a VM de um aluno, conectar-se à VM, parar a VM e excluir a VM nesta página. Você pode iniciá-los nesta página ou permitir que os alunos iniciem as máquinas virtuais. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>Adicionar usuários ao laboratório

1. Selecione **Usuários** no menu à esquerda. Por padrão, a opção **Restringir acesso** está habilitada. Quando essa configuração está habilitada, um usuário não pode se registrar com o laboratório mesmo que o usuário tenha o link de registro, a menos que o usuário esteja na lista de usuários. Somente os usuários na lista podem se registrar no laboratório usando o link de registro enviado. Neste procedimento, você pode adicionar usuários à lista. Como alternativa, você pode desativar a opção **Restringir acesso**, o que permite que os usuários se registrem no laboratório, desde que eles tenham o link de registro. 
2. Selecione **Adicionar usuários** na barra de ferramentas. 

    ![Botão Adicionar usuários](../media/how-to-configure-student-usage/add-users-button.png)
1. Na página **Adicionar usuários**, insira endereços de email dos usuários em linhas separadas ou em uma única linha e separados por ponto e vírgula. 

    ![Adicionar endereços de email do usuário](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Clique em **Salvar**. Você vê os endereços de email de usuários e seus status (registrados ou não) na lista. 

    ![Lista de usuários](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-for-users"></a>Definir cotas para usuários
Defina cotas por usuário usando as seguintes etapas: 

1. Selecione **Usuários** no menu à esquerda se a página não estiver ativa. 
2. Selecione **Cota por usuário: 10 horas** na barra de ferramentas. 
3. Na página **Cota por usuário**, especifique o número de horas que você deseja conceder a cada usuário (aluno): 
    1. **Número total de horas por usuário**. Os usuários podem usar suas VMs durante o número definido de horas (especificado para este campo), **além do horário agendado**. Se você selecionar essa opção, insira o **número de horas** na caixa de texto. 

        ![Número de horas por usuário](../media/how-to-configure-student-usage/number-of-hours-per-user.png). 
    1. **0 horas (somente agenda)** . Os usuários podem usar suas VMs somente durante o horário agendado ou quando você, como o proprietário de laboratório, ativa as máquinas virtuais para eles.

        ![Horas de zero - somente horário agendado](../media/how-to-configure-student-usage/zero-hours.png)
    4. Clique em **Salvar**. 
5. Agora você vê os valores alterados na barra de ferramentas: **Cota por usuário: &lt;número de horas&gt;** . 

    ![Cota por usuário](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="set-a-schedule-for-the-lab"></a>Definir uma agenda para o laboratório
Se tiver configurado para a configuração de cota como **0 horas (somente agenda)** , você deve definir uma agenda para o laboratório. Neste tutorial, você define o agendamento para que seja uma agenda semanal recorrente.

1. Alterne para a página **Agendamentos** e selecione **Adicionar agendamento** na barra de ferramentas. 

    ![Botão Adicionar agendamento na página Agendamentos](../media/how-to-create-schedules/add-schedule-button.png)
2. Na página **Adicionar agendamento**, alterne para **Semanal** na parte superior. 
3. Para **Dias do agendamento (obrigatório)** , selecione os dias em que você deseja que o agendamento entre em vigor. No exemplo abaixo, a marca Segunda-Sexta foi selecionada. 
4. No campo **De**, insira a **data de início do agendamento** ou escolha uma data, selecionando o botão **calendário**. Esse campo é obrigatório. 
5. Para **data de término do agendamento**, insira ou selecione uma data de término em que as VMs devem ser desligadas. 
6. Para **Hora de início**, selecione a hora em que você deseja que as VMs sejam iniciadas. A hora de início é necessária se a hora de término não está definida. Selecione **Remover evento de início** se você quiser especificar apenas a hora de término. Se a **Hora de início** é desabilitada, selecione **Adicionar evento de início** ao lado da lista suspensa para habilitá-lo. 
7. Para **Hora de término**, selecione a hora em que você deseja que as VMs sejam desligadas. A hora de término é necessária se a hora de início não está definida. Selecione **Remover evento de interrupção** se você quiser especificar apenas a hora de início. Se a **Hora de término** é desabilitada, selecione **Adicionar evento de interrupção** ao lado da lista suspensa para habilitá-lo.
8. Para **Fuso horário (obrigatório)** , selecione o fuso horário das horas de início e de término especificadas por você.  
9. Para **Anotações**, insira qualquer descrição ou anotações para o agendamento. 
10. Clique em **Salvar**. 

    ![Agendamento semanal](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-an-email-with-the-registration-link"></a>Envie um email com o link de registro

1. Alterne para a exibição de **Usuários** se ainda não estiver na página. 
2. Selecione usuário específico ou todos os usuários na lista. Para selecionar usuários específicos, selecione as caixas de seleção na primeira coluna da lista. Para selecionar todos os usuários, marque a caixa de seleção na frente o título da primeira coluna (**Nome**) ou marque todas as caixas de seleção para todos os usuários na lista. Você pode ver o status do **estado de convite** nessa lista.  Na imagem a seguir, o estado de convite para todos os alunos é definido como **Convite não enviado**. 

    ![Selecionar alunos](../media/tutorial-setup-classroom-lab/select-students.png)
1. Selecione o **ícone de email (envelope)** em uma das linhas (ou) selecione **Enviar convite** na barra de ferramentas. Você também pode passar o mouse sobre um nome do aluno na lista para ver o ícone de email. 

    ![Enviar link de registro por email](../media/tutorial-setup-classroom-lab/send-email.png)
4. Em **Enviar link de registro pela página de email**, siga estas etapas: 
    1. Digite uma **mensagem opcional** que você deseja enviar aos alunos. O email inclui automaticamente o link de registro. 
    2. Em **Enviar link de registro pela página de email**, selecione **Enviar**. Você verá o status do convite mudar para **Enviando convite** e, em seguida, para **Convite enviado**. 
        
        ![Convites enviados](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou um laboratório de sala de aula e configurou o laboratório. Para saber como um aluno pode acessar uma VM no laboratório usando o link de registro, vá para o seguinte tutorial:

> [!div class="nextstepaction"]
> [Conectar a uma VM no laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)

