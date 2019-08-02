---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 026717dff2b6883eb643497dec91226e4afe8133
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171596"
---
O Azure fornece as seguintes funções internas de RBAC para autorizar o acesso a dados de blob e fila usando o Azure AD e OAuth:

- [Proprietário de dados de Blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Use para definir a propriedade e gerenciar o controle de acesso do POSIX para armazenamento do Azure Data Lake Storage Gen2 (visualização). Para obter mais informações, consulte [Controle de acesso no Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Colaborador de dados de Blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Use para conceder permissões de leitura/gravação/exclusão de recursos de armazenamento de Blob.
- [Leitor de dados de Blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Use para conceder permissões somente leitura aos recursos de armazenamento de Blob.
- [Colaborador de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Use para conceder permissões de leitura/gravação/exclusão às filas do Azure.
- [Leitor de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Use para conceder permissões somente leitura para as filas do Azure.
- [Processador de mensagem da fila de armazenamento de dados](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Use grant inspecionar, recuperar e exclui as permissões para as mensagens nas filas de armazenamento do Azure.
- [Remetente de mensagem da fila de armazenamento de dados](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Usar para conceder adicionar permissões para as mensagens nas filas de armazenamento do Azure.

> [!NOTE]
> Tenha em mente que as atribuições de função RBAC podem levar até cinco minutos para propagar.
