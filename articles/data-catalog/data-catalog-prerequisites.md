---
title: "Pré-requisitos do Catálogo de Dados do Azure | Microsoft Docs"
description: "Saiba mais sobre os pré-requisitos necessário para começar a usar o Catálogo de Dados do Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 3fdef7bb58a5cd5dfbe4d37d9baf9c8e392ebe42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-catalog-prerequisites"></a>Pré-requisitos de Catálogo de Dados do Azure

Você precisa se encarregar algumas questões antes de configurar o Catálogo de Dados do Azure. Não se preocupe, esse processo não demora.

## <a name="azure-subscription"></a>Assinatura do Azure
Para configurar o Catálogo de Dados, você deve ser o proprietário ou o coproprietário de uma assinatura do Azure.

As assinaturas do Azure ajudam a organizar o acesso aos recursos de serviço de nuvem como o Catálogo de Dados. As assinaturas também ajudam a controlar como o uso de recursos é relatado, cobrado e pago. Cada assinatura pode ter uma configuração de cobrança e pagamento separada para que você possa ter assinaturas e planos que variam por departamento, projeto, escritório regional e assim por diante. Cada serviço de nuvem pertence a uma assinatura e você precisa ter uma assinatura antes de configurar o Catálogo de Dados. Para saber mais, confira [Gerenciar contas, assinaturas e funções administrativas](../active-directory/active-directory-assign-admin-roles.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Para configurar o Catálogo de Dados, você deve entrar com uma conta de usuário do Azure AD (Azure Active Directory).

O Azure Active Directory (Azure AD) fornece uma maneira fácil para a sua empresa gerenciar identidades e acesso, tanto na nuvem quanto local. Os usuários podem usar uma única conta corporativa ou de estudante para logon único em qualquer aplicativo Web local ou em nuvem. O Catálogo de Dados usa o Azure AD para autenticar as credenciais. Para saber mais, confira [O que é o Azure Active Directory?](../active-directory/active-directory-whatis.md).

> [!NOTE]
> Usando o [portal do Azure](http://portal.azure.com/), você pode entrar usando uma conta pessoal da Microsoft ou uma conta corporativa ou de estudante do Azure Active Directory. Para configurar o Catálogo de Dados usando o portal do Azure ou o [portal do Catálogo de Dados](http://www.azuredatacatalog.com), você deve entrar com uma conta do Azure Active Directory, não com uma conta pessoal.
>
>

## <a name="active-directory-policy-configuration"></a>Configuração de política do Active Directory
Poderá haver uma situação em que você consiga entrar no portal do Catálogo de Dados, mas, ao tentar entrar na ferramenta de registro de fonte de dados, receba uma mensagem de erro impedindo a entrada. Esse comportamento problemático poderá ocorrer somente quando você estiver na rede da empresa ou poderá ocorrer somente quando você estiver se conectando de fora da rede da empresa.

A ferramenta de registro de fonte de dados usa a Autenticação de Formulários para validar suas credenciais de usuário para o Active Directory. Para ajudá-lo a entrar com êxito, um administrador do Active Directory deve habilitar a Autenticação de Formulários na Política de Autenticação Global.

Na Política de Autenticação Global, métodos de autenticação podem ser habilitados separadamente para conexões de intranet e extranet, conforme mostrado na seguinte captura de tela. Erros de conexão poderão ocorrer se a Autenticação de Formulários não estiver habilitada para a rede da qual você está se conectando.

 ![Política de Autenticação Global do Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte [Configurando políticas de autenticação](https://technet.microsoft.com/library/dn486781.aspx).
