---
title: Fazer backup de um segredo, uma chave ou um certificado armazenado no Azure Key Vault | Microsoft Docs
description: Use este documento para ajudar a fazer backup de um segredo, uma chave ou um certificado armazenado no Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/18/2021
ms.author: sudbalas
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 399f55d379f99a784fed97d7e9f72c456eb1f914
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484805"
---
# <a name="azure-key-vault-backup"></a>Backup do Azure Key Vault

Este documento mostra como fazer backup de segredos, chaves e certificados armazenados no cofre de chaves. Um backup destina-se a fornecer uma cópia offline de todos os segredos no caso improvável de você perder o acesso ao cofre de chaves.

## <a name="overview"></a>Visão geral

O Azure Key Vault fornece automaticamente vários recursos para ajudar você a manter a disponibilidade e evitar a perda de dados. Faça backup dos segredos somente se você tiver uma justificativa comercial crítica. O backup de segredos no cofre de chaves pode introduzir desafios operacionais, como a manutenção de vários conjuntos de logs, permissões e backups quando os segredos expiram ou são girados.

O Key Vault mantém a disponibilidade em cenários de desastre e fará automaticamente o failover das solicitações para uma região emparelhada sem exigir nenhuma intervenção por parte do usuário. Para obter mais informações, confira [Disponibilidade e redundância do Azure Key Vault](./disaster-recovery-guidance.md).

Caso deseje obter proteção contra a exclusão acidental ou mal-intencionada de segredos, configure a exclusão temporária e limpe os recursos de proteção no cofre de chaves. Para obter mais informações, confira [Visão geral da exclusão temporária do Azure Key Vault](./soft-delete-overview.md).

## <a name="limitations"></a>Limitações

> [!IMPORTANT]
> O Key Vault não dá suporte para a capacidade de fazer backup de mais de 500 versões anteriores de um objeto de chave, segredo ou certificado. A tentativa de fazer backup de um objeto de chave, segredo ou certificado poderá resultar em um erro. Não é possível excluir versões anteriores de uma chave, um segredo ou um certificado.

No momento, o Key Vault não fornece uma forma de fazer backup de todo um cofre de chaves em uma só operação. Qualquer tentativa de usar os comandos listados neste documento para fazer um backup automatizado de um cofre de chaves poderá resultar em erros e não terá suporte da Microsoft nem da equipe do Azure Key Vault. 

Considere também as seguintes consequências:

* O backup de segredos que têm várias versões poderá causar erros de tempo limite.
* Um backup cria um instantâneo pontual. Os segredos podem ser renovados durante um backup, causando incompatibilidade das chaves de criptografia.
* Se você exceder os limites de serviço do cofre de chaves de solicitações por segundo, o cofre de chaves será restrito e o backup falhará.

## <a name="design-considerations"></a>Considerações sobre o design

Quando você fizer backup de um objeto do cofre de chaves, como um segredo, uma chave ou um certificado, a operação de backup baixará o objeto como um blob criptografado. Esse blob não poderá ser descriptografado fora do Azure. Para obter dados utilizáveis desse blob, restaure o blob em um cofre de chaves na mesma assinatura e [geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="prerequisites"></a>Pré-requisitos

Para fazer backup de um objeto do cofre de chaves, você precisará ter: 

* Permissões no nível de colaborador ou superiores em uma assinatura do Azure.
* Um cofre de chaves primário que contenha os segredos que você deseja copiar em backup.
* Um cofre de chaves secundário em que os segredos serão restaurados.

## <a name="back-up-and-restore-from-the-azure-portal"></a>Backup e restauração por meio do portal do Azure

Siga as etapas desta seção para fazer backup de objetos e restaurá-los usando o portal do Azure.

### <a name="back-up"></a>Fazer backup

1. Vá para o portal do Azure.
2. Selecione seu cofre de chaves.
3. Acesse o objeto (segredo, chave ou certificado) que deseja copiar em backup.

    ![Captura de tela mostrando o local de seleção da configuração de Chaves e um objeto em um cofre de chaves.](../media/backup-1.png)

4. Selecione o objeto.
5. Selecione **Baixar Backup**.

    ![Captura de tela mostrando o local de seleção do botão Baixar Backup em um cofre de chaves.](../media/backup-2.png)
    
6. Selecione **Baixar**.

    ![Captura de tela mostrando o local de seleção do botão Baixar em um cofre de chaves.](../media/backup-3.png)
    
7. Armazene o blob criptografado em uma localização segura.

### <a name="restore"></a>Restaurar

1. Vá para o portal do Azure.
2. Selecione seu cofre de chaves.
3. Acesse o tipo de objeto (segredo, chave ou certificado) que deseja restaurar.
4. Selecione **Restaurar Backup**.

    ![Captura de tela mostrando o local de seleção de Restaurar Backup em um cofre de chaves.](../media/backup-4.png)
    
5. Acesse a localização em que você armazenou o blob criptografado.
6. Selecione **OK**.

## <a name="back-up-and-restore-from-the-azure-cli-or-azure-powershell"></a>Backup e restauração por meio da CLI do Azure ou do Azure PowerShell

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
## Log in to Azure
az login

## Set your subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a provider
az provider register -n Microsoft.KeyVault

## Back up a certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/powershell)

```azurepowershell
## Log in to Azure
Connect-AzAccount

## Set your subscription
Set-AzContext -Subscription '{AZURE SUBSCRIPTION ID}'

## Back up a certificate in Key Vault
Backup-AzKeyVaultCertificate -VaultName '{Certificate Name}' -Name '{Key Vault Name}'

## Back up a key in Key Vault
Backup-AzKeyVaultKey -VaultName '{Key Name}' -Name '{Key Vault Name}'

## Back up a secret in Key Vault
Backup-AzKeyVaultSecret -VaultName '{Key Vault Name}' -Name '{Secret Name}'

## Restore a certificate in Key Vault
Restore-AzKeyVaultCertificate -VaultName '{Key Vault Name}' -InputFile '{File Path}'

## Restore a key in Key Vault
Restore-AzKeyVaultKey -VaultName '{Key Vault Name}' -InputFile '{File Path}'

## Restore a secret in Key Vault
Restore-AzKeyVaultSecret -VaultName '{Key Vault Name}' -InputFile '{File Path}'
```
---

## <a name="next-steps"></a>Próximas etapas

Ative o [log e o monitoramento](./logging.md) para o Key Vault.
