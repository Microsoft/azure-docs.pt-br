---
title: Usar o portal do Azure para configurar chaves gerenciadas pelo cliente para o serviço de importação/exportação
description: Saiba como usar o portal do Azure para configurar chaves gerenciadas pelo cliente com Azure Key Vault para o serviço de importação/exportação do Azure. As chaves gerenciadas pelo cliente permitem criar, girar, desabilitar e revogar controles de acesso.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 01/14/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 71c322385c9b70eb46e1c478c1322752ee4561e5
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98706185"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Usar chaves gerenciadas pelo cliente no Azure Key Vault para o serviço de importação/exportação

A importação/exportação do Azure protege as chaves do BitLocker usadas para bloquear as unidades por meio de uma chave de criptografia. Por padrão, as chaves do BitLocker são criptografadas com chaves gerenciadas pela Microsoft. Para obter mais controle sobre as chaves de criptografia, você também pode fornecer chaves gerenciadas pelo cliente.

As chaves gerenciadas pelo cliente devem ser criadas e armazenadas em um Azure Key Vault. Para obter mais informações sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](../key-vault/general/overview.md)

Este artigo mostra como usar chaves gerenciadas pelo cliente com o serviço de importação/exportação no [portal do Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você criou um trabalho de importação ou exportação de acordo com as instruções em:

    - [Crie um trabalho de importação para BLOBs](storage-import-export-data-to-blobs.md).
    - [Crie um trabalho de importação para arquivos](storage-import-export-data-to-files.md).
    - [Criar um trabalho de exportação para BLOBs](storage-import-export-data-from-blobs.md)

2. Você tem um Azure Key Vault existente com uma chave que pode ser usada para proteger sua chave do BitLocker. Para saber como criar um cofre de chaves usando o portal do Azure, consulte [início rápido: criar um Azure Key Vault usando o portal do Azure](../key-vault/general/quick-create-portal.md).

    - A **exclusão reversível** e **não a limpeza** são definidas em seu Key Vault existente. Essas propriedades não são habilitadas por padrão. Para habilitar essas propriedades, consulte as seções intituladas **habilitando a exclusão reversível** e **habilitando a proteção de limpeza** em um dos seguintes artigos:

        - [Como usar a exclusão reversível com o PowerShell](../key-vault/general/key-vault-recovery.md).
        - [Como usar a exclusão reversível com a CLI](../key-vault/general/key-vault-recovery.md).
    - O cofre de chaves existente deve ter uma chave RSA de tamanho de 2048 ou mais. Para obter mais informações sobre chaves, consulte [sobre chaves](../key-vault/keys/about-keys.md).
    - O Key Vault deve estar na mesma região que a conta de armazenamento para seus dados.  
    - Se você não tiver um Azure Key Vault existente, também poderá criá-lo em linha, conforme descrito na seção a seguir.

## <a name="enable-keys"></a>Habilitar chaves

A configuração da chave gerenciada pelo cliente para o serviço de importação/exportação é opcional. Por padrão, o serviço de importação/exportação usa uma chave gerenciada da Microsoft para proteger sua chave do BitLocker. Para habilitar as chaves gerenciadas pelo cliente no portal do Azure, siga estas etapas:

1. Vá para a folha de **visão geral** do trabalho de importação.
2. No painel direito, selecione **escolher como as chaves do BitLocker são criptografadas**.

    ![Escolher opção de criptografia](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. Na folha **criptografia** , você pode exibir e copiar a chave do BitLocker do dispositivo. Em **tipo de criptografia**, você pode escolher como deseja proteger sua chave do BitLocker. Por padrão, uma chave gerenciada da Microsoft é usada.

    ![Exibir chave do BitLocker](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Você tem a opção de especificar uma chave gerenciada pelo cliente. Depois de selecionar a chave gerenciada pelo cliente, **selecione Key Vault e uma chave**.

    ![Selecionar chave gerenciada pelo cliente](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. Na folha **Selecionar chave do Azure Key Vault**, a assinatura é preenchida automaticamente. Para **Cofre de chaves**, você pode selecionar um cofre de chaves existente na lista suspensa.

    ![Selecionar ou criar Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Você também pode selecionar **Criar** para criar um cofre de chaves. Na **folha criar cofre de chaves**, insira o grupo de recursos e o nome do cofre de chaves. Aceite todos os outros padrões. Selecione **Examinar + criar**.

    ![Criar novo Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Examine as informações associadas ao cofre de chaves e selecione **criar**. Aguarde alguns minutos para que a criação do cofre de chaves seja concluída.

    ![Criar Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. Na **Azure Key Vault selecionar chave de**, você pode selecionar uma chave no cofre de chaves existente.

9. Se você criou um novo cofre de chaves, selecione **criar novo** para criar uma chave. O tamanho da chave RSA pode ser 2048 ou maior.

    ![Criar nova chave no Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Se a exclusão reversível e a proteção de limpeza não estiverem habilitadas quando você criar o cofre de chaves, o cofre de chaves será atualizado para ter a exclusão reversível e a proteção de limpeza habilitada.

10. Forneça o nome da sua chave, aceite os outros padrões e selecione **criar**.

    ![Criar nova chave](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Selecione a **versão** e escolha **selecionar**. Você será notificado de que uma chave é criada no cofre de chaves.

    ![Chave criada no cofre de chaves](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

Na folha **criptografia** , você pode ver o cofre de chaves e a chave selecionada para a chave gerenciada pelo cliente.

> [!IMPORTANT]
> Você só pode desabilitar chaves gerenciadas da Microsoft e mover para chaves gerenciadas pelo cliente em qualquer estágio do trabalho de importação/exportação. No entanto, você não pode desabilitar a chave gerenciada pelo cliente depois de criá-la.

## <a name="troubleshoot-customer-managed-key-errors"></a>Solucionar erros de chave gerenciada pelo cliente

Se você receber erros relacionados à chave gerenciada pelo cliente, use a tabela a seguir para solucionar problemas:

| Código do erro     |Detalhes     | Recuperado?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | O acesso à chave gerenciada pelo cliente é revogado.                                                       | Sim, verifique se: <ol><li>O cofre de chaves ainda tem o MSI na política de acesso.</li><li>A política de acesso tem as permissões obter, encapsular e desencapsular habilitadas.</li><li>Se o Key Vault estiver em uma VNet por trás do firewall, verifique se **permitir que os serviços confiáveis da Microsoft** estejam habilitados.</li><li>Verifique se o MSI do recurso de trabalho foi redefinido para `None` usar APIs.<br>Em caso afirmativo, defina o valor de volta para `Identity = SystemAssigned` . Isso recria a identidade para o recurso de trabalho.<br>Depois que a nova identidade tiver sido criada, habilite `Get` , `Wrap` e `Unwrap` permissões para a nova identidade na política de acesso do Key Vault</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | A chave gerenciada pelo cliente está desabilitada.                                         | Sim, habilitando a versão de chave     |
| CmkErrorKeyNotFound      | Não é possível localizar a chave gerenciada pelo cliente. | Sim, se a chave tiver sido excluída, mas ainda estiver dentro da duração da limpeza, usando [desfazer remoção da chave do cofre de chaves](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval).<br>Restante <ol><li>Sim, se o cliente tiver o backup da chave e o restaurar.</li><li>Não, caso contrário.</li></ol>
| CmkErrorVaultNotFound |Não é possível localizar o cofre de chaves da chave gerenciada pelo cliente. |   Se o cofre de chaves tiver sido excluído:<ol><li>Sim, se estiver na duração da proteção de limpeza, usando as etapas em [recuperar um cofre de chaves](../key-vault/general/soft-delete-overview.md#key-vault-recovery).</li><li>Não, se estiver além da duração da proteção de limpeza.</li></ol><br>Caso contrário, se o cofre de chaves tiver sido migrado para um locatário diferente, sim, ele poderá ser recuperado usando uma das etapas a seguir:<ol><li>Reverter o cofre de chaves de volta para o locatário antigo.</li><li>Defina `Identity = None` e, em seguida, defina o valor de volta para `Identity = SystemAssigned` . Isso exclui e recria a identidade depois que a nova identidade é criada. Habilite `Get` `Wrap` `Unwrap` as permissões, e para a nova identidade na política de acesso do Key Vault.</li></ol>|

## <a name="next-steps"></a>Próximas etapas

- [O que é Azure Key Vault](../key-vault/general/overview.md)?