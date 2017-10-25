---
ms.assetid: 
title: "Azure Key Vault – Como usar a exclusão reversível com a CLI"
description: "Usar exemplos de caso de exclusão reversível com trechos de código da CLI"
author: BrucePerlerMS
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: bruceper
ms.openlocfilehash: 3ee2c5dfb99d734cde25894174466b8e49823c67
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Como usar a exclusão reversível do Key Vault com a CLI

O recurso de exclusão reversível do Azure Key Vault permite a recuperação de cofres e objetos de cofre excluídos. Especificamente, a exclusão reversível atende aos seguintes cenários:

- Suporte à exclusão reversível de cofres de chaves
- Suporte à exclusão reversível de objetos do cofre de chaves, chaves, segredos e certificados

## <a name="prerequisites"></a>Pré-requisitos

- CLI do Azure 2.0 – Se essa configuração não existir para o seu ambiente, veja [Gerenciar Key Vault usando a CLI 2.0](key-vault-manage-with-cli2.md).

Para saber mais de referência específicas do Key Vault para CLI, veja [Referência de Key Vault da CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/keyvault).

## <a name="required-permissions"></a>Permissões necessárias

As operações de Key Vault são gerenciadas separadamente por meio de permissões de RBAC (controle de acesso baseado em função) da seguinte maneira:

