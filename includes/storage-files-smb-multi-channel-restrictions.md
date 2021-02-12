---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: bbf0530c1a7f1a747d456d87efc106418f23b7ba
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052844"
---
O SMB Multichannel para compartilhamentos de arquivos do Azure atualmente tem as seguintes restrições:
- Só pode ser usado com contas de armazenamento de repositórios localmente redundantes.
- Somente com suporte para clientes do Windows. 
- O número máximo de canais é quatro.
- Não há suporte para SMB Direct.
- Não há suporte para pontos de extremidade privados para contas de armazenamento.
- Para contas de armazenamento com Active Directory Domain Services local (AD DS) ou [autenticação baseada em identidade](../articles/storage/files/storage-files-active-directory-overview.md) do Azure AD DS habilitada para arquivos do Azure, os clientes SMB não poderão usar o explorador de arquivos do Windows para configurar permissões NTFS em diretórios e arquivos.
    - Use a ferramenta [icacls](/windows-server/administration/windows-commands/icacls) do Windows ou o comando [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) em vez de configurar permissões.

