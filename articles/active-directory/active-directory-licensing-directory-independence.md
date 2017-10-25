---
title: "Características da interação entre locatários do Azure Active Directory | Microsoft Docs"
description: "Gerenciar os locatários do Azure Active Directory considerando os locatários como recursos totalmente independentes"
services: active-tenant
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 2b862b75-14df-45f2-a8ab-2a3ff1e2eb08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 5c9225212e51d03ae40e3a55a6b9e6c0e74b5ced
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Entender como interagem vários locatários do Azure Active Directory

No Azure AD (Azure Active Directory), cada locatário é um recurso totalmente independente: um par logicamente independente dos outros locatários gerenciados. Não há nenhuma relação pai-filho entre locatários. Essa independência entre locatários inclui independência de recursos, independência administrativa e independência de sincronização.

## <a name="resource-independence"></a>Independência de recursos
* Se você criar ou excluir um recurso de um locatário, ele não afetará nenhum recurso em outro locatário, com a exceção parcial de usuários externos. 
* Se você usar um de seus nomes de domínio com um locatário, ele não poderá ser usado com nenhum outro locatário.

## <a name="administrative-independence"></a>Independência administrativa
Se um usuário não administrativo do locatário “Contoso” criar um locatário de teste “Teste”:

* Por padrão, o usuário que cria um locatário é adicionado como um usuário externo nesse novo locatário e é atribuído à função de administrador global nesse locatário.
* Os administradores do locatário “Contoso” não têm privilégios administrativos diretos no diretório “Teste”, a menos que um administrador de “Teste” conceda especificamente esses privilégios a eles. No entanto, os administradores do “Contoso” poderão controlar o acesso ao locatário “Teste” se controlarem a conta de usuário que criou “Teste”.
* Se você adicionar ou remover uma função de administrador de um usuário em um locatário, a alteração não afetará as funções de administrador que o usuário tem em outro locatário.

## <a name="synchronization-independence"></a>Independência de sincronização
Configure cada locatário do Azure AD de maneira independente para sincronizar os dados de uma única instância de:

* Ferramenta do Azure AD Connect, para sincronizar dados com uma única floresta do AD.
* Conector de locatário do Azure Active Directory para o Forefront Identity Manager, para sincronizar dados com uma ou mais florestas locais e/ou fontes de dados não Azure AD.

## <a name="add-an-azure-ad-tenant"></a>Adicionar um locatário do Azure AD
Para adicionar um locatário do Azure AD no Portal do Azure, entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do Azure AD e, à esquerda, selecione **Novo**.

> [!NOTE]
> Ao contrário de outros recursos do Azure, os locatários não são recursos filho de uma assinatura do Azure. Caso sua assinatura do Azure seja cancelada ou expire, você ainda poderá acessar os dados de locatário usando o Azure PowerShell, a API do Graph do Azure ou o Centro de Administração do Office 365. Você também pode [associar outra assinatura ao locatário](active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral ampla dos problemas de licenciamento e as melhores práticas do Azure AD, consulte [O que é o licenciamento de locatário do Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md).
