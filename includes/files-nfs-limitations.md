---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4b708e80bf335ba8bdce074285857a6f8b77b972
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103439100"
---
Durante a visualização, o NFS tem as seguintes limitações:

- O NFS 4,1 atualmente dá suporte apenas à maioria dos recursos da [especificação de protocolo](https://tools.ietf.org/html/rfc5661). Alguns recursos, como delegações e retorno de chamada de todos os tipos, atualizações de bloqueio e Downgrades, autenticação Kerberos e criptografia não têm suporte.
- Se a maioria de suas solicitações for centrada em metadados, a latência será pior quando comparada com as operações de leitura/gravação/atualização.
- Deve criar uma nova conta de armazenamento para criar um compartilhamento NFS.
- Somente as APIs REST do plano de gerenciamento têm suporte. As APIs REST do plano de dados não estão disponíveis, o que significa que ferramentas como Gerenciador de Armazenamento não funcionarão com compartilhamentos NFS, nem será possível procurar dados de compartilhamento NFS no portal do Azure.
- Não há suporte para AzCopy no momento.
- Disponível somente para a camada Premium.
- Os compartilhamentos NFS aceitam apenas UID/GID numéricos. Para evitar que seus clientes enviem UID/GID alfanumérico, você deve desabilitar o mapeamento de ID.
- Os compartilhamentos só podem ser montados de uma conta de armazenamento em uma VM individual, ao usar links privados. A tentativa de montar compartilhamentos de outras contas de armazenamento falhará.
- É melhor contar com as permissões atribuídas ao grupo primário. Às vezes, as permissões alocadas para o grupo não primário do usuário podem resultar em acesso negado devido a um bug conhecido.

### <a name="azure-storage-features-not-yet-supported"></a>Recursos de armazenamento do Azure ainda não suportados

Além disso, os seguintes recursos de arquivos do Azure não estão disponíveis com compartilhamentos NFS:

- Autenticação baseada em identidade
- Suporte de Backup do Azure
- Instantâneos
- Exclusão reversível
- Suporte completo para criptografia-em trânsito (para obter detalhes, consulte [segurança de NFS](../articles/storage/files/storage-files-compare-protocols.md#security))
- Sincronização de Arquivos do Azure (disponível somente para clientes Windows, aos quais o NFS 4,1 não oferece suporte)
