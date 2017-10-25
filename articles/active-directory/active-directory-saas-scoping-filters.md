---
title: Provisionar aplicativos com filtros de escopo | Microsoft Docs
description: "Saiba como usar filtros de escopo para impedir que objetos em aplicativos que dão suporte a provisionamento automatizado do usuário sejam provisionados caso um objeto não atenda às suas necessidades de negócios."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: bcfbda74-e4d4-4859-83bc-06b104df3918
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9639235e12de15d4eec2cf887156ac922e63e0ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Provisionamento de aplicativo com base em atributo com filtros de escopo
O objetivo deste artigo é explicar como usar filtros de escopo para definir regras baseadas em atributo que determinam quais usuários serão provisionados a um aplicativo.

## <a name="scoping-filter-use-cases"></a>Casos de uso do filtro de escopo

Um filtro de escopo permite que o serviço de provisionamento do Azure AD (Azure Active Directory) inclua ou exclua qualquer usuário que tenha um atributo que corresponda a um valor específico. Por exemplo, ao provisionar usuários do Azure AD para um aplicativo SaaS utilizado por uma equipe de vendas, você poderá especificar que apenas os usuários com um atributo "Departamento" de "Vendas" deverá estar no escopo para provisionamento.

Os filtros de escopo podem ser utilizados de modo diferente, dependendo do tipo de conector de provisionamento:

* **Provisionamento de saída do Azure AD para aplicativos SaaS**. Quando o Azure AD é o sistema de origem, [atribuições de usuário e grupo](active-directory-coreapps-assign-user-azure-portal.md) são o método mais comum para determinar quais usuários estão no escopo para provisionamento. Essas atribuições também são utilizadas para habilitar o logon único e fornecer um método exclusivo para gerenciar o acesso e o provisionamento. Os filtros de escopo podem ser utilizados opcionalmente, além de atribuições ou, em vez de eles, para filtrar usuários com base em valores de atributos.

    >[!TIP]
    > É possível desabilitar o provisionamento com base em atribuições para um aplicativo empresarial alterando as configurações no menu [Escopo](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) nas configurações de provisionamento para **Sincronizar todos os usuários e grupos**. Usar esta opção mais filtros de escopo baseados em atributos oferece um desempenho mais rápido que o uso de atribuições baseadas em grupo.  

* **Provisionamento de entrada de aplicativos HCM para o Azure AD e o Active Directory**. Quando um [aplicativo HCM como o Workday](active-directory-saas-workday-tutorial.md) for o sistema de origem, os filtros de escopo serão o método principal para determinar quais usuários deverão ser provisionados do aplicativo HCM para o Active Directory ou o Azure AD.

Por padrão, os conectores de provisionamento do Azure AD não possuem filtros de escopo baseados em atributos configurados. 

## <a name="scoping-filter-construction"></a>Construção do filtro de escopo

Um filtro de escopo consiste em uma ou mais *cláusulas*. As cláusulas determinam quais usuários poderão passar pelo filtro de escopo avaliando os atributos de cada usuário. Por exemplo, é possível ter uma cláusula que exija que o atributo "Estado" do usuário seja igual a "Nova York", então somente usuários de Nova York serão provisionados para o aplicativo. 

Uma cláusula exclusiva define uma condição única para um valor de atributo único. Se várias cláusulas forem criadas em um único filtro de escopo, elas serão avaliadas em conjunto utilizando a lógica "AND". Isso significa que todas as cláusulas devem ser avaliadas como "verdadeiras" para que um usuário ser provisionado.

Por fim, vários filtros de escopo podem ser criados para um único aplicativo. Se houver múltiplos filtros de escopo, eles serão avaliados em conjunto utilizando a lógica "OR". Isso significa que, se todas as cláusulas em qualquer um dos filtros de escopo configurados forem avaliadas como "true", o usuário será provisionado.

Cada usuário ou grupo processado pelo serviço de provisionamento do Azure AD sempre é avaliado individualmente em relação a cada filtro de escopo.

Como um exemplo, considere o seguinte filtro de escopo:

