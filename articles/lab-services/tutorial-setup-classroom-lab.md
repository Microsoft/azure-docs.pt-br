---
title: Configurar um laboratório de sala de aula usando o Azure Lab Services | Microsoft Docs
description: Neste tutorial, você usará o Azure Lab Services para configurar um laboratório de sala de aula com máquinas virtuais que são usadas por alunos da sua turma.
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: 8093a1fd270cdba8bdccaf48737bf6737bdd394d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98787411"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutorial: Configurar um laboratório de sala de aula 
Neste tutorial, você configura um laboratório de sala de aula com máquinas virtuais que são usadas por alunos na sala de aula.  

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Criar um laboratório de sala de aula
> * Adicionar usuários ao laboratório
> * Definir uma agenda para o laboratório
> * Enviar um email de convite para os alunos

## <a name="prerequisites"></a>Pré-requisitos
Neste tutorial, você configura um laboratório com máquinas virtuais para sua classe. Para configurar um laboratório de sala de aula em uma conta de laboratório, é necessário ser membro de uma destas funções na conta de laboratório: Proprietário, Criador de Laboratório ou Colaborador. A conta que você usou para criar uma conta de laboratório é adicionada automaticamente à função de proprietário. Portanto, você pode usar a conta de usuário usada para criar uma conta de laboratório para criar um laboratório de sala de aula. 

Este é o fluxo de trabalho típico ao usar o Azure Lab Services:

1. Um criador de conta pode adicionar outros usuários à função **Criador de Laboratório**. Por exemplo, o criador/administrador da conta de laboratório adiciona educadores à função **Criador de Laboratório** para que eles possam criar laboratórios para suas aulas. 
2. Em seguida, os educadores criam laboratórios com VMs para suas aulas e enviam links de registro para os participantes. 
3. Os alunos usam o link de registro que recebem dos educadores para se registrarem no laboratório. Depois de registrados, eles podem usar VMs nos laboratórios para fazerem os trabalhos de aula e os deveres de casa. 

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula
Nessa etapa, você cria um laboratório para sua classe no Azure. 

