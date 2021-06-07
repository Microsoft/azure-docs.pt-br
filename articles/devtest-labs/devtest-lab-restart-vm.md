---
title: Reiniciar uma VM em um laboratório no Azure DevTest Labs | Microsoft Docs
description: Este artigo fornece etapas para reiniciar de forma rápida e fácil as máquinas virtuais (VM) no Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d2bcbcbea613aa84da91789fb9f118afd7023fab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85481978"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Reiniciar uma VM em um laboratório no Azure DevTest Labs
Você pode reiniciar de forma rápida e fácil uma máquina virtual em DevTest Labs seguindo as etapas neste artigo. Considere o seguinte antes de reiniciar uma VM:

- A máquina virtual deve estar em execução para que o recurso de reinicialização seja habilitado.
- Se um usuário estiver conectado a uma VM em execução ao executar um reinício, deverá se reconectar à VM depois que ela for iniciada novamente.
- Se um artefato estiver sendo aplicado quando você reiniciar a máquina virtual, você receberá um aviso de que talvez o artefato não seja aplicado.

    ![Aviso ao reiniciar durante a aplicação de artefatos](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Se a máquina virtual estiver paralisada durante a aplicação de um artefato, você poderá reiniciar o recurso de VM como uma maneira potencial para resolver o problema.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Etapas para reiniciar uma VM em um laboratório no Azure DevTest Labs
1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório que inclui a VM que você deseja reiniciar.
1. No painel esquerdo, selecione **Minhas Máquinas Virtuais**.
1. Na lista de VMs, selecione uma VM em execução.
1. Na parte superior do painel de gerenciamento de VMs, selecione **Reiniciar**.

    ![Botão Reiniciar VM](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Monitore o status do reinício ao selecionar o ícone **Notificações** no canto superior direito da janela.

    ![Exibição do status do reinício da VM](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Você também pode reiniciar uma VM em execução ao selecionar seu botão de reticências (...) na lista **Minhas Máquinas Virtuais**.

![Reiniciar VM por meio das reticências](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Próximas etapas
* Uma vez reiniciada, será possível reconectar-se à VM selecionando **Conectar** em seu painel de gerenciamento.
* Explore a [Galeria de modelos de início rápido do DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
