---
title: Configurar aplicativos SaaS para colaboração B2B – Azure AD
description: Saiba como configurar aplicativos SaaS para Azure Active Directory colaboração B2B e exibir recursos adicionais disponíveis.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0db5e609a5e6b7d74809810a50f86d72ae85675
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90705503"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Configurar aplicativos SaaS para colaboração B2B

A colaboração B2B do Azure Active Directory (Azure AD) funciona com a maioria dos aplicativos que se integra no Azure AD. Nesta seção, examinaremos as instruções de como configurar alguns aplicativos SaaS populares para usar com o B2B do Azure AD.

Antes de examinarmos as instruções específicas do aplicativo, aqui estão algumas regras gerais:

* Para a maioria dos aplicativos, a configuração do usuário precisa acontecer manualmente. Ou seja, os usuários devem ser criados manualmente no aplicativo também.

* Para os aplicativos que oferecem suporte para a configuração automática, como o Dropbox, convites separados são criados a partir dos aplicativos. Os usuários devem aceitar cada convite.

* Nos atributos do usuário, para atenuar problemas com o disco de perfil do usuário (UDP) danificado nos usuários convidados, sempre defina o **Identificador do Usuário** para **user.mail**.


## <a name="dropbox-business"></a>Dropbox Business

Para permitir que os usuários façam logon usando suas contas da organização, você deve configurar manualmente o Dropbox Business para usar o Azure AD como um provedor de identidade SAML (Security Assertion Markup Language). Se o Dropbox Business não foi configurado para fazer isso, não poderá solicitar ou, caso contrário, permitirá que os usuários façam logon usando o Azure AD.

1. Para adicionar o aplicativo Dropbox Business no Azure AD, selecione **Aplicativos da empresa** no painel esquerdo, em seguida, clique em **Adicionar**.

   ![Botão "Adicionar" na página Aplicativos da empresa](media/configure-saas-apps/add-dropbox.png)

2. Na janela **Adicionar um aplicativo**, digite **dropbox** na caixa de pesquisa, em seguida, selecione **Dropbox for Business** na lista de resultados.

   ![Procurar "dropbox" na página Adicionar um aplicativo](media/configure-saas-apps/add-app-dialog.png)

3. Na página **Logon único**, selecione **Logon único** no painel esquerdo, em seguida, digite **user.mail** na caixa **Identificador do Usuário**. (É definido como UPN por padrão.)

   ![Configurar o logon único para o aplicativo](media/configure-saas-apps/configure-app-sso.png)

4. Para baixar o certificado a usar para a configuração do Dropbox, selecione **Configurar DropBox**, em seguida, selecione a **URL do Serviço de Logon Único da SAML** na lista.

   ![Baixar o certificado para a configuração do Dropbox](media/configure-saas-apps/download-certificate.png)

5. Entre no Dropbox com a URL de logon na página **Logon único**.

   ![Captura de tela mostrando a página de entrada do Dropbox](media/configure-saas-apps/sign-in-to-dropbox.png)

6. No menu, selecione **Console Admin**.

   ![Link "Console Admin" no menu Dropbox](media/configure-saas-apps/dropbox-menu.png)

7. Na caixa de diálogo **Autenticação**, selecione **Mais**, carregue o certificado, em seguida, na caixa **URL de Logon**, digite a URL de logon único da SAML.

   ![Link "Mais" na caixa de diálogo Autenticação recolhida](media/configure-saas-apps/dropbox-auth-01.png)

   ![A "URL de Logon" na caixa de diálogo Autenticação expandida](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Para configurar a definição automática do usuário no portal do Azure, selecione **Provisionamento** no painel esquerdo, selecione **Automático** na caixa **Modo de Provisionamento**, em seguida, selecione **Autorizar**.

   ![Configurar o provisionamento automático do usuário no portal do Azure](media/configure-saas-apps/set-up-automatic-provisioning.png)

Depois dos usuários convidados ou membros terem sido configurados no aplicativo Dropbox, eles receberão um convite separado do Dropbox. Para usar o logon único do Dropbox, os convidados devem aceitar o convite clicando em um link.

## <a name="box"></a>Box
Você pode permitir que os usuários autentiquem os usuários convidados do Box com sua conta do Azure AD usando a federação com base no protocolo SAML. Neste procedimento, é possível carregar os metadados no Box.com.

1. Adicione o aplicativo Box a partir dos aplicativos da empresa.

2. Configure o logon único na seguinte ordem:

   ![Captura de tela mostrando as definições de configuração de logon único](media/configure-saas-apps/configure-box-sso.png)

   a. Na caixa **URL de Logon**, verifique se a URL de Logon está definida corretamente para o Box no portal do Azure. Essa é a URL do seu locatário Box.com. Ele deve seguir a Convenção de nomenclatura *https://.box.com* .  
   O **Identificador** não se aplica a esse aplicativo, mas ainda aparece como um campo obrigatório.

   b. Na caixa **Identificador do usuário**, digite **user.mail** (para o SSO das contas de convidado).

   c. Em **Certificado de Assinatura de SAML**, clique em **Criar novo certificado**.

   d. Para começar a configurar seu locatário Box.com para usar o Azure AD como um provedor de identidade, baixe o arquivo de metadados e salve-o em sua unidade local.

   e. Encaminhe o arquivo de metadados para a equipe de suporte do Box, que configura o logon único para você.

3. Para a configuração automática do usuário do Azure AD, no painel esquerdo, selecione **Provisionamento**, em seguida, selecione **Autorizar**.

   ![Autorizar o Azure AD a conectar o Box](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Como os convidados do Dropbox, os convidados do Box também devem resgatar seu convite no aplicativo Box.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos na colaboração B2B do Azure AD:

- [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
- [Grupos dinâmicos e colaboração B2B](use-dynamic-groups.md)
- [Mapeamento de declarações de usuário de colaboração B2B](claims-mapping.md)
- [Microsoft 365 compartilhamento externo](o365-external-user.md)

