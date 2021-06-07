---
title: 'Tutorial: Configurar o Oracle Fusion ERP para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Oracle Fusion ERP.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: da6e1a8ba31f8f4991bde4803191598a015a68b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358432"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Tutorial: Configurar o Oracle Fusion ERP para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Oracle Fusion ERP e no Azure AD (Active Directory) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o Oracle Fusion ERP.

> [!NOTE]
>  Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia. Para obter mais informações sobre os termos de uso gerais do Microsoft Azure para as versões prévias de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* Um [locatário do Oracle Fusion ERP](https://www.oracle.com/applications/erp/).
* Uma conta de usuário no Oracle Fusion ERP com permissões de Administrador.

## <a name="assign-users-to-oracle-fusion-erp"></a>Atribuir usuários ao Oracle Fusion ERP 
O Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao Oracle Fusion ERP. Depois de decidir, será possível atribuir esses usuários e/ou grupos ao Oracle Fusion ERP seguindo estas instruções:
 
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Dicas importantes para atribuir usuários ao Oracle Fusion ERP 

 * Recomendamos que somente um usuário do Azure AD seja atribuído ao Oracle Fusion ERP para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Oracle Fusion ERP, é necessário selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função Acesso padrão são excluídos do provisionamento.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Configurar o Oracle Fusion ERP para provisionamento

Antes de configurar o Oracle Fusion ERP para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no Oracle Fusion ERP.

1. Entre no [Console de Administração do Oracle Fusion ERP](https://cloud.oracle.com/sign-in)

2. Clique no Navegador no canto superior esquerdo. Em **Ferramentas**, selecione **Console de Segurança**.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/login.png" alt-text="Captura de tela da página Navegador no console de administração do Oracle Fusion E R P. Ferramentas e Console de segurança estão realçados." border="false":::

3. Navegue até **Usuários**.
    
    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user.png" alt-text="Captura de tela de um painel no console de administração do Oracle Fusion E R P. O item Usuários está realçado." border="false":::

4. Salve o nome de usuário e a senha da conta de usuário administrador que você usará para fazer logon no console de administração do Oracle Fusion ERP. Esses valores precisam ser inseridos nos campos **Nome de Usuário Administrador** e **Senha** na guia Provisionamento do aplicativo Oracle Fusion ERP no portal do Azure.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Adicionar o Oracle Fusion ERP da galeria

Antes de configurar o Oracle Fusion ERP para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o Oracle Fusion ERP da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Oracle Fusion ERP da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Oracle Fusion ERP**, selecione **Oracle Fusion ERP** no painel de resultados.

    ![Oracle Fusion ERP na lista de resultados](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Configurar o provisionamento automático de usuário para o Oracle Fusion ERP 

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Oracle Fusion ERP com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Oracle Fusion ERP, seguindo as instruções fornecidas no [tutorial de logon único do Oracle Fusion ERP](oracle-fusion-erp-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

> [!NOTE]
> Para saber mais sobre o ponto de extremidade SCIM do Oracle Fusion ERP, veja a [API REST para Recursos Comuns no Oracle Applications Cloud](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html).

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Fuze no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Oracle Fusion ERP**.

    ![O link do Oracle Fusion ERP na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` em **URL do Locatário**. Insira o nome de usuário administrador e a senha recuperados anteriormente nos campos **Nome de Usuário Administrador** e **Senha**. Clique em **Testar Conectividade** entre o Azure AD e o Oracle Fusion ERP. 

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/admin.png" alt-text="Captura de tela da seção Credenciais de administrador. Um botão Testar conexão e os campos de URL do Locatário, nome de usuário administrador e senha do administrador estão visíveis." border="false":::

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Oracle Fusion ERP**.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png" alt-text="Captura de tela da seção Mapeamentos. Em Nome, Sincronizar Usuários do Azure Active Directory com o Oracle Fusion E R P está visível." border="false":::

9. Examine os atributos de usuário sincronizados do Azure AD com o Oracle Fusion ERP na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Oracle Fusion ERP para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png" alt-text="Captura de tela da página Mapeamentos de Atributos. Uma tabela lista os atributos do Azure Active Directory e do Oracle Fusion E R P e a precedência de correspondência." border="false":::

10. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Oracle Fusion ERP**.

    ![Mapeamentos de Grupo do Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Examine os atributos de grupo sincronizados do Azure AD com o Oracle Fusion ERP na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no Oracle Fusion ERP para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Grupo do Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD no Oracle Fusion ERP, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para o Oracle Fusion ERP escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

    Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Oracle Fusion ERP.

    Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Oracle Fusion ERP dá suporte apenas à Autenticação Básica para o ponto de extremidade SCIM.
* O Oracle Fusion ERP não dá suporte ao provisionamento de grupo.
* As funções no Oracle Fusion ERP são mapeadas para grupos no Azure AD. Para atribuir funções a usuários no Oracle Fusion ERP do Azure AD, você precisará atribuir usuários aos grupos do Azure AD desejados que são nomeados com o nome de funções no Oracle Fusion ERP.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
