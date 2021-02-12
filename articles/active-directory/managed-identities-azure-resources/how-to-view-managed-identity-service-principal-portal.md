---
title: Exibir a entidade de serviço de uma identidade gerenciada no portal do Azure-Azure AD
description: Instruções passo a passo para exibir a entidade de serviço de uma identidade gerenciada usando o portal do Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b25f2691646192faf4d4c133835b456038abd94
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003728"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Exibir a entidade de serviço de uma identidade gerenciada no portal do Azure

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprende como exibir a entidade de serviço de uma identidade gerenciada usando o portal do Azure.

 > [!NOTE] 
 > As entidades de serviço são aplicativos empresariais. 

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md).
- Caso ainda não tenha uma conta do Azure, [inscreva-se para obter uma conta gratuita](https://azure.microsoft.com/free/).
- Habilite a [identidade designada pelo sistema em uma máquina virtual](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) ou [aplicativo](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Exibir a entidade de serviço

Esse procedimento demonstra como exibir a entidade de serviço de uma VM com a identidade atribuída do sistema habilitada (as mesmas etapas se aplicam para um aplicativo).

1. Clique no **Azure Active Directory** e, em seguida, clique em **Aplicativos empresariais**.
2. Em **tipo de aplicativo**, escolha **todos os aplicativos** e clique em **aplicar**.
3. Na caixa filtro de pesquisa, digite o nome do recurso do Azure que tem a identidade gerenciada habilitada ou escolha-o na lista apresentada.

   ![Exibir as entidades gerenciadas de serviço de identidade no portal](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Próximas etapas

[Identidades gerenciadas para os recursos do Azure](./overview.md)