---
title: Práticas recomendadas usando Azure Key Vault HSM gerenciado
description: Este documento explica algumas das práticas recomendadas para usar Key Vault
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 7a30a7ab6689b602bc9ad4f696a6fe54c80f2151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994023"
---
# <a name="best-practices-when-using-managed-hsm"></a>Práticas recomendadas ao usar HSM gerenciado

## <a name="control-access-to-your-managed-hsm"></a>Controlar o acesso ao HSM gerenciado

HSM gerenciado é um serviço de nuvem que protege as chaves de criptografia. Como essas chaves são confidenciais e críticas para os negócios, certifique-se de proteger o acesso aos HSMs gerenciados, permitindo apenas aplicativos e usuários autorizados. Este [artigo](access-control.md) fornece uma visão geral do modelo de acesso. Ele explica a autenticação e a autorização e o controle de acesso baseado em função.
- Crie um [grupo de segurança Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md) para os administradores do HSM (em vez de atribuir a função de administrador a indivíduos). Isso impedirá o "bloqueio de administração" em caso de exclusão de conta individual.
- Bloquear o acesso a seus grupos de gerenciamento, assinaturas, grupos de recursos e HSMs gerenciados-use o RBAC do Azure para controlar o acesso aos seus grupos de gerenciamento, assinaturas e grupos de recursos
- Criar atribuições de função por chave usando o [RBAC local HSM gerenciado](access-control.md#data-plane-and-managed-hsm-local-rbac)
- Usar entidade de acesso de privilégios mínimos para atribuir funções

## <a name="choose-regions-that-support-availability-zones"></a>Escolher regiões que dão suporte a zonas de disponibilidade

- Para garantir a melhor disponibilidade e a resiliência de zona, escolha regiões do Azure nas quais [zonas de disponibilidade](../../availability-zones/az-overview.md) têm suporte. Essas regiões aparecem como "regiões recomendadas" no portal do Azure.

## <a name="backup"></a>Backup

- Certifique-se de fazer backups regulares de seu HSM. Os backups podem ser feitos no nível do HSM e em chaves específicas. 

## <a name="turn-on-logging"></a>Ativar o registro em log

- [Ative o registro em log](logging.md) para o HSM. Configure também alertas.

## <a name="turn-on-recovery-options"></a>Ativar opções de recuperação

- A [exclusão reversível](../general/soft-delete-overview.md) está ativada por padrão.
- Ative a proteção de limpeza se você quiser se proteger contra a exclusão forçada do HSM mesmo depois que a exclusão reversível estiver ativada.

## <a name="next-steps"></a>Próximas etapas

- Consulte [backup/restauração completo](backup-restore.md) para obter informações sobre backup/restauração completo do HSM.
- Consulte [log HSM gerenciado](logging.md) para saber como usar Azure monitor para configurar o registro em log
- Consulte [gerenciar chaves HSM gerenciadas](key-management.md) para o gerenciamento de chaves.
- Consulte [Gerenciamento de função HSM gerenciado](role-management.md) para gerenciar atribuições de função.
