---
title: Exigir transferência segura para garantir conexões seguras
titleSuffix: Azure Storage
description: Saiba como exigir transferência segura para solicitações para o armazenamento do Azure. Quando você precisar de transferência segura para uma conta de armazenamento, todas as solicitações provenientes de uma conexão insegura serão rejeitadas.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a0a942daae6b106bce928f8214e92c0c6294203d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555707"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Exigir transferência segura para garantir conexões seguras

Você pode configurar sua conta de armazenamento para aceitar solicitações de conexões seguras somente definindo a propriedade de **transferência segura necessária** para a conta de armazenamento. Quando você precisar de transferência segura, todas as solicitações provenientes de uma conexão insegura serão rejeitadas. A Microsoft recomenda que você sempre precise de transferência segura para todas as suas contas de armazenamento.

Quando a transferência segura é necessária, uma chamada para uma operação da API REST do armazenamento do Azure deve ser feita via HTTPS. Qualquer solicitação feita via HTTP é rejeitada.

A conexão com um compartilhamento de arquivos do Azure via SMB sem criptografia falha quando a transferência segura é necessária para a conta de armazenamento. Exemplos de conexões inseguras incluem as feitas sobre SMB 2,1, SMB 3,0 sem criptografia ou algumas versões do cliente SMB do Linux.

Por padrão, a propriedade de **transferência segura necessária** é habilitada quando você cria uma conta de armazenamento.

> [!NOTE]
> Como o armazenamento do Azure não dá suporte a HTTPS para nomes de domínio personalizado, essa opção não será aplicada ao usar um nome de domínio personalizado. Não há suporte para contas de armazenamento clássicas.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Exigir transferência segura no portal do Azure

Você pode ativar a propriedade **transferência segura necessária** ao criar uma conta de armazenamento no [portal do Azure](https://portal.azure.com). Você também pode habilitá-la para uma conta de armazenamento existente.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Requer transferência segura de uma conta de armazenamento nova

1. Abra o painel **Criar conta de armazenamento** no portal do Azure.
1. Em **Transferência segura obrigatória**, selecione **habilitado**.

   ![Criar uma folha da conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Requer transferência segura de uma conta de armazenamento existente

1. Selecionar uma conta de armazenamento existente no portal do Azure.
1. No painel do menu de conta de armazenamento, em **DEFINIÇÕES**, selecione **Configuração**.
1. Em **Transferência segura obrigatória**, selecione **habilitado**.

   ![Painel do menu da conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Exigir transferência segura do código

Para exigir transferência segura de forma programática, defina a propriedade _enableHttpsTrafficOnly_ como _true_ na conta de armazenamento. Você pode definir essa propriedade usando a API REST do provedor de recursos de armazenamento, bibliotecas de cliente ou ferramentas:

* [REST API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [CLI](/cli/azure/storage/account)
* [NodeJS](https://www.npmjs.com/package/@azure/arm-storage/)
* [SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [SDK do Python](https://pypi.org/project/azure-mgmt-storage)
* [SDK do Ruby](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Exigir transferência segura com o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O exemplo requer o módulo Az do Azure PowerShell, versão 0.7 ou posterior. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps).

Execute `Connect-AzAccount` para criar uma conexão com o Azure.

 Use a linha de comando a seguir para verificar a configuração:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Use a linha de comando a seguir para habilitar a configuração:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Exigir transferência segura com CLI do Azure

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Use o seguinte comando para verificar a configuração:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Use o seguinte comando para habilitar a configuração:

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Próximas etapas

[Recomendações de segurança para o armazenamento de blobs](../blobs/security-recommendations.md)
