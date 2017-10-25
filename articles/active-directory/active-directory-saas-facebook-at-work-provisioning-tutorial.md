---
title: "Tutorial: configurar o Workplace by Facebook para provisionamento de usuário | Microsoft Docs"
description: "Saiba como provisionar e desprovisionar automaticamente contas de usuário do Azure AD para o Workplace by Facebook."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: a347eedbf5511dc83e1bc7721667441cfb87cb59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configure-workplace-by-facebook-for-user-provisioning"></a>Tutorial: configurar o Workplace by Facebook para provisionamento de usuário

Este tutorial mostra as etapas necessárias para provisionar e cancelar o provisionamento de contas de usuário automaticamente do Azure AD (Azure Active Directory) para o Workplace by Facebook.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Workplace by Facebook, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Workplace by Facebook habilitada para SSO (logon único)

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma [oferta de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-workplace-by-facebook"></a>Atribuir usuários ao Workplace by Facebook

O Azure AD usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, decida quais usuários e grupos no Azure AD representam os usuários que precisam de acesso ao aplicativo Workplace by Facebook. Depois disso, você pode atribuir esses usuários ao aplicativo Workplace by Facebook seguindo a instruções em [Atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

>[!IMPORTANT]
>*   Teste a configuração de provisionamento atribuindo um único usuário do Azure AD ao Workplace by Facebook. Atribua usuários e grupos adicionais mais tarde.
>*   Ao atribuir um usuário ao Workplace by Facebook, você deve selecionar uma função de usuário válida. A função de Acesso Padrão não funciona para provisionamento.

## <a name="enable-automated-user-provisioning"></a>Habilitar o provisionamento automatizado de usuários

Esta seção orienta você sobre como conectar o Azure AD à API de provisionamento de conta de usuário do Workplace by Facebook. Você também aprenderá a configurar o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no Workplace by Facebook. Isso se baseia na atribuição de usuários e grupos no Azure AD.

>[!Tip]
>Você também pode optar por habilitar o SSO baseado em SAML para o Workplace by Facebook, seguindo as instruções fornecidas no [Portal do Azure](https://portal.azure.com). O SSO pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Configurar o provisionamento de conta de usuário para o Workplace by Facebook no Azure AD

O Azure AD dá suporte à capacidade de sincronizar automaticamente os detalhes da conta de usuários atribuídos ao Workplace by Facebook. Essa sincronização automática permite que o Workplace by Facebook obtenha os dados necessários para autorizar o acesso aos usuários, antes que eles tentem se conectar pela primeira vez. Ele também desprovisiona os usuários do Workplace by Facebook quando o acesso tiver sido revogado no Azure AD.

1. No [Portal do Azure](https://portal.azure.com), selecione **Azure Active Directory** > **Aplicativos Empresariais** > **Todos os aplicativos**.

2. Se você já tiver configurado o Workplace by Facebook para SSO, pesquise a instância do Workplace by Facebook usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **Workplace by Facebook** na galeria de aplicativos. Selecione o **Workplace by Facebook** nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione a instância do Workplace by Facebook e, depois, a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**. 

    ![Captura de tela das opções de provisionamento do Workplace by Facebook](./media/active-directory-saas-facebook-at-work-provisioning-tutorial/provisioning.png)

5. Na seção **Credenciais de Administrador**, insira o **Token Secreto** e a **URL de Locatário** do administrador do Workplace by Facebook.

6. No Portal do Azure, selecione **Testar conexão** para garantir que o Azure AD possa se conectar ao aplicativo Workplace by Facebook. Se a conexão falhar, verifique se sua conta do Workplace by Facebook tem permissões de Administrador de Equipe.

7. Insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção.

8. Selecione **Salvar**.

9. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o Workplace by Facebook**.

10. Na seção **Mapeamentos de Atributo**, examine os atributos de usuário que são sincronizados do Azure AD para o Workplace by Facebook. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Workplace by Facebook em operações de atualização. Para confirmar as alterações, selecione **Salvar**.

11. Para habilitar o serviço de provisionamento do Azure AD no Workplace by Facebook, na seção **Configurações,** altere o **Status de provisionamento** para **Ativado**.

12. Selecione **Salvar**.

Para obter mais informações sobre como configurar o provisionamento automático, consulte [a documentação do Facebook](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers).

Agora você pode criar uma conta de teste. Aguarde até 20 minutos para verificar se a conta foi sincronizada com o Workplace by Facebook.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para aplicativos empresariais](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar Logon Único](active-directory-saas-facebook-at-work-tutorial.md)

