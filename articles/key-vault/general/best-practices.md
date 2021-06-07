---
title: Práticas recomendadas para usar o Key Vault-Azure Key Vault | Microsoft Docs
description: Saiba mais sobre as práticas recomendadas para Azure Key Vault, incluindo o controle de acesso, quando usar cofres de chaves separados, backup, log e opções de recuperação.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: e81cbd7e6584f4a280ab9507a989b52d3b188f2d
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566601"
---
# <a name="best-practices-to-use-key-vault"></a>Práticas recomendadas para usar Key Vault

## <a name="use-separate-key-vaults"></a>Usar cofres de chaves separados

Nossa recomendação é usar um cofre por aplicativo por ambiente (desenvolvimento, pré-produção e produção). Isso ajuda você a não compartilhar segredos entre ambientes e também reduz a ameaça em caso de uma violação.

## <a name="control-access-to-your-vault"></a>Controlar o acesso ao seu cofre

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia, como certificados, cadeias de conexão e senhas. Como esses dados são confidenciais e comercialmente críticos, é necessário proteger o acesso aos seus cofres de chaves permitindo apenas aplicativos e usuários autorizados. Este [artigo](secure-your-key-vault.md) fornece uma visão geral do modelo de acesso Key Vault. Explica a autenticação e a autorização e descreve como proteger o acesso ao seus cofres de chaves.

As sugestões ao controlar o acesso ao cofre são as seguintes:
1. Bloquear o acesso à sua assinatura, grupo de recursos e cofres de chaves (RBAC do Azure)
2. Criar políticas de acesso para cada cofre
3. Use a entidade de acesso de privilégios mínimos para conceder acesso
4. Ativar [pontos de extremidade de serviço de rede virtual](overview-vnet-service-endpoints.md) e firewall

## <a name="backup"></a>Backup

Certifique-se de fazer backups regulares de seu cofre em atualizar/excluir/criar objetos em um cofre.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell comandos de backup

* [Certificado de backup](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Chave de Backup](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Segredo de backup](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>CLI do Azure comandos de backup

* [Certificado de backup](/cli/azure/keyvault/certificate#az-keyvault-certificate-backup)
* [Chave de Backup](/cli/azure/keyvault/key#az-keyvault-key-backup)
* [Segredo de backup](/cli/azure/keyvault/secret#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>Ativar registro em log

[Ative o registro em log](logging.md) para seu cofre. Configure também alertas.

## <a name="turn-on-recovery-options"></a>Ativar opções de recuperação

1. Ative a [exclusão reversível](soft-delete-overview.md).
2. Ative a proteção de limpeza se você quiser se proteger contra a exclusão forçada do segredo/cofre mesmo depois que a exclusão reversível estiver ativada.