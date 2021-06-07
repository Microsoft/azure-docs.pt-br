---
title: 'Tutorial: Configurar o Infor CloudSuite para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Infor CloudSuite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 8fdd2c8a326fbdc68d1aec65377f4c465c5ee4c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353894"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Tutorial: Configurar o Infor CloudSuite para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Infor CloudSuite e no Azure AD (Azure Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o Infor CloudSuite.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Infor CloudSuite](https://www.infor.com/products/infor-os)
* Uma conta de usuário no Infor CloudSuite com permissões de Administrador.

## <a name="assigning-users-to-infor-cloudsuite"></a>Como atribuir usuários ao Infor CloudSuite

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Infor CloudSuite. Depois de decidir, você poderá atribuir esses usuários e/ou grupos ao Infor CloudSuite seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Dicas importantes para atribuir usuários ao Infor CloudSuite

* É recomendável que somente um usuário do Azure AD seja atribuído ao CloudSuite para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao CloudSuite, você precisa selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Configurar o Infor CloudSuite para provisionamento

1. Entre em seu [Console de Administração do Infor CloudSuite](https://www.infor.com/customer-center). Clique no ícone de usuário e navegue até o **gerenciamento de usuário**.

    ![Console de Administração do Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Clique no ícone de menu no canto superior esquerdo da tela. Clique em **Gerenciar**.

    ![Adicionar SCIM do Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Navegue até **Contas do SCIM**.

    ![Conta do SCIM do Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Adicione um usuário administrador clicando no ícone de adição. Forneça uma **Senha do SCIM** e digite a mesma senha em **Confirmar Senha**. Clique no ícone de pasta para salvar a senha. Você verá um **Identificador de Usuário** gerado para o usuário administrador.

    ![Usuário administrador do Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Senha do Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/password.png)

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/identifier.png" alt-text="Captura de tela do console de administração do Infor CloudSuite mostrando uma linha de tabela realçada. Essa linha contém um identificador de usuário, senhas e um carimbo de data/hora." border="false":::

5. Para gerar o token de portador, copie o **Identificador de Usuário** e a **Senha do SCIM**. Cole-os no bloco de notas++ separados por dois-pontos. Codifique o valor da cadeia de caracteres navegando até **Plug-ins > Ferramentas MIME > Codificação Basic64**. 

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/token.png" alt-text="Captura de tela de um documento do Bloco de Notas++. No menu Plug-ins, as ferramentas MIME estão realçadas. No menu de ferramentas MIME, a codificação Base64 está realçada." border="false":::

3.  Copie o token de portador. Esse valor será inserido no campo Token Secreto na guia Provisionamento do aplicativo Infor CloudSuite no portal do Azure.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Adicionar o Infor CloudSuite da galeria

Antes de configurar o CloudSuite para o provisionamento automático de usuário com o Azure AD, é necessário adicioná-lo da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o Infor CloudSuite da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Infor CloudSuite**, selecione **Infor CloudSuite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Infor CloudSuite na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Como configurar o provisionamento automático de usuário para o Infor CloudSuite 

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Infor CloudSuite com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Infor CloudSuite seguindo as instruções fornecidas no [tutorial de logon único do Infor CloudSuite](./infor-cloud-suite-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

> [!NOTE]
> Para saber mais sobre o ponto de extremidade do SCIM do Infor CloudSuite, veja [isto](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Infor CloudSuite no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Infor CloudSuite**.

    ![O Infor CloudSuite na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` em **URL do Locatário**. Insira o valor do token de portador recuperado anteriormente no **Token Secreto**. Clique em **Testar Conexão** para verificar se o Azure AD pode se conectar ao Infor CloudSuite. Se a conexão falhar, verifique se a sua conta do Infor CloudSuite tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Infor CloudSuite**.

    ![Mapeamentos de Usuário do Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário sincronizados do Azure AD com o Infor CloudSuite na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Infor CloudSuite para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Usuário do Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Infor CloudSuite**.

    ![Mapeamentos de Grupo do Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Examine os atributos de grupo sincronizados do Azure AD com o Infor CloudSuite na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no Infor CloudSuite para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Grupo do Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para Infor CloudSuite, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para o Infor CloudSuite escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório das atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Infor CloudSuite.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)