---
title: Guia de referência de operações de Azure Active Directory
description: Este guia de referência de operações descreve as verificações e ações que você deve executar para proteger e manter o gerenciamento de identidades e acesso, autenticação, governança e operações
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "74535308"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Guia de referência de operações de Azure Active Directory

Este guia de referência de operações descreve as verificações e ações que você deve executar para proteger e manter as seguintes áreas:

- **[Gerenciamento de acesso e identidade](active-directory-ops-guide-iam.md)** – capacidade de gerenciar o ciclo de vida de identidades e seus direitos.
- **[Gerenciamento de autenticação](active-directory-ops-guide-auth.md)** -capacidade de gerenciar credenciais, definir a experiência de autenticação, delegar atribuição, uso de medidas e definir políticas de acesso com base na postura de segurança corporativa.
- **[Governança](active-directory-ops-guide-govern.md)** – capacidade de avaliar e atestar o acesso concedido a identidades sem privilégios e privilegiadas, auditoria e alterações de controle no ambiente.
- **[Operações](active-directory-ops-guide-ops.md)** – otimize o Azure Active Directory de operações (Azure AD).

Algumas recomendações aqui podem não ser aplicáveis ao ambiente de todos os clientes, por exemplo, AD FS práticas recomendadas talvez não se apliquem se sua organização usar a sincronização de hash de senha.

> [!NOTE]
> Essas recomendações são atuais a partir da data de publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente suas práticas de identidade à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo. As recomendações podem ser alteradas quando as organizações assinam uma licença de Azure AD Premium diferente. Por exemplo, Azure AD Premium P2 incluirá mais recomendações de governança.

## <a name="stakeholders"></a>Participantes

Cada seção neste guia de referência recomenda atribuir participantes para planejar e implementar tarefas-chave com êxito. A tabela a seguir descreve a lista de todos os participantes deste guia:

| Stakeholder | Descrição |
| :- | :- |
| Equipe de operações IAM | Essa equipe lida com o gerenciamento das operações diárias do sistema de gerenciamento de identidades e acesso |
| Equipe de produtividade | Essa equipe possui e gerencia os aplicativos de produtividade, como email, compartilhamento de arquivos e colaboração, mensagens instantâneas e conferências. |
| Proprietário do Aplicativo | Essa equipe possui o aplicativo específico de um negócio e geralmente é uma perspectiva técnica em uma organização. |
| Equipe de arquitetura do batalha | Essa equipe planeja e projeta as práticas de segurança de informações de uma organização. |
| Equipe de operações do batalha | Essa equipe executa e monitora as práticas de segurança de informações implementadas da equipe de arquitetura do batalha. |

## <a name="next-steps"></a>Próximas etapas

Introdução às [verificações e ações de gerenciamento de identidade e acesso](active-directory-ops-guide-iam.md).
