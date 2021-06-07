---
title: Pré-requisitos de API-Azure Marketplace
description: Pré-requisitos para usar as APIs de Portal do Cloud Partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/23/2020
ms.openlocfilehash: 374cc3398d1037b19ceddcbdaafd333f643e44ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91261151"
---
# <a name="api-prerequisites"></a>Pré-requisitos de API

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao e continuarão funcionando no Partner Center. A transição apresenta pequenas alterações. Examine as alterações listadas em [portal do Cloud Partner referência de API](cloud-partner-portal-api-overview.md) para garantir que seu código continue funcionando após a transição para o Partner Center. Use apenas APIs de CPP para produtos existentes que já foram integrados antes da transição para o Partner Center; os novos produtos devem usar as APIs de envio do Partner Center.

Você precisa de dois ativos programáticos necessários para usar Portal do Cloud Partner APIs: uma entidade de serviço e um token de acesso do Azure Active Directory (AD do Azure).

## <a name="create-service-principal-in-azure-active-directory-tenant"></a>Criar entidade de serviço no locatário Azure Active Directory

Primeiro, você precisa criar uma entidade de serviço no locatário do Azure AD. Este locatário será atribuído a seu próprio conjunto de permissões no Portal do Cloud Partner. Seu código chamará APIs usando esse locatário em vez de suas credenciais pessoais. Para obter uma explicação completa da criação de uma entidade de serviço, consulte [como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="add-service-principal-to-your-account"></a>Adicionar entidade de serviço à sua conta

Agora que você criou a entidade de serviço em seu locatário, você pode adicioná-la como um usuário à sua conta do portal do Partner Center. Assim como um usuário, a entidade de serviço pode ser um proprietário ou um colaborador no portal. Para obter detalhes, consulte **as próximas etapas** abaixo.

## <a name="next-steps"></a>Próximas etapas

Consulte [gerenciar aplicativos do Azure ad](partner-center-portal/manage-account.md#manage-azure-ad-applications).
