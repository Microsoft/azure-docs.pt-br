---
title: 'Tutorial: configurar o StarLeaf para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o StarLeaf.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 07c476c0de644ac63c577d466f4691b5cf415334
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357922"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Tutorial: configurar o StarLeaf para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no StarLeaf e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no StarLeaf.

> [!NOTE]
>  Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário do StarLeaf](https://starleaf.com/).
* Uma conta de usuário no StarLeaf com permissões de administrador.

## <a name="assign-users-to-starleaf"></a>Atribuir usuários ao StarLeaf
Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e grupos no Azure AD precisam de acesso ao StarLeaf. Em seguida, você pode atribuir os usuários e grupos ao StarLeaf seguindo [estas instruções](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Dicas importantes para atribuir usuários ao StarLeaf

* É recomendável que um único usuário do Azure AD seja atribuído ao StarLeaf para testar a configuração automática de provisionamento de usuário. Usuários e grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao StarLeaf, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função Acesso padrão são excluídos do provisionamento.

## <a name="set-up-starleaf-for-provisioning"></a>Configurar o StarLeaf para provisionamento

Antes de configurar o StarLeaf para o provisionamento automático de usuário com o Azure AD, será necessário configurar o provisionamento do SCIM no StarLeaf:

1. Entre no console do [administrador do StarLeaf](https://portal.starleaf.com/#page=login). Navegue até **integrações**  >  **Adicionar integração**.

    ![Captura de tela do StarLeaf admin console com as integrações e adicionar opções de integração chamadas.](media/starleaf-provisioning-tutorial/image00.png)

2. Selecione o **tipo** a ser Microsoft Azure Active Directory. Insira um nome adequado em **nome**. Clique em **Aplicar**.

    ![Captura de tela da caixa de diálogo Adicionar integração com as caixas de texto tipo e nome chamadas.](media/starleaf-provisioning-tutorial/image01.png)

3.  A **URL base scim** e os valores de **token de acesso** serão exibidos. Esses valores serão inseridos nos campos **URL do locatário** e **token secreto** na guia provisionamento do aplicativo StarLeaf no portal do Azure. 

    ![Captura de tela da caixa de diálogo Editar integração com as caixas de texto tipo, nome e URL base SCIM chamadas.](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>Adicionar o StarLeaf da Galeria

Para configurar o StarLeaf para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o StarLeaf da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o StarLeaf da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa** , em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **StarLeaf** , selecione **StarLeaf** no painel de resultados.
    ![StarLeaf na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Configurar o provisionamento automático de usuário para o StarLeaf

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no StarLeaf com base em atribuições de usuário e/ou grupo no Azure AD.

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **StarLeaf**.

    ![O link do StarLeaf na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções de gerenciamento com a opção de provisionamento chamada out.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa modo de provisionamento com a opção automática chamada out.](common/provisioning-automatic.png)

5. Na seção credenciais de administrador, insira a **URL base do scim** e os valores de **token de acesso** recuperados anteriormente na **URL do locatário** e no **token secreto** , respectivamente. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao StarLeaf. Se a conexão falhar, verifique se sua conta do StarLeaf tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa **Enviar uma notificação por email quando ocorrer uma falha** .

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para StarLeaf**.

    ![Captura de tela da seção mapeamentos mostrando a opção sincronizar Azure Active Directory usuários para StarLeaf.](media/starleaf-provisioning-tutorial/usermapping.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o StarLeaf na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no StarLeaf para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Captura de tela da seção mapeamentos de atributo mostrando nove mapeamentos exibidos.](media/starleaf-provisioning-tutorial/userattribute.png)


10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


11. Para habilitar o serviço de provisionamento do Azure AD para o StarLeaf, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para o StarLeaf escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no StarLeaf.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Limitações do conector

* No momento, o StarLeaf não dá suporte ao provisionamento de grupo. 
* StarLeaf exige que os valores de **email** e **nome de usuário** tenham o mesmo valor de origem.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [examinar os logs e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
