---
title: 'Tutorial: configurar o Salesforce para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Conheça as etapas necessárias para executar no Salesforce e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Salesforce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: 2a4d3f3f9465b8813cdf6ee26760d819d73a08c1
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94353097"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Tutorial: configurar o Salesforce para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser executadas no Salesforce e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Salesforce.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure Active Directory
* Locatário de Salesforce.com

> [!IMPORTANT]
> Se você estiver usando uma conta Salesforce.com de avaliação, não será possível configurar o provisionamento automatizado de usuários. Contas de avaliação não têm o acesso à API necessária habilitado até que sejam compradas. Você pode contornar essa limitação usando uma [conta de desenvolvedor gratuita](https://developer.salesforce.com/signup) para concluir este tutorial.

Se você estiver usando um ambiente de área restrita do Salesforce, consulte o [Tutorial de integração de área restrita do Salesforce](./salesforce-sandbox-tutorial.md).

## <a name="assigning-users-to-salesforce"></a>Como atribuir usuários ao Salesforce

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários ou grupos no Azure AD precisam de acesso ao aplicativo Salesforce. Depois de decidir, você pode atribuir esses usuários ao aplicativo Salesforce seguindo as instruções em [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Dicas importantes para atribuir usuários ao Salesforce

* Recomendamos a atribuição de um único usuário do Azure AD ao Salesforce para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Salesforce, você deve selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento

    > [!NOTE]
    > Este aplicativo importa perfis do Salesforce como parte do processo de provisionamento, que o cliente deve desejar selecionar durante a atribuição de usuários no Azure AD. Observe que os perfis que são importados do Salesforce aparecem como Funções no Azure AD.

## <a name="enable-automated-user-provisioning"></a>Habilitar o provisionamento automatizado de usuários

Esta seção orienta você pela conexão do Azure AD com a [API de provisionamento de conta de usuário do Salesforce-v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)e pela configuração do serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no Salesforce com base na atribuição de usuário e de grupo no Azure AD.

> [!Tip]
> Você também pode optar por habilitar o Logon Único baseado em SAML para o Salesforce, seguindo as instruções fornecidas no [Portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o provisionamento automático de conta de usuário

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Salesforce.

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2. Se você já tiver configurado o Salesforce para logon único, pesquise sua instância do Salesforce usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **Salesforce** na galeria de aplicativos. Selecione o Salesforce nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione sua instância do Salesforce e selecione a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela mostra a página de provisionamento do Salesforce, com o modo de provisionamento definido como automático e outros valores que você pode definir.](./media/salesforce-provisioning-tutorial/provisioning.png)

5. Na seção **Credenciais de Administrador** , forneça as seguintes definições de configuração:

    a. Na caixa de texto **Nome de Usuário Administrador** , digite o nome da conta do Salesforce com o perfil **Administrador do Sistema** no Salesforce.com atribuído.

    b. Na caixa de texto **Senha do Administrador** , digite a senha dessa conta.

6. Para obter o token de segurança do Salesforce, abra uma nova guia e entre na mesma conta de administrador do Salesforce. No canto superior direito da página, clique em seu nome e em **Configurações**.

    ![Captura de tela mostra o link configurações selecionado.](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Habilitar o provisionamento automático de usuários.")

7. No painel de navegação esquerdo, clique em **Minhas Informações Pessoais** para expandir a seção correspondente e clique em **Redefinir Meu Token de Segurança**.
  
    ![A captura de tela mostra redefinir meu token de segurança selecionado de minhas informações pessoais.](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Habilitar o provisionamento automático de usuários.")

8. Na página **Redefinir Token de Segurança** , clique no botão **Redefinir Token de Segurança**.

    ![Captura de tela mostra a página do token de segurança REST, com texto explicativo e a opção para redefinir o token de segurança](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Habilitar o provisionamento automático de usuários.")

9. Marque a caixa de entrada de email associada a essa conta de administrador. Procure um email do Salesforce.com que contenha o novo token de segurança.

10. Copie o token, vá até a janela do Azure AD e cole-o no campo **Token Secreto**.

11. A **URL do locatário** deve ser inserida se a instância do Salesforce está na Salesforce Government Cloud. Caso contrário, ela é opcional. Insira a URL do locatário usando o formato "https:// \<your-instance\> . My.Salesforce.com", substituindo \<your-instance\> pelo nome da sua instância do Salesforce.

12. No Portal do Azure, clique em **Testar Conexão** para garantir que o Azure AD possa se conectar ao seu aplicativo Salesforce.

13. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção abaixo.

14. Clique em **Salvar.**  

15. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o Salesforce.**

16. Na seção **Mapeamentos de Atributo** , revise os atributos de usuário que serão sincronizados do Azure AD para o Salesforce. Observe que os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder as contas de usuário no Salesforce para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

17. Para habilitar o serviço de provisionamento do Azure AD para o Salesforce, altere o **Status de Provisionamento** para **Ativado** na seção Configurações

18. Clique em **Salvar.**

> [!NOTE]
> Depois que os usuários são provisionados no aplicativo Salesforce, o administrador precisa definir as configurações específicas de idioma para eles. Consulte este artigo para obter mais detalhes sobre [a](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) configuração de idioma.

Isso iniciará a sincronização inicial de todos os usuários e/ou grupos atribuídos ao Salesforce na seção Usuários e Grupos. Observe que a sincronização inicial leva mais tempo do que as sincronizações posteriores, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo Salesforce.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="common-issues"></a>Problemas comuns
* Se você estiver tendo problemas para autorizar o acesso ao Salesforce, verifique o seguinte:
    * As credenciais usadas têm acesso de administrador ao Salesforce.
    * A versão do Salesforce que você está usando dá suporte a Acesso via Web (por exemplo, Developer, Enterprise, sandbox e edições ilimitadas do Salesforce.)
    * O acesso à API Web está habilitado para o usuário.
* O serviço de provisionamento do Azure AD dá suporte à linguagem de provisionamento, à localidade e ao fuso horário de um usuário. Esses atributos estão nos mapeamentos de atributo padrão, mas não têm um atributo de origem padrão. Verifique se você selecionou o atributo de origem padrão e se o atributo de origem está no formato esperado pelo SalesForce. Por exemplo, localeSidKey para inglês (Estados Unidos) é en_US. Examine as diretrizes fornecidas [aqui](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) para determinar o formato localeSidKey apropriado. Os formatos languageLocaleKey podem ser encontrados [aqui](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5). Além de garantir que o formato esteja correto, talvez seja necessário garantir que o idioma esteja habilitado para seus usuários, conforme descrito [aqui](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5). 
* **SalesforceLicenseLimitExceeded:** Não foi possível criar o usuário no aplicativo de destino porque não há licenças disponíveis para esse usuário. Adquira licenças adicionais para o aplicativo de destino ou examine as atribuições de usuário e a configuração de mapeamento de atributo para garantir que os usuários corretos sejam atribuídos com os atributos corretos.
* **SalesforceDuplicateUserName:** O usuário não pode ser provisionado porque tem um ' nome de usuário ' Salesforce.com que está duplicado em outro locatário Salesforce.com.No Salesforce.com, os valores para o atributo ' username ' devem ser exclusivos em todos os locatários do Salesforce.com.Por padrão, o userPrincipalName de um usuário em Azure Active Directory se torna seu ' username ' em Salesforce.com.Você tem duas opções.Uma opção é localizar e renomear o usuário com o ' nome de usuário ' duplicado no outro locatário do Salesforce.com, se você administrar esse outro locatário também.A outra opção é remover o acesso do usuário Azure Active Directory para o locatário Salesforce.com com o qual seu diretório está integrado. Tentaremos novamente essa operação na próxima tentativa de sincronização. 
* **SalesforceRequiredFieldMissing:** O Salesforce requer que determinados atributos estejam presentes no usuário para criar ou atualizar o usuário com êxito. Este usuário não tem um dos atributos necessários. Certifique-se de que atributos como email e alias sejam preenchidos em todos os usuários que você deseja que sejam provisionados no Salesforce. Você pode definir o escopo de usuários que não têm esses atributos usando [filtros de escopo baseados em atributo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 
* O mapeamento de atributo padrão para provisionamento para o Salesforce inclui a expressão SingleAppRoleAssignments para mapear appRoleAssignments no Azure AD para ProfileName no Salesforce. Verifique se os usuários não têm várias atribuições de função de aplicativo no Azure AD, pois o mapeamento de atributo só dá suporte ao provisionamento de uma função. 
* O Salesforce exige que as atualizações de email sejam aprovadas manualmente antes de serem alteradas. Como resultado, você poderá ver várias entradas nos logs de provisionamento para atualizar o email do usuário (até que a alteração de email tenha sido aprovada).


## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar logon único](./salesforce-tutorial.md)