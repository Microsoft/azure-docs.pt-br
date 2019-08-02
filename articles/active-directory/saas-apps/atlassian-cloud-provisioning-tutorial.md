---
title: 'Tutorial: Configurar Atlassian Cloud para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Atlassian Cloud.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: f168e2746afa278880ad7ceb21f78666151d5aa1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672698"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Tutorial: Configurar Atlassian Cloud para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Atlassian Cloud e o Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos ao Atlassian Cloud.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os termos do Microsoft Azure gerais de uso para recursos de visualização, consulte [termos de uso adicionais para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Atlassian Cloud](https://www.atlassian.com/licensing/cloud)
* Uma conta de usuário no Atlassian Cloud com permissões de administrador.

> [!NOTE]
> Integração de provisionamento do Azure AD depende de **API de SCIM do Atlassian Cloud**, que está disponível para as equipes do Atlassian Cloud.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Adicionar o Atlassian Cloud por meio da galeria

Antes de configurar Atlassian Cloud para provisionamento com o Azure AD automático de usuário, você precisará adicionar o Atlassian Cloud da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Atlassian Cloud da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No  **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione a **novo aplicativo** botão na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Atlassian Cloud**, selecione **Atlassian Cloud** no painel de resultados e em seguida, clique o **Add** botão para adicionar o aplicativo.

    ![Atlassian Cloud na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Atribuir usuários ao Atlassian Cloud

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Atlassian Cloud. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Atlassian Cloud, seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Dicas importantes para atribuir usuários ao Atlassian Cloud

* É recomendável que um único usuário do Azure AD seja atribuído ao Atlassian Cloud para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Atlassian Cloud, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Configurando o provisionamento automático de usuário ao Atlassian Cloud 

Esta seção orienta você pelas etapas de configuração do AD do Azure serviço de provisionamento para criar, atualizar e desabilitar usuários e/ou grupos no Atlassian Cloud com base no usuário e/ou as atribuições de grupo no AD do Azure.

> [!TIP]
> Você também pode optar por habilitar baseado em SAML single sign-on para Atlassian Cloud, seguindo as instruções fornecidas na [tutorial do logon único Atlassian Cloud](atlassian-cloud-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Atlassian Cloud no Azure AD:

1. Entrar para o [portal do Azure](https://portal.azure.com) e selecione **aplicativos empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Atlassian Cloud**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Atlassian Cloud**.

    ![O link para o Atlassian Cloud na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento do Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Sob o **credenciais de administrador** seção, insira o **URL do locatário** e **Token secreto** da conta do Atlassian Cloud. Exemplos desses valores são:

   * No **URL do locatário** campo, preencher o ponto de extremidade de locatário específico que você recebe do Atlassian, conforme descrito na etapa 6. Por exemplo: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * No campo **Token Secreto**, preencha o token secreto conforme descrito na Etapa 6.

6. Navegue até [Gerenciador de organização Atlassian](https://admin.atlassian.com) **> provisionamento do usuário** e clique em **criar um Token**. Cópia de **URL base do diretório** e **Token de portador** para o **URL do locatário** e **Token secreto** campos, respectivamente.

    ![Provisionamento do Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![de provisionamento do Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)

    ![Provisionamento do Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Ao popular os campos mostrados na etapa 5, clique em **Conexão de teste** para garantir que o Azure AD pode se conectar ao Atlassian Cloud. Se a conexão falhar, verifique se que sua conta do Atlassian Cloud tem permissões de administrador e tente novamente.

    ![Provisionamento do Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento do Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. Clique em **Salvar**.

10. Sob o **mapeamentos** seção, selecione **sincronizar Azure usuários do Active Directory ao Atlassian Cloud**.

    ![Provisionamento do Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD ao Atlassian Cloud na **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder as contas de usuário no Atlassian Cloud para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. Sob o **mapeamentos** seção, selecione **sincronizar grupos do Azure Active Directory ao Atlassian Cloud**.

    ![Provisionamento do Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD ao Atlassian Cloud na **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder os grupos no Atlassian Cloud para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento do Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço para Atlassian Cloud de provisionamento do AD do Azure, altere o **Status de provisionamento** para **na** no **configurações** seção.

    ![Provisionamento do Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. Definir os usuários e/ou grupos que você gostaria para provisionar o Atlassian Cloud, escolhendo os valores desejados em **escopo** na **configurações** seção.

    ![Provisionamento do Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento do Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar o **detalhes de sincronização** seção para monitorar o progresso e siga os links para relatório de atividade, que descreve todas as ações executadas pelo serviço no Atlassian Cloud de provisionamento do Azure AD de provisionamento.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Permite o provisionamento de usuários somente de Atlassian Cloud [domínios verificados](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian Cloud não dá suporte atualmente renomeações de grupo. Isso significa que todas as alterações para o displayName de um grupo no Azure AD não ser atualizadas e refletidas no Atlassian Cloud.
* O valor de **mail** atributo de usuário no Azure AD é populado somente se o usuário tem uma caixa de correio do Microsoft Exchange. Se o usuário não tiver uma, é recomendável para mapear um atributo diferente desejado para o **emails** atributo no Atlassian Cloud.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