| Operação | Descrição | Permissão de usuário |
|:--|:--|:--|
|Listar|Lista os cofres de chaves excluídos.|Microsoft.KeyVault/deletedVaults/read|
|Recuperar|Recupera o cofre de chaves excluído.|Microsoft.KeyVault/vaults/write|
|Limpar|Remove permanentemente um cofre de chaves excluído e todo o seu conteúdo.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Para saber mais sobre permissões e controle de acesso, veja [Proteger seu cofre de chaves](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Habilitar a exclusão reversível

Para poder recuperar um cofre de chaves excluído, ou objetos armazenados em um cofre de chaves, primeiro você deve habilitar a exclusão reversível para esse cofre de chaves.

### <a name="existing-key-vault"></a>Cofre de chaves existente

Para um cofre de chaves existente chamado ContosoVault, habilite a exclusão reversível da seguinte maneira. 

>[!NOTE]
>Atualmente, você precisa usar a manipulação de recursos do Azure Resource Manager para gravar a propriedade *enableSoftDelete* diretamente no recurso Key Vault.

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Novo cofre de chaves

A habilitação da exclusão reversível para um novo cofre de chaves é feita no momento da criação adicionando o sinalizador de habilitação de exclusão reversível ao comando create.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Verificar a habilitação da exclusão reversível

Para verificar se um cofre de chaves tem exclusão reversível habilitada, execute o comando *show* e procure o atributo 'Exclusão Reversível Habilitada?' e sua configuração, true ou false.

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Excluir um cofre de chaves protegido por exclusão reversível

O comando para excluir (ou remover) um cofre de chaves permanece o mesmo, mas seu comportamento muda dependendo da habilitação ou não da exclusão reversível.

```azurecli
az keyvault delete --name ContosoVault
```

> [!IMPORTANT]
>Se você executar o comando anterior para um cofre de chaves que não tenha a exclusão reversível habilitada, você excluirá permanentemente esse cofre de chaves e todo seu conteúdo sem opções de recuperação.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Como a exclusão reversível protege seus cofres de chaves

Com a exclusão reversível habilitada:

- Quando um cofre de chaves é excluído, ele é removido de seu grupo de recursos e colocado em um namespace reservado, associado apenas ao local onde foi criado. 
- Os objetos em um cofre de chaves excluído, como chaves, segredos e certificados, não estão acessíveis e permanecem assim enquanto o cofre de chaves que os contêm estiver no estado excluído. 
- O nome DNS de um cofre de chaves em um estado excluído ainda é reservado, portanto, não é possível criar um novo cofre de chaves com o mesmo nome.  

Você pode exibir cofres de chave no estado excluído, associados à sua assinatura, usando o seguinte comando:

```azurecli
az keyvault list-deleted
```

A *ID do Recurso* na saída faz referência à ID do recurso original desse cofre. Como este cofre de chaves está em um estado excluído, não há um recurso com essa ID de recurso. O campo *Id* pode ser usado para identificar o recurso durante a recuperação ou limpeza. O campo *Data de Limpeza Agendada* indica quando o cofre será excluído permanentemente (limpo) se nenhuma ação for realizada para esse cofre excluído. O período de retenção padrão, usado para calcular a *Data de Limpeza Agendada*, é de 90 dias.

## <a name="recovering-a-key-vault"></a>Recuperação de um cofre de chaves

Para recuperar um cofre de chaves, você precisa especificar o nome do cofre de chaves, o grupo de recursos e o local. Anote o local e o grupo de recursos do cofre de chaves excluído, pois você precisará deles para um processo de recuperação de cofre de chaves.

```azurecli
az keyvault recover --location westus --name ContosoVault
```

Após a recuperação de um cofre de chaves, o resultado é um novo recurso com a ID de recurso original do cofre de chaves. Se o grupo de recursos no qual o cofre de chaves residia tiver sido removido, um novo grupo de recursos com o mesmo nome deverá ser criado antes da recuperação do cofre de chaves.

## <a name="key-vault-objects-and-soft-delete"></a>Objetos do cofre de chaves e exclusão reversível

Para uma chave, "ContosoFirstKey", em um cofre de chaves chamado "ContosoVault" com a exclusão reversível habilitada, essa chave seria excluída da seguinte maneira.

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Com o cofre de chaves habilitado para exclusão reversível, uma chave excluída ainda aparece como excluída, exceto, quando você lista ou recupera explicitamente as chaves excluídas. A maioria das operações em uma chave no estado excluído falhará, exceto para listagem, recuperação ou limpeza de uma chave excluída. 

Por exemplo, para solicitar a listagem de chaves excluídas em um cofre de chaves, use o seguinte comando:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Estado de transição 

Quando você exclui uma chave em um cofre de chaves com exclusão reversível habilitada, talvez demore alguns segundos para a transição ser concluída. Durante esse estado de transição, pode parecer que a chave não esteja no estado ativo ou no estado excluído. Esse comando listará todas as chaves excluídas em seu cofre de chaves chamado "ContosoVault".

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Usar exclusão reversível com objetos de cofre de chaves

Assim como os cofres de chave, uma chave, segredo ou certificado excluído permanecerá no estado excluído por até 90 dias, a menos que você o recupere ou limpe. 

#### <a name="keys"></a>simétricas

Para recuperar uma chave excluída:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Para excluir permanentemente uma chave:

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

>[!NOTE]
>A limpeza de uma chave a excluirá permanentemente, ou seja, ela não poderá ser recuperada.

As ações de **recuperação** e **limpeza** têm suas próprias permissões associadas em uma política de acesso de cofre de chaves. Para que um usuário ou entidade de serviço possa executar uma ação de **recuperação** ou **limpeza**, ele precisa ter a permissão respectiva para esse objeto (chave ou segredo) na política de acesso de cofre de chaves. Por padrão, a permissão de **limpeza** não é adicionada à política de acesso de um cofre de chaves quando o atalho "todos" for usado para conceder todas as permissões a um usuário. Você deve conceder explicitamente a permissão de **limpeza**. Por exemplo, o comando a seguir concede a permissão user@contoso.com para executar várias operações em chaves no *ContosoVault*, incluindo **limpar**.

#### <a name="set-a-key-vault-access-policy"></a>Definir uma política de acesso de cofre de chaves

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Se você tiver um cofre de chaves para o qual acabou de habilitar a exclusão reversível, talvez você não tenha as permissões de **recuperação** e **limpeza**.

#### <a name="secrets"></a>Segredos

Assim como as chaves, os segredos em um cofre de chaves são operados com seus próprios comandos. Veja a seguir os comandos para excluir, listar, recuperar e limpar os segredos.

- Excluir um segredo chamado SQLPassword: 
```azurecli
az keyvault secret delete --vault-name ContosoVault -name SQLPassword
```

- Listar todos os segredos excluídos em um cofre de chaves: 
```azurecli
az keyvault secret list-deleted --vault-name ContosoVault
```

- Recuperar um segredo no estado excluído: 
```azurecli
az keyvault secret recover --name SQLPassword --vault-name ContosoVault
```

- Limpar um segredo no estado excluído: 
```azurecli
az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
```

>[!NOTE]
>A limpeza de um segredo o excluirá permanentemente, ou seja, ele não poderá ser recuperado.

## <a name="purging-and-key-vaults"></a>Limpeza e cofres de chave

### <a name="key-vault-objects"></a>Objetos do cofre de chaves

A limpeza de uma chave, segredo ou certificado o excluirá permanentemente, ou seja, não será possível recuperá-lo. O cofre de chaves que continha o objeto excluído permanecerá intacto, assim como todos os outros objetos no cofre de chaves. 

### <a name="key-vaults-as-containers"></a>Cofres de chave como contêineres
Quando um cofre de chaves é limpo, todo seu conteúdo, incluindo chaves, segredos e certificados, serão excluídos permanentemente. Para limpar um cofre de chaves, use o comando `az keyvault purge`. Você pode encontrar o local dos cofres de chave excluído de sua assinatura usando o comando `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
```

>[!NOTE]
>A limpeza de um cofre de chaves o excluirá permanentemente, ou seja, não será possível recuperá-lo.

### <a name="purge-permissions-required"></a>Permissões de limpeza necessárias
- Para limpar um cofre de chaves excluído, de modo que o cofre e todo seu conteúdo seja removido permanentemente, o usuário precisa de permissão RBAC para executar uma operação *Microsoft.KeyVault/locations/deletedVaults/purge/action*. 
- Para listar a chave excluída, o cofre de um usuário precisa de permissão RBAC para executar a permissão *Microsoft.KeyVault/deletedVaults/read*. 
- Por padrão, somente um administrador de assinatura tem essas permissões. 

### <a name="scheduled-purge"></a>Limpeza agendada

A listagem de seus objetos de cofre de chaves excluídos mostra o agendamento da limpeza pelo Key Vault. O campo *Data de Limpeza Agendada* indica quando o objeto do cofre de chaves será excluído permanentemente se nenhuma ação for realizada. Por padrão, o período de retenção para um objeto do cofre de chaves excluído é de 90 dias.

>[!NOTE]
>Um objeto de cofre limpo, disparado pelo campo *Data de Limpeza Agendada*, será excluído permanentemente. Ele não é recuperável.

## <a name="other-resources"></a>Outros recursos

- Para obter uma visão geral do recurso de exclusão reversível do Key Vault, veja [Visão geral da exclusão reversível do Azure Key Vault](key-vault-ovw-soft-delete.md).
- Para obter uma visão geral do uso do Azure Key Vault, veja [Introdução ao Azure Key Vault](key-vault-get-started.md).

