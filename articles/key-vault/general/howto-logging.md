---
title: Como habilitar o log de Azure Key Vault
description: Como habilitar o registro em log para Azure Key Vault, que salva informações em uma conta de armazenamento do Azure que você fornece.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 62035b2fe6c3db71e392a05946ea3f230dfa030e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604603"
---
# <a name="how-to-enable-key-vault-logging"></a>Como habilitar o registro em log do Key Vault

Depois de criar um ou mais cofres de chaves, provavelmente você desejará monitorar como e quando os cofres de chaves serão acessados e por quem. Para obter detalhes completos sobre o recurso, consulte [log de Key Vault](logging.md).

O que é registrado em log:

* Todas as solicitações à API REST autenticadas, incluindo solicitações que falharam devido a permissões de acesso, erros do sistema ou solicitações inválidas.
* As operações do próprio cofre de chaves, incluindo a criação, a exclusão, a configuração de políticas de acesso ao cofre de chaves e a atualização dos atributos do cofre de chaves, como as marcas.
* Operações em chaves e segredos no cofre de chaves, incluindo:
  * A criação, modificação ou exclusão dessas chaves ou segredos.
  * A assinatura, verificação, criptografia, descriptografia, encapsulamento e desencapsulamento de chaves, obtenção de segredos e listagem de chaves e segredos (e suas versões).
* Solicitações não autenticadas que resultam em uma resposta 401. Por exemplo, solicitações que não têm um token de portador estão malformadas ou expiradas ou têm um token inválido.  
* Os eventos de notificação da Grade de Eventos sobre vencimento próximo, item vencido e política de acesso ao cofre foram alterados (o evento da nova versão não é registrado). Os eventos são registrados independentemente da presença de uma assinatura de evento criada no cofre de chaves. Para obter mais informações, confira [Esquema de evento da Grade de Eventos do Key Vault](../../event-grid/event-schema-key-vault.md)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário ter o seguinte:

* Um cofre da chave existente que você esteja usando.  
* Ambiente de [Azure cloud Shell](https://shell.azure.com) bash
* Armazenamento suficiente no Azure para seus logs do Cofre da Chave.

Este guia comandos são formatados para [Cloud Shell](https://shell.azure.com) com bash como um ambiente.

## <a name="connect-to-your-key-vault-subscription"></a>Conectar-se à sua assinatura do Key Vault

A primeira etapa na configuração do registro de chave está se conectando à assinatura que contém o cofre de chaves. Isso é especialmente importante se você tiver várias assinaturas associadas à sua conta.

Com o CLI do Azure, você pode exibir todas as suas assinaturas usando o comando [AZ Account List](/cli/azure/account#az_account_list) e, em seguida, conectar-se a uma usando o [conjunto de contas AZ](/cli/azure/account#az_account_set):

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

Com Azure PowerShell, você pode primeiro listar suas assinaturas usando o cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) e, em seguida, conectar-se a uma usando o cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext) : 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>Criar uma conta de armazenamento para seus logs

Embora você possa usar uma conta de armazenamento existente para seus logs, criaremos uma nova conta de armazenamento dedicada a logs de Key Vault. 

Para facilidade de gerenciamento, também usaremos o mesmo grupo de recursos que contém o cofre de chaves. No guia de início rápido e de [Azure PowerShell](quick-create-powershell.md) [CLI do Azure](quick-create-cli.md) , esse grupo de recursos é denominado **MyResource** Group e o local é *lesteus*. Substitua esses valores pelos seus próprios, conforme aplicável. 

Também precisaremos fornecer um nome de conta de armazenamento. Os nomes de conta de armazenamento devem ser exclusivos, entre 3 e 24 caracteres de comprimento e usar apenas números e letras minúsculas.  Por fim, criaremos uma conta de armazenamento do SKU "Standard_LRS".

Com o CLI do Azure, use o comando [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) . 

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

Com Azure PowerShell, use o cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) . Você precisará fornecer o local que corresponde ao grupo de recursos.

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

