---
title: Gerenciar laboratórios no Azure Lab Services | Microsoft Docs
description: Saiba como criar e configurar um laboratório de sala de aula, exibir todos os laboratórios, compartilhar o link de registro com um usuário de laboratório ou excluir um laboratório.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: db7a2f58f99252a7e5076dd86c37b65bbe8ea37a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434202"
---
# <a name="manage-labs-in-azure-lab-services"></a>Gerenciar laboratórios no Azure Lab Services 
Este artigo descreve como criar e excluir um laboratório de sala de aula. Ele também mostra como exibir todos os laboratórios em uma conta de laboratório. 

## <a name="prerequisites"></a>Pré-requisitos
Para configurar um laboratório de sala de aula em uma conta de laboratório, você deve ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta que você usou para criar uma conta de laboratório é adicionada automaticamente a essa função. Um proprietário de laboratório pode adicionar outros usuários à função Criador de Laboratório usando as etapas do seguinte artigo: [Adicionar um usuário à função Criador de Laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue até [Site do Azure Lab Services](https://labs.azure.com). Ainda não há suporte para o Internet Explorer 11. 
1. Selecione **Iniciar sessão** e insira suas credenciais. Selecione ou insira uma **ID de usuário** que é um membro da função **Criador de laboratório** na conta do laboratório e insira a senha. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft. 
1. Selecione **Novo laboratório**. 
    
    ![Criar um laboratório de sala de aula](./media/tutorial-setup-classroom-lab/new-lab-button.png)
1. Na janela **Novo laboratório**, execute as seguintes ações: 
    1. Especifique um **nome** para o laboratório. 
    1. Selecione o **tamanho das máquinas virtuais** de que você precisa para a classe. Para obter a lista de tamanhos disponíveis, consulte a seção [Tamanhos de VM](#vm-sizes). 
    1. Selecione a **imagem da máquina virtual** que você deseja usar para o laboratório de sala de aula. Se você selecionar uma imagem do Linux, verá uma opção para **habilitar a Conexão de Área de Trabalho Remota**. Para obter detalhes, veja [Habilitar conexão de área de trabalho remota para Linux](how-to-enable-remote-desktop-linux.md).

        Se você tiver entrado usando credenciais do proprietário da conta do laboratório, verá uma opção para habilitar mais imagens para o laboratório. Para obter mais informações, consulte [Habilitar imagens no momento da criação do laboratório](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation).
    1. Revise o **preço total por hora** exibido na página. 
    1. Clique em **Salvar**.

        ![Captura de tela que mostra a janela "novo laboratório".](./media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Você verá uma opção para selecionar um local para o laboratório se a conta do laboratório tiver sido configurada para a opção [permitir que o criador do laboratório escolha a localização do laboratório](allow-lab-creator-pick-lab-location.md). 
4. Na página **Credenciais da máquina virtual**, especifique as credenciais padrão para todas as VMs no laboratório.
    1. Especifique o **nome do usuário** para todas as VMs no laboratório.
    2. Especifique a **senha** do usuário. 

        > [!IMPORTANT]
        > Anote o nome de usuário e a senha. Eles não serão mostrados novamente.
    3. Desabilite a opção **Usar a mesma senha para todas as máquinas virtuais** se desejar que os alunos definam suas próprias senhas. Esta etapa é **opcional**. 

        Um educador pode optar por usar a mesma senha para todas as VMs no laboratório ou permitir que os alunos definam senhas para suas VMs. Por padrão, essa configuração é habilitada para todas as imagens do Windows e do Linux, exceto para o Ubuntu. Quando você seleciona a VM do **Ubuntu**, essa configuração é desabilitada para que os alunos definam uma senha ao se conectarem pela primeira vez.  

        ![Janela Novo laboratório](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Em seguida, selecione **Próximo** na página **Credenciais da máquina virtual**. 
5. Na página **Políticas de laboratório**, execute as seguintes etapas:
    1. Insira o número de horas alocadas para cada usuário (**cota para cada usuário**) fora do horário agendado para o laboratório. 
    2. Para a opção **Desligamento automático de máquinas virtuais**, especifique se deseja que a VM seja desligada automaticamente quando o usuário se desconectar. Você também poderá especificar por quanto tempo a VM deverá esperar até o usuário se reconectar antes do desligamento automático. Para obter mais informações, consulte [Habilitar o desligamento automático de VMs ao desconectar](how-to-enable-shutdown-disconnect.md).
    3. Em seguida, selecione **Concluir**. 

        ![Cota para cada usuário](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Você deverá ver a tela a seguir que mostra o status da criação da VM de modelo. A criação do modelo no laboratório leva até 20 minutos. 

    ![Status da criação da VM de modelo](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na página **Modelo**, execute as seguintes etapas: Essas etapas são **opcionais** para o tutorial.

    1. Conecte-se à VM modelo selecionando **Conectar**. Se for uma VM de modelo do Linux, você escolherá se deseja se conectar usando SSH ou a Área de Trabalho Remota da GUI.  É necessária uma configuração adicional para usar uma Área de Trabalho Remota da GUI. Confira [Habilitar a Área de Trabalho Remota gráfica para Máquinas Virtuais do Linux](how-to-use-remote-desktop-linux-student.md) para mais informações.
    1. Selecione **Redefinição de senha** para redefinir a senha da VM. 
    1. Instale e configure software em sua VM modelo. 
    1. **Pare** a VM.  
    1. Insira uma **descrição** do modelo
9.  Na página **Modelo**, selecione **Publicar** na barra de ferramentas. 

    ![Botão Publicar modelo](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Depois de publicar, você não pode cancelar a publicação. 
10. Na página **Publicar modelo**, insira o número de máquinas virtuais que deseja criar no laboratório e, em seguida, selecione **Publicar**. 

    ![Publicar modelo – número de VMs](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Você verá o **status de publicação** do modelo na página. Esse processo pode levar até uma hora. 

    ![Publicar modelo – andamento](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Alterne para a página **Pool de máquinas virtuais** selecionando Máquinas virtuais no menu esquerdo ou selecionando o bloco Máquinas virtuais. Confirme que você vê cinco máquinas virtuais no estado **Não atribuído**. Essas máquinas virtuais ainda não foram atribuídas aos alunos. Elas devem estar no estado **Parado**. Você pode iniciar a VM de um aluno, conectar-se à VM, parar a VM e excluir a VM nesta página. Você pode iniciá-los nesta página ou permitir que os alunos iniciem as máquinas virtuais. 

    ![Máquinas virtuais no estado parado](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Execute as seguintes tarefas nessa página (não execute estas etapas para o tutorial. Essas etapas são apenas para fins informativos): 
    
    1. Para alterar a capacidade do laboratório (número de VMs no laboratório), selecione **Capacidade do laboratório** na barra de ferramentas.
    2. Para iniciar todas as VMs de uma só vez, selecione **Iniciar todas** na barra de ferramentas. 
    3. Para iniciar uma VM específica, selecione a seta para baixo no **Status** e, em seguida, selecione **Iniciar**. Inicie também uma VM selecionando uma VM na primeira coluna e, em seguida, selecionando **Iniciar** na barra de ferramentas.                

### <a name="vm-sizes"></a>Tamanhos de VM  

| Tamanho | Núcleos | RAM | Descrição | 
| ---- | ----- | --- | ----------- | 
| Pequena | 2 | 3,5 GB | Este tamanho é mais adequado a linha de comando, abertura de navegador da Web, servidores Web de baixo tráfego e bancos de dados pequenos a médios. |
| Médio | 4 | 7 GB | Este tamanho é mais adequado a bancos de dados relacionais, cache na memória e análise | 
| Médio (Virtualização aninhada) | 4 | 16 GB | Este tamanho é mais adequado a bancos de dados relacionais, cache na memória e análise. Este tamanho também dá suporte à virtualização aninhada. <p>Este tamanho pode ser usado em cenários em que cada aluno precisa de várias VMs. Os educadores podem usar a virtualização aninhada para configurar algumas máquinas virtuais aninhadas de pequeno tamanho dentro da máquina virtual. </p> |
| GPU Pequena (Computação) | 6 | 56 GB | <p>Esse tamanho é mais adequado a aplicativos com uso intensivo de computação e de rede, como aplicativos de inteligência artificial e de aprendizado profundo.</p><p>O Azure Lab Services instala e configura automaticamente os drivers de GPU necessários quando você cria um laboratório com imagens de GPU. </p> | 
| GPU Pequena (Visualização) | 6 | 56 GB | Este tamanho é mais adequado a visualização remota, streaming, jogos e codificação usando estruturas como OpenGL e DirectX. | 
| grande | 8 | 16 GB | Este tamanho é mais adequado a aplicativos que precisam de CPUs mais rápidas, melhor desempenho do disco local, bancos de dados grandes, caches de memória grandes. |
| Grande (Virtualização aninhada) | 8 | 32 GB | Este tamanho é mais adequado a aplicativos que precisam de CPUs mais rápidas, melhor desempenho do disco local, bancos de dados grandes, caches de memória grandes. Este tamanho também dá suporte à virtualização aninhada. |  
| GPU Média (Visualização) | 12 | 112 GB | Este tamanho é mais adequado a visualização remota, streaming, jogos e codificação usando estruturas como OpenGL e DirectX. | 

> [!NOTE]
> Talvez você não veja alguns desses tamanhos de VM na lista ao criar um laboratório de sala de aula. A lista é preenchida com base na capacidade atual do local do laboratório. Se o criador da conta de laboratório [permitir que os criadores de laboratório escolham um local para ele ](allow-lab-creator-pick-lab-location.md), você poderá tentar escolher um local diferente para o laboratório e ver se o tamanho da VM está disponível. 

## <a name="view-all-labs"></a>Exibir todos os laboratórios

1. Navegue até [Portal do Azure Lab Services](https://labs.azure.com).
1. Selecione **Entrar**. Selecione ou insira uma **ID de usuário** que é um membro da função **Criador de laboratório** na conta do laboratório e insira a senha. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft. 

    [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]
1. Confirme se você vê todos os laboratórios na conta de laboratório selecionada. Na peça do laboratório, você verá o número de máquinas virtuais no laboratório e a cota de cada usuário (fora da hora agendada).

    ![Todos os laboratórios](./media/how-to-manage-classroom-labs/all-labs.png)
1. Use a lista suspensa na parte superior para selecionar uma conta de laboratório diferente. Você verá laboratórios na conta de laboratório selecionada. 

## <a name="delete-a-classroom-lab"></a>Excluir um laboratório de sala de aula

1. Na peça do laboratório, selecione três pontos (...) no canto e, em seguida, selecione **Excluir**. 

    ![Botão Excluir](./media/how-to-manage-classroom-labs/delete-button.png)
1. Na caixa de diálogo **Excluir laboratório**, selecione **Excluir** para continuar com a exclusão. 

## <a name="switch-to-another-classroom-lab"></a>Alternar para outro laboratório de sala de aula

Para alternar do atual laboratório de sala de aula para outro, selecione a lista suspensa de laboratórios na conta de laboratório na parte superior.

![Selecione o laboratório na lista suspensa na parte superior](./media/how-to-manage-classroom-labs/switch-lab.png)

Você também pode criar um novo laboratório usando o **Novo laboratório** nessa lista suspensa. 

> [!NOTE]
> Você também pode usar o módulo do PowerShell Az.LabServices (visualização) para gerenciar os laboratórios. Para obter mais informações, consulte a [página inicial do Az.LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Para alternar para uma conta de laboratório diferente, selecione a lista suspensa ao lado da conta de laboratório e selecione a outra conta de laboratório. 

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como usuário do laboratório, laboratórios de acesso](how-to-use-classroom-lab.md)

