---
title: "Atributos sombra do serviço de sincronização do Azure AD Connect | Microsoft Docs"
description: "Descreve como os atributos sombra funcionam no serviço de sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 0b6a7f22d744480a40a878c979986cdd7667109c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Atributos sombra do serviço de sincronização do Azure AD Connect
A maioria dos atributos é representada da mesma maneira no Azure AD e no Active Directory local. Mas alguns atributos têm tratamentos especiais, e o valor do atributo no Azure AD pode ser diferente do que é sincronizado no Azure AD Connect.

## <a name="introducing-shadow-attributes"></a>Introdução aos atributos sombra
Alguns atributos têm duas representações no Azure AD. O valor local e um valor calculado são armazenados. Esses atributos adicionais são chamados de atributos sombra. Os dois atributos mais comuns nos quais é possível ver esse comportamento são **userPrincipalName** e **proxyAddress**. A alteração nos valores de atributo ocorre quando há valores nesses atributos que representam domínios não verificados. Mas o mecanismo de sincronização no Connect lê o valor do atributo sombra e, da perspectiva dele, o atributo foi confirmado pelo Azure AD.

Não é possível ver os atributos sombra usando o portal do Azure ou o PowerShell. Mas entender o conceito ajuda você a solucionar determinados cenários nos quais o atributo tem valores diferentes no local e na nuvem.

Para entender melhor o comportamento, veja este exemplo da Fabrikam:  
![Domínios](./media/active-directory-aadconnectsyncservice-shadow-attributes/domains.png)  
Eles têm vários sufixos UPN no Active Directory local, mas verificaram apenas um.

### <a name="userprincipalname"></a>userPrincipalName
Um usuário tem os seguintes valores de atributo em um domínio não verificado:

| Atributo | Valor |
| --- | --- |
| userPrincipalName local | lee.sperry@fabrikam.com |
| shadowUserPrincipalName do Azure AD | lee.sperry@fabrikam.com |
| userPrincipalName do Azure AD | lee.sperry@fabrikam.onmicrosoft.com |

O atributo userPrincipalName é o valor que você vê ao usar o PowerShell.

Como o valor do atributo real local é armazenado no Azure AD, quando você verifica o domínio fabrikam.com, o Azure AD atualiza o atributo userPrincipalName com o valor de shadowUserPrincipalName. Não é necessário sincronizar as alterações no Azure AD Connect para que esses valores sejam atualizados.

### <a name="proxyaddresses"></a>proxyAddresses
O mesmo processo para incluir somente os domínios verificados também ocorre para proxyAddresses, mas com alguma lógica adicional. A verificação de domínios verificados ocorre apenas para usuários de caixa de correio. Um usuário ou contato habilitado para email representa um usuário em outra organização do Exchange, e você pode adicionar quaisquer valores no proxyAddresses a esses objetos.

Para um usuário de caixa de correio, no local ou no Exchange Online, somente os valores de domínios verificados são exibidos. O resultado pode ser este:

| Atributo | Valor |
| --- | --- |
| proxyAddresses local | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| proxyAddresses do Exchange Online | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

Nesse caso, **smtp:abbie.spencer@fabrikam.com** foi removido, pois esse domínio não foi verificado. Mas o Exchange também adicionou **SIP:abbie.spencer@fabrikamonline.com**. A Fabrikam não usou o Lync/Skype local, mas o Azure AD e o Exchange Online se preparam para isso.

Essa lógica para proxyAddresses é conhecida como **ProxyCalc**. ProxyCalc é invocado a cada alteração em um usuário, quando:

- O usuário tiver recebido um plano de serviço que inclua o Exchange Online mesmo não tendo licença para o Exchange. Por exemplo, se o usuário receber a SKU do Office E3, mas só com o SharePoint Online. Isso será verdadeiro mesmo se sua caixa de correio ainda for local.
- O atributo msExchRecipientTypeDetails tem um valor.
- Você faz uma alteração no proxyAddresses ou no userPrincipalName.

ProxyCalc pode levar algum tempo para processar uma alteração em um usuário, e não é síncrono com o processo de exportação do Azure AD Connect.

> [!NOTE]
> A lógica do ProxyCalc tem alguns comportamentos adicionais para cenários avançados não documentados neste tópico. Este tópico é fornecido para você entender o comportamento, e não documentar toda a lógica interna.

### <a name="quarantined-attribute-values"></a>Valores de atributo em quarentena
Os atributos sombra também são usados quando há valores de atributo duplicados. Para saber mais, veja [resiliência de atributo duplicada](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Consulte também
* [Sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Azure Active Directory](active-directory-aadconnect.md).
