---
title: "Azure Active Directory B2C: solucionar problemas criando locatários | Microsoft Docs"
description: "Problemas e resoluções para criar um Azure Active Directory ou um locatário do Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 7ba4c6b2-161b-45b5-b3bd-ccb662f5d7a0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 81af4536fc223319369aff262d42149cfbf1a1e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Problemas e resoluções para criar um Azure Active Directory ou um locatário do Azure Active Directory B2C 

## <a name="create-an-azure-ad-tenant"></a>Criar um locatário do Azure AD
Caso não consiga criar um locatário do Azure Active Directory (Azure AD) na primeira tentativa, tente novamente. Se o problema persistir, contate o Suporte do Azure.

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um locatário do Azure AD B2C
Se você encontrar problemas ao [criar um locatário do Azure Active Directory B2C (Azure AD B2C)](active-directory-b2c-get-started.md), tente as seguintes opções:

* Se o locatário do Azure AD B2C não aparecer na lista de locatários, tente criá-lo novamente.
* Se o locatário do Azure AD B2C aparecer na sua lista de locatários e você vir a seguinte mensagem de erro, exclua o locatário e crie-o novamente:

    "Não foi possível concluir a criação do locatário B2C 'contosob2c'. Acesse este [link](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) para obter mais diretrizes".
* Há problemas conhecidos quando você exclui um locatário do Azure AD B2C existente e o recria com o mesmo nome de domínio. Quando você criar um novo locatário do Azure AD B2C, você deverá usar um nome de domínio diferente.
* Se essas resoluções não funcionarem, entre em contato com o Suporte do Azure. Para obter mais informações, consulte [Arquivar solicitações de suporte para o Azure AD B2C](active-directory-b2c-support.md).

