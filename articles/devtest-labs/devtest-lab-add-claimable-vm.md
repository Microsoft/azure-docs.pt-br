---
title: Criar e gerenciar VMs declaráveis no Azure DevTest Labs | Microsoft Docs
description: Saiba como usar o portal do Azure para adicionar uma máquina virtual declarável no Azure DevTest Labs e ver os processos a seguir para reivindicar/cancelar a declaração de uma máquina virtual.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1fe949d7104fe6437087e77c403cc0d921ebd025
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88270971"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Criar e gerenciar VMs requisitáveis no Azure DevTest Labs
Você adiciona uma VM declarável a um laboratório de maneira semelhante a como [adiciona uma VM padrão](devtest-lab-add-vm.md) – de uma *base* que é uma [imagem personalizada](devtest-lab-create-template.md), [fórmula](devtest-lab-manage-formulas.md), ou [imagem do Marketplace](devtest-lab-configure-marketplace-images.md). Este tutorial explica como usar o Portal do Azure para adicionar uma VM requisitável a um laboratório no DevTest Labs e mostra o processo que um usuário segue para declarar a VM e cancelar sua declaração.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Etapas para adicionar uma VM declarável a um laboratório no Azure DevTest Labs
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
3. Selecione **Criar** para adicionar a VM especificada ao laboratório.

   A página do laboratório exibirá o status da criação da VM - primeiro como **Criando** e, em seguida, como **Em execução**, depois que a VM for iniciada.

> [!NOTE]
> Se você implantar VMs de laboratório por meio dos  [modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md), poderá criar VMs exigíveis definindo a propriedade **allowClaim** como true na seção de propriedades.


## <a name="using-a-claimable-vm"></a>Usando uma VM declarável

Um usuário pode declarar qualquer VM na lista de “Máquinas virtuais declaráveis” realizando uma destas etapas:

* Na lista de “Máquinas virtuais requisitáveis” na parte inferior d painel Visão geral do laboratório, clique com o botão direito do mouse em uma das VMs da lista e escolha **Declarar computador**.

  ![Solicite uma VM declarável específica.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Na parte superior do painel “Visão geral”, escolha **Declarar qualquer uma**. Uma máquina virtual aleatória é atribuída da lista de VMs declaráveis.

  ![Solicite uma VM declarável.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Depois que um usuário alegar uma VM, o DevTest Labs iniciará o computador e o moverá para a lista de "minhas máquinas virtuais" do usuário do laboratório. Isso significa que o usuário do laboratório agora terá privilégios de proprietário neste computador. O tempo necessário para esta etapa pode variar dependendo dos horários de inicialização, bem como quaisquer outras ações personalizadas sendo executadas durante o evento de declaração. Depois de reivindicado, o computador não está mais disponível no pool de declaração.  

## <a name="unclaim-a-vm"></a>Cancelar a declaração de uma VM

Quando um usuário termina de usar uma VM declarada e deseja disponibilizá-la para outra pessoa, ele pode retornar a VM declarada à lista de máquinas virtuais requisitáveis seguindo uma destas etapas:

- Na lista "Minhas máquinas virtuais", clique com o botão direito do mouse em uma das VMs na lista – ou selecione o botão as reticências (...) – e escolha **Cancelar declaração**.

  ![Cancelar declaração de uma VM na lista de VM.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- Na lista "Minhas máquinas virtuais", selecione uma VM para abrir seu painel de gerenciamento e, em seguida, selecione **Cancelar declaração** na barra de menus superior.

  ![Cancele a declaração de uma VM no painel de gerenciamento da VM.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Quando um usuário não alega uma VM, ele não tem mais permissões de proprietário para essa VM de laboratório específica e está disponível para ser reivindicado por qualquer outro usuário de laboratório no estado que foi retornado ao pool. 

### <a name="transferring-the-data-disk"></a>Transferindo o disco de dados
Se uma VM requisitável tiver um disco de dados anexado a ela e um usuário cancelar a sua requisição, o disco de dados permanecerá com a VM. Quando outro usuário, então, requisitar essa VM, esse novo usuário requisitará o disco de dados assim como a VM.

Isso é conhecido como "transferência do disco de dados". O disco de dados torna-se disponível na lista **Meus discos de dados** do novo usuário para que ele o gerencie.

![Cancele a requisição dos discos de dados.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Próximas etapas
* Após sua criação, é possível conectar-se à VM selecionando **Conectar** em seu painel de gerenciamento.
* Explore a [Galeria de modelos do DevTest Labs Azure Resource Manager guia de início rápido](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