Em ambos os casos, observe a "ID" da conta de armazenamento. A operação CLI do Azure retorna a "ID" na saída. Para obter a "ID" com Azure PowerShell, use [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) e atribuiu a saída a uma variável $SA. Em seguida, você pode ver a conta de armazenamento com $sa. ID. (O "$sa. Contexto "a propriedade também será usada, posteriormente neste artigo.)

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

A "ID" da conta de armazenamento estará no formato "/subscriptions/<Your-Subscription-ID>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/<seu-Unique-Storage-Account-Name>".

> [!NOTE]
> Se você decidir usar uma conta de armazenamento existente, ela deverá usar a mesma assinatura que o cofre de chaves e deverá usar o modelo de implantação Azure Resource Manager, em vez do modelo de implantação clássico.

## <a name="obtain-your-key-vault-resource-id"></a>Obter a ID de recurso do cofre de chaves

No início rápido da [CLI](quick-create-cli.md) e no [início rápido do PowerShell](quick-create-powershell.md), você criou uma chave com um nome exclusivo.  Use esse nome novamente nas etapas abaixo.  Se não se lembrar do nome do cofre de chaves, você poderá usar o comando CLI do Azure [AZ keyvault List](/cli/azure/keyvault#az_keyvault_list) ou o cmdlet Azure PowerShell [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) para listá-los.

Use o nome do cofre de chaves para localizar sua ID de recurso.  Com CLI do Azure, use o comando [AZ keyvault show](/cli/azure/keyvault#az_keyvault_show) .

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

Com Azure PowerShell, use o cmdlet [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) .

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

A ID de recurso para o cofre de chaves estará no formato "/subscriptions/<Your-Subscription-ID>/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/<seu-Unique-keyvault-Name>". Observe-o para a próxima etapa.

## <a name="enable-logging"></a>Habilitar registro em log

Você pode habilitar o log para Key Vault usando o CLI do Azure, Azure PowerShell ou portal do Azure.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="azure-cli"></a>CLI do Azure

Use o comando CLI do Azure [AZ monitor Diagnostics-Settings Create](/cli/azure/monitor/diagnostic-settings) junto com a ID da conta de armazenamento e a ID de recurso do Key Vault.

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

Opcionalmente, você pode definir uma política de retenção para seus logs, para que os logs mais antigos sejam excluídos automaticamente após um período de tempo especificado. Por exemplo, você pode definir uma política de retenção que exclui automaticamente os logs com mais de 90 dias.

Com o CLI do Azure, use o comando [AZ monitor Diagnostic-Settings Update](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_update) . 

```azurecli-interactive
az monitor diagnostic-settings update --name "Key vault retention policy" --resource "<key-vault-resource-id>" --set retentionPolicy.days=90
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Use o cmdlet [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) , com o sinalizador **habilitado** definido como **$true** e a categoria definida como `AuditEvent` (a única categoria para Key Vault log):

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

Opcionalmente, você pode definir uma política de retenção para seus logs, para que os logs mais antigos sejam excluídos automaticamente após um período de tempo especificado. Por exemplo, você pode definir uma política de retenção que exclui automaticamente os logs com mais de 90 dias.

Com Azure PowerShell, use o cmdlet [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) .

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

# <a name="azure-portal"></a>[Portal do Azure](#tab/azure-portal)

Para definir as configurações de diagnóstico no portal, siga estas etapas.

1. Selecione as configurações de diagnóstico no menu folha de recursos.

    :::image type="content" source="../media/diagnostics-portal-1.png" alt-text="Portal de diagnóstico 1":::

1. Clique em "+ adicionar configuração de diagnóstico"

    :::image type="content" source="../media/diagnostics-portal-2.png" alt-text="Portal de diagnóstico 2":::
 
1. Selecione um nome para chamar sua configuração de diagnóstico. Para configurar o log para Azure Monitor para Key Vault, selecione a opção "AuditEvent" e "enviar para Log Analytics espaço de trabalho". Em seguida, escolha a assinatura e Log Analytics espaço de trabalho para o qual você deseja enviar seus logs.

    :::image type="content" source="../media/diagnostics-portal-3.png" alt-text="Portal de diagnóstico 3":::

    Caso contrário, selecione as opções que pertencem aos logs que você deseja selecionar

1. Depois de selecionar as opções desejadas, selecione salvar.

    :::image type="content" source="../media/diagnostics-portal-4.png" alt-text="Portal de diagnóstico 4":::

---

## <a name="access-your-logs"></a>Acessar seus logs

Os logs de Key Vault são armazenados no contêiner "insights-logs-auditevent" na conta de armazenamento que você forneceu. Para exibir os logs, você precisa baixar blobs.

Primeiro, liste todos os BLOBs no contêiner.  Com o CLI do Azure, use o comando [AZ Storage blob List](/cli/azure/storage/blob#az_storage_blob_list) .

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

Com Azure PowerShell, use a lista [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) todos os BLOBs neste contêiner, digite:

```powershell
Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context
```

Como você verá na saída do comando CLI do Azure ou do cmdlet Azure PowerShell, o nome dos BLOBs está no formato `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json` . Os valores de data e hora usam UTC.

Como a mesma conta de armazenamento pode ser usada para coletar logs de vários recursos, a ID do recurso completa no nome do blob será muito útil para acessar ou baixar apenas os blobs que você precisa. Mas, antes de fazer isso, primeiro vamos mostrar como baixar todos os blobs.

Com o CLI do Azure, use o comando [AZ Storage blob download](/cli/azure/storage/blob#az_storage_blob_download) , passe os nomes dos BLOBs e o caminho para o arquivo no qual você deseja salvar os resultados.

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

Com Azure PowerShell, use o cmdlet [gt-AzStorageBlobs](/powershell/module/az.storage/get-azstorageblob) para obter uma lista dos BLOBs e, em seguida, redirecione-o para o cmdlet [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent) para baixar os logs para o caminho escolhido.

```powershell-interactive
$blobs = Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

Quando você executa esse segundo cmdlet no PowerShell, o **/** delimitador nos nomes de blob cria uma estrutura de pasta completa na pasta de destino. Você usará essa estrutura para baixar e armazenar os blobs como arquivos.

Use caracteres curinga para baixar seletivamente os blobs. Por exemplo:

* Se você tiver vários cofres da chave e quiser baixar logs de apenas um cofre da chave, chamado CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Se você tiver vários grupos de recursos e quiser baixar os logs para apenas um grupo de recursos, use `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Se você quiser baixar todos os logs do mês de janeiro de 2019, use `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Agora você está pronto para começar a examinar o conteúdo dos logs. Mas, antes de passarmos para isso, você precisa conhecer mais dois comandos:

Para obter detalhes sobre como ler os logs, consulte [log de Key Vault: interpretar seus logs de Key Vault](logging.md#interpret-your-key-vault-logs)

## <a name="use-azure-monitor-logs"></a>Usar os logs do Azure Monitor

É possível usar a solução do Key Vault nos logs do Azure Monitor para examinar os logs `AuditEvent` do Key Vault. Nos logs do Azure Monitor, você usa consultas de log para analisar dados e obter as informações necessárias.

Para obter mais informações, incluindo como configurar isso, confira [Azure Key Vault no Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter informações conceituais, incluindo como interpretar logs de Key Vault, consulte [log de Key Vault](logging.md)
- Para obter um tutorial que usa o Azure Key Vault em um aplicativo Web .NET, confira [Usar o Azure Key Vault em um aplicativo Web](tutorial-net-create-vault-azure-web-app.md).
- Para referências de programação, consulte [Guia do desenvolvedor do Cofre da Chave do Azure](developers-guide.md).
