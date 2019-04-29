---
title: Habilitar a ambientes de várias VMs no Azure Lab Services | Microsoft Docs
description: Saiba como criar um ambiente com várias máquinas virtuais em uma máquina virtual de modelo em um laboratório de sala de aula do Azure Lab Services.
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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 6faf32232c42f863bff52fdfb3c0714aee8e9b88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702380"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Criar um ambiente com várias VMs em um modelo de VM de um laboratório de sala de aula
Atualmente o Azure Lab Services permite que você configurar a máquina virtual de um modelo em um laboratório e disponibilizar uma cópia única para cada um dos seus usuários. Mas se você for um professor ensina a uma classe IT sobre como configurar firewalls ou servidores, você talvez precise fornecer cada um dos seus alunos com um ambiente em que várias máquinas virtuais podem se comunicar entre si em uma rede.

A virtualização aninhada permite que você crie um ambiente de várias VMs dentro de máquina de virtual do modelo de um laboratório. Publicar o modelo fornece a cada usuário no laboratório com uma máquina virtual configurada com várias VMs dentro dele.

## <a name="what-is-nested-virtualization"></a>O que é virtualização aninhada?
A virtualização aninhada permite que você crie máquinas virtuais dentro de uma máquina virtual. A virtualização aninhada é feita por meio do Hyper-V e só está disponível em VMs do Windows.

Para obter mais informações sobre a virtualização aninhada, consulte os seguintes artigos:

- [Virtualização aninhada no Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Como habilitar a virtualização aninhada em uma VM do Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Usar a virtualização aninhada no Azure Lab Services
As etapas importantes são:

1. Criar uma **grande** dimensionado **Windows** máquina de modelo para o laboratório. 
2. Conectar-se a ele e [habilitar a virtualização aninhada](../../virtual-machines/windows/nested-virtualization.md).


O procedimento a seguir fornece as etapas detalhadas: 

1. Se você ainda não tiver um, crie uma conta de laboratório. Para obter instruções, consulte [Tutorial: Configurar uma conta de laboratório com o Azure Lab Services](tutorial-setup-lab-account.md).
2. Navegue até [Site do Azure Lab Services](https://labs.azure.com). 
3. Selecione **Iniciar sessão** e insira suas credenciais. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft. 
4. Na janela **Novo laboratório**, execute as seguintes ações: 
    1. Especifique um **nome** para o laboratório. 
    2. Especifique o **número máximo de máquinas virtuais** no laboratório. É possível aumentar ou diminuir o número de VMs após criar o laboratório ou em um laboratório existente. Para saber mais, confira [Update number of VMs in a lab](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab) (Atualizar número de VMs em um laboratório)
    6. Clique em **Salvar**.

        ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na página **Selecionar especificações da máquina virtual**, siga estas etapas:
    1. Selecione **grande** para o tamanho das máquinas virtuais (VMs) a ser criado no laboratório. Atualmente, apenas o tamanho grande dá suporte a virtualização aninhada.
    2. Escolha uma imagem de máquina virtual que é um **imagem do Windows**. A virtualização aninhada só está disponível em computadores Windows. 
    3. Selecione **Avançar**.

        ![Definir as especificações da VM](../media/how-to-enable-multi-vm-environment/large-windows-vm.png)    
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
8. Sobre o **configurar modelo** página, selecione **Connect** para se conectar ao modelo de VM para configurar a virtualização aninhada. Você também pode configurar posteriormente depois de concluir as etapas neste assistente. 
9. Dentro da máquina virtual de modelo, configurar a virtualização aninhada e configurar uma rede virtual com várias máquinas virtuais. Para obter instruções passo a passo, consulte [como habilitar a virtualização aninhada em uma VM do Azure](../../virtual-machines/windows/nested-virtualization.md). Aqui está um resumo rápido das etapas: 
    1. Habilite o recurso Hyper-V na máquina virtual do modelo.
    2. Configurar uma rede virtual interna com conectividade de internet para as máquinas virtuais aninhadas
    3. Criar máquinas virtuais por meio do Gerenciador do Hyper-V
    4. Atribuir um endereço IP para as máquinas virtuais
10. Selecione **Avançar** na página do modelo. 
11. Na página **Publicar o modelo**, execute as seguintes ações. 
    1. Publicar o modelo imediatamente e selecionar **Publicar**.  

        > [!WARNING]
        > Depois de publicar, você não pode cancelar a publicação. 
    2. Para publicar mais tarde, selecione **Salvar para mais tarde**. É possível publicar a VM modelo após a conclusão do assistente. Para obter detalhes de como configurar e publicar após a conclusão do assistente, confira a seção [Publicar o modelo](how-to-create-manage-template.md#publish-the-template-vm) no artigo [Como gerenciar laboratórios de sala de aula](how-to-manage-classroom-labs.md).

        ![Publicar modelo](../media/how-to-enable-multi-vm-environment/publish-template-page.png)
11. Você vê o **andamento da publicação** do modelo. Esse processo pode levar até uma hora. 

    ![Publicar modelo – andamento](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Você verá a página a seguir quando o modelo for publicado com êxito. Selecione **Concluído**.

    ![Publicar modelo – êxito](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Você verá o **painel** do laboratório. 
    
    ![Painel de laboratório de sala de aula](../media/how-to-enable-multi-vm-environment/dashboard.png)


## <a name="next-steps"></a>Próximas etapas

Agora, cada usuário obtém uma única máquina virtual que inclui um ambiente de várias VMs dentro dele. Para saber como adicionar usuários ao laboratório e enviar um link de registro a eles, consulte o artigo a seguir: [Adicionar usuários ao laboratório](tutorial-setup-classroom-lab.md#add-users-to-the-lab).