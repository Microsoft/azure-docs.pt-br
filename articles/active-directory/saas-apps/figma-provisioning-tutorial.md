---
title: 'Tutorial: Configurar o provisionamento automático de usuário do figma com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o figma.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 38ebba8803e584e9b5d1179281fcff3a3f98d5a4
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67848169"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Tutorial: Configurar o figma para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no figma e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no figma.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do figma](https://www.figma.com/pricing/).
* Uma conta de usuário no figma com permissões de administrador.

## <a name="assign-users-to-figma"></a>Atribuir usuários ao figma.
Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao figma. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao figma seguindo as instruções aqui:
 
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Dicas importantes para atribuir usuários ao figma

 * É recomendável que um único usuário do Azure AD seja atribuído ao figma para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao figma, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de acesso padrão são excluídos do provisionamento.

## <a name="set-up-figma-for-provisioning"></a>Configurar o figma para provisionamento

Antes de configurar o figma para o provisionamento automático de usuário com o Azure AD, será necessário recuperar algumas informações de provisionamento do figma.

1. Entre no console do [administrador do figma](https://www.Figma.com/). Clique no ícone de engrenagem ao lado de seu locatário.

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/image0.png)

2. Navegue até **geral > atualizar log em configurações**.

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/figma03.png)

3. Copie a **ID do locatário**. Esse valor será usado para construir a URL do ponto de extremidade SCIM a ser inserida no campo **URL do locatário** na guia provisionamento do aplicativo Figma no portal do Azure.

    ![Figma criar token](media/Figma-provisioning-tutorial/figma-tenantid.png)

4. Role para baixo e clique em **gerar token de API**.

    ![Figma criar token](media/Figma-provisioning-tutorial/token.png)

5. Copie o valor do **token de API** . Esse valor será inserido no campo **token secreto** na guia provisionamento do seu aplicativo Figma no portal do Azure. 

    ![Figma criar token](media/Figma-provisioning-tutorial/figma04.png)

## <a name="add-figma-from-the-gallery"></a>Adicionar o figma da Galeria

Para configurar o figma para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o figma da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **figma**, selecione **figma** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Figma na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Configurando o provisionamento automático de usuário para o figma 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no figma com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o figma, seguindo as instruções fornecidas no [tutorial de logon único do figma](figma-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Para configurar o provisionamento automático de usuário para figma no Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Figma**.

    ![O link do figma na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://www.figma.com/scim/v2/<TenantID>` a **URL de locatário** em que **tenantid** é o valor que você recuperou do figma anteriormente. Insira o valor do **token de API** no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao figma. Se a conexão falhar, verifique se sua conta do figma tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Salvar**.

10. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para figma**.

    ![Mapeamentos de usuário figma](media/Figma-provisioning-tutorial/figma05.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD para o figma na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no figma para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do figma](media/Figma-provisioning-tutorial/figma06.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o figma, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o figma escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no figma.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)