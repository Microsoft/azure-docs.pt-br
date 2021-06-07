---
title: Tutorial de rotação para recursos com dois conjuntos de credenciais
description: Use este tutorial para aprender a automatizar a rotação de um segredo para recursos que usam dois conjuntos de credenciais de autenticação.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 1f656a41b0f447b90f58ec14173e418a2defb72e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484822"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Automatizar a rotação de um segredo para recursos com dois conjuntos de credenciais de autenticação

A melhor maneira de se autenticar nos serviços do Azure é usando uma [identidade gerenciada](../general/authentication.md), mas há alguns cenários em que essa não é uma opção. Nesses casos, são usadas chaves de acesso ou senhas. Você deve fazer a rotação das chaves de acesso e senhas com frequência.

Este tutorial mostra como automatizar a rotação periódica de segredos para bancos de dados e serviços que usam dois conjuntos de credenciais de autenticação. Especificamente, este tutorial mostra como fazer a rotação das chaves de conta do Armazenamento do Azure armazenadas no Azure Key Vault como segredos. Você usará uma função disparada pela notificação da Grade de Eventos do Azure. 

> [!NOTE]
> As chaves da conta de armazenamento poderão ser gerenciadas automaticamente no Key Vault se você fornecer tokens de assinatura de acesso compartilhado para acesso delegado à conta de armazenamento. Há serviços que exigem cadeias de conexão de conta de armazenamento com chaves de acesso. Para esse cenário, recomendamos esta solução.

Aqui está a solução de rotação descrita neste tutorial: 

![Diagrama que mostra a solução de rotação.](../media/secrets/rotation-dual/rotation-diagram.png)

Nesta solução, o Azure Key Vault armazena as chaves de acesso individuais da conta de Armazenamento como versões do mesmo segredo, alternando entre a chave primária e a secundária nas versões subsequentes. Quando uma chave de acesso é armazenada na versão mais recente do segredo, a chave alternativa é regenerada e adicionada ao Key Vault como a nova versão mais recente do segredo. A solução fornece um ciclo de rotação inteiro do aplicativo a fim de fazer a atualização para a chave regenerada mais recente. 

