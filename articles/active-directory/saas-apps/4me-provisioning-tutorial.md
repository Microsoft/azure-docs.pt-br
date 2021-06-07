---
title: 'Tutorial: Configurar o 4me para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no 4me.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: c0c428997cfba8871a29d9bfe0df0a6920a1d22f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998661"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Tutorial: Configurar o 4me para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no 4me e no Azure AD (Azure Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no 4me.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do 4me](https://www.4me.com/trial/)
* Uma conta de usuário no 4me com permissões de Administrador.

## <a name="add-4me-from-the-gallery"></a>Adicionar o 4me por meio da galeria

Antes de configurar o 4me para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o 4me por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o 4me por meio da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **4me**, selecione **4me** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![4me na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Como atribuir usuários ao 4me

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam ter acesso ao 4me. Depois de decidir isso, você poderá atribuir esses usuários e/ou grupos ao 4me seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Dicas importantes para atribuir usuários ao 4me

* Recomendamos que somente um usuário do Azure AD seja atribuído ao 4me para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao 4me, você precisará selecionar qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Configurar o provisionamento automático de usuário para o 4me 

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no 4me com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o 4me seguindo as instruções fornecidas no [tutorial de logon único do 4me](4me-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o 4me no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **4me**.

    ![O link do 4me na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Para recuperar a **URL do Locatário** e o **Token Secreto** da sua conta do 4me, siga o passo a passo descrito na Etapa 6.

6. Entre no console do administrador do 4me. Procure **Configurações**.

    ![Configurações do 4me](media/4me-provisioning-tutorial/4me01.png)

    Digite **aplicativos** na barra de pesquisa.

    ![Aplicativos do 4me](media/4me-provisioning-tutorial/4me02.png)

    Abra a lista suspensa **SCIM** para recuperar o Token Secreto e o ponto de extremidade do SCIM.

    ![SCIM do 4me](media/4me-provisioning-tutorial/4me03.png)

7. Depois de preencher os campos mostrados na Etapa 5, clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao 4me. Se a conexão falhar, verifique se a sua conta do 4me tem permissões de Administrador e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Save** (Salvar).

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o 4me**.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-user-mapping.png" alt-text="Captura de tela da página Mapeamentos. Em Nome, a opção Sincronizar Usuários do Azure Active Directory com o FourMe está realçada." border="false":::
    
11. Examine os atributos de usuário que serão sincronizados do Azure AD para o 4me na seção **Mapeamento de Atributo**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência entre as contas de usuário no 4me em operações de atualização. Verifique se o [4me dá suporte à filtragem](https://developer.4me.com/v1/scim/users/) no atributo correspondente escolhido. Selecione o botão **Salvar** para confirmar as alterações.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-user-attributes.png" alt-text="Captura de tela da página Mapeamentos de Atributos. Uma tabela lista os atributos do Azure Active Directory, os atributos correspondentes do FourMe e o status correspondente." border="false":::
    
12. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o 4me**.

    :::image type="content" source="media/4me-provisioning-tutorial/4me-group-mapping.png" alt-text="Captura de tela da página Mapeamentos. Em Nome, a opção Sincronizar Grupos do Azure Active Directory com o FourMe está realçada." border="false":::
    
13. Examine os atributos de grupo sincronizados do Azure AD com o 4me na seção **Mapeamento de Atributo**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no 4me em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Mapeamentos de Grupo no 4me](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD no 4me, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

16. Defina os usuários e/ou os grupos que você quer provisionar no 4me escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no 4me.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O 4me tem diferentes URLs de ponto de extremidade do SCIM para ambientes de teste e produção. A primeira termina em **.qa**, enquanto a segunda termina em **.com**
* Os Tokens Secretos gerados pelo 4me têm uma data de validade de um mês após a geração.
* O 4me não dá suporte a operações **DELETE**

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
