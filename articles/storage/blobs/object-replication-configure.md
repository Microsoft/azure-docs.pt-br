---
title: Configurar a replicação de objeto
titleSuffix: Azure Storage
description: Saiba como configurar a replicação de objeto para copiar blobs de blocos de maneira assíncrona de um contêiner em uma conta de armazenamento para outra.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 2b6855d72b644a3fe1fa46c883eb7414383a1a57
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031694"
---
# <a name="configure-object-replication-for-block-blobs"></a>Configurar a replicação de objeto para BLOBs de blocos

A replicação de objetos copia de modo assíncrono os blobs de blocos entre uma conta de armazenamento de origem e uma conta de destino. Para obter mais informações sobre a replicação de objeto, consulte [replicação de objeto](object-replication-overview.md).

Ao configurar a replicação de objeto, você cria uma política de replicação que especifica a conta de armazenamento de origem e a conta de destino. Uma política de replicação inclui uma ou mais regras que especificam um contêiner de origem e um contêiner de destino e indicam quais blobs de blocos no contêiner de origem serão replicados.

Este artigo descreve como configurar a replicação de objeto para sua conta de armazenamento usando o portal do Azure, o PowerShell ou a CLI do Azure. Você também pode usar uma das bibliotecas de clientes do provedor de recursos do Armazenamento do Azure para configurar a replicação de objeto.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>Criar uma política de replicação e regras

Antes de configurar a replicação de objeto, crie as contas de armazenamento de origem e de destino se elas ainda não existirem. Ambas as contas precisam ser contas de armazenamento de uso geral v2. Para saber mais informações, confira [Criar uma conta do Armazenamento do Azure](../common/storage-account-create.md).

A replicação de objeto requer que o controle de versão de blob esteja habilitado para a conta de origem e de destino e que o feed de alterações de blob esteja habilitado para a conta de origem. Para saber mais sobre o controle de versão de BLOB, consulte [controle de versão de blob](versioning-overview.md). Para saber mais sobre o feed de alterações, consulte [suporte do feed de alterações no armazenamento de BLOBs do Azure](storage-blob-change-feed.md). Tenha em mente que habilitar esses recursos pode resultar em custos adicionais.

Uma conta de armazenamento pode servir como a conta de origem para até duas contas de destino. As contas de origem e de destino podem estar na mesma região ou em regiões diferentes. Eles também podem residir em assinaturas diferentes e em locatários diferentes do Azure Active Directory (Azure AD). Somente uma política de replicação pode ser criada para cada par de contas.

Ao configurar a replicação de objeto, você cria uma política de replicação na conta de destino por meio do provedor de recursos de armazenamento do Azure. Depois que a política de replicação é criada, o armazenamento do Azure atribui a ela uma ID de política. Em seguida, você deve associar essa política de replicação à conta de origem usando a ID da política. A ID da política nas contas de origem e de destino deve ser a mesma para que a replicação ocorra.

Para configurar uma política de replicação de objeto para uma conta de armazenamento, você deve receber a função de **colaborador** de Azure Resource Manager, com escopo para o nível da conta de armazenamento ou superior. Para obter mais informações, consulte [funções internas do Azure](../../role-based-access-control/built-in-roles.md) na documentação do controle de acesso baseado em função do Azure (RBAC do Azure).

### <a name="configure-object-replication-when-you-have-access-to-both-storage-accounts"></a>Configurar a replicação de objeto quando você tiver acesso a ambas as contas de armazenamento

Se você tiver acesso às contas de armazenamento de origem e de destino, poderá configurar a política de replicação de objeto em ambas as contas.

Antes de configurar a replicação de objeto no portal do Azure, crie os contêineres de origem e de destino nas respectivas contas de armazenamento se eles ainda não existirem. Além disso, habilite o controle de versão do blob e o feed de alterações na conta de origem e habilite o controle de versão do blob na conta de destino.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

O portal do Azure cria automaticamente a política na conta de origem depois de configurá-la para a conta de destino.

Para criar uma política de replicação no portal do Azure, siga estas etapas:

1. Navegue até a conta de armazenamento de origem no portal do Azure.
1. Em **serviço blob**, selecione **replicação de objeto**.
1. Selecione **configurar regras de replicação**.
1. Selecione a assinatura de destino e a conta de armazenamento.
1. Na seção **Pares de contêineres**, selecione um contêiner de origem da conta de origem e um contêiner de destino da conta de destino. Você pode criar até dez pares de contêineres por política de replicação.

    A imagem a seguir mostra um conjunto de regras de replicação.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Captura de tela mostrando regras de replicação no portal do Azure":::

