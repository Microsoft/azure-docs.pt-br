---
title: Definir configurações de uso em laboratórios de sala de aula dos Serviços de Laboratório do Azure | Microsoft Docs
description: Saiba como configurar o número de usuários para o laboratório, registrá-los no laboratório, controlar o número de horas que eles podem usar a VM e muito mais.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 86f22864c416ad2a90bea09c02675d6eb3322308
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385631"
---
# <a name="configure-usage-settings-and-policies"></a>Configurar políticas e configurações de uso
Este artigo descreve como adicionar usuários ao laboratório, registrá-los no laboratório, controlar o número de horas que eles podem usar a VM, entre outros. 


## <a name="add-users-to-the-lab"></a>Adicionar usuários ao laboratório
Se você tiver o **Restringir acesso** habilitado, adicione usuários (endereços de email) à lista.

1. Selecione **Usuários** no menu à esquerda.
2. Selecione **Adicionar usuários** na barra de ferramentas. 

    ![Botão Adicionar usuários](../media/how-to-configure-student-usage/add-users-button.png)
1. Na página **Adicionar usuários**, insira endereços de email dos usuários em linhas separadas ou em uma única linha e separados por ponto e vírgula. 

    ![Adicionar endereços de email do usuário](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Clique em **Salvar**. Você vê os endereços de email de usuários e seus status (registrados ou não) na lista. 

    ![Lista de usuários](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>Compartilhar link de registro com alunos
Para enviar o link de registro para os alunos, use um dos métodos a seguir. O primeiro método mostra como enviar emails para alunos com o link de registro e uma mensagem opcional. O segundo método mostra como obter o link de registro que você pode compartilhar com outras pessoas como desejar. 

Se a opção **Restringir o acesso** estiver habilitada para o laboratório, somente os usuários na lista de usuários poderão usar o link de registro para se registrar no laboratório. Essa opção é habilitada por padrão. 

### <a name="send-email-to-users"></a>Enviar email aos usuários
Azure Lab Services permite que os professores enviem convites por email a todos ou a alunos selecionados sem precisar usar outro cliente de email. Os professores podem focalizar um aluno individual na lista para ver o ícone de email de cada aluno ou selecionar um ou mais alunos e usar **Enviar convite** na barra de ferramentas. Esse recurso envia um email com um link de registro e uma mensagem (se houver) adicionado pelo professor. Depois que o convite for enviado, o estado do convite será alterado para **convite enviado** para que os professores possam controlar quais alunos já receberam o link de registro e a data em que ele foi enviado.

1. Alterne para a exibição de **Usuários** se ainda não estiver na página. 
2. Selecione usuário específico ou todos os usuários na lista. Para selecionar usuários específicos, selecione as caixas de seleção na primeira coluna da lista. Para selecionar todos os usuários, marque a caixa de seleção na frente o título da primeira coluna (**Nome**) ou marque todas as caixas de seleção para todos os usuários na lista. Você pode ver o status do **estado de convite** nessa lista.  Na imagem a seguir, o estado de convite para todos os alunos é definido como **Convite não enviado**. 

    ![Selecionar alunos](../media/tutorial-setup-classroom-lab/select-students.png)
1. Selecione o **ícone de email (envelope)** em uma das linhas (ou) selecione **Enviar convite** na barra de ferramentas. Você também pode passar o mouse sobre um nome do aluno na lista para ver o ícone de email. 

    ![Enviar link de registro por email](../media/tutorial-setup-classroom-lab/send-email.png)
4. Em **Enviar link de registro pela página de email**, siga estas etapas: 
    1. Digite uma **mensagem opcional** que você deseja enviar aos alunos. O email inclui automaticamente o link de registro. 
    2. Em **Enviar link de registro pela página de email**, selecione **Enviar**. Você verá o status do convite mudar para **Enviando convite** e, em seguida, para **Convite enviado**. 
        
        ![Convites enviados](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="get-registration-link"></a>Obter link de registro
1. Alterne para a exibição **Usuários** selecionando **usuários** no menu à esquerda. 
2. Selecione o bloco **Obter link de registro**.

    ![Link de registro do aluno](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Na caixa de diálogo **Registro de usuário**, selecione o botão **Copiar**. O link é copiado para a área de transferência. Cole-o em um editor de email e envie um email para o aluno. 

    ![Link de registro do aluno](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Na caixa de diálogo **Registro de usuário**, selecione **Fechar**. 
4. Compartilhe o **link de registro** com um aluno para que o aluno possa se registrar para a classe. 

## <a name="view-users-registered-with-the-lab"></a>Exibir usuários registrados no laboratório

Selecione **usuários** no menu à esquerda para ver a lista de usuários registrados com o laboratório. 

![Lista de usuários registrados no laboratório](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-for-users"></a>Definir cotas para usuários
Defina cotas por usuário usando as seguintes etapas: 

1. Selecione **Usuários** no menu à esquerda se a página não estiver ativa. 
2. Selecionar **cota por usuário: 10 horas** na barra de ferramentas. 
3. Na página **Cota por usuário**, especifique o número de horas que você deseja conceder a cada usuário (aluno): 
    1. **Número total de horas por usuário**. Os usuários podem usar suas VMs durante o número definido de horas (especificado para este campo), **além do horário agendado**. Se você selecionar essa opção, insira o **número de horas** na caixa de texto. 

        ![Número de horas por usuário](../media/how-to-configure-student-usage/number-of-hours-per-user.png). 
    1. **0 horas (somente agenda)** . Os usuários podem usar suas VMs somente durante o horário agendado ou quando você, como o proprietário de laboratório, ativa as máquinas virtuais para eles.

        ![Horas de zero - somente horário agendado](../media/how-to-configure-student-usage/zero-hours.png)
    4. Clique em **Salvar**. 
5. Agora você vê os valores alterados na barra de ferramentas: **Cota por usuário: &lt;número de horas&gt;** . 

    ![Cota por usuário](../media/how-to-configure-student-usage/quota-per-user.png)



> [!IMPORTANT]
> Antes de enviar o link de registro para os alunos, os professores deverão definir o agendamento da classe se escolherem 0 horas de cota ou especificar as horas de cota para o laboratório.
>
> O [tempo de execução agendado de VMs](how-to-create-schedules.md) não conta com a cota alocada a um usuário. A cota é para o tempo fora dos horários agendados que um aluno passa em VMs. 

### <a name="add-users-by-uploading-a-csv-file"></a>Adicionar usuários ao carregar um arquivo CSV
Você também pode adicionar usuários ao carregar um arquivo CSV com endereços de email dos usuários.

1. Crie um arquivo CSV com endereços de email de usuários em uma coluna.

    ![Cota por usuário](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Na página **Usuários** do laboratório, selecione **Carregar CSV** na barra de ferramentas.

    ![Botão Carregar CSV](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Selecione o arquivo CSV com endereços de email do usuário. Quando você seleciona **Abrir** após selecionar o arquivo CSV, você vê a seguinte janela **Adicionar usuários**. A lista de endereço de email é preenchida com endereços de email do arquivo CSV. 

    ![Adicionar janela de usuários populada com endereços de email do arquivo CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. Selecione **Salvar** na janela **Adicionar usuários**. 
5. Confirme que você vê os usuários na lista de usuários. 

    ![Lista de usuários adicionados](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>Gerenciar VMs do usuário
Após o registro dos alunos no Azure Lab Services usando o link de registro fornecido, você verá as VMs atribuídas aos alunos na guia **Máquinas virtuais**. 

![Máquinas virtuais atribuídas aos alunos](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

É possível executar as seguintes tarefas em uma VM de aluno: 

- Pare uma VM, se ela estiver em execução. 
- Inicie uma VM, se ela estiver parada. 
- Conecte-se à VM. 
- Exclua a VM. 
- Veja o número de horas que os usuários usaram a máquina virtual. 

## <a name="update-number-of-virtual-machines-in-lab"></a>Atualizar o número de máquinas virtuais no laboratório
Para atualizar o número de máquinas virtuais no laboratório, execute as seguintes etapas da página **Máquinas Virtuais**:

1. Selecione **Máquinas virtuais** no menu à esquerda. 
2. Selecione **Capacidade do laboratório: &lt;número&gt; de computadores** na barra de ferramentas. 
3. Insira o **número** de máquinas virtuais.
4. Clique em **Salvar**.

    ![Máquinas virtuais no laboratório](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)
