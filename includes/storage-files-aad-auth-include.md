---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 84fa97ec964d490eb9571c7e030704562a4a81d0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560544"
---
[Os arquivos do Azure](../articles/storage/files/storage-files-introduction.md) oferecem suporte à autenticação baseada em identidade sobre o protocolo SMB por meio [do Active Directory Domain Services local (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) e [Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md). Este artigo se concentra em como os compartilhamentos de arquivos do Azure podem usar os serviços de domínio, no local ou no Azure, para dar suporte ao acesso baseado em identidade aos compartilhamentos de arquivos do Azure por SMB. Habilitar o acesso baseado em identidade para seus compartilhamentos de arquivos do Azure permite que você substitua servidores de arquivos existentes por compartilhamentos de arquivos do Azure sem substituir o serviço de diretório existente, mantendo acesso contínuo de usuário a compartilhamentos. 

Os arquivos do Azure impõe a autorização no acesso do usuário ao compartilhamento e aos níveis de diretório/arquivo. A atribuição de permissão de nível de compartilhamento pode ser executada em usuários do Azure Active Directory (AD do Azure) ou grupos gerenciados por meio do modelo de [controle de acesso baseado em função do Azure (RBAC do Azure)](../articles/role-based-access-control/overview.md) . Com o RBAC, as credenciais usadas para o acesso ao arquivo devem estar disponíveis ou sincronizadas com o Azure AD. Você pode atribuir funções internas do Azure como o leitor de compartilhamento SMB de dados de arquivo de armazenamento a usuários ou grupos no Azure AD para conceder acesso de leitura a um compartilhamento de arquivos do Azure.

No nível de diretório/arquivo, os arquivos do Azure dão suporte à preservação, herança e imposição de [DACLs do Windows](/windows/win32/secauthz/access-control-lists) , assim como qualquer servidor de arquivos do Windows. Você pode optar por manter as DACLs do Windows ao copiar dados via SMB entre o compartilhamento de arquivos existente e os compartilhamentos de arquivos do Azure. Se você planeja impor autorização ou não, pode usar compartilhamentos de arquivos do Azure para fazer backup de ACLs junto com seus dados.