---
title: Funções internas de RBAC local do HSM Gerenciado – Azure Key Vault | Microsoft Docs
description: Uma visão geral das funções internas do HSM Gerenciado que podem ser atribuídas a usuários, entidades de serviço, grupos e identidades gerenciadas
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: a5ecd8f13a3255a565168f62f358a6a38e3cbab4
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445210"
---
# <a name="managed-hsm-local-rbac-built-in-roles"></a>Funções internas de RBAC local do HSM Gerenciado

O RBAC local do HSM Gerenciado tem várias funções internas que você pode atribuir a usuários, entidades de serviço, grupos e identidades gerenciadas. Para permitir que uma entidade de segurança execute uma operação, você deve atribuir a elas uma função que concede permissão para executar essas operações. Todas essas funções e operações só permitem que você gerencie as permissões das operações do plano de dados. Para gerenciar as permissões do painel de controle do recurso HSM Gerenciado (como a criação de um HSM gerenciado ou a atualização, a movimentação ou a exclusão de um existente), você precisará usar o [RBAC do Azure (controle de acesso baseado em função do Azure)](../../role-based-access-control/overview.md).

## <a name="built-in-roles"></a>Funções internas

|Nome da função|Descrição|ID|
|---|---|---|
|Administrador de HSM Gerenciado| Concede acesso completo a todas as ações de dados.|a290e904-7015-4bba-90c8-60543313cdb4|
|Responsável pela Criptografia do HSM Gerenciado| Concede acesso completo a todas as principais operações de gerenciamento e de criptografia de chaves|515eb02d-2335-4d2d-92f2-b1cbdf9c3778|
|Usuário de Criptografia do HSM Gerenciado|Concede acesso para criar e usar chaves para operações criptográficas. Não é possível excluir as chaves permanentemente.|21dbd100-6940-42c2-9190-5d6cb909625b|
|Administrador de Política do HSM Gerenciado| Concede permissão para criar e excluir atribuições de função|4bd23610-cdcf-4971-bdee-bdc562cc28e4|
|Auditor de Criptografia do HSM Gerenciado|Concede permissão de leitura para ler (mas não para usar) as chaves|2c18b078-7c48-4d3a-af88-5a3a1b3f82b3|
|Criptografia de Serviço do HSM Gerenciado| Concede permissão para usar uma chave para criptografia de serviço. |33413926-3206-4cdd-b39a-83574fe37a17|
|Backup do HSM Gerenciado| Concede permissão para executar um backup completo ou de chave individual do HSM. |7b127d3c-77bd-4e3e-bbe0-dbb8971fa7f8|

## <a name="permitted-operations"></a>Operações permitidas
> [!NOTE]  
> - O 'X' indica que aquela função tem permissão para executar a ação de dados em questão. A célula vazia indica que aquela função não tem permissão para executar a ação de dados em questão.
> - Todos os nomes das ações de dados têm o prefixo "Microsoft.KeyVault/managedHsm", que é omitido nas tabelas abaixo para poupar espaço.
> - Todos os nomes das funções têm o prefixo "HSM Gerenciado", que é omitido na tabela abaixo para poupar espaço.

|Ação de dados | Administrador | Crypto Officer | Usuário de Criptografia | Administrador de políticas | Criptografia de Serviço | Backup | Auditor de Criptografia|
|---|---|---|---|---|---|---|---|
|**Gerenciamento de Domínio de Segurança**|
/securitydomain/download/action|<center>X</center>||||||
/securitydomain/upload/action|<center>X</center>||||||
/securitydomain/upload/read|<center>X</center>||||||
/securitydomain/transferkey/read|<center>X</center>||||||
|**Gerenciamento de chaves**|
|/keys/read/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||<center>X</center>|
|/keys/write/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/create|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/delete|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/read/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/recover/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/delete|<center>X</center>|<center>X</center>|||||<center>X</center>|
|/keys/backup/action|<center>X</center>|<center>X</center>|<center>X</center>|||<center>X</center>|
|/keys/restore/action|<center>X</center>|<center>X</center>|||||
|/keys/export/action|<center>X</center>|<center>X</center>|||||
|/keys/import/action|<center>X</center>|<center>X</center>|||||
|**Principais operações criptográficas**|
|/keys/encrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/decrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/wrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/unwrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/sign/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/verify/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|**Gerenciamento de funções**|
|/roleAssignments/delete/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/read/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/write/action|<center>X</center>|||<center>X</center>|||
|/roleDefinitions/read/action|<center>X</center>|||<center>X</center>|||
|**Gerenciamento de backup/restauração**|
|/backup/start/action|<center>X</center>|||||<center>X</center>|
|/backup/status/action|<center>X</center>|||||<center>X</center>|
|/restore/start/action|<center>X</center>||||||
|/restore/status/action|<center>X</center>||||||
||||||||

## <a name="next-steps"></a>Próximas etapas

- Obtenha uma visão geral do [RBAC do Azure (controle de acesso baseado em função do Azure)](../../role-based-access-control/overview.md).
- Confira um tutorial sobre [Gerenciamento de funções no HSM Gerenciado](role-management.md)
