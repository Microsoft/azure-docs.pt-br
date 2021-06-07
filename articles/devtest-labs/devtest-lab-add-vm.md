---
title: Adicionar uma VM a um laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como usar o portal do Azure para adicionar uma máquina virtual a um laboratório no Azure DevTest Labs. Você pode escolher uma base que seja uma imagem personalizada ou uma fórmula.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07efcf93b963009494021b90e6c730c4615301ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91297616"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Adicionar uma VM a um laboratório no Azure DevTest Labs
Se você já tiver [criado sua primeira VM](tutorial-create-custom-lab.md#add-a-vm-to-the-lab), provavelmente isso foi feito por meio de uma [imagem do Marketplace](devtest-lab-configure-marketplace-images.md) pré-carregada. Agora, se quiser adicionar VMs subsequentes ao laboratório, você também poderá escolher uma *base* que seja uma [imagem personalizada](devtest-lab-create-template.md) ou uma [fórmula](devtest-lab-manage-formulas.md). Este tutorial orienta você pelo Portal do Azure para adicionar uma máquina virtual a um laboratório no DevTest Labs.

Este artigo também mostra como gerenciar os artefatos para uma VM em seu laboratório.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Etapas para adicionar uma VM a um laboratório no Azure DevTest Labs
1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços** e, em seguida, selecione **DevTest Labs** na seção **DEVOPS**. Se você selecionar * (estrela) próximo a **DevTest Labs** na seção **DEVOPS**. Essa ação adicionará o **DevTest Labs** no menu de navegação esquerdo para que você possa acessá-lo facilmente da próxima vez. Em seguida, será possível selecionar **DevTest Labs** no menu de navegação esquerdo.

    ![Todos os serviços – selecionar DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Na lista de laboratórios, selecione o laboratório no qual você deseja criar a VM.
2. Na página de **Visão Geral** do laboratório, selecione **+ Adicionar**.

    ![Botão Adicionar VM](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Na página **Escolher uma base**, selecione uma imagem do Marketplace para a VM.
1. Na guia **Configurações Básicas** da **Máquina Virtual**, execute as seguintes ações:
    1. Insira um nome para a VM na caixa de texto **nome da Máquina Virtual**. A caixa de texto é previamente preenchida para você, com um nome exclusivo gerado automaticamente. O nome corresponde ao nome de usuário no seu endereço de email, seguido por um número exclusivo de três dígitos. Esse recurso poupa o tempo de pensar em um nome de computador e digitá-lo sempre que você criar um computador. Se desejar, você poderá substituir esse campo preenchido automaticamente por um nome de sua escolha. Para substituir o nome preenchido automaticamente para a VM, insira um nome na caixa de texto **Nome da máquina virtual**.
    2. Insira um **Nome de Usuário** que receberá privilégios de administrador na máquina virtual. O **nome de usuário** para o computador é previamente preenchido com um nome exclusivo gerado automaticamente. O nome corresponde ao nome de usuário no seu endereço de email. Esse recurso poupa o tempo de escolher um nome de usuário sempre que você cria um novo computador. Mais uma vez, se quiser, você poderá substituir esse campo preenchido automaticamente por um nome de sua escolha. Para substituir o valor preenchido automaticamente para um nome de usuário, insira um valor na caixa de texto **Nome de Usuário**. Esse usuário recebe privilégios de **administrador** na máquina virtual.
    3. Se você estiver criando a primeira VM no laboratório, insira uma **senha** para o usuário. Para salvar essa senha como uma senha padrão no Azure Key Vault associado ao laboratório, selecione **Salvar como senha padrão**. A senha padrão é salva no cofre de chaves com o nome: **VmPassword**. Quando você tenta criar VMs subsequentes no laboratório, **VmPassword** é selecionada automaticamente para a **senha**. Para substituir o valor, desmarque a caixa de seleção **Usar um segredo salvo** e digite uma senha.

        ![Captura de tela que mostra a página "criar recurso de laboratório" com "configurações básicas" selecionadas e os valores de "configurações de usuário" inseridos.](./media/tutorial-create-custom-lab/new-virtual-machine.png)

        Você também pode salvar segredos no cofre de chaves primeiro e, em seguida, usá-lo ao criar uma VM no laboratório. Para obter mais informações, veja [Armazenar segredos em um cofre de chaves](devtest-lab-store-secrets-in-key-vault.md). Para usar uma senha armazenada em um cofre de chaves, selecione **Usar um segredo salvo** e especifique um valor de chave que corresponda ao seu segredo (senha).
    4. Na seção **Mais opções**, selecione **Alterar tamanho**. Selecione um dos itens predefinidos que especificam os núcleos do processador, o tamanho da RAM e o tamanho do disco rígido da VM a ser criada.
    5. Selecione **Adicionar ou Remover Artefatos**. Selecione e configure os artefatos que você quer adicionar à imagem base.
    **Observação:** se você for iniciante em Laboratórios de Desenvolvimento/Teste ou na configuração de artefatos, veja a seção [Adicionar um artefato existente a uma VM](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) e volte aqui quando terminar.
2. Alterne para a guia **Configurações Avançadas** na parte superior e execute as seguintes ações:
    1. Para alterar a rede virtual na qual está a VM, selecione **Alterar VNet**.
    2. Para alterar a sub-rede, selecione **Alterar sub-rede**.
    3. Especifique se o endereço IP da VM é **público, privado ou compartilhado**.
    4. Para excluir automaticamente a VM, especifique a **data e hora de validade**.
    5. Para tornar a VM requisitável por um usuário do laboratório, selecione **Sim** para a opção **Tornar esta máquina requisitável**.
    6. Especifique o número de **instâncias da VM** que você quer disponibilizar aos usuários do laboratório.

        ![Escolher uma base](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Selecione **Criar** para adicionar a VM especificada ao laboratório.

   A página do laboratório exibirá o status da criação da VM - primeiro como **Criando** e, em seguida, como **Em execução**, depois que a VM for iniciada.

    ![Status de criação da VM](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Adicionar um artefato existente a uma VM
Durante a criação de uma VM, você pode adicionar artefatos existentes. Cada laboratório inclui artefatos do Repositório Público de Artefatos de Laboratórios de Desenvolvimento/Teste, bem como artefatos criados e adicionados por você a seu próprio Repositório de Artefatos.

* Os *artefatos* do Azure DevTest Labs permitem que você especifique *ações* que são executadas quando a VM é provisionada, como executar scripts do Windows PowerShell, executando comandos Bash e instalar software.
* Os *parâmetros* de artefato permitem que você personalize o artefato para seu cenário específico.

Para saber como criar artefatos, confira o artigo [Aprenda a criar seus próprios artefatos para uso com Laboratórios de Desenvolvimento/Teste](devtest-lab-artifact-author.md).

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório contendo a VM com a qual você quer trabalhar.
1. Selecione **Minhas máquinas virtuais**.
1. Selecione a VM desejada.
1. Selecione **Gerenciar artefatos**.
1. Selecione **Aplicar artefatos**.
1. No painel **Aplicar artefatos**, selecione o artefato que você deseja adicionar à VM.
1. No painel **Adicionar artefatos**, insira os valores de parâmetro necessários e quaisquer parâmetros opcionais dos quais você precise.
1. Selecione **Adicionar** para adicionar o artefato e retornar ao painel **Aplicar artefatos**.
1. Continue adicionando artefatos à sua VM conforme o necessário.
1. Após adicionar os artefatos, você pode [alterar a ordem em que eles são executados](#change-the-order-in-which-artifacts-are-run). Você pode também voltar e [exibir ou modificar um artefato](#view-or-modify-an-artifact).
1. Quando você terminar de adicionar artefatos, selecione **Aplicar**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Alterar a ordem de execução dos artefatos
Por padrão, as ações dos artefatos são executadas na ordem em que eles são adicionados à VM.
As etapas a seguir ilustram como alterar a ordem de execução dos artefatos.

1. Na parte superior do painel **Aplicar artefatos**, selecione o link que indica o número de artefatos adicionados à VM.

    ![Número de artefatos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. No painel **Artefatos selecionados**, arraste e solte os artefatos na ordem desejada. **Observação:** se tiver problemas ao arrastar o artefato, verifique se está arrastando do lado esquerdo do artefato.
1. Selecione **OK** quando tiver concluído.

## <a name="view-or-modify-an-artifact"></a>exibir ou modificar um artefato
As etapas a seguir ilustram como exibir ou modificar os parâmetros de um artefato:

1. Na parte superior do painel **Aplicar artefatos**, selecione o link que indica o número de artefatos adicionados à VM.

    ![Número de artefatos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. No painel **Artefatos selecionados**, selecione o artefato que você deseja exibir ou editar.
1. No painel **Adicionar artefato**, faça as alterações necessárias e selecione **OK** para fechar o painel **Adicionar artefato**.
1. Selecione **OK** para fechar o painel **Artefatos selecionados**.

## <a name="save-azure-resource-manager-template"></a>Salvar modelo do Azure Resource Manager
Um modelo do Azure Resource Manager é uma forma declarativa de definir uma implantação repetível.
As etapas a seguir explicam como salvar o modelo do Azure Resource Manager para a VM que está sendo criada.
Depois de salvo, você pode usar o modelo do Azure Resource Manager para [implantar novas VMs com o Azure PowerShell](../azure-resource-manager/templates/overview.md).

1. No painel **Máquina Virtual**, selecione **Exibir Modelo do Azure Resource Manager**.
2. No painel **Exibir o modelo do Azure Resource Manager**, selecione o texto do modelo.
3. Copie o texto selecionado para a área de transferência.
4. Selecione **OK** para fechar o painel **Exibir Modelo do Azure Resource Manager**.
5. Abra um editor de texto.
6. Cole o texto do modelo da área de transferência.
7. Salve o arquivo para uso posterior.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
* Após a criação da VM, você poderá se conectar à VM selecionando **Conectar** no painel da VM.
* Saiba como [criar artefatos personalizados para sua VM do DevTest Labs](devtest-lab-artifact-author.md).
* Explore a [Galeria de modelos do DevTest Labs Azure Resource Manager guia de início rápido](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
