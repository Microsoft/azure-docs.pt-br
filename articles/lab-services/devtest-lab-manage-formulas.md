---
title: Gerenciar fórmulas no Azure DevTest Labs para criar VMs | Microsoft Docs
description: Saiba como atualizar e remover fórmulas do Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: d8f2ae64e2f8e694de5a7cf5aa9049e63998dca0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60562426"
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

1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.

3. Na lista de laboratórios, selecione o laboratório desejado.  

4. Na página do laboratório, selecione **Fórmulas (bases reutilizáveis)**.
   
    ![Menu Fórmula](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. Na página **Fórmulas**, selecione **+ Adicionar**.
   
    ![Adicionar uma fórmula](./media/devtest-lab-create-formulas/add-formula.png)

6. Na página **Escolher uma base**, selecione a base (imagem personalizada, imagem do Marketplace ou fórmula) por meio da qual você deseja criar a fórmula.
   
    ![Lista de base](./media/devtest-lab-create-formulas/base-list.png)

7. Na guia **Configurações básicas** da página **Criar fórmula**, especifique os seguintes valores:
   
    * **Nome da fórmula** – digite um nome para a fórmula. Esse valor será exibido na lista de imagens de base quando você criar uma máquina virtual. O nome é validado durante a digitação e, se não servir, uma mensagem indicará os requisitos para um nome válido.
    * **Nome de usuário** – insira um nome de usuário para receber privilégios de administrador.
    * **Senha** – digite – ou selecione na lista suspensa – um valor associado ao segredo (senha) que você deseja usar para o usuário especificado. Para saber como salvar segredos em um cofre de chaves e usá-los ao criar recursos de laboratório, consulte [Store secrets in Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md) (Armazenar segredos no Azure Key Vault).
    * **Tamanho da VM** – selecione **Alterar tamanho** para alterar o tamanho da VM. 
    * **Artefatos** – selecione a página **Adicionar ou remover artefatos**, na qual você escolhe e configura os artefatos que deseja adicionar à imagem base. Para obter mais informações sobre os artefatos, consulte [Criar artefatos personalizados para a máquina virtual do Azure DevTest Labs](devtest-lab-artifact-author.md).
8. Alterne para a guia **Configurações avançadas** e especifique os seguintes valores:
    - **Rede virtual** – para alterar a rede virtual, selecione **Alterar Vnet**. 
    - **Sub-rede** – para alterar a sub-rede, selecione **Alterar Sub-rede**. 
    - **Configuração de endereço IP** -especifique se você deseja os endereços de IP compartilhados, privados ou públicos. Para obter mais informações sobre endereços IP compartilhados, consulte [Noções básicas dos endereços IP compartilhados no Azure DevTest Labs](./devtest-lab-shared-ip.md).
    - **Data e hora de validade** – especifique a data e a hora de validade para a VM para que ela seja excluída automaticamente. 
    - **Tornar essa máquina reivindicável** - tornar uma máquina "reivindicável" significa que ela não será propriedade de ninguém no momento da criação. Em vez disso, os usuários do laboratório poderão assumir a propriedade ("reivindicar") a máquina na página do laboratório.     
    - **Número de instâncias declaráveis** -especificar quantas instâncias declaráveis que você deseja criar. 
8. Selecione **Enviar** para criar a fórmula.

9. Após a criação da fórmula, ela é exibida na lista da página **Fórmulas**.

### <a name="create-a-formula-from-a-vm"></a>Criar uma fórmula de uma VM
As etapas a seguir o orientarão durante o processo de criação de uma fórmula com base em uma VM existente. 

> [!NOTE]
> Para criar uma fórmula de uma VM, a VM deve ter sido criada após 30 de março de 2016. 
> 
> 

1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
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

1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na página do laboratório, selecione **Fórmulas (bases reutilizáveis)**.
   
    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na página **Fórmulas do laboratório**, selecione a fórmula que deseja modificar.
6. Na página **Atualizar fórmula**, faça as edições desejadas e selecione **Atualizar**.

## <a name="delete-a-formula"></a>Excluir uma fórmula
Para excluir uma fórmula, siga estas etapas:

1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na página **Configurações**, selecione **Fórmulas**.
   
    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na página **Fórmulas do laboratório**, clique nas reticências à direita da fórmula que você deseja excluir.
   
    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. No menu de contexto da fórmula, selecione **Excluir**.
   
    ![Menu de contexto da Fórmula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Selecione **Sim** na caixa de diálogo de confirmação de exclusão.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas
* [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Próximas etapas
Depois de criar uma fórmula para uso durante a criação de uma VM, a próxima etapa será [adicionar uma VM ao seu laboratório](devtest-lab-add-vm.md).

