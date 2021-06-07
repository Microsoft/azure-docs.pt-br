---
title: Como gerenciar contas de usuários inativos no Azure Active Directory | Microsoft Docs
description: Saiba mais sobre como detectar e manipular contas de usuário no Azure Active Directory que se tornaram obsoletos
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/21/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fb517f8c50ad2c32f23542e60069a0e0a496a2d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98660657"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Como: Gerenciar contas de usuários inativos no Azure AD

Em ambientes grandes, as contas de usuário nem sempre são excluídas quando os funcionários deixam uma organização. Como administrador de TI, você precisa detectar e tratar essas contas de usuário obsoletas, pois elas representam um risco à segurança.

Este artigo explica um método para tratar as contas de usuário obsoletas no Azure Active Directory. 

## <a name="what-are-inactive-user-accounts"></a>O que são contas de usuário inativas?

Contas inativas são contas de usuário que não são mais necessárias para que os membros da sua organização acessem seus recursos. Um identificador-chave de contas inativas é que elas não foram usadas *há algum tempo* para entrar no seu ambiente. Como as contas inativas são ligadas à atividade de entrada, você pode usar o carimbo de data/hora da última entrada bem-sucedida para detectá-las. 

O desafio desse método é definir o que significa *há algum tempo* no seu ambiente. Por exemplo, os usuários podem não entrar em um ambiente *há algum tempo*, porque eles estão de férias. Ao definir qual é seu delta para contas de usuários inativos, você precisa considerar todos os motivos legítimos que eles possam ter para não entrar no seu ambiente. Em muitas organizações, o delta para contas de usuários inativos é entre 90 e 180 dias. 

A última entrada bem-sucedida pode fornecer insights sobre a necessidade contínua do usuário de acessar os recursos.  Ela pode ajudar a determinar se a associação de grupo ou o acesso ao aplicativo ainda é necessário ou pode ser removido. Para o gerenciamento de usuários externos, você pode saber se um usuário externo ainda está ativo dentro do locatário ou se deve ser excluído. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Como detectar contas de usuário inativas

Para detectar contas inativas, avalie a propriedade **lastSignInDateTime** exposta pelo tipo de recurso **signInActivity** da API do **Microsoft Graph**. Use essa propriedade para implementar uma solução para os seguintes cenários:

- **Usuários por nome**: Nesse cenário, você procura um usuário específico por nome, o que permite avaliar o lastSignInDateTime: `https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Usuários por data**: Nesse cenário, você solicita uma lista de usuários com um lastSignInDateTime que antecede uma data especificada: `https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>O que você precisa saber

Esta seção explica o que você precisa saber sobre a propriedade lastSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Como posso acessar essa propriedade?

A propriedade **lastSignInDateTime** é exposta pelo [tipo de recurso signInActivity](/graph/api/resources/signinactivity?view=graph-rest-beta) da [API REST do Microsoft Graph](/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>A propriedade lastSignInDateTime está disponível por meio do cmdlet Get-AzureAdUser?

Não.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Qual edição do Azure Active Directory preciso ter para acessar a propriedade?

Você pode acessar essa propriedade em todas as edições do Azure Active Directory.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Qual permissão preciso ter para ler a propriedade?

Para ler essa propriedade, você precisa conceder os seguintes direitos: 

- AuditLogs.Read.All
- Organisation.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>Quando o Azure Active Directory atualiza a propriedade?

Cada entrada interativa bem-sucedida resulta em uma atualização do armazenamento de dados subjacente. Normalmente, as entradas bem-sucedidas aparecem no relatório de entradas relacionado em 10 minutos.
 

### <a name="what-does-a-blank-property-value-mean"></a>O que significa um valor de propriedade em branco?

Para gerar um carimbo de data/hora lastSignInDateTime, você precisa de uma entrada bem-sucedida. Como a propriedade lastSignInDateTime é um novo recurso, o valor da propriedade lastSignInDateTime pode ficar em branco se:

- A última entrada bem-sucedida de um usuário ocorreu antes de abril de 2020.
- A conta de usuário afetada nunca foi usada para uma entrada bem-sucedida.

## <a name="next-steps"></a>Próximas etapas

* [Obter dados usando a API de Relatório do Azure Active Directory com certificados](tutorial-access-api-with-certificates.md)
* [Referência da API de auditoria](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [Referência da API de relatório de atividade de entrada](/graph/api/resources/signin?view=graph-rest-beta)