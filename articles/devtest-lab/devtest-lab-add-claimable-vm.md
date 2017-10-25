---
title: "Adicionar uma VM declarável a um laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como adicionar uma máquina virtual declarável a um laboratório no Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: tarcher
ms.openlocfilehash: 98950d72e90b0e178bae2fffa7644fd824a25eea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Adicionar uma VM declarável a um laboratório no Azure DevTest Labs
Você adiciona uma VM declarável a um laboratório de maneira semelhante a como [adiciona uma VM padrão](devtest-lab-add-vm.md) – de uma *base* que é uma [imagem personalizada](devtest-lab-create-template.md), [fórmula](devtest-lab-manage-formulas.md), ou [imagem do Marketplace](devtest-lab-configure-marketplace-images.md). Este tutorial explica como usar o portal do Azure para adicionar uma VM declarável a um laboratório no DevTest Labs e mostra o processo que um usuário segue para declarar a VM.

> [!NOTE]
> Se você implantar VMs de laboratório por meio de [modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md), poderá criar VMs declaráveis definindo a propriedade **allowClaim** como verdadeiro na seção de propriedades.
>
>

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Etapas para adicionar uma VM declarável a um laboratório no Azure DevTest Labs
1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório no qual você deseja criar a VM de declaração.  
1. Na folha **Visão geral** do laboratório, selecione **+ Adicionar**.  

    ![Botão Adicionar VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Na folha **Escolher uma base** , selecione uma base para a VM.
1. Na folha **Máquina virtual**, insira um nome para a nova máquina virtual na caixa de texto **Nome da máquina virtual**.

    ![Folha VM de Laboratório](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Insira um **Nome de Usuário** que receberá privilégios de administrador na máquina virtual.  
1. Se você quiser usar uma senha armazenada em seu [repositório secreto](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), selecione **Usar um segredo salvo** e especifique um valor de chave que corresponda ao seu segredo (senha). Caso contrário, digite uma senha no campo de texto rotulado **Digite um valor**.
1. O **tipo de disco de máquina virtual** determina que tipo de disco de armazenamento é permitido para as máquinas virtuais no laboratório.
1. Selecione **Tamanho da máquina virtual** e selecione um dos itens predefinidos que especificam os núcleos de processador, o tamanho da RAM e o tamanho do disco rígido da VM a ser criada.
1. Selecione **Artefatos** e, na lista de artefatos, selecione e configure os artefatos que você deseja adicionar à imagem base. Se você for iniciante em Laboratórios de Desenvolvimento/Teste ou na configuração de artefatos, veja a seção [Adicionar um artefato existente a uma VM](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) e volte aqui quando terminar.
1. Selecione **Configurações avançadas** para configurar as opções de expiração e as opções de rede da VM. Em **Opções de declaração**, escolha **Sim** para tornar a máquina declarável.

  ![Opte por tornar a VM declarável.](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Se quiser exibir ou copiar o modelo do Azure Resource Manager, veja a seção [Salvar modelo do Azure Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) e retorne para cá quando terminar.
1. Selecione **Criar** para adicionar a VM especificada ao laboratório.
1. A folha do laboratório exibe o status da criação da VM; primeiro como **Criando** e como **Executando** após a inicialização da VM.


## <a name="using-a-claimable-vm"></a>Usando uma VM declarável

Um usuário pode declarar qualquer VM na lista de “Máquinas virtuais declaráveis” realizando uma destas etapas:

* Na lista de “Máquinas virtuais declaráveis” na parte inferior da folha Visão Geral do laboratório, clique com o botão direito do mouse em uma das VMs da lista e escolha **Declarar computador**.

 ![Solicite uma VM declarável específica.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Na parte superior da folha **Visão Geral**, escolha **Declarar qualquer um**. Uma máquina virtual aleatória é atribuída da lista de VMs declaráveis.

 ![Solicite uma VM declarável.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Depois que um usuário declara uma VM, ela é movida para cima em sua lista de “Minhas máquinas virtuais” e não é mais declarável por nenhum outro usuário.

## <a name="next-steps"></a>Próximas etapas
* Após a criação da VM, você poderá se conectar à VM selecionando **Conectar** na folha da VM.
* Explorar a [galeria de modelos de Início Rápido do Azure Resource Manager do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)
