---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ff2ed5abbf2ce67a0b96a5da450b83832403db1f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269373"
---
Os [Arquivos do Azure](../articles/storage/files/storage-files-introduction.md) dão suporte à autenticação baseada em identidade via protocolo SMB (versão prévia) por meio do [Azure AD (Azure Active Directory) Domain Services](../articles/active-directory-domain-services/overview.md). As VMs (máquinas virtuais) do Windows ingressadas no domínio podem acessar os compartilhamentos de arquivos do Azure usando as credenciais do [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md). 

Você pode gerenciar o acesso de nível de compartilhamento de arquivos do Azure para uma identidade, como um usuário, grupo no Azure AD com o [controle de acesso baseado em função (RBAC)](../articles/role-based-access-control/overview.md). É possível definir funções personalizadas do RBAC que abrangem conjuntos comuns de permissões usadas para acessar os arquivos do Azure. Ao atribuir a função RBAC personalizada a uma identidade do Azure AD, essa identidade recebe acesso a um compartilhamento de arquivos do Azure de acordo com essas permissões.

Como parte da versão prévia, os Arquivos do Azure também dão suporte à preservação, herança e imposição de [DACLs do NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) em todos os arquivos e diretórios em um compartilhamento de arquivos. Se você copiar dados de um compartilhamento de arquivos para os Arquivos do Azure, ou vice-versa, poderá especificar que as DACLs do NTFS sejam mantidas. Dessa forma, é possível implementar cenários de backup usando os Arquivos do Azure, preservando as DACLs do NTFS entre o compartilhamento de arquivos local e o compartilhamento de arquivos na nuvem. 

> [!NOTE]
> - Não há suporte para autenticação de serviço de domínio do AD do Azure para acesso ao SMB para VMs do Linux. Só as VMs do Windows têm suporte.
> - Não há suporte para autenticação de serviço de domínio do AD do Azure para acesso ao SMB para computador associado do domínio do Active Directory. Enquanto isso, você pode considerar aproveitar [sincronização de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) para iniciar a migração de seus dados para arquivos do Azure e continuar para impor o controle de acesso usando as credenciais do AD do seu local ingressado no domínio do AD máquinas. 
> - Autenticação de serviço de domínio do AD do Azure para acesso ao SMB está disponível somente para contas de armazenamento criadas após 24 de setembro de 2018.
> - Não há suporte para autenticação de serviço de domínio do AD do Azure para acesso ao SMB e NTFS DACL persistente em compartilhamentos de arquivos do Azure gerenciados pelo serviço de sincronização de arquivos do Azure. 