1. Se desejar, especifique um ou mais filtros para copiar somente os blobs que correspondam a um padrão de prefixo. Por exemplo, se você especificar um prefixo `b`, somente os blobs cujo nome começar com essa letra serão replicados. Você pode especificar um diretório virtual como parte do prefixo. A cadeia de caracteres de prefixo não dá suporte a caracteres curinga.

    A imagem a seguir mostra filtros que restringem quais blobs são copiados como parte de uma regra de replicação.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Captura de tela mostrando filtros para uma regra de replicação":::

1. Por padrão, o escopo de cópia é definido para copiar somente objetos novos. Para copiar todos os objetos no contêiner ou para copiar objetos de uma data e hora personalizadas, selecione o link **alterar** e configure o escopo de cópia para o par de contêineres.

    A imagem a seguir mostra um escopo de cópia personalizado que copia objetos de uma data e hora especificadas em diante.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Captura de tela mostrando o escopo de cópia personalizados para a replicação de objeto":::

1. Selecione **Salvar e aplicar** para criar a política de replicação e iniciar a replicação de dados.

Depois de configurar a replicação de objeto, o portal do Azure exibe a política de replicação e as regras, conforme mostrado na imagem a seguir.

:::image type="content" source="media/object-replication-configure/object-replication-policies-portal.png" alt-text="Captura de tela mostrando a política de replicação de objeto no portal do Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para criar uma política de replicação com o PowerShell, primeiro instale a versão [2.5.0](https://www.powershellgallery.com/packages/Az.Storage/2.5.0) ou posterior do módulo AZ. Storage PowerShell. Para obter mais informações sobre como instalar o Azure PowerShell, consulte [Instalar o Azure PowerShell com o PowerShellGet](/powershell/azure/install-az-ps).

O exemplo a seguir mostra como criar uma política de replicação nas contas de origem e de destino. Lembre-se de substituir os valores entre colchetes angulares pelos seus próprios valores:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar uma política de replicação com CLI do Azure, primeiro instale CLI do Azure versão 2.11.1 ou posterior. Para obter mais informações, consulte Introdução [ao CLI do Azure](/cli/azure/get-started-with-azure-cli).

Em seguida, habilite o controle de versão de blob nas contas de armazenamento de origem e de destino e habilite o feed de alterações na conta de origem chamando o comando [AZ Storage Account blob-Service-Properties Update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) . Lembre-se de substituir os valores entre colchetes angulares pelos seus próprios valores:

```azurecli
az login

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning \
    --enable-change-feed

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Crie os contêineres de origem e de destino nas respectivas contas de armazenamento.

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-1 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-2 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
```

Crie uma nova política de replicação e uma regra associada na conta de destino chamando a [conta de armazenamento AZ ou a criação da política](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create).

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container-1 \
    --destination-container dest-container-1 \
    --min-creation-time '2020-09-10T00:00:00Z' \
    --prefix-match a

```

O armazenamento do Azure define a ID de política para a nova política quando ela é criada. Para adicionar mais regras à política, chame a [conta de armazenamento AZ ou a regra de política adicionar](/cli/azure/storage/account/or-policy/rule#az_storage_account_or_policy_rule_add) e forneça a ID da política.

```azurecli
az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-container source-container-2 \
    --destination-container dest-container-2 \
    --policy-id <policy-id> \
    --prefix-match b
```

Em seguida, crie a política na conta de origem usando a ID da política.

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --policy-id <policy-id> |
    az storage account or-policy create --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --policy "@-"
```

---

### <a name="configure-object-replication-when-you-have-access-only-to-the-destination-account"></a>Configurar a replicação de objeto quando você tiver acesso somente à conta de destino

Se você não tiver permissões para a conta de armazenamento de origem, poderá configurar a replicação de objeto na conta de destino e fornecer um arquivo JSON que contenha a definição de política para outro usuário para criar a mesma política na conta de origem. Por exemplo, se a conta de origem estiver em um locatário do Azure AD diferente da conta de destino, você poderá usar essa abordagem para configurar a replicação de objeto.

Tenha em mente que você deve receber a função de **colaborador** de Azure Resource Manager no escopo do nível da conta de armazenamento de destino ou superior para criar a política. Para obter mais informações, consulte [funções internas do Azure](../../role-based-access-control/built-in-roles.md) na documentação do controle de acesso baseado em função do Azure (RBAC do Azure).

A tabela a seguir resume quais valores usar para a ID de política e IDs de regra no arquivo JSON em cada cenário.

