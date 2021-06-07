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
ms.openlocfilehash: 06db7bcb5698f152dd5062762fdb3d59ae326e22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102603276"
---
O SMB Multichannel para compartilhamentos de arquivos do Azure atualmente tem as seguintes restrições:
- Só pode ser usado com contas de armazenamento de repositórios localmente redundantes.
- Somente com suporte para clientes do Windows. 
- O número máximo de canais é quatro.
- Não há suporte para SMB Direct.
- Não há suporte para pontos de extremidade privados para contas de armazenamento.
- Para contas de armazenamento com Active Directory Domain Services local (AD DS) ou [autenticação baseada em identidade](../articles/storage/files/storage-files-active-directory-overview.md) do Azure AD DS habilitada para arquivos do Azure, os clientes SMB não poderão usar o explorador de arquivos do Windows para configurar permissões NTFS em diretórios e arquivos.
    - Use a ferramenta [icacls](/windows-server/administration/windows-commands/icacls) do Windows ou o comando [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) em vez de configurar permissões.

