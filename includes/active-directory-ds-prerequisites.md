---
title: Arquivo de inclusão
description: Incluir arquivo com os pré-requisitos
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 1fba8cc9ae40cf5539016bbd73de65f557a64136
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359357"
---
> [!IMPORTANT]
> **Habilite a sincronização de hash de senha para Azure Active Directory Domain Services, antes de concluir as tarefas neste artigo.**
>
> Siga as instruções a seguir, dependendo do tipo de usuários em seu diretório do Microsoft Azure Active Directory. Se você tiver uma combinação de contas de usuário somente em nuvem e sincronizadas em seu diretório do Microsoft Azure Active Directory conclua ambos os conjuntos de instruções. Você não poderá realizar as seguintes operações no caso de você está tentando usar uma conta de convidado de B2B (exemplo, o gmail ou MSA de um provedor de identidade diferente que podemos permitir) porque não temos a senha para esses usuários sincronizados com o domínio gerenciado, pois essas são contas de convidado no diretório. As informações completas sobre essas contas, incluindo suas senhas seria fora do Azure AD e como essas informações não estão no Azure AD, portanto, ele não até mesmo obter sincronizado com o domínio gerenciado. 
> - [Instruções para contas de usuário somente na nuvem](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Instruções para as contas de usuário sincronizadas de um diretório local](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
