---
title: Criptografar discos do sistema operacional usando chaves gerenciadas pelo cliente no Azure DevTest Labs
description: Saiba como criptografar discos de sistema operacional (SO) usando chaves gerenciadas pelo cliente no Azure DevTest Labs.
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 26ef4ff1529483da9956c6dcc43807af0ffd6463
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341200"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>Criptografar discos do sistema operacional (SO) usando chaves gerenciadas pelo cliente no Azure DevTest Labs
A criptografia do lado do servidor (SSE) ajuda a proteger os dados e atender aos compromissos de conformidade e segurança de sua organização. A SSE criptografa automaticamente os dados armazenados em discos gerenciados no Azure (so e discos de dados) em repouso, por padrão, ao mantê-los para a nuvem. Saiba mais sobre a [criptografia de disco](../virtual-machines/disk-encryption.md) no Azure. 

No DevTest Labs, todos os discos do sistema operacional e os discos de dados criados como parte de um laboratório são criptografados usando chaves gerenciadas por plataforma. No entanto, como um proprietário de laboratório, você pode optar por criptografar discos de so de máquina virtual de laboratório usando suas próprias chaves. Se você optar por gerenciar a criptografia com suas próprias chaves, poderá especificar uma **chave gerenciada pelo cliente** a ser usada para criptografar dados em discos do sistema operacional do laboratório. Para saber mais sobre criptografia do lado do servidor (SSE) com chaves gerenciadas pelo cliente e outros tipos de criptografia de disco gerenciado, confira [chaves gerenciadas pelo cliente](../virtual-machines/disk-encryption.md#customer-managed-keys). Além disso, consulte [restrições com o uso de chaves gerenciadas pelo cliente](../virtual-machines/disks-enable-customer-managed-keys-portal.md#restrictions).

> [!NOTE]
> - Atualmente, a criptografia de disco com uma chave gerenciada pelo cliente tem suporte apenas para discos do sistema operacional no DevTest Labs. 
> 
> - A configuração se aplica a discos do sistema operacional recém-criados no laboratório. Se você optar por alterar o conjunto de criptografia de disco em algum momento, os discos mais antigos no laboratório continuarão a ser criptografados usando o conjunto de criptografia de disco anterior. 

A seção a seguir mostra como um proprietário de laboratório pode configurar a criptografia usando uma chave gerenciada pelo cliente.

## <a name="pre-requisites"></a>Pré-requisitos

1. Se você não tiver um conjunto de criptografia de disco, siga este artigo para [configurar um Key Vault e um conjunto de criptografia de disco](../virtual-machines/disks-enable-customer-managed-keys-portal.md). Observe os seguintes requisitos para o conjunto de criptografia de disco: 

    - O conjunto de criptografia de disco precisa estar **na mesma região e assinatura que o seu laboratório**. 
    - Verifique se você (proprietário do laboratório) tem pelo menos um **acesso em nível de leitor** ao conjunto de criptografia de disco que será usado para criptografar os discos do sistema operacional de laboratório. 
1. Para laboratórios criados antes de 8/1/2020, o proprietário do laboratório precisará garantir que a identidade atribuída do sistema de laboratório esteja habilitada. Para fazer isso, o proprietário do laboratório pode ir para seu laboratório, clicar em **configuração e políticas**, clicar na folha **identidade (versão prévia)** , alterar **o status** de identidade atribuído ao sistema para **ativado** e clicar em **salvar**. Para novos laboratórios criados após 8/1/2020, a identidade atribuída ao sistema do laboratório será habilitada por padrão. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/managed-keys.png" alt-text="Chaves gerenciadas":::
1. Para que o laboratório manipule a criptografia para todos os discos do sistema operacional do laboratório, o proprietário do laboratório precisa conceder explicitamente a função de leitor de **identidade atribuída pelo sistema** do laboratório no conjunto de criptografia de disco, bem como a função colaborador de máquina virtual na assinatura subjacente do Azure. O proprietário do laboratório pode fazer isso concluindo as seguintes etapas:

   
    1. Verifique se você é membro da [função Administrador de acesso do usuário](../role-based-access-control/built-in-roles.md#user-access-administrator) no nível de assinatura do Azure para poder gerenciar o acesso do usuário aos recursos do Azure. 
    1. Na página **conjunto de criptografia de disco** , selecione **controle de acesso (iam)** no menu à esquerda. 
    1. Selecione **+ Adicionar** na barra de ferramentas e selecione **Adicionar uma atribuição de função**.  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="Adicionar gerenciamento de função-menu":::
    1. Na página **Adicionar atribuição de função** , selecione a função **leitor** ou uma função que permita mais acesso. 
    1. Digite o nome do laboratório para o qual o conjunto de criptografia de disco será usado e selecione o nome do laboratório (identidade atribuída pelo sistema para o laboratório) na lista suspensa. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="Selecione a identidade gerenciada pelo sistema do laboratório":::        
    1. Selecione **Salvar** na barra de ferramentas. 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="Salvar atribuição de função":::
3. Adicione a **identidade atribuída pelo sistema** do laboratório à função **colaborador da máquina virtual** usando a **Subscription**  ->  página **iam (controle de acesso à assinatura)** . As etapas são semelhantes àquelas nas etapas anteriores. 

    
    1. Navegue até a página de **assinatura** no portal do Azure. 
    1. Selecione **IAM (Controle de acesso)** . 
    1. Selecione **+ Adicionar** na barra de ferramentas e selecione **Adicionar uma atribuição de função**. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="Assinatura – página IAM (controle de acesso de >)":::
    1. Na página **Adicionar atribuição de função** , selecione **colaborador de máquina virtual** para a função.
    1. Digite o nome do laboratório e selecione o **nome do laboratório** (identidade atribuída pelo sistema para o laboratório) na lista suspensa. 
    1. Selecione **Salvar** na barra de ferramentas. 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>Criptografar discos do sistema operacional de laboratório com uma chave gerenciada pelo cliente 

1. Na home page de seu laboratório na portal do Azure, selecione **configuração e políticas** no menu à esquerda. 
1. Na página **configuração e políticas** , selecione **discos (versão prévia)** na seção **criptografia** . Por padrão, o **tipo de criptografia** é definido como **criptografia em repouso com uma chave gerenciada por plataforma**.

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="Guia discos da página configuração e políticas":::
1. Para **tipo de criptografia**, selecione **criptografia em repouso com uma chave gerenciada pelo cliente** na lista suspensa. 
1. Para **conjunto de criptografia de disco**, selecione o conjunto de criptografia de disco que você criou anteriormente. É o mesmo conjunto de criptografia de disco que a identidade atribuída pelo sistema do laboratório pode acessar.
1. Selecione **Salvar** na barra de ferramentas. 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="Habilitar a criptografia com a chave gerenciada pelo cliente":::
1. Na caixa de mensagem com o seguinte texto: *essa configuração será aplicada a máquinas recém-criadas no laboratório. O disco do sistema operacional antigo permanecerá criptografado com o antigo conjunto de criptografia de disco*, selecione **OK**. 

    Uma vez configurado, os discos do sistema operacional de laboratório serão criptografados com a chave gerenciada pelo cliente fornecida usando o conjunto de criptografia de disco. 
   
## <a name="how-to-validate-if-disks-are-being-encrypted"></a>Como validar se os discos estão sendo criptografados

1. Vá para uma máquina virtual de laboratório criada depois de habilitar a criptografia de disco com uma chave gerenciada pelo cliente no laboratório.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/enabled-encryption-vm.png" alt-text="VM com criptografia de disco habilitada":::
1. Clique no grupo de recursos da VM e clique no disco do sistema operacional.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/vm-resource-group.png" alt-text="Grupo de recursos da VM":::
1. Acesse criptografia e valide se a criptografia está definida como chave gerenciada pelo cliente com o conjunto de criptografia de disco selecionado.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/validate-encryption.png" alt-text="Validar criptografia":::
  
## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir: 

- [Azure Disk Encryption](../virtual-machines/disk-encryption.md). 
- [Chaves gerenciadas pelo cliente](../virtual-machines/disk-encryption.md#customer-managed-keys)