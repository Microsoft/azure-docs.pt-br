---
title: Conceder permissão a aplicativos para acessar um Azure Key Vault usando o Azure RBAC | Microsoft Docs
description: Saiba como fornecer acesso a chaves, segredos e certificados usando o controle de acesso baseado em função do Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: f7a0190d664e3330d2a6205014c00c61c1183dd3
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936236"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control-preview"></a>Fornecer acesso a chaves, certificados e segredos do Key Vault com um controle de acesso baseado em função do Azure (versão prévia)

> [!NOTE]
> Key Vault provedor de recursos dá suporte a dois tipos de recursos: **cofres** e **HSMs gerenciados**. O controle de acesso descrito neste artigo se aplica somente a **cofres**. Para saber mais sobre o controle de acesso para HSM gerenciado, consulte [controle de acesso HSM gerenciado](../managed-hsm/access-control.md).

O Azure RBAC (controle de acesso baseado em função) do Azure é um sistema de autorização criado em [Azure Resource Manager](../../azure-resource-manager/management/overview.md) que fornece gerenciamento de acesso refinado de recursos do Azure.

O RBAC do Azure permite que os usuários gerenciem permissões de chave, segredos e certificados. Ele fornece um local para gerenciar todas as permissões em todos os cofres de chaves. 

O modelo RBAC do Azure fornece a capacidade de definir permissões em diferentes níveis de escopo: grupo de gerenciamento, assinatura, grupo de recursos ou recursos individuais.  O RBAC do Azure para o Key Vault também fornece a capacidade de ter permissões separadas em chaves, segredos e certificados individuais

Para obter mais informações, consulte [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md).

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Práticas recomendadas para chaves, segredos e certificados individuais

Nossa recomendação é usar um cofre por aplicativo por ambiente (desenvolvimento, pré-produção e produção).

As permissões individuais de chaves, segredos e certificados devem ser usadas somente para cenários específicos:

-   Aplicativos de várias camadas que precisam separar o controle de acesso entre camadas

-   Compartilhando segredo individual entre vários aplicativos

Mais sobre as diretrizes de gerenciamento de Azure Key Vault, consulte:

