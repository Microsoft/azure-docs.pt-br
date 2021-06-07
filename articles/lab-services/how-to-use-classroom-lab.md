---
title: Como acessar um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Saiba como registrar-se em um laboratório de sala de aula, exibir todos os laboratórios que você pode acessar, iniciar/parar uma VM no laboratório e conectar-se à VM.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 6d5585082473a363df5f25f331475536544ae828
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96433131"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Como acessar um laboratório de sala de aula no Azure Lab Services
Este artigo descreve como se registrar em um laboratório de sala de aula, exibir todos os laboratórios que você pode acessar, iniciar/interromper uma VM no laboratório e se conectar a ela. 

## <a name="register-to-the-lab"></a>Registrar-se no laboratório

1. Navegue até a **URL de registro** recebida do educador. Não é necessário usar a URL de registro após concluir o registro. Em vez disso, use a URL: [https://labs.azure.com](https://labs.azure.com). Ainda não há suporte para o Internet Explorer 11. 

    ![Registrar-se no laboratório](./media/tutorial-connect-vm-in-classroom-lab/register-lab.png)
1. Entre no serviço usando sua conta da escola para concluir o registro. 

    > [!NOTE]
    > Uma conta Microsoft é necessária para usar o Azure Lab Services. Se estiver tentando usar sua conta não Microsoft, como as contas do Yahoo ou do Google, para entrar no portal, siga as instruções para criar uma conta Microsoft que será vinculada à sua conta não Microsoft. Em seguida, siga as etapas para concluir o processo de registro. 
1. Depois de registrado, confirme que consegue ver a máquina virtual do laboratório ao qual você tem acesso. 

    ![VMs acessíveis](./media/tutorial-connect-vm-in-classroom-lab/accessible-vms.png)
1. Aguarde até a máquina virtual ficar pronta. No bloco da VM, observe os seguintes campos:
    1. Na parte superior do bloco, você verá o **nome do laboratório**.
    1. À direita, você verá o ícone que representa o **SO (sistema operacional)** da VM. Neste exemplo, é o sistema operacional Windows. 
    1. Você verá os ícones/os botões na parte inferior do bloco para iniciar/interromper a VM e se conectar a ela. 
    1. À direita dos botões, você verá o status da VM. Confirme se o status da VM mostrado está **Interrompida**.

        ![VM no estado Interrompido](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>Iniciar ou interromper a VM
1. **Inicie** a VM selecionando o primeiro botão, conforme mostrado na imagem a seguir. Esse processo demora um pouco.  

    ![Iniciar a VM](./media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Confirme se o status da VM está definido como **Em execução**. 

    ![VM no estado de execução](./media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Observe que o ícone do primeiro botão foi alterado para representar uma operação de **interrupção**. Selecione esse botão para interromper a VM. 

## <a name="connect-to-the-vm"></a>Conectar-se à VM

1. Selecione o segundo botão, conforme mostrado na imagem a seguir, para se **conectar** à VM do laboratório. 

    ![Conectar-se a uma VM](./media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Siga uma das etapas a seguir: 
    1. Para máquinas virtuais do **Windows**, salve o arquivo **RDP** no disco rígido. Abra o arquivo RDP para conectar a máquina virtual. Use o **nome de usuário** e a **senha** obtidos com seu educador para entrar no computador. 
    3. Para máquinas virtuais do **Linux**, você pode usar **SSH** ou **RDP** (se habilitado) para se conectar a elas. Para obter mais informações, veja [Habilitar conexão de área de trabalho remota para computadores Linux](how-to-enable-remote-desktop-linux.md). 
    1. Se estiver usando um **Mac** para se conectar à VM do laboratório, siga as instruções na próxima seção. 

## <a name="progress-bar"></a>Barra de progresso 
A barra de progresso no bloco mostra o número de horas usadas em relação ao número de [horas de cota](how-to-configure-student-usage.md#set-quotas-for-users) atribuídas a você. Esse tempo é o tempo adicional alocado para você, além do horário agendado para o laboratório. A cor da barra de progresso e o texto sob a barra de progresso variam de acordo com os seguintes cenários:

- Se uma aula estiver em andamento (dentro do agendamento da aula), a barra de progresso ficará esmaecida para representar as horas de cotas que não estão sendo usadas. 

    ![Barra de progresso na cor cinza](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Se uma cota não for atribuída (zero hora), o texto **Disponível somente durante as aulas** será mostrado no lugar da barra de progresso. 
    
    ![Status quando nenhuma cota é definida](./media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Se você ficou **sem cota**, a cor da barra de progresso ficará **vermelha**. 

    ![Barra de progresso na cor vermelha](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- A cor da barra de progresso é **azul** nas horas fora do horário agendado para o laboratório e um pouco do tempo de cota foi usado. 

    ![Barra de progresso na cor azul](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-labs"></a>Exibir todos os laboratórios
Depois de se registrar nos laboratórios, você pode exibir todos os laboratórios executando as seguintes etapas: 

1. Navegue até [https://labs.azure.com](https://labs.azure.com). Ainda não há suporte para o Internet Explorer 11. 
2. Entre no serviço usando a conta de usuário usada para se registrar no laboratório. 
3. Confirme que você vê todos os laboratórios aos quais tem acesso. 

    ![Exibir todos os laboratórios](./media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
 