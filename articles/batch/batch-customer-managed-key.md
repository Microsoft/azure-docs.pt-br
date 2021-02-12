---
title: Configurar chaves gerenciadas pelo cliente para sua conta do lote do Azure com Azure Key Vault e identidade gerenciada
description: Saiba como criptografar dados do lote usando chaves gerenciadas pelo cliente.
author: pkshultz
ms.topic: how-to
ms.date: 01/25/2021
ms.author: peshultz
ms.openlocfilehash: 01dc21f067b03ad8e07a05a18aa6312ed7f7189e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789406"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Configurar chaves gerenciadas pelo cliente para sua conta do lote do Azure com Azure Key Vault e identidade gerenciada

Por padrão, o lote do Azure usa chaves gerenciadas por plataforma para criptografar todos os dados do cliente armazenados no serviço de lote do Azure, como certificados, metadados do trabalho/tarefa. Opcionalmente, você pode usar suas próprias chaves, ou seja, chaves gerenciadas pelo cliente, para criptografar os dados armazenados no lote do Azure.

As chaves que você fornecer devem ser geradas em [Azure Key Vault](../key-vault/general/basic-concepts.md)e devem ser acessadas com [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

Há dois tipos de identidades gerenciadas: [ *atribuído pelo sistema* e *atribuído pelo usuário*](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

Você pode criar sua conta do lote com identidade gerenciada atribuída pelo sistema ou criar uma identidade gerenciada atribuída pelo usuário separada que terá acesso às chaves gerenciadas pelo cliente. Examine a [tabela de comparação](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) para entender as diferenças e considere qual opção funciona melhor para sua solução. Por exemplo, se você quiser usar a mesma identidade gerenciada para acessar vários recursos do Azure, será necessária uma identidade gerenciada atribuída pelo usuário. Caso contrário, uma identidade gerenciada atribuída pelo sistema associada à sua conta do lote pode ser suficiente. O uso de uma identidade gerenciada atribuída pelo usuário também oferece a opção de impor chaves gerenciadas pelo cliente na criação da conta do lote, conforme mostrado [no exemplo a seguir](#create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys).

> [!IMPORTANT]
> O suporte para chaves gerenciadas pelo cliente no lote do Azure está atualmente em visualização pública para as regiões Europa Ocidental, Europa Setentrional, Norte da Suíça, EUA Central, Sul EUA Central, Oeste EUA Central, leste dos EUA, leste dos EUA 2, oeste dos EUA 2, US Gov-Virgínia e US Gov Arizona.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>Criar uma conta do lote com identidade gerenciada atribuída pelo sistema

Se você não precisar de uma identidade gerenciada separada pelo usuário, poderá habilitar a identidade gerenciada atribuída pelo sistema ao criar sua conta do lote.

### <a name="azure-portal"></a>Portal do Azure

No [portal do Azure](https://portal.azure.com/), ao criar contas do lote, selecione **sistema atribuído** no tipo de identidade na guia **avançado** .

![Captura de tela de uma nova conta do lote com o tipo de identidade atribuído pelo sistema.](./media/batch-customer-managed-key/create-batch-account.png)

Depois que a conta é criada, você pode encontrar um GUID exclusivo no campo **ID da entidade de segurança** na seção **Propriedades** . O **tipo de identidade** será exibido `System assigned` .

![Captura de tela mostrando um GUID exclusivo no campo ID da entidade de identidade.](./media/batch-customer-managed-key/linked-batch-principal.png)

Você precisará desse valor para conceder a essa conta do lote acesso ao Key Vault.

### <a name="azure-cli"></a>CLI do Azure

Ao criar uma nova conta do lote, especifique `SystemAssigned` para o `--identity` parâmetro.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

Depois que a conta for criada, você poderá verificar se a identidade gerenciada atribuída pelo sistema foi habilitada nessa conta. Lembre-se de anotar o `PrincipalId` , pois esse valor será necessário para conceder a essa conta do lote acesso ao key Vault.

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> A identidade gerenciada atribuída pelo sistema criada em uma conta do lote é usada somente para recuperar chaves gerenciadas pelo cliente do Key Vault. Essa identidade não está disponível em pools do lote.

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

Se preferir, você pode [criar uma identidade gerenciada atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) que pode ser usada para acessar as chaves gerenciadas pelo cliente.

Você precisará do valor da **ID do cliente** dessa identidade para que ele acesse a Key Vault.

## <a name="configure-your-azure-key-vault-instance"></a>Configurar sua instância do Azure Key Vault

O Azure Key Vault no qual suas chaves serão geradas deve ser criado no mesmo locatário que a sua conta do lote. Ele não precisa estar no mesmo grupo de recursos ou mesmo na mesma assinatura.

### <a name="create-an-azure-key-vault"></a>Criar um Cofre de chaves do Azure

Ao [criar uma instância de Azure Key Vault](../key-vault/general/quick-create-portal.md) com chaves gerenciadas pelo cliente para o lote do Azure, verifique se a **exclusão reversível** e a **proteção de limpeza** estão ambas habilitadas.

![Instantâneo da tela de criação de Key Vault.](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Adicionar uma política de acesso à sua instância do Azure Key Vault

No portal do Azure, depois que o Key Vault for criado, na configuração **política** de acesso em, adicione o acesso à conta do lote usando a identidade gerenciada. Em **permissões de chave**, **selecione obter**, **encapsular chave** e **desencapsular chave**.

![Screenshow mostrando a tela Adicionar política de acesso.](./media/batch-customer-managed-key/key-permissions.png)

No campo **selecionar** , em **principal**, preencha um dos seguintes:

- Para identidade gerenciada atribuída pelo sistema: Insira o `principalId` que você recuperou anteriormente ou o nome da conta do lote.
- Para identidade gerenciada atribuída pelo usuário: Insira a **ID do cliente** que você recuperou anteriormente ou o nome da identidade gerenciada atribuída pelo usuário.

![Captura da tela da entidade de segurança.](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Gerar uma chave no Azure Key Vault

Na portal do Azure, vá para a instância de Key Vault na seção **chave** , selecione **gerar/importar**. Selecione o **tipo de chave** a ser `RSA` e o tamanho da **chave RSA** para que sejam pelo menos `2048` bits. `EC` Atualmente, não há suporte para tipos de chave como uma chave gerenciada pelo cliente em uma conta do lote.

![Criar uma chave](./media/batch-customer-managed-key/create-key.png)

Depois que a chave for criada, clique na chave recém-criada e na versão atual, copie o **identificador de chave** na seção **Propriedades** .  Certifique-se de que em **operações permitidas**, **encapsular chave** e **desencapsular chave** estejam ambos verificados.

## <a name="enable-customer-managed-keys-on-a-batch-account"></a>Habilitar chaves gerenciadas pelo cliente em uma conta do lote

Depois de seguir as etapas acima, você poderá habilitar as chaves gerenciadas pelo cliente em sua conta do lote.

### <a name="azure-portal"></a>Portal do Azure

Na [portal do Azure](https://portal.azure.com/), vá para a página conta do lote. Na seção **criptografia** , habilite a **chave gerenciada pelo cliente**. Você pode usar o identificador de chave diretamente ou pode selecionar o cofre de chaves e, em seguida, clicar em **selecionar um cofre de chaves e uma chave**.

![Captura de tela mostrando a seção de criptografia e a opção para habilitar a chave gerenciada pelo cliente](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>CLI do Azure

Depois que a conta do lote for criada com a identidade gerenciada atribuída pelo sistema e o acesso a Key Vault for concedido, atualize a conta do lote com a `{Key Identifier}` URL em `keyVaultProperties` parâmetro. Também defina **encryption_key_source** como `Microsoft.KeyVault` .

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys"></a>Criar uma conta do lote com identidade gerenciada atribuída pelo usuário e chaves gerenciadas pelo cliente

Usando o cliente .NET de gerenciamento do lote, você pode criar uma conta do lote que terá uma identidade gerenciada atribuída pelo usuário e chaves gerenciadas pelo cliente.

```c#
EncryptionProperties encryptionProperties = new EncryptionProperties()
{
    KeySource = KeySource.MicrosoftKeyVault,
    KeyVaultProperties = new KeyVaultProperties()
    {
        KeyIdentifier = "Your Key Azure Resource Manager Resource ID"
    }
};

BatchAccountIdentity identity = new BatchAccountIdentity()
{
    Type = ResourceIdentityType.UserAssigned,
    UserAssignedIdentities = new Dictionary<string, BatchAccountIdentityUserAssignedIdentitiesValue>
    {
            ["Your Identity Azure Resource Manager ResourceId"] = new BatchAccountIdentityUserAssignedIdentitiesValue()
    }
};
var parameters = new BatchAccountCreateParameters(TestConfiguration.ManagementRegion, encryption:encryptionProperties, identity: identity);

var account = await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount", parameters); 
```

## <a name="update-the-customer-managed-key-version"></a>Atualizar a versão da chave gerenciada pelo cliente

Ao criar uma nova versão de uma chave, atualize a conta do lote para usar a nova versão. Siga estas etapas:

1. Navegue até sua conta do lote em portal do Azure e exiba as configurações de criptografia.
2. Insira o URI para a nova versão de chave. Como alternativa, você pode selecionar a Key Vault e a chave novamente para atualizar a versão.
3. Salve suas alterações.

Você também pode usar CLI do Azure para atualizar a versão.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```

## <a name="use-a-different-key-for-batch-encryption"></a>Usar uma chave diferente para a criptografia em lote

Para alterar a chave usada para criptografia em lote, siga estas etapas:

1. Navegue até sua conta do lote e exiba as configurações de criptografia.
2. Insira o URI para a nova chave. Como alternativa, você pode selecionar a Key Vault e escolher uma nova chave.
3. Salve suas alterações.

Você também pode usar CLI do Azure para usar uma chave diferente.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes

- **Há suporte para chaves gerenciadas pelo cliente para contas do lote existentes?** Não. As chaves gerenciadas pelo cliente só têm suporte para novas contas do lote.
- **Posso selecionar os tamanhos de chave RSA com mais de 2048 bits?** Sim, `3072` também há suporte para os tamanhos de chave RSA e `4096` bits.
- **Quais operações estão disponíveis depois que uma chave gerenciada pelo cliente é revogada?** A única operação permitida será a exclusão da conta se o lote perder o acesso à chave gerenciada pelo cliente.
- **Como devo restaurar o acesso à minha conta do lote se eu excluir acidentalmente a chave de Key Vault?** Como a proteção de limpeza e a exclusão reversível estão habilitadas, você pode restaurar as chaves existentes. Para obter mais informações, consulte [recuperar um Azure Key Vault](../key-vault/general/key-vault-recovery.md).
- **Posso desabilitar chaves gerenciadas pelo cliente?** Você pode definir o tipo de criptografia da conta do lote de volta para "chave gerenciada da Microsoft" a qualquer momento. Depois disso, você é livre para excluir ou alterar a chave.
- **Como posso girar minhas chaves?** As chaves gerenciadas pelo cliente não são giradas automaticamente. Para girar a chave, atualize o identificador de chave ao qual a conta está associada.
- **Depois de restaurar o acesso, quanto tempo levará para que a conta do lote funcione novamente?** Pode levar até 10 minutos para que a conta seja acessível novamente quando o acesso for restaurado.
- **Enquanto a conta do lote não está disponível, o que acontece com meus recursos?** Todos os pools que estão em execução quando o acesso ao lote para chaves gerenciadas pelo cliente são perdidos continuarão a ser executados. No entanto, os nós passarão para um estado indisponível e as tarefas deixarão de ser executadas (e serão recolocadas na fila). Quando o acesso for restaurado, os nós ficarão disponíveis novamente e as tarefas serão reiniciadas.
- **Este mecanismo de criptografia se aplica a discos de VM em um pool do lote?** Não. Para pools de configuração do serviço de nuvem, nenhuma criptografia é aplicada ao sistema operacional e ao disco temporário. Para pools de configuração de máquina virtual, o sistema operacional e todos os discos de dados especificados serão criptografados com uma chave gerenciada da plataforma Microsoft por padrão. No momento, você não pode especificar sua própria chave para esses discos. Para criptografar o disco temporário de VMs para um pool do lote com uma chave gerenciada da plataforma Microsoft, você deve habilitar a propriedade [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) em seu pool de [configuração de máquina virtual](/rest/api/batchservice/pool/add#virtualmachineconfiguration) . Para ambientes altamente confidenciais, é recomendável habilitar a criptografia de disco temporária e evitar o armazenamento de dados confidenciais no sistema operacional e nos discos de dados. Para obter mais informações, consulte [criar um pool com criptografia de disco habilitada](./disk-encryption.md)
- **A identidade gerenciada atribuída pelo sistema na conta do lote está disponível nos nós de computação?** Não. A identidade gerenciada atribuída pelo sistema é atualmente usada somente para acessar o Azure Key Vault para a chave gerenciada pelo cliente.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [as práticas recomendadas de segurança no lote do Azure](security-best-practices.md).
- Saiba mais sobre[Azure Key Vault](../key-vault/general/basic-concepts.md).
