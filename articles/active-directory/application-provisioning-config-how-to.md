---
title: "Como configurar provisionamento do usuário para um aplicativo de galeria do Azure AD | Microsoft Docs"
description: "Como você pode rapidamente configurar provisionamento e desprovisionamento de conta de usuário avançado para aplicativos já listados na Galeria de Aplicativos do Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 2e38fcb30ea7632339a3ba8815a536872cfcc69e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Como configurar provisionamento do usuário para um aplicativo de galeria do Azure AD

*Provisionamento de contas de usuário* é o ato de criar, atualizar e/ou desabilitar os registros de conta de usuário no armazenamento de perfil do usuário local do aplicativo. A maioria dos aplicativos de nuvem e SaaS armazenam a função de usuários e permissões em seu próprio armazenamento de perfil do usuário local e a presença de um registro de usuário no armazenamento local é *necessária* para logon único e acesso ao trabalho.

No portal do Azure, a guia de **provisionamento** no painel de navegação à esquerda de um aplicativo empresarial exibe quais modos de provisionamento tem suporte para esse aplicativo. Existem dois valores válidos:

## <a name="configuring-an-application-for-manual-provisioning"></a>Configurar um aplicativo para o provisionamento manual

Provisionamento *manual* significa que as contas de usuário devem ser criadas manualmente usando os métodos fornecidos pelo aplicativo. Isso pode significar fazendo logon em um portal administrativo para que o aplicativo e adicionar usuários usando uma interface do usuário baseada na web. Ou ele pode carregar uma planilha com detalhes da conta de usuário, usando um mecanismo fornecido pelo aplicativo. Consulte a documentação fornecida pelo aplicativo ou entre em contato com o desenvolvedor do aplicativo para determinar a wat mecanismos estão disponíveis.

Se Manual é o único modo mostrado para um determinado aplicativo, isso significa que nenhum automático do conector de provisionamento do Azure AD foi criado para o aplicativo ainda. Ou, isso significa que o aplicativo não oferece suporte ao pré-requisito do API de gerenciamento do usuário no qual criar um conector de provisionamento automatizado.

Se você quiser solicitar suporte para o provisionamento automático para um determinado aplicativo, você poderá preencher uma solicitação em <http://aka.ms/aadapprequest>.

## <a name="configuring-an-application-for-automatic-provisioning"></a>Configurar um aplicativo para o provisionamento automático

*Automático* significa que o conector de provisionamento do Azure AD foi desenvolvido para este aplicativo. Para saber mais sobre o serviço de provisionamento do Azure AD e como ele funciona, confira [Automatizar o provisionamento e o desprovisionamento de usuário para aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Para obter mais informações sobre como provisionar usuários e grupos a um aplicativo específico, consulte [Gerenciamento de provisionamento de conta de usuário para aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

As etapas reais necessárias para habilitar e configurar o provisionamento automático variam dependendo do aplicativo.

>[!NOTE]
>Você deve começar encontrando o tutorial de instalação específicas de configuração de provisionamento para o seu aplicativo e seguir as etapas para configurar o aplicativo e o Azure AD para criar a conexão de provisionamento. 
>
>

Tutoriais de aplicativos podem ser encontrados em [lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Uma coisa importante a considerar ao configurar o provisionamento ser examinar e configurar os mapeamentos de atributo e fluxos de trabalho que definem quais fluxo propriedades de usuário (ou grupo) do Azure AD para o aplicativo. Isso inclui a configuração de "propriedade correspondente" usada para identificar exclusivamente e corresponder usuários/grupos entre os dois sistemas. Para obter mais informações sobre esse processo importante.

## <a name="next-steps"></a>Próximas etapas
[Personalizar os mapeamentos de atributos de provisionamento de usuário para aplicativos SaaS no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