- [Visão geral da segurança do Azure Key Vault](security-overview.md)
- [Limites de serviço Azure Key Vault](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations-preview"></a>Funções internas do Azure para Key Vault operações do plano de dados (versão prévia)
> [!NOTE]
> `Key Vault Contributor` a função destina-se a operações do plano de gerenciamento para gerenciar cofres de chaves. Ele não permite acesso a chaves, segredos e certificados.

| Função interna | Descrição | ID |
| --- | --- | --- |
| Key Vault administrador (versão prévia) | Execute todas as operações de plano de dados em um cofre de chaves e todos os objetos nela, incluindo certificados, chaves e segredos. Não é possível gerenciar os recursos do cofre de chaves nem gerenciar atribuições de função. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault o responsável por certificados (versão prévia) | Execute qualquer ação nos certificados de um cofre de chaves, exceto gerenciar permissões. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault de criptografia (versão prévia)| Execute qualquer ação nas chaves de um cofre de chaves, exceto gerenciar permissões. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Criptografia do serviço de criptografia Key Vault (visualização) | Ler metadados de chaves e executar operações de encapsulamento/desencapsulamento. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault usuário de criptografia (visualização) | Executar operações criptográficas usando chaves. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Leitor de Key Vault (versão prévia)| Ler metadados de cofres de chaves e seus certificados, chaves e segredos. Não é possível ler valores confidenciais, como conteúdo secreto ou material de chave. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Diretor de segredos Key Vault (versão prévia)| Execute qualquer ação nos segredos de um cofre de chaves, exceto gerenciar permissões. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Usuário Key Vault segredos (visualização)| Ler conteúdo secreto. Funciona somente para cofres de chaves que usam o modelo de permissão ' controle de acesso baseado em função do Azure '. | 4633458b-17de-408a-b874-0445c86b69e6 |

Para obter mais informações sobre as definições de funções internas do Azure, consulte [funções internas do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Usando as permissões de segredo, chave e certificado do RBAC do Azure com Key Vault

O novo modelo de permissão do RBAC do Azure para o Key Vault fornece uma alternativa ao modelo de permissões de política de acesso do cofre. 

### <a name="prerequisites"></a>Pré-requisitos

Para adicionar as atribuições de função, você precisa ter:

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- as permissões `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, como [Administrador de Acesso do Usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) ou [Proprietário](../../role-based-access-control/built-in-roles.md#owner)

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Habilitar permissões do RBAC do Azure no Key Vault

> [!IMPORTANT]
> A configuração do modelo de permissão do RBAC do Azure invalida todas as permissões de políticas de acesso. Isso pode causar interrupções quando funções equivalentes do Azure não são atribuídas.

1.  Habilitar permissões do RBAC do Azure no novo cofre de chaves:

    ![Habilitar permissões de RBAC do Azure-novo cofre](../media/rbac/image-1.png)

2.  Habilitar permissões do RBAC do Azure no cofre de chaves existente:

    ![Habilitar permissões do RBAC do Azure-cofre existente](../media/rbac/image-2.png)

### <a name="assign-role"></a>Atribuir função

> [!Note]
> É recomendável usar a ID de função exclusiva em vez do nome da função em scripts. Portanto, se uma função for renomeada, seus scripts continuarão a funcionar. Durante a visualização, cada função teria o sufixo "(Preview)", que seria removido posteriormente. Neste documento, o nome da função é usado apenas para facilitar a leitura.

CLI do Azure comando para criar uma atribuição de função:

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```

No portal do Azure, a tela atribuições de função do Azure está disponível para todos os recursos na guia controle de acesso (IAM).

![Atribuição de função-guia (IAM)](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>Atribuição de função de escopo do grupo de recursos

1.  Vá para o grupo de recursos do cofre de chaves.
    ![Atribuição de função-grupo de recursos](../media/rbac/image-4.png)

2.  Clique em controle de acesso (IAM) adicionar \> atribuição de função \>

3.  Criar Key Vault a função leitor "Key Vault Reader (visualização)" para o usuário atual

    ![Adicionar função-grupo de recursos](../media/rbac/image-5.png)

CLI do Azure:
```azurecli
az role assignment create --role "Key Vault Reader (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```

A atribuição de função acima fornece a capacidade de listar objetos do cofre de chaves no Key Vault.

### <a name="key-vault-scope-role-assignment"></a>Atribuição de função de escopo de Key Vault

1. Vá para a \> guia controle de acesso de Key Vault (iam)

2. Clique em Adicionar atribuição de função \> Adicionar

3. Crie a função do diretor de segredos chave "Key Vault o responsável pela versão prévia (visualização)" para o usuário atual.

    ![Atribuição de função-cofre de chaves](../media/rbac/image-6.png)

 CLI do Azure:

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

Depois de criar a atribuição de função acima, você pode criar/atualizar/excluir segredos.

4. Crie um novo segredo (segredos \> + gerar/importar) para testar a atribuição de função no nível de segredo.

    ![Adicionar o cofre de chave de função](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>Atribuição de função de escopo secreto

1. Abra um dos segredos criados anteriormente, observe a visão geral e o controle de acesso (IAM) (visualização)

2. Clique na guia controle de acesso (IAM) (visualização)

    ![Atribuição de função-segredo](../media/rbac/image-8.png)

3. Crie a função do diretor de segredos chave "Key Vault o responsável (versão prévia)" para o usuário atual, assim como foi feito acima para o Key Vault.

CLI do Azure:

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

### <a name="test-and-verify"></a>Testar e verificar

> [!NOTE]
> Os navegadores usam Caching e a atualização de página é necessária após a remoção das atribuições de função.<br>
> Permitir vários minutos para que as atribuições de função sejam atualizadas

1. Valide a adição de novo segredo sem a função "diretor de segredos Key Vault" no nível do cofre de chaves.

Vá para a guia IAM (controle de acesso do Key Vault) e remova a atribuição de função "Key Vault do responsável por segredos (versão prévia)" para este recurso.

![Remover atribuição-cofre de chaves](../media/rbac/image-9.png)

Navegue até o segredo criado anteriormente. Você pode ver todas as propriedades secretas.

![Exibição de segredo com acesso](../media/rbac/image-10.png)

Criar novo segredo (segredos \> + gerar/importar) deve mostrar o erro abaixo:

   ![Criar novo segredo](../media/rbac/image-11.png)

2.  Validar a edição de segredo sem a função "Key Vault Secret" no nível de segredo.

-   Vá para a guia IAM (controle de acesso secreto) criada anteriormente e remova a atribuição de função "Key Vault de segredos (versão prévia)" para esse recurso.

-   Navegue até o segredo criado anteriormente. Você pode ver as propriedades secretas.

   ![Exibição de segredo sem acesso](../media/rbac/image-12.png)

3. Valide os segredos lidos sem a função de leitor no nível do cofre de chaves.

-   Acesse a guia IAM (controle de acesso do grupo de recursos do Key Vault) e remova a atribuição de função "leitor de Key Vault (versão prévia)".

-   Navegar para a guia segredos do Key Vault deve mostrar o erro abaixo:

   ![Guia segredo-erro](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Criando funções personalizadas 

[comando AZ role Definition Create](/cli/azure/role/definition#az-role-definition-create)

**(Script de bash da CLI)</br>**
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```

Para obter mais informações sobre como criar funções personalizadas, consulte:

[Funções personalizadas do Azure](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>Limites e desempenho conhecidos

-   2000 atribuições de função do Azure por assinatura

-   Latência de atribuições de função: no desempenho esperado atual, levará até 10 minutos (600 segundos) depois que as atribuições de função forem alteradas para que a função seja aplicada

## <a name="learn-more"></a>Saiba mais

- [Visão geral do RBAC do Azure](../../role-based-access-control/overview.md)
- [Tutorial de funções personalizadas](../../role-based-access-control/tutorial-custom-role-cli.md)