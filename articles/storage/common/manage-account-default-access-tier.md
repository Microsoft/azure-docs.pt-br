---
title: Gerenciar a camada de acesso padrão de uma conta de armazenamento do Azure
description: Saiba como alterar a camada de acesso padrão de uma conta de armazenamento de BLOBs ou de GPv2
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 10d4e99d7bbebb6bc7d7def308e233507ed99ce9
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98166466"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>Gerenciar a camada de acesso padrão de uma conta de armazenamento do Azure

Cada conta de armazenamento do Azure tem uma camada de acesso padrão, Hot, fria ou arquivo morto. Você atribui a camada de acesso ao criar uma conta de armazenamento. A camada de acesso padrão é quente.

Você pode alterar a camada de conta padrão definindo o atributo **camada de acesso** na conta de armazenamento. A alteração da camada de conta aplica-se a todos os objetos armazenados na conta que não têm um conjunto de camadas explícita. Alternar a camada de conta de operações de gravação quentes para frias (por 10.000) para todos os BLOBs sem uma camada de conjunto somente nas contas GPv2 e alternar de fria para quente incorre em operações de leitura (por 10.000) e de recuperação de dados (por GB) para todos os BLOBs no armazenamento de BLOBs e contas de GPv2.

Para BLOBs com a camada definida no nível do objeto, a camada de conta não se aplica. A camada de arquivo só pode ser aplicada no nível de objeto. Você pode alternar entre as camadas de acesso a qualquer momento.

Você pode alterar a camada de acesso padrão depois que uma conta de armazenamento tiver sido criada seguindo as etapas abaixo.

## <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Alterar a camada de acesso de conta padrão de uma conta de armazenamento de BLOBs ou de GPv2

Os cenários a seguir usam o portal do Azure ou o PowerShell:

# <a name="portal"></a>[Portal](#tab/portal)

1. Entre no [portal do Azure](https://portal.azure.com).

1. No portal do Azure, pesquise e selecione **Todos os recursos**.

1. Selecione sua conta de armazenamento.

1. Em **configurações**, selecione **configuração** para exibir e alterar a configuração da conta.

1. Selecione a camada de acesso certa para suas necessidades: defina a **camada de acesso** como **fria** ou **quente**.

1. Clique em **salvar** na parte superior.

![Alterar a camada de conta padrão no portal do Azure](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O script do PowerShell a seguir pode ser usado para alterar a camada de conta. A variável `$rgName` deve ser inicializada com o nome do grupo de recursos. A variável `$accountName` deve ser inicializada com o nome da conta de armazenamento.

```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

---

## <a name="next-steps"></a>Próximas etapas

- [Como gerenciar a camada de um blob em uma conta de armazenamento do Azure](../blobs/manage-access-tier.md)
- [Determinar se o desempenho premium beneficiaria seu aplicativo](../blobs/storage-blob-performance-tiers.md)
- [Avaliar o uso de suas contas de armazenamento atuais, habilitando as métricas do Armazenamento do Azure](../blobs/monitor-blob-storage.md)
