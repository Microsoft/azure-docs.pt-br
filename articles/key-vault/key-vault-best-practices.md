---
title: Práticas recomendadas para usar o Key Vault - Azure Key Vault | Microsoft Docs
description: Este documento explica algumas das práticas recomendadas para usar o Key Vault
services: key-vault
author: mbaldwin
manager: barbkess
tags: azure-key-vault
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: ee4418700cec5de1dc404c3669dd5de315aab983
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640762"
---
# <a name="best-practices-to-use-key-vault"></a>Práticas recomendadas para usar o Cofre de chaves

## <a name="control-access-to-your-vault"></a>Controlar o acesso ao seu cofre

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia, como certificados, cadeias de conexão e senhas. Como esses dados são confidenciais e comercialmente críticos, é necessário proteger o acesso aos seus cofres de chaves permitindo apenas aplicativos e usuários autorizados. Isso [artigo](key-vault-secure-your-key-vault.md) fornece uma visão geral do modelo de acesso do Cofre de chaves. Explica a autenticação e a autorização e descreve como proteger o acesso ao seus cofres de chaves.

Sugestões enquanto controla o acesso ao seu cofre são da seguinte maneira:
1. Bloquear o acesso a sua assinatura, o grupo de recursos e a chave de cofres (RBAC)
2. Criar políticas de acesso para cada cofre
3. Usar a entidade de segurança de acesso com privilégios mínimos para conceder acesso
4. Ativar o Firewall e [pontos de extremidade de serviço de rede virtual](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Usar o Cofre de chaves separado

Nossa recomendação é usar um cofre por aplicativo por ambiente (desenvolvimento, pré-produção e produção). Isso ajuda você a compartilhar não segredos em ambientes e também reduz a ameaça em caso de violação.

## <a name="backup"></a>Backup

Verifique se você regulares voltar cumulativos de atualizações para seu [cofre](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) em update/delete/criação de objetos dentro de um cofre.

## <a name="turn-on-logging"></a>Ativar o registro em log

[Ativar o log](key-vault-logging.md) para o cofre. Também Configure alertas.

## <a name="turn-on-recovery-options"></a>Ativar as opções de recuperação

1. Ative [exclusão reversível](key-vault-ovw-soft-delete.md).
2. Ative a proteção de limpeza se você quiser se proteger contra a exclusão de força do segredo / cofre mesmo após a exclusão reversível está ativada.
