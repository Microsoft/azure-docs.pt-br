---
title: Gerenciar fórmulas no Azure DevTest Labs para criar VMs | Microsoft Docs
description: Este artigo ilustra como criar uma fórmula de uma base (imagem personalizada, imagem do Marketplace ou outra fórmula) ou de uma máquina virtual existente.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f012a8c59a8e938334c3e1abc4c7b3ccd0e48d3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91308887"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Gerenciar fórmulas do Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Este artigo ilustra como criar uma fórmula de uma base (imagem personalizada, imagem do Marketplace ou outra fórmula) ou de uma máquina virtual existente. Este artigo também orienta você por meio do gerenciamento de fórmulas existentes.

## <a name="create-a-formula"></a>Criar uma fórmula
Qualquer pessoa com permissões de *Usuários* no DevTest Labs pode criar VMs usando uma fórmula como base. Existem duas maneiras de criar fórmulas: 

* De uma base – use quando quiser definir todas as características da fórmula.
* Com base em uma VM de laboratório existente: use quando desejar criar uma fórmula com base nas configurações de uma VM existente.

Para saber mais sobre como adicionar usuários e permissões, consulte [Adicionar proprietários e usuários no Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Criar uma fórmula de uma base
As etapas a seguir o orientarão no processo de criação de uma fórmula usando uma imagem personalizada, uma imagem do Marketplace ou outra fórmula.

1. Entre no [portal do Azure](https://portal.azure.com).

2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.

3. Na lista de laboratórios, selecione o laboratório desejado.  

4. Na página do laboratório, selecione **fórmulas (bases reutilizáveis)** no menu à esquerda.
5. Na página **Fórmulas**, selecione **+ Adicionar**.
   
    ![Adicionar uma fórmula](./media/devtest-lab-create-formulas/add-formula.png)
6. Na página **escolher uma base** , selecione a base (imagem personalizada, imagem do Marketplace ou imagem da Galeria de imagens compartilhada) da qual você deseja criar a fórmula.

    :::image type="content" source="./media/devtest-lab-create-formulas/select-base.png" alt-text="Selecionar a imagem base&quot;:::
1. Na guia **Configurações básicas** da página **Criar fórmula**, especifique os seguintes valores:
   
    * **Nome da fórmula** – digite um nome para a fórmula. Esse valor será exibido na lista de imagens de base quando você criar uma máquina virtual. O nome é validado durante a digitação e, se não servir, uma mensagem indicará os requisitos para um nome válido.
    - Insira uma **Descrição** opcional para a fórmula. 
    * **Nome de usuário** – insira um nome de usuário para receber privilégios de administrador.
    * **Senha** – digite – ou selecione na lista suspensa – um valor associado ao segredo (senha) que você deseja usar para o usuário especificado. Para saber como salvar segredos em um cofre de chaves e usá-los ao criar recursos de laboratório, consulte [Store secrets in Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md) (Armazenar segredos no Azure Key Vault).

        Selecione **usar um segredo salvo** se desejar usar um segredo de Azure Key Vault em vez de usar uma senha. 
    * **Tamanho da máquina virtual** – selecione **alterar tamanho** para alterar o tamanho da VM. 
    - **Tipo de disco do so** -selecione o tipo de disco que você deseja usar (HDD Standard, SSD Standard ou SSD Premium).
    * **Artefatos** – selecione a página **Adicionar ou remover artefatos**, na qual você escolhe e configura os artefatos que deseja adicionar à imagem base. Para obter mais informações sobre os artefatos, consulte [Criar artefatos personalizados para a máquina virtual do Azure DevTest Labs](devtest-lab-artifact-author.md).

        ![Página configurações básicas](./media/devtest-lab-create-formulas/basic-settings.png)
8. Alterne para a guia **Configurações avançadas** e especifique os seguintes valores:
    - **Rede virtual** – para alterar a rede virtual, selecione **Alterar Vnet**. 
    - **Sub-rede** – para alterar a sub-rede, selecione **Alterar Sub-rede**. 
    - **Configuração de endereço IP** -especifique se você deseja os endereços de IP compartilhados, privados ou públicos. Para obter mais informações sobre endereços IP compartilhados, consulte [Noções básicas dos endereços IP compartilhados no Azure DevTest Labs](./devtest-lab-shared-ip.md).
    - **Data e hora de expiração** -você não pode editar este campo. 
    - **Tornar essa máquina reivindicável** - tornar uma máquina &quot;reivindicável" significa que ela não será propriedade de ninguém no momento da criação. Em vez disso, os usuários do laboratório poderão assumir a propriedade ("reivindicar") a máquina na página do laboratório.  

        ![Captura de tela que mostra as configurações avançadas da página "criar fórmula (base reutilizável)".](./media/devtest-lab-create-formulas/advanced-settings.png)
    - Se você selecionou uma imagem da Galeria de imagens compartilhada como base, você também verá o campo **versão da imagem** que permite selecionar a versão da imagem da galeria que você deseja usar como base. 

        ![Página Configurações avançadas](./media/devtest-lab-create-formulas/advanced-settings-shared-image-gallery.png)
8. Selecione **Enviar** para criar a fórmula.

9. Após a criação da fórmula, ela é exibida na lista da página **Fórmulas**.

### <a name="create-a-formula-from-a-vm"></a>Criar uma fórmula de uma VM
As etapas a seguir o orientarão durante o processo de criação de uma fórmula com base em uma VM existente. 

> [!NOTE]
> Para criar uma fórmula de uma VM, a VM deve ter sido criada após 30 de março de 2016. 
> 
> 

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na página **Visão geral** do laboratório, selecione a VM com base na qual você deseja criar a fórmula.
   
    ![VMs do Labs](./media/devtest-lab-create-formulas/my-vms.png)
5. Na página da VM, selecione **Criar fórmula (base reutilizável)**.
   
    ![Criar fórmula](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Na página **Criar fórmula**, digite um **Nome** e uma **Descrição** para sua nova fórmula.
   
    ![Página Criar fórmula](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Selecione **OK** para criar a fórmula.

## <a name="modify-a-formula"></a>Modificar uma Fórmula
Para modificar uma fórmula, siga estas etapas:

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na página do laboratório, selecione **Fórmulas (bases reutilizáveis)**.
   
    ![Captura de tela que mostra a página do laboratório com "fórmulas (bases reutilizáveis)" selecionada.](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na página **Fórmulas do laboratório**, selecione a fórmula que deseja modificar.
6. Na página **Atualizar fórmula**, faça as edições desejadas e selecione **Atualizar**.

## <a name="delete-a-formula"></a>Excluir uma fórmula
Para excluir uma fórmula, siga estas etapas:

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na página **Configurações**, selecione **Fórmulas**.
   
    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na página **Fórmulas do laboratório**, clique nas reticências à direita da fórmula que você deseja excluir.
   
    ![Captura de tela que mostra a página "fórmulas de laboratório" com as reticências para as opções de fórmula realçadas.](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. No menu de contexto da fórmula, selecione **Excluir**.
   
    ![Menu de contexto da Fórmula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Selecione **Sim** na caixa de diálogo de confirmação de exclusão.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas
* [Imagens personalizadas ou fórmulas?](devtest-lab-faq.md#what-is-the-difference-between-a-custom-image-and-a-formula)

## <a name="next-steps"></a>Próximas etapas
Depois de criar uma fórmula para uso durante a criação de uma VM, a próxima etapa será [adicionar uma VM ao seu laboratório](devtest-lab-add-vm.md).

