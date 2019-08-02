---
title: Criar um locatário do Azure para um aplicativo multilocatário
description: Diretrizes para fornecedores independentes de software sobre como integrar com o Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69cc625500af60a753ad8e7db0363954088f3307
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659574"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Criar um locatário do Azure para um aplicativo multilocatário  

Para fornecer acesso a seu aplicativo multilocatário, você deve criar um locatário do Azure Active Directory para registrar o aplicativo e habilitar a federação de identidades do seu cliente. Ver [escolher o protocolo de Federação correto para seu aplicativo multilocatário](isv-choose-multi-tenant-federation.md). Este locatário permitirá que você teste seu aplicativo e a federação em um ambiente que é semelhante de seus ambientes de clientes do Azure AD.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Custos de hospedagem de um aplicativo multilocatário

O Azure Active Directory está disponível em três SKUs, gratuito, Basic e Premium. [Ver a comparação de recursos detalhada](https://azure.microsoft.com/pricing/details/active-directory/).

Você pode criar sua assinatura do Azure e o Azure active directory gratuito e usar os recursos básicos.

## <a name="create-your-tenant"></a>Crie seu locatário

1. Crie seu locatário. Ver [configurar um ambiente de desenvolvimento](../develop/quickstart-create-new-tenant.md).

2. Ativar e testar o acesso de logon único para seu aplicativo,

   a. **Para aplicativos de OIDC ou Oath**, [registre seu aplicativo](../develop/quickstart-register-app.md) como um aplicativo multilocatário. Selecione as contas em qualquer diretório organizacional e a opção de contas Microsoft pessoal em tipos de conta com suporte

   b. **Para aplicativos e WS-Fed-baseado em SAML**, você [baseado em SAML de configurar o logon único](configure-single-sign-on-non-gallery-applications.md) aplicativos usando um modelo genérico de SAML no Azure AD.

Você também pode [converter um aplicativo de locatário único para vários locatários](../develop/howto-convert-app-to-be-multi-tenant.md) se necessário.

## <a name="next-steps"></a>Próximas etapas

[Integrar o SSO em seu aplicativo](isv-sso-content.md)
