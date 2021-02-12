---
title: Configuração de SSO (logon único) com um clique do aplicativo do Azure Marketplace | Microsoft Docs
description: Etapas para a configuração de SSO com um clique para seu aplicativo do Azure Marketplace.
services: active-directory
documentationCenter: na
author: kenwith
manager: daveba
ms.reviewer: kenwith
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50f705e587010d956c29a71127e97dd85cb517ea
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258245"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Configuração de aplicativos de logon único com um clique

 Neste tutorial, você aprenderá a executar a configuração de SSO (logon único) com um clique para aplicativos do Azure AD (Azure Active Directory) que dão suporte ao SAML por meio do Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Introdução ao SSO com um clique

O recurso SSO com um clique foi projetado para configurar o logon único para aplicativos do Azure Marketplace que dão suporte ao protocolo SAML. Na página de configuração de SSO do Azure AD, essa opção permite que você configure automaticamente os metadados do Azure AD no lado do aplicativo. Dessa forma, você poderá configurar rapidamente o SSO com um mínimo de esforço manual.

## <a name="advantages-of-one-click-sso"></a>Vantagens do SSO com um clique

- Configuração de SSO rápido de aplicativos do Azure Marketplace que exigem a configuração manual no lado do aplicativo.
- Configuração de SSO mais eficiente e precisa.
- Nenhuma comunicação de parceiro ou suporte necessário para a instalação. O aplicativo fornece a interface do usuário para a configuração do SAML.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura ativa do aplicativo a ser configurado com o SSO. Você também precisa ter credenciais de administrador.
- A **extensão Entrada Segura dos Meus Aplicativos** da Microsoft instalada no navegador. Para obter mais informações, confira [Acessar e usar aplicativos no portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="one-click-sso-configuration-steps"></a>Etapas da configuração de SSO com um clique

1. Adicione o aplicativo por meio do Azure Marketplace.

2. Selecione **Logon único**.

3. Selecione **Habilitar logon único**.

4. Popule os valores de configuração obrigatórios na seção **Configuração Básica do SAML**.

    > [!NOTE]
    > Se o aplicativo tiver declarações personalizadas que você precisa configurar, lide com elas antes de executar o SSO com um clique.

5. Se o recurso de SSO de um clique estiver disponível para seu aplicativo do Azure Marketplace, você verá a tela a seguir. Talvez seja necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** selecionando **Instalar a extensão**.

   ![Instalar a extensão de navegador Entrada Segura dos Meus Aplicativos](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Depois de adicionar a extensão ao navegador, selecione **Configurar \<Application Name\>** . Depois de ser redirecionado para o portal de administração do aplicativo, entre como administrador.

   ![Configurar o nome do aplicativo](./media/one-click-sso-tutorial/setup-sso.png)

7. A extensão do navegador configura automaticamente o SSO no aplicativo. Confirme selecionando **Sim**.

   ![Como salvar os dados populados automaticamente](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Se a configuração de SSO do aplicativo exigir etapas adicionais, siga os prompts para executar as etapas.

8. Depois que a configuração for concluída, selecione **OK** para salvar as alterações.

   ![Salvar os dados populados automaticamente](./media/one-click-sso-tutorial/save-data.png)

9. Uma janela de confirmação será exibida para informá-lo de que as configurações de SSO foram definidas com êxito.

   ![SSO configurado](./media/one-click-sso-tutorial/sso-configured.png)

10. Após a configuração bem-sucedida, você será desconectado do aplicativo e retornará ao portal do Azure.

11. Selecione **Testar** para testar o logon único.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md)
* [O que é a extensão do navegador Entrada Segura dos Meus Aplicativos?](../user-help/my-apps-portal-end-user-access.md)