1. Trinta dias antes da data de validade de um segredo, o Key Vault publica o evento de expiração próxima na Grade de Eventos.
1. A Grade de Eventos verifica as assinaturas de eventos e usa HTTP POST para chamar o ponto de extremidade do aplicativo de funções assinado para o evento.
1. O aplicativo de funções identifica a chave alternativa (não a mais recente) e chama a conta de armazenamento para regenerá-la.
1. O aplicativo de funções adiciona nova chave regenerada ao Azure Key Vault como uma nova versão do segredo.

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. [Crie uma gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure [Cloud Shell](https://shell.azure.com/). Este tutorial está usando o portal do Cloud Shell com o PowerShell
* Azure Key Vault.
* Duas contas de armazenamento do Azure.

Você poderá usar este link de implantação se não tiver um cofre de chaves existente e contas de armazenamento existentes:

[![Link rotulado como Fazer a implantação no Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. Em **Grupo de recursos**, selecione **Criar**. Nomeie o grupo como **akvrotation** e clique em **Ok**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

    ![Captura de tela que mostra como criar um grupo de recursos.](../media/secrets/rotation-dual/dual-rotation-1.png)

Agora você terá um cofre de chaves e duas contas de armazenamento. Verifique essa configuração na CLI do Azure ou no Azure PowerShell executando este comando:
# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az resource list -o table -g vaultrotation
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)

```azurepowershell
Get-AzResource -Name 'vaultrotation*' | Format-Table
```
---

Os resultados serão algo parecido com esta saída:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
vaultrotation-kv         vaultrotation      westus      Microsoft.KeyVault/vaults
vaultrotationstorage     vaultrotation      westus      Microsoft.Storage/storageAccounts
vaultrotationstorage2    vaultrotation      westus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>Criar e implantar a função de rotação de chaves

Em seguida, você criará um aplicativo de funções com uma identidade gerenciada pelo sistema, além dos outros componentes necessários. Você também implantará a função de rotação para as chaves da conta de armazenamento.

A função de rotação do aplicativo de funções exige os seguintes componentes e a seguinte configuração:
- Um plano do Serviço de Aplicativo do Azure
- Uma conta de armazenamento para gerenciar gatilhos do aplicativo de funções
- Uma política de acesso para acessar segredos no Key Vault
- A função do serviço de operador da chave da conta de armazenamento atribuída ao aplicativo de funções para que ela possa acessar as chaves de acesso da conta de armazenamento
- Uma função de rotação de chaves com um gatilho de evento e um gatilho HTTP (rotação sob demanda)
- Uma assinatura de evento da Grade de Eventos para o evento **SecretNearExpiry**

1. Selecione o link de implantação de modelo do Azure: 

   [![Link de implantação de modelo do Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Na lista **Grupo de recursos**, selecione **vaultrotation**.
1. Na caixa **GR da Conta de Armazenamento**, insira o nome do grupo de recursos em que sua conta de armazenamento está localizada. Mantenha o valor padrão **[resourceGroup().name]** se sua conta de armazenamento já estiver localizada no mesmo grupo de recursos em que você implantará a função de rotação de chaves.
1. Na caixa **Nome da Conta de Armazenamento**, insira o nome da conta de armazenamento que contém as chaves de acesso cuja rotação será feita. Mantenha o valor padrão **[concat(resourceGroup().name, 'storage')]** caso use a conta de armazenamento criada em [Pré-requisitos](#prerequisites).
1. Na caixa **GR do Cofre de Chaves**, insira o nome do grupo de recursos no qual seu cofre de chaves estará localizado. Mantenha o valor padrão **[resourceGroup().name]** se seu cofre de chaves já existir no mesmo grupo de recursos em que você implantará a função de rotação de chaves.
1. Na caixa **Nome do Cofre de Chaves**, insira o nome do cofre de chaves. Mantenha o valor padrão **[concat(resourceGroup().name, '-kv')]** caso use o cofre de chaves criado em [Pré-requisitos](#prerequisites).
1. Na caixa **Tipo de Plano do Serviço de Aplicativo**, selecione o plano de hospedagem. O **Plano Premium** será necessário somente quando o cofre de chaves estiver atrás do firewall.
1. Na caixa **Nome do Aplicativo de Funções**, insira o nome do aplicativo de funções.
1. Na caixa **Nome do Segredo**, insira o nome do segredo em que você armazenará as chaves de acesso.
1. Na caixa **URL do Repositório**, insira a localização do GitHub do código de função. Neste tutorial, será possível usar **https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

   ![Uma captura de tela que mostra como criar e implantar uma função.](../media/secrets/rotation-dual/dual-rotation-2.png)

Depois de concluir as etapas anteriores, você terá uma conta de armazenamento, um farm de servidores, um aplicativo de funções e o Application Insights. Quando a implantação estiver concluída, você verá esta página:

   ![Uma captura de tela que mostra se a página Sua implantação está concluída.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Se você encontrar uma falha, poderá selecionar **Reimplantar** para concluir a implantação dos componentes.


É possível encontrar modelos de implantação e o código da função de rotação em [Exemplos do Azure](https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Adicionar chaves de acesso da conta de armazenamento ao Key Vault

Primeiro, defina sua política de acesso a fim de conceder à entidade de usuário permissões para **gerenciar segredos**:
# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az keyvault set-policy --upn <email-address-of-user> --name vaultrotation-kv --secret-permissions set delete get list
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -UserPrincipalName <email-address-of-user> --name vaultrotation-kv -PermissionsToSecrets set,delete,get,list
```
---

Agora você pode criar um segredo com uma chave de acesso da conta de armazenamento como valor. Você também precisará da ID do recurso da conta de armazenamento, do período de validade do segredo e da ID da chave a ser adicionada ao segredo para que a função de rotação possa regenerar a chave na conta de armazenamento.

Determine a ID do recurso da conta de armazenamento. É possível encontrar esse valor na propriedade `id`.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Liste as chaves de acesso da conta de armazenamento para que você possa obter os principais valores:
# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Adicione um segredo ao cofre de chaves com uma data de validade definida como amanhã, um período de validade de 60 dias e uma ID de recurso da conta de armazenamento. Execute este comando, usando os valores recuperados para `key1Value` e `storageAccountResourceId`:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name vaultrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key1'
    ProviderAddress='<storageAccountResourceId>'
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

O segredo acima disparará o evento `SecretNearExpiry` em alguns minutos. Esse evento disparará uma função para girar o segredo com um término definido como 60 dias. Nessa configuração, o evento 'SecretNearExpiry' disparará a cada 30 dias (30 dias antes da expiração) e a função de rotação alternará a rotação entre key1 e key2.

É possível verificar se as chaves de acesso foram regeneradas recuperando a chave de conta de armazenamento, bem como o segredo do Key Vault e comparando-os.

Use este comando para obter as informações secretas:
# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey -AsPlainText
```
---

Observe que a `CredentialId` é atualizada para o `keyName` alternativo e o `value` é regenerado:

![Uma captura de tela que mostra a saída do comando 'az keyvault secret show' para a primeira conta de armazenamento.](../media/secrets/rotation-dual/dual-rotation-4.png)

Recupere as chaves de acesso para comparar os valores:
# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Observe que o `value` da chave é igual ao segredo do cofre de chaves:

![Captura de tela que mostra a saída do comando a z storage account keys list para a primeira conta de armazenamento.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Adicionar chaves de armazenamento para rotação

Você pode reutilizar o mesmo aplicativo de funções para fazer a rotação das chaves para várias contas de armazenamento. 

Para adicionar chaves da conta de armazenamento a uma função existente para rotação, você precisa:
- Da função do serviço de operador da chave da conta de armazenamento atribuída ao aplicativo de funções para que ela possa acessar as chaves de acesso da conta de armazenamento.
- De uma assinatura de evento da Grade de Eventos para o evento **SecretNearExpiry**.

1. Selecione o link de implantação de modelo do Azure: 

   [![Link de implantação de modelo do Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. Na lista **Grupo de recursos**, selecione **vaultrotation**.
1. Na caixa **GR da Conta de Armazenamento**, insira o nome do grupo de recursos em que sua conta de armazenamento está localizada. Mantenha o valor padrão **[resourceGroup().name]** se sua conta de armazenamento já estiver localizada no mesmo grupo de recursos em que você implantará a função de rotação de chaves.
1. Na caixa **Nome da Conta de Armazenamento**, insira o nome da conta de armazenamento que contém as chaves de acesso cuja rotação será feita.
1. Na caixa **GR do Cofre de Chaves**, insira o nome do grupo de recursos no qual seu cofre de chaves estará localizado. Mantenha o valor padrão **[resourceGroup().name]** se seu cofre de chaves já existir no mesmo grupo de recursos em que você implantará a função de rotação de chaves.
1. Na caixa **Nome do Cofre de Chaves**, insira o nome do cofre de chaves.
1. Na caixa **Nome do Aplicativo de Funções**, insira o nome do aplicativo de funções.
1. Na caixa **Nome do Segredo**, insira o nome do segredo em que você armazenará as chaves de acesso.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

   ![Captura de tela que mostra como criar uma conta de armazenamento adicional.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Adicionar outra chave de acesso da conta de armazenamento ao Key Vault

Determine a ID do recurso da conta de armazenamento. É possível encontrar esse valor na propriedade `id`.
# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Liste as chaves de acesso da conta de armazenamento para que você possa obter o valor key2:
# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage2 -ResourceGroupName vaultrotation
```
---

Adicione um segredo ao cofre de chaves com uma data de validade definida como amanhã, um período de validade de 60 dias e uma ID de recurso da conta de armazenamento. Execute este comando, usando os valores recuperados para `key2Value` e `storageAccountResourceId`:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
az keyvault secret set --name storageKey2 --vault-name vaultrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key2';
    ProviderAddress='<storageAccountResourceId>';
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey2 -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Use este comando para obter as informações secretas:
# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey2
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey2 -AsPlainText
```
---

Observe que a `CredentialId` é atualizada para o `keyName` alternativo e o `value` é regenerado:

![Uma captura de tela que mostra a saída do comando 'az keyvault secret show' para a segunda conta de armazenamento.](../media/secrets/rotation-dual/dual-rotation-8.png)

Recupere as chaves de acesso para comparar os valores:
# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Observe que o `value` da chave é igual ao segredo do cofre de chaves:

![Captura de tela que mostra a saída do comando a z storage account keys list para a segunda conta de armazenamento.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-rotation-functions-for-two-sets-of-credentials"></a>Funções de rotação do Key Vault para dois conjuntos de credenciais

Um modelo de funções de rotação para dois conjuntos de credenciais e várias funções prontas para uso:

- [Modelo do projeto](https://serverlesslibrary.net/sample/bc72c6c3-bd8f-4b08-89fb-c5720c1f997f)
- [Cache Redis](https://serverlesslibrary.net/sample/0d42ac45-3db2-4383-86d7-3b92d09bc978)
- [Conta de armazenamento](https://serverlesslibrary.net/sample/0e4e6618-a96e-4026-9e3a-74b8412213a4)
- [Cosmos DB](https://serverlesslibrary.net/sample/bcfaee79-4ced-4a5c-969b-0cc3997f47cc)

> [!NOTE]
> As funções de rotação acima serão criadas por um membro da comunidade, não pela Microsoft. O Azure Functions da Comunidade não é compatível com nenhum programa ou serviço de suporte da Microsoft. Além disso, ele será disponibilizado no estado atual, sem garantias de qualquer tipo.

## <a name="next-steps"></a>Próximas etapas

- Tutorial: [Rotação de segredos para um conjunto de credenciais](./tutorial-rotation.md)
- Visão geral: [Monitoramento do Key Vault com a Grade de Eventos do Azure](../general/event-grid-overview.md)
- Como fazer: [Criar sua primeira função no portal do Azure](../../azure-functions/functions-get-started.md)
- Como [Receber emails quando um segredo do Key Vault for alterado](../general/event-grid-logicapps.md)
- Referência: [Esquema de eventos da Grade de Eventos do Azure para o Azure Key Vault](../../event-grid/event-schema-key-vault.md)
