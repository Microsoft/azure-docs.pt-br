---
title: Atualizar a senha da conta de armazenamento AD DS
description: Saiba como atualizar a senha da conta de Active Directory Domain Services que representa sua conta de armazenamento. Isso impede que a conta de armazenamento seja limpa quando a senha expirar, impedindo falhas de autenticação.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: d16f11a85c6b370b0187975cce965bf3e1b5ba3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85510247"
---
# <a name="update-the-password-of-your-storage-account-identity-in-ad-ds"></a>Atualize a senha da sua identidade de conta de armazenamento no AD DS

Se você registrou a Active Directory Domain Services (AD DS) identidade/conta que representa sua conta de armazenamento em uma unidade organizacional ou domínio que impõe o tempo de expiração da senha, você deve alterar a senha antes da duração máxima da senha. Sua organização pode executar scripts de limpeza automatizados que excluem contas quando sua senha expirar. Por isso, se você não alterar sua senha antes de ela expirar, sua conta poderá ser excluída, o que fará com que você perca o acesso aos compartilhamentos de arquivos do Azure.

Para disparar a rotação de senha, você pode executar o `Update-AzStorageAccountADObjectPassword` comando do [módulo AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases). Esse comando deve ser executado em um ambiente ingressado no AD DS local usando um usuário híbrido com permissão de proprietário para a conta de armazenamento e AD DS permissões para alterar a senha da identidade que representa a conta de armazenamento. O comando executa ações semelhantes à rotação de chaves da conta de armazenamento. Especificamente, ele obtém a segunda chave Kerberos da conta de armazenamento e a usa para atualizar a senha da conta registrada no AD DS. Em seguida, ele regenera a chave Kerberos de destino da conta de armazenamento e atualiza a senha da conta registrada no AD DS. Você deve executar esse comando em um ambiente ingressado no AD DS local.

```PowerShell
# Update the password of the AD DS account registered for the storage account
# You may use either kerb1 or kerb2
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```