| Ao criar o arquivo JSON para esta conta... | Definir a ID da política para este valor | Definir IDs de regra para este valor |
|-|-|-|
| Conta de destino | O valor da cadeia de caracteres *padrão*. O armazenamento do Azure criará o valor da ID de política para você. | Uma cadeia de caracteres vazia. O armazenamento do Azure criará os valores de ID de regra para você. |
| Conta de origem | O valor da ID da política retornado quando você baixa a política definida na conta de destino como um arquivo JSON. | Os valores das IDs de regra retornaram quando você baixa a política definida na conta de destino como um arquivo JSON. |

O exemplo a seguir define uma política de replicação na conta de destino com uma única regra que corresponde ao prefixo *b* e define o tempo de criação mínimo para BLOBs que devem ser replicados. Lembre-se de substituir os valores entre colchetes angulares pelos seus próprios valores:

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "<source-account>",
    "destinationAccount": "<dest-account>",
    "rules": [
      {
        "ruleId": "",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2020-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para configurar a replicação de objeto na conta de destino com um arquivo JSON no portal do Azure, siga estas etapas:

1. Crie um arquivo JSON local que define a política de replicação na conta de destino. Defina o campo **PolicyId** como *padrão* para que o armazenamento do Azure defina a ID da política.

    Uma maneira fácil de criar um arquivo JSON que define uma política de replicação é primeiro criar uma política de replicação de teste entre duas contas de armazenamento na portal do Azure. Em seguida, você pode baixar as regras de replicação e modificar o arquivo JSON conforme necessário.

1. Navegue até as configurações de **replicação de objeto** da conta de destino na portal do Azure.
1. Selecione **carregar regras de replicação**.
1. Carregue o arquivo JSON. O portal do Azure exibe a política e as regras que serão criadas, conforme mostrado na imagem a seguir.

    :::image type="content" source="media/object-replication-configure/replication-rules-upload-portal.png" alt-text="Captura de tela mostrando como carregar um arquivo JSON para definir uma política de replicação":::

1. Selecione **carregar** para criar a política de replicação na conta de destino.

Em seguida, você pode baixar um arquivo JSON que contém a definição de política que você pode fornecer a outro usuário para configurar a conta de origem. Para baixar esse arquivo JSON, siga estas etapas:

1. Navegue até as configurações de **replicação de objeto** da conta de destino na portal do Azure.
1. Selecione o botão **mais** ao lado da política que você deseja baixar e, em seguida, selecione **baixar regras**, conforme mostrado na imagem a seguir.

    :::image type="content" source="media/object-replication-configure/replication-rules-download-portal.png" alt-text="Captura de tela mostrando como baixar regras de replicação para um arquivo JSON":::

1. Salve o arquivo JSON em seu computador local para compartilhar com outro usuário para configurar a política na conta de origem.

O arquivo JSON baixado inclui a ID de política que o armazenamento do Azure criou para a política na conta de destino. Você deve usar a mesma ID de política para configurar a replicação de objeto na conta de origem.

Tenha em mente que carregar um arquivo JSON para criar uma política de replicação para a conta de destino por meio do portal do Azure não cria automaticamente a mesma política na conta de origem. Outro usuário deve criar a política na conta de origem antes de o armazenamento do Azure começar a replicar objetos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para baixar um arquivo JSON que contém a definição de política de replicação para a conta de destino do PowerShell, chame o comando [Get-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/get-azstorageobjectreplicationpolicy) para retornar a política. Em seguida, converta a política em JSON e salve-a como um arquivo local, conforme mostrado no exemplo a seguir. Lembre-se de substituir valores entre colchetes angulares e o caminho do arquivo pelos seus próprios valores:

```powershell
$rgName = "<resource-group>"
$destAccountName = "<destination-storage-account>"

$destPolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName
$destPolicy | ConvertTo-Json -Depth 5 > c:\temp\json.txt
```

Para usar o arquivo JSON para definir a política de replicação na conta de origem com o PowerShell, recupere o arquivo local e converta de JSON em um objeto. Em seguida, chame o comando [set-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/set-azstorageobjectreplicationpolicy) para configurar a política na conta de origem, conforme mostrado no exemplo a seguir. Lembre-se de substituir valores entre colchetes angulares e o caminho do arquivo pelos seus próprios valores:

```powershell
$object = Get-Content -Path C:\temp\json.txt | ConvertFrom-Json
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $object.PolicyId `
    -SourceAccount $object.SourceAccount `
    -DestinationAccount $object.DestinationAccount `
    -Rule $object.Rules
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para gravar a definição de política de replicação para a conta de destino em um arquivo JSON de CLI do Azure, chame o comando [AZ Storage Account ou-Policy show](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_show) e a saída para um arquivo.

O exemplo a seguir grava a definição de política em um arquivo JSON chamado *policy.jsem*. Lembre-se de substituir valores entre colchetes angulares e o caminho do arquivo pelos seus próprios valores:

```azurecli
az storage account or-policy show \
    --account-name <dest-account-name> \
    --policy-id  <policy-id> > policy.json
```

Para usar o arquivo JSON para configurar a política de replicação na conta de origem com CLI do Azure, chame o comando [AZ Storage Account ou-Policy Create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) e referencie o *policy.jsno* arquivo. Lembre-se de substituir valores entre colchetes angulares e o caminho do arquivo pelos seus próprios valores:

```azurecli
az storage account or-policy create \
    -resource-group <resource-group> \
    --source-account <source-account-name> \
    --policy @policy.json
```

---

## <a name="check-the-replication-status-of-a-blob"></a>Verificar o status de replicação de um blob

Você pode verificar o status de replicação de um blob na conta de origem usando o portal do Azure, o PowerShell ou o CLI do Azure. As propriedades de replicação de objeto não são populadas até que a replicação seja concluída ou falhou.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para verificar o status de replicação de um blob na conta de origem no portal do Azure, siga estas etapas:

1. Navegue até a conta de origem no portal do Azure.
1. Localize o contêiner que inclui o blob de origem.
1. Selecione o blob para exibir suas propriedades. Se o blob tiver sido replicado com êxito, você verá na seção **replicação de objeto** que o status está definido como *concluído*. A ID da política de replicação e a ID da regra que governa a replicação de objeto para esse contêiner também são listadas.

:::image type="content" source="media/object-replication-configure/check-replication-status-source.png" alt-text="Captura de tela mostrando o status de replicação para um blob na conta de origem":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar o status de replicação de um blob na conta de origem com o PowerShell, obtenha o valor da propriedade **ReplicationStatus** de replicação de objeto, conforme mostrado no exemplo a seguir. Lembre-se de substituir os valores entre colchetes angulares pelos seus próprios valores:

```powershell
$ctxSrc = (Get-AzStorageAccount -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName).Context
$blobSrc = Get-AzStorageBlob -Container $srcContainerName1 `
    -Context $ctxSrc `
    -Blob <blob-name>
$blobSrc.BlobProperties.ObjectReplicationSourceProperties[0].Rules[0].ReplicationStatus
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar o status de replicação de um blob na conta de origem com CLI do Azure, obtenha o valor da propriedade **status** de replicação do objeto, conforme mostrado no exemplo a seguir:

```azurecli
az storage blob show \
    --account-name <source-account-name> \
    --container-name <source-container-name> \
    --name <source-blob-name> \
    --query 'objectReplicationSourceProperties[].rules[].status' \
    --output tsv \
    --auth-mode login
```

---

Se o status de replicação de um blob na conta de origem indicar falha, investigue as seguintes causas possíveis:

- Verifique se a política de replicação de objeto está configurada na conta de destino.
- Verifique se o contêiner de destino ainda existe.
- Se o blob de origem tiver sido criptografado com uma chave fornecida pelo cliente como parte de uma operação de gravação, a replicação do objeto falhará. Para obter mais informações sobre chaves fornecidas pelo cliente, consulte [fornecer uma chave de criptografia em uma solicitação para o armazenamento de BLOBs](encryption-customer-provided-keys.md).

## <a name="remove-a-replication-policy"></a>Remover uma política de replicação

Para remover uma política de replicação e as respectivas regras associadas, use o portal do Azure o PowerShell ou a CLI.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para remover uma política de replicação no portal do Azure, siga estas etapas:

1. Navegue até a conta de armazenamento de origem no portal do Azure.
1. Em **Configurações**, selecione **Replicação de objeto**.
1. Clique no botão **Mais** ao lado do nome da política.
1. Selecione **Excluir Regras**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para remover uma política de replicação, exclua a política da conta de origem e da conta de destino. A exclusão da política também exclui todas as regras associadas a ela.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para remover uma política de replicação, exclua a política da conta de origem e da conta de destino. A exclusão da política também exclui todas as regras associadas a ela.

```azurecli
az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da replicação de objeto](object-replication-overview.md)
- [Habilitar e gerenciar o controle de versão de blob](versioning-enable.md)
- [Processar feed de alterações no armazenamento de BLOBs do Azure](storage-blob-change-feed-how-to.md)
