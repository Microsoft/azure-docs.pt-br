---
title: "Sincronização do Azure AD Connect: Noções Básicas sobre Usuários e Contatos | Microsoft Docs"
description: "Explica usuários e contatos na sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: markvi;andkjell
ms.openlocfilehash: 495e926a1975128707212ba571d47cfc7c9507b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-understanding-users-and-contacts"></a>Azure AD Connect Sync: noções básicas sobre usuários e contatos
Há vários motivos diferentes de por que existem várias florestas do Active Directory e várias topologias de implantação diferentes. Os modelos comuns incluem uma implantação do recurso em conta e florestas sincronizadas de GAL (Lista de Endereços Global) após uma fusão e aquisição. Mas mesmo que haja modelos puros, modelos híbridos são comuns também. A configuração padrão da sincronização do Azure AD Connect não assume nenhum modelo específico, mas dependendo de como a compatibilidade de usuário foi selecionada na guia de instalação, comportamentos diferentes podem ser observados.

Neste tópico, veremos como a configuração padrão se comporta em certas topologias. Veremos que a configuração e o Editor de regras de sincronização podem ser usados para examinar a configuração.

A configuração pressupõe algumas regras gerais:

* Independentemente da ordem em que importamos por meio dos Active Directories de origem, o resultado final seria sempre o mesmo.
* Uma conta ativa sempre também contribuirá com informações de logon, incluindo **userPrincipalName** e **sourceAnchor**.
* Uma conta desabilitada contribuirá userPrincipalName e sourceAnchor, a menos que uma caixa de correio vinculada, se não houver nenhuma conta ativa a ser localizada.
* Uma conta com uma caixa de correio vinculada nunca será usada para userPrincipalName e sourceAnchor. Pressupõe-se que uma conta ativa será encontrada posteriormente.
* Um objeto de contato pode ser provisionado no AD do Azure como um contato ou como um usuário. Você realmente não sabe até que todas as florestas do Active Directory de origem sejam processadas.

## <a name="contacts"></a>Contatos
Ter contatos que representam um usuário em uma floresta diferente é comum após uma fusão e aquisição em que uma solução GALSync está fazendo a ponte entre duas ou mais florestas do Exchange. O objeto de contato está sempre unindo o espaço do conector ao metaverso, usando o atributo de email. Se já houver um objeto de contato ou um objeto de usuário com o mesmo endereço de email, os objetos serão unidos. Isso é configurado na regra **Entrada do AD – Junção de Contato**. Há também uma regra denominada **Entrada no AD – Contato Comum** com um fluxo de atributos para o atributo de metaverso **sourceObjectType** com a constante **Contato**. Essa regra tem precedência muito baixa, portanto se nenhum objeto de usuário estiver associado ao mesmo objeto de metaverso, a regra Entrada do **AD – Usuário Comum** contribuirá com o valor Usuário para esse atributo. Com essa regra, esse atributo terá o valor Contato, se nenhum usuário tiver sido associado; e o valor Usuário se pelo menos um usuário tiver sido encontrado.

Para o provisionamento de um objeto no Azure AD, a regra **Saída para o AAD – Junção de Contato** criará um objeto de contato se o atributo **sourceObjectType** estiver definido como **Contato**. Se esse atributo estiver definido como **Usuário**, a regra **Saída para o AAD – Junção de Usuário** criará um objeto de usuário, em vez disso.
É possível que um objeto seja promovido de Contato para Usuário quando mais Active Directories de origem forem importados e sincronizados.

Por exemplo, em uma topologia de GALSync encontraremos objetos de contato para todos da segunda floresta quando importarmos a primeira floresta. Isso testará novos objetos de contato no conector AAD. Quando mais tarde, importarmos e sincronizarmos a segunda floresta, encontraremos os verdadeiros usuários e os uniremos aos objetos de metaverso existentes. Em seguida, excluiremos os objetos de contato no AAD e criaremos um novo objeto de usuário, em vez disso.

Se você tiver uma topologia em que usuários são representados como contatos, certifique-se de selecionar para combinar usuários no atributo de email no guia de instalação. Se você selecionar outra opção, terá uma configuração dependente de pedido. Objetos de contato sempre ingressarão no atributo de email, mas objetos de usuário só ingressarão no atributo de email se essa opção foi selecionada no guia de instalação. Você pode acabar com dois objetos diferentes no metaverso, com o mesmo atributo de email, se o objeto de contato tiver sido importado antes do objeto de usuário. Durante a exportação para o AD do Azure, um erro será gerado. Esse comportamento ocorre por design e indica dados incorretos ou que a topologia não foi identificada corretamente durante a instalação.

## <a name="disabled-accounts"></a>Contas desabilitadas
Contas desabilitadas são sincronizadas também ao AD do Azure. Contas desabilitadas são comuns para representar recursos no Exchange, por exemplo, salas de conferência. A exceção são usuários com uma caixa de correio vinculada. Conforme mencionado anteriormente, eles nunca provisionarão uma conta no AD do Azure.

O pressuposto é que, se uma conta de usuário desabilitada for encontrada, não encontraremos outra conta ativa posteriormente e o objeto será provisionado no AD do Azure com o userPrincipalName e sourceAnchor encontrados. Caso outra conta ativa seja associada ao mesmo objeto de metaverso, seu userPrincipalName e sourceAnchor serão usados.

## <a name="changing-sourceanchor"></a>Alterando o sourceAnchor
Quando um objeto é exportado para o AD do Azure, não é mais permitido alterar o sourceAnchor. Quando o objeto é exportado do atributo de metaverso **cloudSourceAnchor**, é definido com o valor **sourceAnchor** aceito pelo Azure AD. Se o **sourceAnchor** for alterado e não corresponder a **cloudSourceAnchor**, a regra **Saída para o AAD – Junção de Usuário** gerará o erro **atributo sourceAnchor foi alterado**. Nesse caso, a configuração ou os dados ou devem ser corrigidos para que o mesmo sourceAnchor esteja presente no metaverso novamente antes que o objeto possa ser sincronizado outra vez.

## <a name="additional-resources"></a>Recursos adicionais
* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

