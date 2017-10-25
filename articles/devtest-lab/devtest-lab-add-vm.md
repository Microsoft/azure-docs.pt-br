---
title: "Adicionar uma VM a um laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como adicionar uma máquina virtual a um laboratório no Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
ms.openlocfilehash: 449bffb040dafc8edd0b8b0afd80dbea35cd28ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Adicionar uma VM a um laboratório no Azure DevTest Labs
Se você já tiver [criado sua primeira VM](devtest-lab-create-first-vm.md), provavelmente isso foi feito por meio de uma [imagem do Marketplace](devtest-lab-configure-marketplace-images.md) pré-carregada. Agora, se quiser adicionar VMs subsequentes ao laboratório, você também poderá escolher uma *base* que seja uma [imagem personalizada](devtest-lab-create-template.md) ou uma [fórmula](devtest-lab-manage-formulas.md). Este tutorial orienta você pelo Portal do Azure para adicionar uma máquina virtual a um laboratório no DevTest Labs.

Este artigo também mostra como gerenciar os artefatos para uma VM em seu laboratório.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Etapas para adicionar uma VM a um laboratório no Azure DevTest Labs
1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório no qual você deseja criar a VM.  
1. Na folha **Visão geral** do laboratório, selecione **+ Adicionar**.  

    ![Botão Adicionar VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Na folha **Escolher uma base** , selecione uma base para a VM.
1. Na folha **Máquina virtual**, insira um nome para a nova máquina virtual na caixa de texto **Nome da máquina virtual**.

    ![Folha VM de Laboratório](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Insira um **Nome de Usuário** que receberá privilégios de administrador na máquina virtual.  
1. Se você quiser usar uma senha armazenada em seu [repositório secreto](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), selecione **Usar um segredo salvo** e especifique um valor de chave que corresponda ao seu segredo (senha). Caso contrário, digite uma senha no campo de texto rotulado **Digite um valor**.
1. O **tipo de disco de máquina virtual** determina que tipo de disco de armazenamento é permitido para as máquinas virtuais no laboratório.
1. Selecione **Tamanho da máquina virtual** e selecione um dos itens predefinidos que especificam os núcleos de processador, o tamanho da RAM e o tamanho do disco rígido da VM a ser criada.
1. Selecione **Artefatos** e, na lista de artefatos, selecione e configure os artefatos que você deseja adicionar à imagem base.
    **Observação:** se você for iniciante em Laboratórios de Desenvolvimento/Teste ou na configuração de artefatos, veja a seção [Adicionar um artefato existente a uma VM](#add-an-existing-artifact-to-a-vm) e volte aqui quando terminar.
1. Selecione **Configurações avançadas** para configurar as opções de expiração e as opções de rede da VM. 

   Para definir uma opção de expiração, escolha o ícone de calendário para especificar uma data em que a VM será excluída automaticamente.  Por padrão, a VM nunca expirará. 
1. Se quiser exibir ou copiar o modelo do Azure Resource Manager, veja a seção [Salvar modelo do Azure Resource Manager](#save-azure-resource-manager-template) e retorne para cá quando terminar.
1. Selecione **Criar** para adicionar a VM especificada ao laboratório.
1. A folha do laboratório exibe o status da criação da VM; primeiro como **Criando** e como **Executando** após a inicialização da VM.

> [!NOTE]
> [Adicionar uma VM declarável](devtest-lab-add-claimable-vm.md) mostra como tornar a VM declarável para que ela esteja disponível para uso por qualquer usuário no laboratório.
>
>

## <a name="add-an-existing-artifact-to-a-vm"></a>Adicionar um artefato existente a uma VM
Durante a criação de uma VM, você pode adicionar artefatos existentes. Cada laboratório inclui artefatos do Repositório Público de Artefatos de Laboratórios de Desenvolvimento/Teste, bem como artefatos criados e adicionados por você a seu próprio Repositório de Artefatos.

* Os *artefatos* do Azure DevTest Labs permitem que você especifique *ações* que são executadas quando a VM é provisionada, como executar scripts do Windows PowerShell, executando comandos Bash e instalar software.
* Os *parâmetros* de artefato permitem que você personalize o artefato para seu cenário específico.

Para saber como criar artefatos, confira o artigo [Aprenda a criar seus próprios artefatos para uso com Laboratórios de Desenvolvimento/Teste](devtest-lab-artifact-author.md).

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório contendo a VM com a qual você quer trabalhar.  
1. Selecione **Minhas máquinas virtuais**.
1. Selecione a VM desejada.
1. Selecione **Artefatos**. 
1. Selecione **Aplicar artefatos**.
1. Na folha **Aplicar artefatos**, selecione o artefato que você deseja adicionar à VM.
1. Na folha **Adicionar artefatos**, insira os valores de parâmetro necessários e quaisquer parâmetros opcionais dos quais você precise.  
1. Selecione **Adicionar** para adicionar o artefato e retornar à folha **Aplicar artefatos**.
1. Continue adicionando artefatos à sua VM conforme o necessário.
1. Após adicionar os artefatos, você pode [alterar a ordem em que eles são executados](#change-the-order-in-which-artifacts-are-run). Você pode também voltar e [exibir ou modificar um artefato](#view-or-modify-an-artifact).
1. Quando você terminar de adicionar artefatos, selecione **Aplicar**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Alterar a ordem de execução dos artefatos
Por padrão, as ações dos artefatos são executadas na ordem em que eles são adicionados à VM. As etapas a seguir ilustram como alterar a ordem de execução dos artefatos.

1. Na parte superior da folha **Aplicar artefatos**, selecione o link que indica o número de artefatos adicionados à VM.
   
    ![Número de artefatos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Na folha **Artefatos selecionados**, arraste e solte os artefatos na ordem desejada. **Observação:** se tiver problemas ao arrastar o artefato, verifique se está arrastando do lado esquerdo do artefato. 
1. Selecione **OK** quando tiver concluído.  

## <a name="view-or-modify-an-artifact"></a>exibir ou modificar um artefato
As etapas a seguir ilustram como exibir ou modificar os parâmetros de um artefato:

1. Na parte superior da folha **Aplicar artefatos**, selecione o link que indica o número de artefatos adicionados à VM.
   
    ![Número de artefatos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Na folha **Artefatos selecionados**, selecione o artefato que você deseja exibir ou editar.  
1. Na folha **Adicionar artefato**, faça as alterações necessárias e selecione **OK** para fechar a folha **Adicionar artefato**.
1. Selecione **OK** para fechar a folha **Artefatos selecionados**.

## <a name="save-azure-resource-manager-template"></a>Salvar modelo do Azure Resource Manager
Um modelo do Azure Resource Manager é uma forma declarativa de definir uma implantação repetível. As etapas a seguir explicam como salvar o modelo do Azure Resource Manager para a VM que está sendo criada.
Depois de salvo, você pode usar o modelo do Azure Resource Manager para [implantar novas VMs com o Azure PowerShell](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. Na folha **Máquina virtual**, selecione **Exibir Modelo de ARM**.
2. Na **folha Exibir o modelo do Azure Resource Manager**, selecione o texto do modelo.
3. Copie o texto selecionado para a área de transferência.
4. Selecione **OK** para fechar a **folha Exibir Modelo do Azure Resource Manager**.
5. Abra um editor de texto.
6. Cole o texto do modelo da área de transferência.
7. Salve o arquivo para uso posterior.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Próximas etapas
* Após a criação da VM, você poderá se conectar à VM selecionando **Conectar** na folha da VM.
* Saiba como [criar artefatos personalizados para sua VM do DevTest Labs](devtest-lab-artifact-author.md).
* Explore a [galeria de modelos de Início Rápido do Azure Resource Manager do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
