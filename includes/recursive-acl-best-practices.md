---
title: incluir arquivo
description: incluir arquivo
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: b0be074c995fcc62f63a4a2ebf4149d3040e7b6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750098"
---
## <a name="best-practices"></a>Práticas recomendadas

Esta seção fornece algumas diretrizes de práticas recomendadas para definir as ACLs recursivamente. 

#### <a name="handling-runtime-errors"></a>Manipulando erros de tempo de execução

Um erro de tempo de execução pode ocorrer por vários motivos (por exemplo: uma interrupção ou um problema de conectividade de cliente). Se você encontrar um erro de tempo de execução, reinicie o processo de ACL recursivo. As ACLs podem ser reaplicadas a itens sem causar um impacto negativo. 

#### <a name="handling-permission-errors-403"></a>Tratamento de erros de permissão (403)

Se você encontrar uma exceção de controle de acesso durante a execução de um processo de ACL recursivo, a [entidade de segurança](../articles/role-based-access-control/overview.md#security-principal) do AD poderá não ter permissão suficiente para aplicar uma ACL a um ou mais dos itens filho na hierarquia de diretórios. Quando ocorre um erro de permissão, o processo é interrompido e um token de continuação é fornecido. Corrija o problema de permissão e, em seguida, use o token de continuação para processar o conjunto de um restante. Os diretórios e arquivos que já foram processados com êxito não precisam ser processados novamente. Você também pode optar por reiniciar o processo de ACL recursivo. As ACLs podem ser reaplicadas a itens sem causar um impacto negativo. 

#### <a name="credentials"></a>Credenciais 

Recomendamos que você provisione uma entidade de segurança do Azure AD que tenha sido atribuída à função de [proprietário de dados do blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner) no escopo da conta de armazenamento de destino ou do contêiner. 

#### <a name="performance"></a>Desempenho 

Para reduzir a latência, recomendamos que você execute o processo de ACL recursivo em uma VM (máquina virtual) do Azure localizada na mesma região que a sua conta de armazenamento. 

#### <a name="acl-limits"></a>Limites de ACL

O número máximo de ACLs que você pode aplicar a um diretório ou arquivo é de 32 ACLs de acesso e 32 ACLs padrão. Para obter mais informações, consulte [Controle de acesso no Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).