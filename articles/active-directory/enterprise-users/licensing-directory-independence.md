---
title: Características da interação entre vários locatários – Azure AD | Microsoft Docs
description: Noções básicas sobre a independência de dados de suas organizações do Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ce3e4c6a7708fba15560564577c9b01722c8aec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96548045"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Noções básicas sobre como interagem várias organizações do Azure Active Directory interagem

No Azure AD (Azure Active Directory), cada organização do Azure AD é um recurso totalmente independente: um par logicamente independente das outras organizações do Azure AD que você gerencia. Essa independência entre organizações inclui independência de recursos, independência administrativa e independência de sincronização. Não há nenhuma relação pai-filho entre organizações.

## <a name="resource-independence"></a>Independência de recursos

* Se você criar ou excluir um recurso do Azure AD em uma organização, ele não afetará nenhum recurso em outra organização, com a exceção parcial de usuários externos.
* Se você registrar um de seus nomes de domínio com uma organização, ele não poderá ser usado por nenhuma outra organização.

## <a name="administrative-independence"></a>Independência administrativa

Se um usuário não administrativo da organização “Contoso” criar uma organização de teste “Teste”:

* Por padrão, o usuário que criar um locatário será adicionado como um usuário externo nessa nova organização e será atribuído à função de administrador global nessa organização.
* Os administradores da organização “Contoso” não terão privilégios administrativos diretos na organização “Teste”, a menos que um administrador de “Teste” conceda especificamente esses privilégios a eles. No entanto, os administradores da “Contoso” poderão controlar o acesso à organização “Teste” se controlarem a conta de usuário que criou “Teste”.
* Se você adicionar ou remover uma função do Azure AD para um usuário em uma organização, a alteração não afetará as funções a que o usuário está atribuído em qualquer outra organização do Azure AD.

## <a name="synchronization-independence"></a>Independência de sincronização

Configure cada organização do Azure AD de maneira independente para sincronizar os dados de uma única instância de:

* Ferramenta do Azure AD Connect, para sincronizar dados com uma única floresta do AD.
* Azure Active Directory Connector para o Forefront Identity Manager, para sincronizar dados com uma ou mais florestas locais e/ou fontes de dados não Azure AD.

## <a name="add-an-azure-ad-organization"></a>Adicionar uma organização do Azure AD

Para adicionar uma organização do Azure AD no portal do Azure, entre no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do Azure AD e selecione **Novo**.

> [!NOTE]
> Ao contrário de outros recursos do Azure, as organizações do Azure AD não são recursos filho de uma assinatura do Azure. Caso sua assinatura do Azure seja cancelada ou expire, você ainda poderá acessar os dados da organização do Azure AD usando o Azure PowerShell, a API do Microsoft Graph ou o centro de administração do Microsoft 365. Você também pode [associar outra assinatura à organização](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Próximas etapas

Para ver considerações sobre licenciamento do Azure AD e melhores práticas, confira [O que é o licenciamento do Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
