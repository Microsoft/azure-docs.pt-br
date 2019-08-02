---
title: 'Tutorial: Configurar o Zoom para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas usuário ao Zoom.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 0a21a35e6d4a938d41e8bc11cebc1be5738d893e
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670998"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zoom para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Zoom e o Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente provisionar e desprovisionar provisionar usuários e/ou grupos ao Zoom.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os termos do Microsoft Azure gerais de uso para recursos de visualização, consulte [termos de uso adicionais para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Zoom](https://zoom.us/pricing)
* Uma conta de usuário no Zoom com permissões de administrador

## <a name="add-zoom-from-the-gallery"></a>Adicionar o Zoom da Galeria

Antes de configurar o Zoom para provisionamento automático de usuário com o Azure AD, você precisará adicionar o Zoom da Galeria de aplicativo do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zoom da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No  **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione a **novo aplicativo** botão na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zoom**, selecione **Zoom** no painel de resultados e em seguida, clique o **Add** botão para adicionar o aplicativo.

    ![Ampliar a lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>Atribuir usuários ao Zoom

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Zoom. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Zoom, seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>Dicas importantes para atribuir usuários ao Zoom

* É recomendável que um único usuário do Azure AD seja atribuído ao Zoom para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Zoom, você deve selecionar qualquer função específica de aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Configurar o provisionamento automático de usuário para Zoom 

Esta seção orienta você pelas etapas de configuração do AD do Azure serviço de provisionamento para criar, atualizar e desabilitar usuários ou grupos no Zoom com base nas atribuições de usuário e/ou grupo no AD do Azure.

> [!TIP]
> Você também pode optar por habilitar baseado em SAML SSO para o Zoom, seguindo as instruções fornecidas na [tutorial do logon único Zoom](zoom-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Configurar o provisionamento automático de usuário para o Zoom no Azure AD

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Zoom**.

    ![O link do Zoom na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Sob o **credenciais de administrador** , digite `https://api.zoom.us/scim` na **URL de locatário**. Para recuperar o **segredo do Token** da sua conta de Zoom, siga o passo a passo, conforme descrito na etapa 6.

6. Entrar no seu [Console de administração de Zoom](https://zoom.us/signin). Navegue até **Avançado > Aplicar Zoom para desenvolvedores** no painel de navegação à esquerda.

    ![Integrações de zoom](media/zoom-provisioning-tutorial/zoom01.png)

    Navegue até **gerenciar** no canto superior direito da página. 

    ![Instalação de zoom](media/zoom-provisioning-tutorial/zoom02.png)

    Navegue até seu criadas aplicativo do Azure AD. 
    
    ![Aplicativo de zoom](media/zoom-provisioning-tutorial/zoom03.png)

    Selecione **credenciais de aplicativo** no painel de navegação à esquerda.

    ![Aplicativo de zoom](media/zoom-provisioning-tutorial/zoom04.png)

    Recuperar o valor do Token de JWT mostrado abaixo e de entrada dentro de **segredo do Token** campo no Azure AD. Se você precisar de um novo token não expira, você precisará reconfigurar a expiração do tempo que será automaticamente gerar um novo token. 

    ![Instalação de zoom](media/zoom-provisioning-tutorial/zoom05.png)

7. Ao popular os campos mostrados na etapa 5, clique em **Conexão de teste** para garantir que o Azure AD pode se conectar ao Zoom. Se a conexão falhar, verifique se que sua conta de Zoom tem permissões de administrador e tente novamente.

    ![A criptografia do token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Salvar**.

10. Sob o **mapeamentos** seção, selecione **sincronizar Azure usuários do Active Directory ao Zoom**.

    ![Mapeamentos de usuário do zoom](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. Examine os atributos de usuário que são sincronizados do AD do Azure para ampliar a **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder as contas de usuário do Zoom para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.
    
     ![Mapeamentos de usuário do zoom](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o provisionamento de serviço para o Zoom do Azure AD, altere o **Status de provisionamento** ao **na** no **configurações** seção.
    
    ![Status de provisionamento ativado](common/provisioning-toggle-on.png)

14. Definir os usuários e/ou grupos que você gostaria para provisionar o zoom, escolhendo os valores desejados em **escopo** na **configurações** seção.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar o **detalhes de sincronização** seção para monitorar o progresso e siga os links para relatório de atividade, que descreve todas as ações executadas pelo serviço de Zoom de provisionamento do Azure AD de provisionamento.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Zoom não oferece suporte para o provisionamento de grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
