---
title: Configurar as chaves gerenciadas pelo cliente para a conta do Azure Cosmos DB
description: Saiba como configurar chaves gerenciadas pelo cliente para sua conta do Azure Cosmos DB com o Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/19/2021
ms.author: thweiss
ms.openlocfilehash: 3ee566a598ea7fdf060712c934305ef63467e548
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101656509"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Configurar chaves gerenciadas pelo cliente para sua conta do Azure Cosmos DB com o Azure Key Vault
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!NOTE]
> O uso de chaves gerenciadas pelo cliente com o [repositório analítico](analytical-store-introduction.md) Azure Cosmos DB atualmente requer configuração adicional em sua conta. Entre em contato [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com) para obter detalhes.

Os dados armazenados em sua conta do Azure Cosmos são criptografados de forma automática e direta, com as chaves gerenciadas pela Microsoft (**chaves gerenciadas pelo serviço**). Você pode optar por adicionar uma segunda camada de criptografia com chaves gerenciadas por você (**chaves gerenciadas pelo cliente**).

:::image type="content" source="./media/how-to-setup-cmk/cmk-intro.png" alt-text="Camadas de criptografia em relação aos dados do cliente":::

Você deve armazenar chaves gerenciadas pelo cliente no [Azure Key Vault](../key-vault/general/overview.md) e fornecer uma chave para cada conta do Azure Cosmos que esteja habilitada com chaves gerenciadas pelo cliente. Essa chave é usada para criptografar todos os dados armazenados nessa conta.

> [!NOTE]
> Atualmente, as chaves gerenciadas pelo cliente estão disponíveis apenas para novas contas do Azure Cosmos. Você deve configurá-las durante a criação da conta.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a> Registrar o provedor de recursos do Azure Cosmos DB para sua assinatura do Azure

