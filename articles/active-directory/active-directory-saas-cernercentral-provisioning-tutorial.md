---
title: "Tutorial: Configurar o Cerner Central para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs"
description: "Saiba como configurar o Azure Active Directory para provisionar automaticamente usuários para uma lista no Cerner Central."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: asmalser-msft
ms.openlocfilehash: 84613b7f8d7bd031d492a62da0bc53be96ac45a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-cerner-central-for-automatic-user-provisioning"></a>Tutorial: Configurar o Cerner Central para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser executadas no Cerner Central e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para uma lista de usuários no Cerner Central. 


## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory
*   Um locatário do Cerner Central 

> [!NOTE]
> O Azure Active Directory integra-se com o Cerner Central usando o protocolo [SCIM](http://www.simplecloud.info/).

## <a name="assigning-users-to-cerner-central"></a>Atribuir usuários ao Cerner Central

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao Cerner Central. Depois de decidir, atribua esses usuários ao Cerner Central seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Dicas importantes para atribuir usuários ao Cerner Central

*   Recomendamos a atribuição de um único usuário do Azure AD ao Cerner Central para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Depois que o teste inicial for concluído para um único usuário, o Cerner Central recomenda atribuir toda a lista de usuários que deve acessar qualquer solução Cerner (não apenas Cerner Central) para que esta seja provisionada para a lista do usuário do Cerner.  Outras soluções Cerner aproveitam essa lista de usuários na lista do usuário.

*   Ao atribuir um usuário ao Cerner Central, selecione a função **Usuário** na caixa de diálogo de atribuição. Os usuários com a função de "Acesso Padrão" são excluídos do provisionamento.


## <a name="configuring-user-provisioning-to-cerner-central"></a>Configurar o provisionamento de usuários no Cerner Central

Esta seção ensina como conectar o seu Azure AD à Lista do Usuário do Cerner Central à API de provisionamento de conta de usuário do SCIM do Cerner Central e também pela configuração do serviço de provisionamento, a fim de criar, atualizar e desabilitar contas de usuário atribuídas no Cerner Central com base na atribuição de usuário e de grupo do Azure AD.

> [!TIP]
> Você também pode optar por habilitar o SSO baseado em SAML para o Cerner Central seguindo as instruções fornecidas no Portal do Azure (https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares. Para obter mais informações, consulte o [Tutorial de logon único do Cerner Central](active-directory-saas-cernercentral-tutorial.md).


### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Cerner Central no Azure AD:


Para provisionar contas de usuário no Cerner Central, você precisará solicitar uma conta de sistema do Cerner Central do Cerner e gerar um token de portador OAuth que o Azure AD pode usar para se conectar ao ponto de extremidade SCIM do Cerner. Também recomendamos que a integração seja executada em um ambiente de área restrita do Cerner antes da implantação em produção.

1.  A primeira etapa é garantir que as pessoas que gerenciam a integração do Cerner e do Azure AD tenham uma conta no CernerCare, que é exigida para acessar a documentação necessária para concluir as instruções. Se for necessário, use as URLs abaixo para criar contas do CernerCare em cada ambiente aplicável.

   * Área restrita: https://sandboxcernercare.com/accounts/create

   * Produção:  https://cernercare.com/accounts/create  

2.  Em seguida, será necessário criar uma conta do sistema para o Azure AD. Use as instruções a seguir para solicitar uma Conta do Sistema para seus ambientes de produção e de área restrita.

   * Instruções:  https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Área restrita: https://sandboxcernercentral.com/system-accounts/

   * Produção:  https://cernercentral.com/system-accounts/

3.  Em seguida, gere um token de portador OAuth para cada uma de suas contas do sistema. Para fazer isso, siga as instruções abaixo.

   * Instruções: https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Área restrita: https://sandboxcernercentral.com/system-accounts/

   * Produção:  https://cernercentral.com/system-accounts/

4. Por fim, você precisa adquirir as IDs de Realm da Lista do Usuário para ambientes de área restrita e de produção Cerner para concluir a configuração. Para saber mais sobre como adquirir isso, consulte: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM. 

5. Agora você pode configurar o Azure AD para provisionar contas de usuário para o Cerner. Entre no [Portal do Azure](https://portal.azure.com) e navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

6. Se você já tiver configurado o Cerner Central para logon único, procure sua instância do Cerner Central usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procure **Cerner Central** na galeria de aplicativos. Selecione o Cerner Central nos resultados da pesquisa e adicione-o à lista de aplicativos.

7.  Selecione sua instância do Cerner Central e selecione a guia **Provisionamento**.

8.  Defina o **Modo de Provisionamento** como **Automático**.

   ![Provisionamento do Cerner Central](./media/active-directory-saas-cernercentral-provisioning-tutorial/Cerner.PNG)

9.  Preencha os campos a seguir em **Credenciais de Administrador**:

   * No campo **URL do Locatário**, insira uma URL no formato abaixo, substituindo "User-Roster-Realm-ID" pela ID do realm que você adquiriu na etapa 4.

> Área restrita: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

> Produção: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * No campo **Segredo do Token**, insira o token de portador OAuth gerado na etapa 3 e clique em **Testar Conexão**.

   * Você verá uma notificação de êxito no lado do superior direito do seu Portal.

10. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção abaixo.

11. Clique em **Salvar**. 

12. Na seção **Mapeamentos de Atributo**, examine os atributos de usuário e grupo que serão sincronizados do Azure AD para o Cerner Central. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário e grupos no Cerner Central para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

13. Para habilitar o serviço de provisionamento do Azure AD para o Cerner Central, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**

14. Clique em **Salvar**. 

Isso inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao Cerner Central na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 20 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo Cerner Central.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).

## <a name="additional-resources"></a>Recursos adicionais

* [Cerner Central: Publicação de dados de identidade usando o Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Tutorial: Configurar o Cerner Central para logon único com o Azure Active Directory](active-directory-saas-cernercentral-tutorial.md)
* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](active-directory-enterprise-apps-manage-provisioning.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Próximas etapas
* [Saiba como fazer revisão de logs e obter relatórios sobre a atividade de provisionamento](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).
