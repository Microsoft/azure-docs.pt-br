---
title: Gerenciar o serviço Blockchain do Azure usando o CLI do Azure
description: Como gerenciar o serviço Blockchain do Azure com o CLI do Azure
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 36b012c486c0c7d3303a81998e88f1605999c899
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87170861"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Gerenciar o serviço Blockchain do Azure usando o CLI do Azure

Além do portal do Azure, você pode usar CLI do Azure para gerenciar Membros blockchain e nós de transação para o serviço Blockchain do Azure.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

Se preferir instalar e usar a CLI localmente, consulte [instalar CLI do Azure](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Prepare o seu ambiente

1. Entrar.

    Entre usando o comando [az login](/cli/azure/reference-index#az-login) se estiver usando uma instalação local da CLI.

    ```azurecli
    az login
    ```

    Siga as etapas exibidas em nosso terminal para concluir o processo de autenticação.

1. Instale a extensão de CLI do Azure.

    Ao trabalhar com referências de extensão para a CLI do Azure, você deve primeiro instalar a extensão.  As extensões da CLI do Azure fornecem acesso a comandos experimentais e de pré-lançamento que ainda não foram enviados como parte da CLI principal.  Para saber mais sobre extensões, incluindo atualização e desinstalação, confira [Usar extensões com a CLI do Azure](/cli/azure/azure-cli-extensions-overview).

    Instale a [extensão para o Azure Blockchain Service](/cli/azure/ext/blockchain/blockchain) executando o seguinte comando:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Criar membro blockchain

Exemplo [cria um membro blockchain](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-create) no serviço Blockchain do Azure que executa o protocolo de razão de quorum em um novo consórcio.

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Azure Blockchain são criados. |
| **name** | Um nome exclusivo que identifica o membro do blockchain do serviço Azure Blockchain. O nome é usado para o endereço do ponto de extremidade público. Por exemplo, `myblockchainmember.blockchain.azure.com`. |
| **local** | Região do Azure em que o membro do blockchain é criado. Por exemplo, `eastus`. Escolha o local mais próximo para os usuários ou para outros aplicativos do Azure. Os recursos podem não estar disponíveis em algumas regiões. |
| **password** | A senha do nó de transação padrão do membro. Use a senha para a autenticação básica ao se conectar ao ponto de extremidade público do nó de transação padrão do membro do blockchain. A senha deve atender a três dos quatro requisitos a seguir: o comprimento deve ter entre 12 & 72 caracteres, um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial que não seja um sinal numérico (#), porcentagem (%), vírgula (,), estrela (*), aspas de fundo ( \` ), aspas duplas ("), aspas simples ('), hífen (-) e semicolumn (;)|
| **protocol** | Protocolo do Blockchain. No momento, há suporte para o protocolo *Quorum*. |
| **consortium** | Nome do consórcio a ser ingressado ou criado. Para obter mais informações sobre os consórcios, confira [Consórcio do Azure Blockchain Service](consortium.md). |
| **consortium-management-account-password** | A senha da conta do consórcio também é conhecida como a senha da conta do membro. A senha da conta do membro é usada para criptografar a chave privada para a conta do Ethereum criada para o membro. Use a conta do membro e a senha da conta do membro para o gerenciamento do consórcio. |
| **sku** | Tipo de camada. Selecione *Standard* ou *Basic*. Use a camada *Basic* para desenvolvimento, teste e prova de conceitos. Use a camada *Standard* para implantações de nível de produção. Você também deverá usar o nível *Standard* se estiver usando o Gerenciador de Dados do Blockchain ou enviando um alto volume de transações particulares. Não há suporte para a alteração do tipo de preço entre Básico e Standard após a criação do membro. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Alterar as senhas de membro blockchain ou as regras de firewall

Exemplo [atualiza a senha de um membro do blockchain, a](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-update)senha de gerenciamento do consórcio e a regra de firewall.

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Azure Blockchain são criados. |
| **name** | Nome que identifica o membro do serviço Blockchain do Azure. |
| **password** | A senha do nó de transação padrão do membro. Use a senha para a autenticação básica ao se conectar ao ponto de extremidade público do nó de transação padrão do membro do blockchain. A senha deve atender a três dos quatro requisitos a seguir: o comprimento deve ter entre 12 & 72 caracteres, um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial que não seja um sinal numérico (#), porcentagem (%), vírgula (,), estrela (*), aspas de fundo ( \` ), aspas duplas ("), aspas simples ('), hífen (-) e semicolumn (;)|
| **consortium-management-account-password** | A senha da conta do consórcio também é conhecida como a senha da conta do membro. A senha da conta do membro é usada para criptografar a chave privada para a conta do Ethereum criada para o membro. Use a conta do membro e a senha da conta do membro para o gerenciamento do consórcio. |
| **firewall – regras** | Endereço IP inicial e final da lista de permissões de IP. |

## <a name="create-transaction-node"></a>Criar nó de transação

[Crie um nó de transação](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-create) dentro de um membro blockchain existente. Ao adicionar nós de transação, você pode aumentar o isolamento de segurança e distribuir a carga. Por exemplo, você poderia ter um ponto de extremidade de nó de transação para diferentes aplicativos cliente.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Azure Blockchain são criados. |
| **local** | Região do Azure do membro blockchain. |
| **nome do membro** | Nome que identifica o membro do serviço Blockchain do Azure. |
| **password** | A senha para o nó de transação. Use a senha para autenticação básica ao se conectar ao ponto de extremidade público do nó de transação. A senha deve atender a três dos quatro requisitos a seguir: o comprimento deve ter entre 12 & 72 caracteres, um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial que não seja um sinal numérico (#), porcentagem (%), vírgula (,), estrela (*), aspas de fundo ( \` ), aspas duplas ("), aspas simples ('), hífen (-) e semicolumn (;)|
| **name** | Nome do nó de transação. |

## <a name="change-transaction-node-password"></a>Alterar senha do nó de transação

Exemplo [atualiza uma senha de nó de transação](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-update) .

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Blockchain do Azure existem. |
| **nome do membro** | Nome que identifica o membro do serviço Blockchain do Azure. |
| **password** | A senha para o nó de transação. Use a senha para autenticação básica ao se conectar ao ponto de extremidade público do nó de transação. A senha deve atender a três dos quatro requisitos a seguir: o comprimento deve ter entre 12 & 72 caracteres, um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial que não seja um sinal numérico (#), porcentagem (%), vírgula (,), estrela (*), aspas de fundo ( \` ), aspas duplas ("), aspas simples ('), hífen (-) e semicolumn (;)|
| **name** | Nome do nó de transação. |

## <a name="list-api-keys"></a>Listar chaves de API

As chaves de API podem ser usadas para acesso de nó semelhante ao nome de usuário e à senha. Há duas chaves de API para dar suporte à rotação de chaves. Use o comando a seguir para [listar suas chaves de API](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-list-api-key).

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Blockchain do Azure existem. |
| **name** | Nome do membro Blockchain do serviço Blockchain do Azure |

## <a name="regenerate-api-keys"></a>Regenerar chaves de API

Use o comando a seguir para [regenerar as chaves de API](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-regenerate-api-key).

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Blockchain do Azure existem. |
| **name** | Nome do membro Blockchain do serviço Blockchain do Azure. |
| **keyName** | Substitua \<keyValue\> por uma key1, Key2 ou ambos. |

## <a name="delete-a-transaction-node"></a>Excluir um nó de transação

Exemplo [exclui um nó de transação de membro blockchain](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-delete).

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Blockchain do Azure existem. |
| **nome do membro** | Nome do membro Blockchain do serviço Blockchain do Azure que também inclui o nome do nó de transação a ser excluído. |
| **name** | Nome do nó de transação a ser excluído. |

## <a name="delete-a-blockchain-member"></a>Excluir um membro do blockchain

Exemplo [exclui um membro blockchain](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-delete).

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Blockchain do Azure existem. |
| **name** | Nome do membro Blockchain do serviço Blockchain do Azure a ser excluído. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Conceder acesso ao usuário do Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parâmetro | Descrição |
|---------|-------------|
| **role** | Nome da função do Azure AD. |
| **assignee** | ID de usuário do Azure AD. Por exemplo, `user@contoso.com` |
| **escopo** | Escopo da atribuição de função. Pode ser um membro blockchain ou um nó de transação. |

**Exemplo:**

Conceder acesso ao nó para o usuário do Azure AD para blockchain **membro**:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Exemplo:**

Conceder acesso ao nó para o usuário do Azure AD para o **nó de transação**blockchain:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Conceder acesso ao nó para a função de aplicativo ou grupo do Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parâmetro | Descrição |
|---------|-------------|
| **role** | Nome da função do Azure AD. |
| **ID do objeto de atribuição** | ID do grupo ou ID do aplicativo do Azure AD. |
| **escopo** | Escopo da atribuição de função. Pode ser um membro blockchain ou um nó de transação. |

**Exemplo:**

Conceder acesso ao nó para a **função de aplicativo**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Remover o acesso ao nó do Azure AD

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parâmetro | Descrição |
|---------|-------------|
| **role** | Nome da função do Azure AD. |
| **assignee** | ID de usuário do Azure AD. Por exemplo, `user@contoso.com` |
| **escopo** | Escopo da atribuição de função. Pode ser um membro blockchain ou um nó de transação. |

## <a name="next-steps"></a>Próximas etapas

Saiba como [configurar os nós de transação do serviço Blockchain do Azure com o portal do Azure](configure-transaction-nodes.md).
