---
title: Personalizar mapeamentos de atributo do Azure AD | Microsoft Docs
description: "Saiba quais são os mapeamentos de atributo para aplicativos SaaS no Active Directory do Azure e como você pode modificá-los para atender às necessidades de negócios."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ca2fdc9c68ea0030d938eeaebd57aafa0e2790f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalizar os mapeamentos de atributos de provisionamento de usuário para aplicativos SaaS no Azure Active Directory
O AD do Microsoft Azure dá suporte para provisionamento de usuário para aplicativos SaaS de terceiros, como Salesforce, Google Apps e outros. Se você tiver provisionamento de usuário para um aplicativo SaaS de terceiro habilitado, o Portal de Gerenciamento controlará seus valores de atributo na forma de uma configuração chamada "mapeamento de atributo".

Há um conjunto predefinido de mapeamentos de atributo entre objetos de usuário do AD do Azure e objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos, como grupos ou contatos. <br> 
 Você pode personalizar mapeamentos de atributo padrão de acordo com suas necessidades comerciais. Isso significa que você pode alterar ou excluir mapeamentos de atributo existentes ou criar novos mapeamentos de atributo.

No portal do Azure AD, você pode acessar esse recurso clicando em uma configuração de **Mapeamentos** em **Provisionamento** na seção **Gerenciar** de um **Aplicativo Enterprise**.


![Salesforce][5] 

Clicar em uma configuração de **Mapeamentos** abre a folha **Mapeamento de Atributo** relacionada.  
Há mapeamentos de atributo que são exigidos por um aplicativo SaaS para funcionar corretamente. Para os atributos necessários, o recurso **Excluir** não está disponível.


![Salesforce][6]  

No exemplo acima, você pode ver que o atributo **Username** de um objeto gerenciado no Salesforce é preenchido com o valor **userPrincipalName** do Objeto do Azure Active Directory vinculado.

Você pode personalizar os **Mapeamentos de Atributo** existentes clicando em um mapeamento. Isso abre a folha **Editar Atributo**.

![Salesforce][7]  


  

## <a name="understanding-attribute-mapping-types"></a>Noções básicas sobre tipos de mapeamento de atributo
Com mapeamentos de atributo, você controla como os atributos são preenchidos em um aplicativo SaaS de terceiro. Há quatro tipos diferentes de mapeamento com suporte:

* **Direto** – o atributo de destino é preenchido com o valor de um atributo do objeto vinculado no AD do Azure.
* **Constante** – o atributo de destino é preenchido com uma cadeia de caracteres especificada por você.
* **Expressão** – o atributo de destino é preenchido com base no resultado de uma expressão de script. 
  Para saber mais, consulte [Escrever expressões para mapeamentos de atributo no Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).
* **Nenhum** – o atributo de destino é deixado inalterado. No entanto, se o atributo de destino estiver vazio, ele será preenchido com o valor padrão que você especificar.

Além desses quatro tipos de mapeamentos de atributo básicos, os mapeamentos de atributo personalizados dão suporte ao conceito de uma atribuição de valor **padrão** opcional. A atribuição do valor padrão assegura que um atributo de destino seja preenchido com um valor, se não houver um valor no AD do Azure nem no objeto de destino. A configuração mais comum é deixar isso em branco.


## <a name="understanding-attribute-mapping-properties"></a>Noções básicas de propriedades de mapeamento de atributo

Na seção anterior, você já foi apresentado à propriedade de tipo de mapeamento de atributo.
Além dessa propriedade, mapeamentos de atributo também dão suporte aos seguintes atributos:

- **Atributo de origem** – o atributo de usuário do sistema de origem (por exemplo, Azure Active Directory).
- **Atributo de destino** – o atributo do usuário no sistema de destino (por exemplo: ServiceNow).
- **Combinar objetos utilizando esse atributo** – se esse mapeamento deve ou não ser utilizado para identificar com exclusividade usuários entre os sistemas de origem e destino. Normalmente, isso é definido no atributo userPrincipalName ou email no Azure AD, que costuma ser mapeado para um campo de nome de usuário em um aplicativo de destino.
- **Precedência de correspondência** – vários atributos de correspondência podem ser definidos. Quando houver múltiplos, os atributos serão avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, mais nenhum atributo correspondente será avaliado.
- **Aplicar esse mapeamento**
    - **Sempre** – aplicar esse mapeamento nas ações de criação e atualização do usuário
    - **Somente durante a criação** – aplicar esse mapeamento somente nas ações de criação de usuário


## <a name="what-you-should-know"></a>O que você deve saber

O Microsoft Azure AD fornece uma implementação muito eficiente de um processo de sincronização. Em um ambiente inicializado, apenas os objetos que precisam de atualização são processados durante um ciclo de sincronização. A atualização de mapeamentos de atributo tem impacto no desempenho de um ciclo de sincronização. Uma atualização a uma configuração de mapeamento de atributo exige que todos os objetos gerenciados sejam reavaliados. É uma prática recomendada manter o número de alterações consecutivas aos seus mapeamentos de atributos no mínimo.

## <a name="next-steps"></a>Próximas etapas

* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Automatizar o provisionamento/desprovisionamento de usuários para aplicativos SaaS](active-directory-saas-app-provisioning.md)
* [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de escopo para provisionamento de usuários](active-directory-saas-scoping-filters.md)
* [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md)
* [Notificações de provisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png

