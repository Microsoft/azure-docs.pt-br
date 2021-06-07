---
title: Exibir e usar o modelo de Azure Resource Manager de uma máquina virtual
description: Saiba como usar o modelo do Azure Resource Manager de uma máquina virtual para criar outras VMs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fb7cb38dfb2c1664017f0e342fefecdd464df45f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696015"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Criar máquinas virtuais usando um modelo do Azure Resource Manager 

Quando você estiver criando uma máquina virtual (VM) em DevTest Labs por meio do [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040), você pode exibir o modelo do Azure Resource Manager antes de salvar a VM. O modelo, em seguida, pode ser usado como base para criar mais VMs do laboratório com as mesmas configurações.

Este artigo descreve várias VMs vs. modelos do Resource Manager de VM única e mostra como exibir e salvar um modelo ao criar uma VM.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Modelos do Resource Manager de várias VMs vs. de uma única VM
Há duas maneiras de criar VMs no DevTest Labs usando um modelo do Resource Manager: provisione o recurso Microsoft. DevTestLab/Labs/VirtualMachines ou provisione o recurso Microsoft. Compute/VirtualMachines. Cada um é usado em cenários diferentes e exige permissões diferentes.

- Modelos do Resource Manager que usam um tipo de recurso Microsoft.DevTestLab/labs/virtualmachines (conforme declarado na propriedade "recurso" no modelo) podem provisionar VMs de laboratório individuais. Nesse caso, cada VM aparece como um item individual na lista de máquinas virtuais do DevTest Labs:

   ![Captura de tela que mostra a lista de máquinas virtuais como itens únicos na lista de máquinas virtuais do DevTest Labs.](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Esse tipo de modelo do Resource Manager pode ser provisionado por meio do comando Azure PowerShell **New-AzResourceGroupDeployment** ou por meio do comando CLI do Azure **AZ Deployment Group Create**. Isso requer permissões de administrador, de modo que os usuários a quem uma função de usuário do DevTest Labs é atribuída não são capazes de realizar a implantação. 

- Modelos do Resource Manager que usam um tipo de recurso Microsoft.Compute/virtualmachines podem provisionar várias VMs como um único ambiente na lista de máquinas virtuais do DevTest Labs:

   ![Lista de VMs com itens individuais na lista de máquinas virtuais do DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   VMs no mesmo ambiente podem ser administradas conjuntamente e compartilham o mesmo ciclo de vida. Os usuários atribuídos a uma função de usuário do DevTest Labs podem criar ambientes usando esses modelos, contanto que o administrador tenha configurado o laboratório dessa forma.

O restante deste artigo discute modelos do Resource Manager que usam o Microsoft.DevTestLab / labs / virtualmachines. Eles são usados por administradores de laboratório para automatizar a criação de VMs de laboratório (por exemplo, VMs reivindicáveis) ou a geração de imagem dourada (por exemplo, a fábrica de imagem).

O artigo [Práticas recomendadas para criação de modelos do Azure Resource Manager](../azure-resource-manager/templates/template-best-practices.md) oferece muitas diretrizes e sugestões para lhe ajudar a criar modelos do Azure Resource Manager que são confiáveis e fáceis de usar.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Exibir e salvar o modelo do Azure Resource Manager de uma máquina virtual
1. Siga as etapas em [Criar sua primeira VM em um laboratório](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) para começar a criar uma máquina virtual.
1. Insira as informações necessárias para sua máquina virtual e adicione qualquer artefato que você desejar nessa VM.
1. Alterne para a guia **Configurações avançadas** . 
1. Na parte inferior da janela de configurações Configurar, escolha **Exibir modelo ARM**.
1. Copie e salve o modelo do Resource Manager para usar posteriormente para criar outra máquina virtual.

   ![Modelo do Resource Manager para salvar para uso posterior](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Depois de salvar o modelo do Resource Manager, você deve atualizar a seção de parâmetros do modelo antes de você poder usá-lo. Você pode criar um parameter.json que personaliza apenas os parâmetros fora do modelo real do Resource Manager. 

![Personalizar parâmetros usando um arquivo JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

O modelo do Resource Manager já está pronto para usar para [criar uma VM](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Definir a data de validade
Em cenários como treinamento, demonstrações e avaliações, talvez você queira criar máquinas virtuais e excluí-las automaticamente após uma duração fixa para que não incorra em custos desnecessários. Você pode criar uma VM de laboratório com uma data de expiração especificando a propriedade **expirationDate** para a VM. Confira o mesmo modelo do Resource Manager em [nosso repositório GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration).



### <a name="next-steps"></a>Próximas etapas
* Aprenda como [Criar ambientes de várias VMs com modelos do Resource Manager](devtest-lab-create-environment-from-arm.md).
* [Implantar um modelo do Resource Manager para criar uma VM](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Explore mais modelos do Resource Manager de início rápido para a automação do DevTest Labs do [repositório GitHub do Public DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