1. Entre no [portal do Azure](https://portal.azure.com/), acesse sua assinatura do Azure e selecione **Provedores de recursos** na guia **configurações**:

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp.png" alt-text="Entrada de provedores de recursos no menu à esquerda":::

1. Pesquise o provedor de recursos do **Microsoft.DocumentDB**. Verifique se o provedor de recursos já está marcado como registrado. Caso contrário, escolha o provedor de recursos e selecione **Registrar**:

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp-register.png" alt-text="Registrar o provedor de recursos do Microsoft.DocumentDB":::

## <a name="configure-your-azure-key-vault-instance"></a>Configurar sua instância do Azure Key Vault

O uso de chaves gerenciadas pelo cliente com Azure Cosmos DB exige que você defina duas propriedades na instância do Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia: **Exclusão reversível** e **Proteção de limpeza**.

Se você criar uma nova instância do Azure Key Vault, habilite essas propriedades durante a criação:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="Habilitar a exclusão reversível e a proteção de limpeza para uma nova instância do Azure Key Vault":::

Se estiver usando uma instância existente do Azure Key Vault, você poderá verificar se essas propriedades estão habilitadas examinando a seção **Propriedades** no portal do Azure. Se qualquer uma dessas propriedades não estiver habilitada, consulte as seções “Habilitar a exclusão reversível” e “Habilitar a proteção de limpeza” em um dos seguintes artigos:

- [Como usar a exclusão reversível com o PowerShell](../key-vault/general/key-vault-recovery.md)
- [Como usar a exclusão reversível com a CLI do Azure](../key-vault/general/key-vault-recovery.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Adicionar uma política de acesso à sua instância do Azure Key Vault

1. No portal do Azure, vá até a instância do Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia. Selecione **Políticas de acesso** no menu à esquerda:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-ap.png" alt-text="Políticas de acesso no menu à esquerda":::

1. Selecione **+ Adicionar política de acesso**.

1. No menu suspenso **Permissões de chave**, selecione as permissões **Obter**, **Desencapsular chave** e **Encapsular chave**:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png" alt-text="Selecionar as permissões certas":::

1. Em **Selecionar entidade de segurança**, selecionar **Nenhuma selecionada**. Em seguida, pesquise a entidade de segurança do **Azure Cosmos DB** e selecione-a (para facilitar a localização, você também pode pesquisar pela ID da entidade de segurança: `a232010e-820c-4083-83bb-3ace5fc29d0b` para qualquer região do Azure, exceto regiões do Azure Governamental onde a ID da entidade de segurança é `57506a73-e302-42a9-b869-6f12d9ec29e9`). Por último, escolha **Selecionar** na parte inferior da tela. Se a entidade de segurança do **Azure Cosmos DB** não estiver na lista, talvez seja necessário registrar novamente o provedor de recursos **Microsoft.DocumentDB**, conforme descrito na seção [Registrar o provedor de recursos](#register-resource-provider) deste artigo.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap.png" alt-text="Selecionar a entidade de segurança do Azure Cosmos DB":::

1. Selecione **Adicionar** para adicionar a nova política de acesso.

1. Selecione **salvar** na instância de Key Vault para salvar todas as alterações.

## <a name="generate-a-key-in-azure-key-vault"></a>Gerar uma chave no Azure Key Vault

1. No portal do Azure, vá até a instância do Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia. Em seguida, selecione **Chaves** no menu à esquerda:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keys.png" alt-text="Entrada de chaves no menu à esquerda":::

1. Selecione **Gerar/Importar**, forneça um nome para a nova chave e selecione um tamanho de chave RSA. Recomendamos um mínimo de 3072 para maior segurança. Em seguida, selecione **Criar**:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-gen.png" alt-text="Criar uma nova chave":::

1. Depois que a chave for criada, selecione a chave recém-criada e, em seguida, sua versão atual.

1. Copie o **Identificador de chave** da chave, exceto a parte após a última barra “/”:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keyid.png" alt-text="Copiar o identificador de chave da chave":::

## <a name="create-a-new-azure-cosmos-account"></a>Criar uma nova conta do Azure Cosmos

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Quando criar uma nova conta do Azure Cosmos DB no portal do Azure, escolha **Chave gerenciada pelo cliente** na etapa **Criptografia**. No campo **URI da chave**, cole o URI/identificador de chave da chave do Azure Key Vault que você copiou da etapa anterior:

:::image type="content" source="./media/how-to-setup-cmk/portal-cosmos-enc.png" alt-text="Definir os parâmetros da chave gerenciada pelo cliente no portal do Azure":::

### <a name="using-azure-powershell"></a><a id="using-powershell"></a> Usar o PowerShell do Azure

Quando criar uma nova conta do Azure Cosmos DB com o PowerShell:

- Passe o URI da chave do Azure Key Vault copiada anteriormente sob a propriedade **keyVaultKeyUri** em **Propertyobject**.

- Use **2019-12-12** ou posterior como a versão da API.

> [!IMPORTANT]
> Você deve definir a propriedade `locations` explicitamente para que a conta seja criada com êxito com as chaves gerenciadas pelo cliente.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

Depois que a conta tiver sido criada, você poderá verificar se as chaves gerenciadas pelo cliente foram habilitadas buscando o URI da chave do Azure Key Vault:

```powershell
Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    | Select-Object -ExpandProperty Properties `
    | Select-Object -ExpandProperty keyVaultKeyUri
```

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo do Azure Resource Manager

Quando você cria uma nova conta do Azure Cosmos por meio de um modelo de Azure Resource Manager:

- Passe o URI da chave do Azure Key Vault que você copiou anteriormente sob a propriedade **keyVaultKeyUri** no objeto **propriedades**.

- Use **2019-12-12** ou posterior como a versão da API.

> [!IMPORTANT]
> Você deve definir a propriedade `locations` explicitamente para que a conta seja criada com êxito com as chaves gerenciadas pelo cliente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}
```

Implante o modelo com o seguinte script do PowerShell:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a> Usar a CLI do Azure

Quando criar uma nova conta do Azure Cosmos por meio da CLI do Azure, passe o URI da chave do Azure Key Vault que você copiou anteriormente no parâmetro `--key-uri`.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

Depois que a conta tiver sido criada, você poderá verificar se as chaves gerenciadas pelo cliente foram habilitadas buscando o URI da chave do Azure Key Vault:

```azurecli-interactive
az cosmosdb show \
    -n $accountName \
    -g $resourceGroupName \
    --query keyVaultKeyUri
```

## <a name="key-rotation"></a>Alteração de chaves

A rotação da chave gerenciada pelo cliente usada pela sua conta do Azure Cosmos pode ser feita de duas maneiras.

- Crie uma nova versão da chave atualmente usada no Azure Key Vault:

  :::image type="content" source="./media/how-to-setup-cmk/portal-akv-rot.png" alt-text="Criar uma nova versão da chave":::

- Troque a chave usada atualmente por uma totalmente diferente atualizando o URI de chave em sua conta. No portal do Azure, vá para sua conta do cosmos do Azure e selecione **criptografia de dados** no menu à esquerda:

    :::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="A entrada do menu de criptografia de dados":::

    Em seguida, substitua o **URI da chave** pela nova chave que você deseja usar e selecione **salvar**:

    :::image type="content" source="./media/how-to-setup-cmk/portal-key-swap.png" alt-text="Atualizar o URI da chave":::

    Veja como realizar o mesmo resultado no PowerShell:

    ```powershell
    $resourceGroupName = "myResourceGroup"
    $accountName = "mycosmosaccount"
    $newKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-new-key>"
    
    $account = Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
        -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    
    $account.Properties.keyVaultKeyUri = $newKeyUri
    
    $account | Set-AzResource -Force
    ```

A versão de chave ou chave anterior pode ser desabilitada após 24 horas, ou depois que os [logs de auditoria Azure Key Vault](../key-vault/general/logging.md) não mostram mais a atividade de Azure Cosmos DB nessa chave ou versão de chave.
    
## <a name="error-handling"></a>Tratamento de erros

Ao usar chaves de Customer-Managed (CMK) em Azure Cosmos DB, se houver erros, Azure Cosmos DB retornará os detalhes do erro junto com um código de substatus HTTP na resposta. Você pode usar esse código de substatus para depurar a causa raiz do problema. Consulte o artigo [códigos de status HTTP para Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) para obter a lista de códigos de substatus HTTP com suporte.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>Há encargos adicionais para habilitar chaves gerenciadas pelo cliente?

Não, não há encargos para habilitar esse recurso.

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>Como as chaves gerenciadas pelo cliente afetam o planejamento de capacidade?

Quando chaves gerenciadas pelo cliente são usadas, as [Unidades de Solicitação](./request-units.md) consumidas por suas operações de banco de dados sofrem um aumento que reflete o processamento adicional necessário para executar a criptografia e a descriptografia dos seus dados. Isso pode levar a uma utilização um pouco mais alta de sua capacidade provisionada. Use a tabela abaixo como orientação:

| Tipo de operação | Aumento da Unidade de Solicitação |
|---|---|
| Leituras de ponto (buscando itens por sua ID) | + 5% por operação |
| Qualquer operação de gravação | + 6% por operação<br/>aprox. + 0,06 RU por propriedade indexada |
| Consultas, leitura de feed de alterações ou feed de conflitos | + 15% por operação |

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Quais dados são criptografados com as chaves gerenciadas pelo cliente?

Todos os dados armazenados em sua conta do Azure Cosmos são criptografados com as chaves gerenciadas pelo cliente, exceto os seguintes metadados:

- Os nomes das suas [contas, bancos de dados e contêineres](./account-databases-containers-items.md#elements-in-an-azure-cosmos-account) do Azure Cosmos DB

- Os nomes dos seus [procedimentos armazenados](./stored-procedures-triggers-udfs.md)

- Os caminhos de propriedade declarados em suas [políticas de indexação](./index-policy.md)

- Os valores das [chaves de partição](./partitioning-overview.md) dos seus contêineres

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>As chaves gerenciadas pelo cliente têm suporte para contas existentes do Azure Cosmos?

Este recurso está atualmente disponível somente para novas contas.

### <a name="is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store"></a>É possível usar chaves gerenciadas pelo cliente em conjunto com o Azure Cosmos DB [repositório analítico](analytical-store-introduction.md)?

Sim, mas isso atualmente requer configuração adicional em sua conta. Entre em contato [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com) para obter detalhes.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Há um plano para dar suporte uma granularidade mais refinada do que as chaves de nível de conta?

Atualmente não, mas as chaves de nível de contêiner estão sendo consideradas.

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>Como saber se as chaves gerenciadas pelo cliente estão habilitadas na minha conta do Azure Cosmos?

Na portal do Azure, acesse sua conta do Azure Cosmos e veja a entrada de **criptografia de dados** no menu à esquerda; Se essa entrada existir, as chaves gerenciadas pelo cliente serão habilitadas em sua conta:

:::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="A entrada do menu de criptografia de dados":::

Você também pode buscar programaticamente os detalhes da sua conta do Azure Cosmos e procurar a presença da `keyVaultKeyUri` propriedade. Consulte acima para saber as maneiras de como fazer isso [no PowerShell](#using-powershell) e [usando a CLI do Azure](#using-azure-cli).

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Como as chaves gerenciadas pelo cliente afetam um backup?

O Azure Cosmos DB realiza [backups regulares e automáticos](./online-backup-and-restore.md) dos dados armazenados em sua conta. Esta operação faz backup dos dados criptografados. Para usar o backup restaurado, a chave de criptografia que você usou no momento do backup é necessária. Isso significa que nenhuma revogação foi feita e a versão da chave que foi usada no momento do backup ainda estará habilitada.

### <a name="how-do-i-revoke-an-encryption-key"></a>Como fazer para revogar uma chave de criptografia?

A revogação de uma chave é feita desabilitando a versão mais recente da chave:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev2.png" alt-text="Desabilitar a versão de uma chave":::

Como alternativa, para revogar todas as chaves de uma instância do Azure Key Vault, você pode excluir a política de acesso concedida à entidade de segurança do Azure Cosmos DB:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev.png" alt-text="Excluir a política de acesso para a entidade de segurança do Azure Cosmos DB":::

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Quais operações estão disponíveis depois que uma chave gerenciada pelo cliente é revogada?

A única operação possível quando a chave de criptografia é revogada é a exclusão da conta.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [criptografia de dados no Azure Cosmos DB](./database-encryption-at-rest.md).
- Obtenha uma visão geral de [acesso seguro aos dados no Cosmos DB](secure-access-to-data.md).