1. Navegue até [Site do Azure Lab Services](https://labs.azure.com). Observe que ainda não há suporte para o Internet Explorer 11. 
2. Selecione **Iniciar sessão** e insira suas credenciais. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft. 
3. Selecione **Novo laboratório**. 
    
    ![Captura de tela que mostra "Azure Lab Services" com o botão "Novo laboratório" selecionado.](./media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Na janela **Novo laboratório**, execute as seguintes ações: 
    1. Especifique um **nome** para o laboratório e selecione **Avançar**.  

        ![Criar um laboratório de sala de aula](./media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. Na página **Credenciais da máquina virtual**, especifique as credenciais padrão para todas as VMs no laboratório. Especifique o **nome** e a **senha** do usuário e, em seguida, selecione **Avançar**.  

        ![Janela Novo laboratório](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Anote o nome de usuário e a senha. Eles não serão mostrados novamente.
    3. Na página **Políticas do laboratório**, selecione **Concluir**. 

        ![Cota para cada usuário](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Você deverá ver a tela a seguir que mostra o status da criação da VM de modelo. Essa operação leva até 20 minutos. 

    ![Status da criação da VM de modelo](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na página **Modelo**, execute as seguintes etapas: Essas etapas são **opcionais** para o tutorial.

    1. Conecte-se à VM modelo selecionando **Conectar**. Se for uma VM de modelo do Linux, você escolherá se deseja se conectar usando SSH ou RDP (se RDP estiver habilitado).
    3. Instale e configure o software necessário para sua classe na VM de modelo. 
    4. **Pare** a VM de modelo.  

    > [!NOTE]
    > As VMs de modelo incorrem em **custos** durante a execução, portanto, verifique se elas estão desligadas quando não precisam estar em execução. 

## <a name="publish-the-template-vm"></a>Publicar a VM modelo
Nessa etapa, você publica a VM de modelo. Quando você publicar a VM do modelo, o Azure Lab Services criará VMs no laboratório usando o modelo. Todas as máquinas virtuais têm a mesma configuração que o modelo.

1. Na página **Modelo**, selecione **Publicar** na barra de ferramentas. 

    ![Botão Publicar modelo](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Depois de publicar, você não pode cancelar a publicação. 
2. Na página **Publicar modelo**, insira o número de máquinas virtuais que deseja criar no laboratório e, em seguida, selecione **Publicar**. 

    ![Publicar modelo – número de VMs](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Você verá o **status de publicação** do modelo na página. Esse processo pode levar até uma hora. 

    ![Publicar modelo – andamento](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Aguarde até que a publicação seja concluída e, em seguida, alterne para a página **Pool de máquinas virtuais** selecionando **Máquinas virtuais** no menu esquerdo ou selecionando o bloco **Máquinas virtuais**. Confirme que você vê cinco máquinas virtuais no estado **Não atribuído**. Essas máquinas virtuais ainda não foram atribuídas aos alunos. Elas devem estar no estado **Parado**. Você pode iniciar a VM de um aluno, conectar-se à VM, parar a VM e excluir a VM nesta página. Você pode iniciá-los nesta página ou permitir que os alunos iniciem as máquinas virtuais. 

    ![Máquinas virtuais no estado parado](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > Quando um educador ativa a VM de um aluno, a cota do aluno não é afetada. A cota de um usuário especifica o número de horas de laboratório disponíveis para o usuário fora da hora da classe agendada. Para obter mais informações sobre cotas, confira [Definir cotas para usuários](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="set-a-schedule-for-the-lab"></a>Definir uma agenda para o laboratório
Crie um evento agendado para o laboratório, de modo que as VMs no laboratório sejam iniciadas/paradas automaticamente em horários específicos. A cota do usuário (padrão: 10 horas) especificada anteriormente é o tempo adicional atribuído a cada usuário fora desse horário agendado. 

1. Alterne para a página **Agendamentos** e selecione **Adicionar evento agendado** na barra de ferramentas. 

    ![Captura de tela que mostra o botão "Adicionar evento agendado" na página "Agendamentos".](./media/how-to-create-schedules/add-schedule-button.png)
2. Na página **Adicionar evento agendado**, execute as seguintes etapas:
    1. Confirme se **Padrão** está selecionado para o **Tipo de evento**.  
    2. Selecione a **data de início** da classe. 
    4. Selecione a **hora de início** em que deseja iniciar as VMs.
    5. Selecione a **hora de término** em que as VMs devem ser desligadas. 
    6. Selecione o **fuso horário** para as horas de início e de término especificadas. 
3. Na mesma página **Adicionar evento agendado**, selecione a agenda atual na seção **Repetir**.  

    ![Botão Adicionar agendamento na página Agendamentos](./media/how-to-create-schedules/select-current-schedule.png)
5. Na caixa de diálogo **Repetir**, execute as seguintes etapas:
    1. Confirme se a opção **toda semana** está definida para o campo **Repetir**. 
    2. Selecione os dias nos quais deseja que a agenda entre em vigor. No exemplo abaixo, a marca Segunda-Sexta foi selecionada. 
    3. Selecione uma **data de término** para a agenda.
    8. Clique em **Salvar**. 

        ![Definir agenda de repetição](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Agora, na página **Adicionar evento agendado**, em **Observações (opcional)** , insira uma descrição ou observações para o agendamento. 
4. Na página **Adicionar evento agendado**, selecione **Salvar**. 

    ![Agendamento semanal](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Navegue até a data de início no calendário para verificar se a agenda está definida.
    
    ![Agenda no calendário](./media/how-to-create-schedules/schedule-calendar.png)

    Para obter mais informações sobre como criar e gerenciar agendas para uma aula, confira [Criar e gerenciar agenda para laboratórios](how-to-create-schedules.md).


## <a name="add-users-to-the-lab"></a>Adicionar usuários ao laboratório

Quando você adiciona os usuários, por padrão, a opção **Restringir acesso** é ativada e, a menos que estejam na lista de usuários, os alunos não poderão se registrar com o laboratório mesmo se tiverem um link de registro. Somente os usuários listados podem se registrar no laboratório usando o link de registro enviado. Você pode desligar a opção **Restringir acesso**, o que permite que os alunos se registrem no laboratório, desde que eles tenham o link de registro. 

### <a name="add-users-from-an-azure-ad-group"></a>Adicionar usuários de um grupo do Azure AD

Você pode sincronizar uma lista de usuários de laboratório com um grupo do Azure AD (Azure Active Directory) existente para que não precise adicionar nem excluir usuários manualmente. 

Um grupo do Azure AD pode ser criado dentro do Azure Active Directory da sua organização para gerenciar o acesso a recursos organizacionais e aplicativos baseados em nuvem. Para saber mais, confira [Grupos do Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md). Se a sua organização usar o Microsoft Office 365 ou os serviços do Azure, ela já terá administradores que gerenciam seu Azure Active Directory. 

> [!IMPORTANT]
> Verifique se a lista de usuários está vazia. Se houver usuários existentes dentro de um laboratório que você adicionou manualmente ou por meio da importação de um arquivo CSV, a opção de sincronizar o laboratório com um grupo existente não será exibida. 

1. No painel esquerdo, selecione **Usuários**. 
1. Clique em **Fazer a sincronização com o grupo**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-sync-group.png" alt-text="Adicionar usuários fazendo a sincronização com um grupo do Azure AD":::
    
1. Você deverá escolher um grupo existente do Azure AD com o qual sincronizar seu laboratório. 
    
    Se você não vê um grupo do Azure AD na lista, isso pode acontecer devido aos seguintes motivos:

    -   Se você é um usuário convidado para um Azure Active Directory (geralmente, se você estiver fora da organização que tem a propriedade do Azure AD) e não pode procurar grupos dentro do Azure AD. Nesse caso, você não poderá adicionar um grupo do Azure AD ao laboratório nesse caso. 
    -   Os grupos do Azure AD criados por meio do Teams não aparecem nesta lista. Você pode adicionar o aplicativo Azure Lab Services dentro do Teams para criar e gerenciar laboratórios diretamente de dentro dele. Confira mais informações sobre [como gerenciar a lista de usuários de um laboratório de dentro do Teams](how-to-manage-user-lists-within-teams.md). 
1. Depois de escolher o grupo do Azure AD com o qual sincronizar seu laboratório, clique em **Adicionar**.
1. Depois que um laboratório for sincronizado, ele efetuará pull de todos dentro do grupo do Azure AD para o laboratório como usuários e você verá a lista de usuários atualizada. Somente as pessoas deste grupo do Azure AD terão acesso ao seu laboratório. A lista de usuários será atualizada a cada 24 horas para corresponder à associação mais recente do grupo do Azure AD. Você também pode clicar no botão Sincronizar na guia Usuários para fazer a sincronização manual com as alterações mais recentes no grupo do Azure AD.
1. Convide os usuários para seu laboratório clicando no botão **Convidar Todos**, que enviará um email a todos os usuários com o link de registro para o laboratório. 

### <a name="add-users-manually-from-emails-or-csv-file"></a>Adicionar usuários manualmente de emails ou do arquivo CSV

Nesta seção, você adicionará os alunos manualmente (por endereço de email ou carregando um arquivo CSV). 

#### <a name="add-users-by-email-address"></a>Adicionar usuários por endereço de email

1. No painel esquerdo, selecione **Usuários**. 
1. Clique em **Adicionar usuários manualmente**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-manually.png" alt-text="Adicionar usuários manualmente":::
1. Selecione **Adicionar por endereço de email** (padrão), insira os endereços de email dos alunos em linhas separadas ou em uma linha separada por pontos e vírgulas. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-email-addresses.png" alt-text="Adicionar endereços de email dos usuários":::
1. Clique em **Salvar**. 

    A lista exibe os endereços de email e os status dos usuários atuais, estando eles registrados no laboratório ou não. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="Lista de usuários":::

    > [!NOTE]
    > Depois que os alunos são registrados no laboratório, a lista exibe os nomes deles. O nome mostrado na lista é construído usando o nome e o sobrenome dos alunos no Azure Active Directory. 

#### <a name="add-users-by-uploading-a-csv-file"></a>Adicionar usuários ao carregar um arquivo CSV

Você também pode adicionar usuários carregando um arquivo CSV que contém os endereços de email deles. 

Um arquivo de texto CSV é usado para armazenar dados de tabela (números e texto) CSV (separados por vírgula). Em vez de armazenar informações em campos de colunas (como em planilhas), um arquivo CSV armazena informações separadas por vírgulas. Cada linha em um arquivo CSV terá o mesmo número de "campos" separados por vírgulas. Você pode usar o Excel para criar e editar arquivos CSV com facilidade.

1. No Microsoft Excel, crie um arquivo CSV que liste os endereços de email dos alunos em uma coluna.

    :::image type="content" source="./media/how-to-configure-student-usage/csv-file-with-users.png" alt-text="Lista de usuários em um arquivo CSV":::
1. Na parte superior do painel **Usuários**, selecione **Adicionar usuários** e escolha **Carregar CSV**.
1. Selecione o arquivo CSV que contém os endereços de email dos alunos e escolha **Abrir**.

    A janela **Adicionar usuários** exibe a lista de endereços de email do arquivo CSV. 
1. Clique em **Salvar**. 
1. No painel **Usuários**, veja a lista de alunos adicionados. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="Lista de usuários adicionados no painel Usuários"::: 

## <a name="send-invitation-emails-to-users"></a>Enviar emails de convite aos usuários

1. Alterne para a exibição **Usuários**, caso ainda não esteja na página, e selecione **Convidar todos** na barra de ferramentas. 

    ![Selecionar alunos](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. Na página **Enviar convite por email**, insira uma mensagem opcional e, em seguida, selecione **Enviar**. O email inclui automaticamente o link de registro. Obtenha esse link de registro selecionando **... (reticências)** na barra de ferramentas e **Link de registro**. 

    ![Enviar link de registro por email](./media/tutorial-setup-classroom-lab/send-email.png)
4. Você verá o status de **convite** na lista **Usuários**. O status deverá ser alterado para **Enviando** e, em seguida, para **Enviado em &lt;data&gt;** . 

Para obter mais informações sobre como adicionar alunos a uma aula e gerenciar o uso do laboratório, consulte [Como configurar o uso do aluno](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou um laboratório para sua classe no Azure. Para saber como um aluno pode acessar uma VM no laboratório usando o link de registro, vá para o seguinte tutorial:

> [!div class="nextstepaction"]
> [Conectar a uma VM no laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)