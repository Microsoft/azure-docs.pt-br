---
title: Armazenar segredos em um cofre de chaves no Azure DevTest Labs | Microsoft Docs
description: Saiba como armazenar segredos em um Azure Key Vault e usá-los ao criar uma VM, fórmula ou ambiente.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5714279ef183cb930d643575466dae3d6cb69bba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481639"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Armazenar segredos em um cofre de chaves no Azure DevTest Labs
Talvez seja necessário inserir um segredo complexo ao usar o Azure DevTest Labs: a senha para sua VM do Windows, a chave SSH pública para sua VM do Linux ou o token de acesso pessoal para clonar o repositório do Git por meio de um artefato. Segredos geralmente são longos e têm caracteres aleatórios. Portanto, inseri-los pode ser complicado e inconveniente, especialmente se você usar o mesmo segredo várias vezes.

Para resolver esse problema e também manter seus segredos em um local seguro, o DevTest Labs dá suporte ao armazenamento de segredos em um [Azure Key Vault](../key-vault/general/overview.md). Quando um usuário salva um segredo pela primeira vez, o serviço DevTest Labs cria automaticamente um cofre de chaves no mesmo grupo de recursos que contém o laboratório e armazena o segredo no cofre de chaves. O DevTest Labs cria um cofre de chaves separado para cada usuário. 

Observe que o usuário do laboratório precisará primeiro criar uma máquina virtual de laboratório antes de poder criar um segredo no cofre de chaves. Isso ocorre porque o DevTest Lab Service precisa associar o usuário do laboratório a um documento de usuário válido antes de poder criar e armazenar segredos em seu cofre de chaves. 


## <a name="save-a-secret-in-azure-key-vault"></a>Salvar um segredo no Azure Key Vault
Para salvar seu segredo no Azure Key Vault, siga as etapas a seguir:

1. Selecione **Meus segredos** no menu esquerdo.
2. Insira um **nome** para o segredo. Você vê esse nome na lista suspensa ao criar uma VM, fórmula ou um ambiente. 
3. Insira o segredo como o **valor**.

    ![Armazenar segredo](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Usar um segredo do Azure Key Vault
Quando você precisa inserir um segredo para criar uma VM, fórmula ou ambiente, você pode inserir um segredo manualmente ou selecione um segredo salvo do cofre de chaves. Para usar um segredo armazenado no cofre de chaves, execute as seguintes ações:

1. Selecione **Usar um segredo salvo**. 
2. Selecione o segredo na lista suspensa para **Escolher um segredo**. 

    ![Usar o segredo na VM](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Usar um segredo em um modelo do Azure Resource Manager
Você pode especificar o nome do segredo em um modelo do Azure Resource Manager que é usado para criar uma VM, conforme mostrado no exemplo a seguir:

![Usar o segredo no ambiente ou em fórmula](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Próximas etapas

- [Criar uma VM usando o segredo](devtest-lab-add-vm.md) 
- [Criar uma fórmula usando o segredo](devtest-lab-manage-formulas.md)
- [Criar um ambiente usando o segredo](devtest-lab-create-environment-from-arm.md)
