---
title: 'Tutorial: Configurar o ZPA (Zscaler Private Access) para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o ZPA (Zscaler Private Access).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 14708ddcc5c0e06ee58f5e9db5945c4e9f1a1d08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937131"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Tutorial: Configurar o ZPA (Zscaler Private Access) para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no ZPA (Zscaler Private Access) e no Azure AD (Active Directory) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o ZPA (Zscaler Private Access).

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do ZPA (Zscaler Private Access)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Uma conta de usuário no ZPA (Zscaler Private Access) com Permissões de administrador.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Como atribuir usuários ao ZPA (Zscaler Private Access)

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao ZPA (Zscaler Private Access). Depois de decidir, você pode atribuir esses usuários e/ou grupos ao ZPA (Zscaler Private Access) seguindo estas instruções:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Dicas importantes para atribuir usuários ao ZPA (Zscaler Private Access)

* É recomendável que um usuário do Azure AD seja atribuído ao ZPA (Zscaler Private Access) para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao ZPA (Zscaler Private Access), é necessário selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Configurar o ZPA (Zscaler Private Access) para provisionamento

1. Entre no [Console de Administração do ZPA (Zscaler Private Access)](https://admin.private.zscaler.com/). Navegue até **Administração > Configuração de IdP**.

    ![Console de Administração do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Verifique se um IdP para **Logon único** está configurado. Se nenhum IdP for configurado, adicione um clicando no ícone de adição no canto superior direito da tela.

    ![Adicionar SCIM do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Siga o assistente **Adicionar Configuração de IdP** para adicionar um IdP. Deixe o campo **Logon único** definido como **Usuário**. Forneça um **Nome** e selecione os **Domínios** na lista suspensa. Clique em **Avançar** para navegar até a próxima janela.

    ![Adicionar IdP do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Baixe o **Certificado do Provedor de Serviços**. Clique em **Avançar** para navegar até a próxima janela.

    ![Certificado SP do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. Na próxima janela, carregue o **Certificado do Provedor de Serviços** baixado anteriormente.

    ![Carregar certificado do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Role para baixo para fornecer a **URL de Logon Único** e a **ID da Entidade IdP**.

    ![ID do IdP do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Role para baixo até **Habilitar a Sincronização do SCIM**. Clique no botão **Gerar Novo Token**. Copie o **Token de Portador**. Esse valor será inserido no campo Token Secreto na guia Provisionamento do aplicativo ZPA (Zscaler Private Access) no portal do Azure.

    ![Criar Token do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Para localizar a **URL do Locatário**, navegue até **Administração > Configuração de IdP**. Clique no nome da configuração de IdP recém-adicionada na página.

    ![Nome do IdP do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Role para baixo para ver o **Ponto de Extremidade de Provedor de Serviços do SCIM** no final da página. Copie o **Ponto de Extremidade de Provedor de Serviços do SCIM**. Esse valor será inserido no campo URL do Locatário na guia Provisionamento do aplicativo ZPA (Zscaler Private Access) no portal do Azure.

    ![URL do SCIM do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Adicionar o ZPA (Zscaler Private Access) da galeria

Antes de configurar o ZPA (Zscaler Private Access) para provisionamento automático de usuário com o Azure AD, é necessário adicionar o ZPA (Zscaler Private Access) da galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ZPA (Zscaler Private Access) da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **ZPA (Zscaler Private Access)** , selecione **ZPA (Zscaler Private Access)** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![ZPA (Zscaler Private Access) na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Configurar o provisionamento automático de usuário para o ZPA (Zscaler Private Access) 

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no ZPA (Zscaler Private Access) com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o ZPA (Zscaler Private Access) seguindo as instruções fornecidas no [tutorial de logon único do ZPA (Zscaler Private Access)](./zscalerprivateaccess-tutorial.md). O logon único pode ser configurado de modo independente do provisionamento automático de usuário, embora os dois sejam complementares.

> [!NOTE]
> Quando os usuários e grupos são provisionados ou desprovisionados, é recomendável reiniciar periodicamente o provisionamento para garantir que as associações de grupo sejam atualizadas corretamente. A realização de uma reinicialização forçará nosso serviço a reavaliar todos os grupos e atualizar as associações.  

> [!NOTE]
> Para saber mais sobre o ponto de extremidade do SCIM do Zscaler Private Access, veja [isto](https://www.zscaler.com/partners/microsoft).

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o ZPA (Zscaler Private Access) no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ZPA (Zscaler Private Access)** .

    ![O link do ZPA (Zscaler Private Access) na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira o valor **Ponto de Extremidade de Provedor de Serviços do SCIM** recuperado anteriormente em **URL do Locatário**. Insira o valor **Token de Portador** recuperado anteriormente no **Token Secreto**. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao ZPA (Zscaler Private Access). Se a conexão falhar, verifique se a sua conta do ZPA (Zscaler Private Access) tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o ZPA (Zscaler Private Access)** .

    ![Mapeamentos de Usuário do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário sincronizados do Azure AD com o ZPA (Zscaler Private Access) na seção **Mapeamentos de Atributo**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no ZPA (Zscaler Private Access) para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Usuário do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o ZPA (Zscaler Private Access)** .

    ![Mapeamentos de Grupo do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Examine os atributos de grupo sincronizados do Azure AD com o ZPA (Zscaler Private Access) na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no ZPA (Zscaler Private Access) para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Grupo do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o ZPA (Zscaler Private Access), altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para o ZPA (Zscaler Private Access) escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no ZPA (Zscaler Private Access).

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)