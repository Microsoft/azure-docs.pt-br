---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: f014ce55dc40723faf1b60f908814f9fa0428b8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99215293"
---
O Azure fornece as seguintes funções internas do Azure para autorizar o acesso aos dados de blob e fila usando o Azure AD e o OAuth:

- [Proprietário de dados de blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Use para definir a propriedade e gerenciar o controle de acesso POSIX para o Azure Data Lake Storage Gen2. Para obter mais informações, consulte [Controle de acesso no Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Colaborador de dados de blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Use para conceder permissões de leitura/gravação/exclusão de recursos de armazenamento de Blob.
- [Leitor de dados de blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Use para conceder permissões somente leitura aos recursos de armazenamento de Blob.
- [Delegador do blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): Obtenha uma chave de delegação de usuário para criar uma assinatura de acesso compartilhado que é assinada com as credenciais do Azure AD para um contêiner ou blob.
- [Colaborador de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Use para conceder permissões de leitura/gravação/exclusão às filas do Azure.
- [Leitor de dados de fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Use para conceder permissões somente leitura para as filas do Azure.
- [Processador de mensagens de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Use para conceder permissões de inspeção, recuperação e exclusão a mensagens em filas do Armazenamento do Azure.
- [Remetente da mensagem de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Use para conceder permissões de adição a mensagens em filas do Armazenamento do Azure.

Somente as funções explicitamente definidas para acesso a dados permitem que uma entidade de segurança acesse dados de blob ou fila. Funções internas, como **Proprietário**, **Colaborador** e **Colaborador de conta de armazenamento** permitem que uma entidade de segurança gerencie uma conta de armazenamento, mas não fornecem acesso aos dados de blob ou fila nessa conta por meio do Azure AD. No entanto, se uma função incluir a **Microsoft.Storage/storageAccounts/listKeys/action**, um usuário ao qual essa função é atribuída poderá acessar os dados na conta de armazenamento por meio da autorização de Chave Compartilhada com as chaves de acesso da conta. Para obter mais informações, consulte [Usar o portal do Azure para acessar dados de blob ou de fila](../articles/storage/blobs/authorize-data-operations-portal.md).

Para obter informações detalhadas sobre as funções internas do Azure para o Armazenamento do Azure para os serviços de dados e o serviço de gerenciamento, consulte a seção **Armazenamento** em [Funções internas do Azure para o RBAC do Azure](../articles/role-based-access-control/built-in-roles.md#storage). Além disso, para obter informações sobre os diferentes tipos de funções que fornecem permissões no Azure, consulte [Funções de administrador de assinatura clássicas, funções do Azure e funções do Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> As atribuições de função do Azure podem levar até 30 minutos para serem propagadas.
