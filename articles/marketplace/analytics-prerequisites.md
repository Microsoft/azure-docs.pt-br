---
title: Pré-requisitos para acessar dados de análise programaticamente
description: Conheça os requisitos que você precisa cumprir antes de poder acessar de forma programática dados de análise do Marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 3b109048be4a94990c26e31aa5bc2ad36fdd0211
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583672"
---
# <a name="prerequisites-to-programmatically-access-analytics-data"></a>Pré-requisitos para acessar dados de análise programaticamente

Antes de poder acessar de forma programática os dados de análise do Marketplace comercial, você precisa atender aos seguintes requisitos.

## <a name="commercial-marketplace-enrollment"></a>Registro do Marketplace comercial

Para acessar dados de análise do Marketplace comercial programaticamente, você precisa estar registrado no programa do Marketplace comercial e ter uma conta do Partner Center. Para saber como, consulte [criar uma conta do Marketplace comercial no Partner Center](./partner-center-portal/create-account.md).

## <a name="create-azure-active-directory-application"></a>Criar aplicativo do Active Directory do Azure

As credenciais de usuário regulares não podem ser usadas para acesso programático de dados de análise do Marketplace comercial. Um aplicativo Azure Active Directory (AD do Azure) precisa ser criado junto com um segredo para acessar as APIs de análise. Para saber como criar um aplicativo e um segredo do Azure AD, consulte [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

## <a name="associate-the-azure-ad-application-to-the-partner-center-tenant"></a>Associar o aplicativo do Azure AD ao locatário do Partner Center

O aplicativo do Azure AD criado no portal do Azure precisa ser vinculado à sua conta do Partner Center. As etapas são as seguintes:

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard).
1. No canto superior direito, selecione o ícone de engrenagem e, em seguida, selecione **configurações de conta**.
1. No menu **configurações de conta** , selecione **Gerenciamento de usuários**.
1. Selecione **aplicativos do Azure ad** e, em seguida, selecione **+ criar aplicativo do Azure ad**.
1. Selecione o aplicativo do Azure AD que você criou em portal do Azure e, em seguida, selecione **Avançar**.
1. Marque a caixa de seleção **Gerenciador (Windows)** e, em seguida, selecione **Adicionar**.

    :::image type="content" source="./media/analytics-programmatic-access/azure-ad-roles.png" alt-text="Ilustra a página criar aplicativo do Azure AD com as caixas de seleção para selecionar funções.":::

## <a name="generate-an-azure-ad-token"></a>Gerar um token do Azure AD

Você precisa gerar um token do Azure AD usando a ID do aplicativo (cliente). Essa ID ajuda a identificar exclusivamente o aplicativo cliente na plataforma de identidade da Microsoft e o segredo do cliente da etapa anterior. Para obter as etapas para gerar um token do Azure AD, confira [chamadas de serviço a serviços usando credenciais de cliente (segredo compartilhado ou certificado)](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow).

> [!NOTE]
> O token é válido por uma hora.

## <a name="next-steps"></a>Próximas etapas

- [Paradigma de acesso programático](analytics-programmatic-access.md)
