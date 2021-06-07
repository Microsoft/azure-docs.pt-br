---
title: Gerenciar a camada de acesso padrão de uma conta de Armazenamento do Azure
description: Saiba como alterar a camada de acesso padrão de uma conta de Armazenamento de Blobs ou da GPv2
author: twooley
ms.author: twooley
ms.date: 01/11/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 026ab6be1fd4ef79f818f796c4725f6613a9bc6d
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277378"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>Gerenciar a camada de acesso padrão de uma conta de Armazenamento do Azure

Cada conta de Armazenamento do Azure tem uma camada de acesso padrão, seja ela quente ou fria. Atribua a camada de acesso ao criar uma conta de armazenamento. A camada de acesso padrão é quente.

Você pode alterar a camada da conta padrão configurando o atributo **Camada de acesso** na conta de armazenamento. A alteração da camada da conta é aplicada a todos os objetos armazenados na conta que não têm uma camada explícita definida. Alternar a camada da conta de quente para fria gera operações de gravação (por 10.000) para todos os blobs sem uma camada definida apenas nas contas da GPv2 e alternar de frio para quente incorre em encargos de operações de leitura (por 10.000) e de recuperação de dados (por GB) para todos blobs no Armazenamento de Blobs e em contas da GPv2.

Para blobs com a camada definida no nível do objeto, a camada da conta não se aplica. A camada de arquivo só pode ser aplicada no nível de objeto. Você pode alternar entre essas camadas de acesso a qualquer momento.

Você pode alterar a camada de acesso padrão depois que uma conta de armazenamento tiver sido criada seguindo as etapas abaixo.

## <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Alterar a camada de acesso padrão da conta de Armazenamento de Blobs ou da GPv2

Os seguintes cenários usam o portal do Azure ou o PowerShell:

# <a name="portal"></a>[Portal](#tab/portal)

1. Entre no [portal do Azure](https://portal.azure.com).

1. No portal do Azure, pesquise e selecione **Todos os recursos**.

1. Selecione sua conta de armazenamento.

1. Em **Configurações**, selecione **Configuração** para veja e alterar a configuração da conta.

1. Selecione a camada de acesso adequada para as suas necessidades: defina a **Camada de acesso** como **Fria** ou **Quente**.

1. Clique em **Salvar** na parte superior.

![Alterar a camada de conta padrão no portal do Azure](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O script de PowerShell a seguir pode ser usado para alterar a camada da conta. A variável `$rgName` deve ser inicializada com o nome do grupo de recursos. A variável `$accountName` deve ser inicializada com o nome da conta de armazenamento.

```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

---

## <a name="next-steps"></a>Próximas etapas

- [Como gerenciar a camada de um blob em uma conta de Armazenamento do Azure](../blobs/manage-access-tier.md)
- [Determinar se o desempenho Premium beneficiaria o seu aplicativo](../blobs/storage-blob-performance-tiers.md)
- [Avaliar o uso de suas contas de armazenamento atuais, habilitando as métricas do Armazenamento do Azure](../blobs/monitor-blob-storage.md)