![Filtro de escopo](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

De acordo com esse filtro de escopo, os usuários devem atender aos seguintes critérios para serem provisionados:

* Eles devem estar em Nova York.
* Eles devem trabalhar no departamento de engenharia.
* O número da ID do funcionário da empresa deve estar entre 1.000.000 e 2.000.000.
* O cargo não deve ser nulo ou nem estar vazio.

## <a name="create-scoping-filters"></a>Criar filtros de escopo
Os filtros de escopo são configurados como parte dos mapeamentos de atributos para cada conector de provisionamento de usuário do Azure AD. O procedimento a seguir presume que você já tenha configurado o provisionamento automático para [um dos aplicativos com suporte](active-directory-saas-tutorial-list.md) e está adicionando um filtro de escopo a ele.

### <a name="create-a-scoping-filter"></a>Criar um filtro de escopo
1. No [Portal do Azure](https://portal.azure.com), vá para a seção **Azure Active Directory** > **Aplicativos Empresariais** > **Todos os aplicativos**.

2. Selecione o aplicativo para o qual você configurou provisionamento automático: por exemplo, "ServiceNow".

3. Selecione a guia **Provisionamento**.

4. Na seção **Mapeamentos**, selecione o mapeamento para o qual você deseja configurar um filtro de escopo: por exemplo, "Sincronizar Usuários do Azure Active Directory ao ServiceNow".

5. Selecione o menu **Escopo do objeto de origem**.

6. Selecione **Adicionar filtro de escopo**.

7. Defina uma cláusula selecionando um **Nome do Atributo** de origem, um **Operador** e um **Valor do Atributo** para corresponder. Há suporte para os seguintes operadores:

   a. **EQUALS**. A cláusula retornará "true" se o atributo avaliado corresponder exatamente ao valor da cadeia de caracteres de entrada (diferencia maiúsculas de minúsculas).

   b. **NOT EQUALS**. A cláusula retornará "true" se o atributo avaliado não corresponder ao valor da cadeia de caracteres de entrada (diferencia maiúsculas de minúsculas).

   c. **IS TRUE**. A cláusula retornará "true" se o atributo avaliado contiver um valor booliano de true.

   d. **IS FALSE**. A cláusula retornará "true" se o atributo avaliado contiver um valor booliano de false.

   e. **IS NULL**. A cláusula retornará "true" se o atributo avaliado estiver vazio.

   f. **IS NOT NULL**. A cláusula retornará "true" se o atributo avaliado não estiver vazio.

   g. **REGEX MATCH**. A cláusula retornará "true" se o atributo avaliado corresponder a um padrão de expressão regular. Por Exemplo: ([1-9][0-9]) corresponde a qualquer número entre 10 e 99.

   h. **NOT REGEX MATCH**. A cláusula retornará "true" se o atributo avaliado não corresponder a um padrão de expressão regular.

8. Selecione **Adicionar nova cláusula de escopo**.

9. Opcionalmente, repita as etapas 7 e 8 para adicionar mais cláusulas de escopo.

10. Em **Título do Filtro de Escopo**, adicione um nome para o filtro de escopo.

11. Selecione **OK**.

12. Selecione **OK** novamente na tela **Filtros de Escopo**. Opcionalmente, repita as etapas 6 a 11 para adicionar outro filtro de escopo.

13. Selecione **Salvar** na tela de **Mapeamento de Atributo**. 

>[!IMPORTANT] 
> Salvar um novo filtro de escopo dispara uma nova sincronização completa para o aplicativo, onde todos os usuários no sistema de origem são avaliados novamente em relação ao novo filtro de escopo. Se um usuário no aplicativo estava anteriormente no escopo de provisionamento, mas sair do escopo, sua conta será desabilitada ou desprovisionada no aplicativo.


## <a name="related-articles"></a>Artigos relacionados
* [Índice de artigos para gerenciamento de aplicativos no Azure Active Directory](active-directory-apps-index.md)
* [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS](active-directory-saas-app-provisioning.md)
* [Personalizar mapeamentos de atributos para provisionamento do usuário](active-directory-saas-customizing-attribute-mappings.md)
* [Escrever expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Notificações de provisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
* [Usar o SCIM para habilitar o provisionamento automático de usuários e grupos do Azure Active Directory para aplicativos](active-directory-scim-provisioning.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)

