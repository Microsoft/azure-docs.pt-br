---
title: Impedir a aceleração automática de entrada no Azure AD usando a política de descoberta de realm inicial
description: Saiba como impedir a aceleração de domain_hint para o IDPs federado.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2021
ms.author: hirsin
ms.openlocfilehash: 67cb1003e139a085d45d01617cd44647bad420f5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692973"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>Desabilitar a aceleração automática para um IDP federado durante a entrada do usuário com a política de descoberta de realm inicial

A HRD ( [Home Realm Discovery Policy](https://docs.microsoft.com/graph/api/resources/homeRealmDiscoveryPolicy) ) oferece aos administradores várias maneiras de controlar como e onde seus usuários se autenticam. A `domainHintPolicy` seção da política HRD é usada para ajudar a migrar usuários federados para credenciais gerenciadas na nuvem, como [Fido](../authentication/howto-authentication-passwordless-security-key.md), garantindo que eles sempre visitem a página de entrada do Azure AD e não sejam acelerados automaticamente para um IDP federado devido a dicas de domínio.

Essa política é necessária em situações em que os aplicativos de um administrador não podem controlar nem atualizar dicas de domínio de adição durante a entrada.  Por exemplo, `outlook.com/contoso.com` o envia o usuário para uma página de logon com o `&domain_hint=contoso.com` parâmetro acrescentado, a fim de acelerar automaticamente o usuário diretamente para o IDP federado para o `contoso.com` domínio. Os usuários com credenciais gerenciadas enviadas para um IDP federado não podem entrar usando suas credenciais gerenciadas, reduzindo a segurança e os usuários frustrantes com experiências de entrada aleatórias. Os administradores que implantam credenciais gerenciadas [também devem configurar essa política](#suggested-use-within-a-tenant) para garantir que os usuários sempre possam usar suas credenciais gerenciadas.

## <a name="domainhintpolicy-details"></a>Detalhes do DomainHintPolicy

A seção DomainHintPolicy da política HRD é um objeto JSON, que permite que um administrador cancele determinados domínios e aplicativos do uso de dicas de domínio.  Funcionalmente, isso informa a página de entrada do Azure AD para se comportar como se um `domain_hint` parâmetro na solicitação de logon não estivesse presente.

### <a name="the-respect-and-ignore-policy-sections"></a>As seções respeitar e ignorar política

|Seção | Significado | Valores |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Se essa dica de domínio for enviada na solicitação, ignore-a. |Matriz de endereços de domínio (por exemplo `contoso.com` ). Também dá suporte a `all_domains`|
|`RespectDomainHintForDomains`| Se essa dica de domínio for enviada na solicitação, respeite-a mesmo se `IgnoreDomainHintForApps` indicar que o aplicativo na solicitação não deve acelerar automaticamente. Isso é usado para reduzir a distribuição de dicas de domínio de substituição em sua rede – você pode indicar que alguns domínios ainda devem ser acelerados. | Matriz de endereços de domínio (por exemplo `contoso.com` ). Também dá suporte a `all_domains`|
|`IgnoreDomainHintForApps`| Se uma solicitação desse aplicativo vier a uma dica de domínio, ignore-a. | Matriz de IDs de aplicativo (GUIDs). Também dá suporte a `all_apps`|
|`RespectDomainHintForApps` |Se uma solicitação desse aplicativo vier a uma dica de domínio, respeite-a mesmo se `IgnoreDomainHintForDomains` incluir esse domínio. Usado para garantir que alguns aplicativos continuem funcionando se você descobrir que eles são interrompidos sem dicas de domínio. | Matriz de IDs de aplicativo (GUIDs). Também dá suporte a `all_apps`|

### <a name="policy-evaluation"></a>Avaliação da política

A lógica DomainHintPolicy é executada em cada solicitação de entrada que contém uma dica de domínio e acelera com base em duas partes de dados na solicitação – o domínio na dica de domínio e a ID do cliente (o aplicativo). Em suma, "respeite" para um domínio ou aplicativo tem precedência sobre uma instrução para "ignorar" uma dica de domínio para um determinado domínio ou aplicativo.

1. Na ausência de qualquer política de dica de domínio, ou se nenhuma das quatro seções fizer referência à dica de aplicativo ou de domínio mencionada, [o restante da política de HRD será avaliada](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies).
1. Se um (ou ambos) `RespectDomainHintForApps` ou `RespectDomainHintForDomains` a seção incluir a dica de aplicativo ou de domínio na solicitação, o usuário será acelerado automaticamente para o IDP federado, conforme solicitado.
1. Se qualquer um (ou ambos) `IgnoreDomainHintsForApps` ou `IgnoreDomainHintsForDomains` fizer referência ao aplicativo ou à dica de domínio na solicitação e eles não forem referenciados pelas seções "respeitar", a solicitação não será acelerada automaticamente e o usuário permanecerá na página de logon do Azure ad para fornecer um nome de usuário.

Depois que um usuário inserir um nome de usuários na página de logon, ele poderá usar suas credenciais gerenciadas, se houver algum registrado.  Se eles optarem por não usar uma credencial gerenciada ou não tiverem sido registrados, eles serão levados ao IDP federado para a entrada de credencial como de costume.

## <a name="suggested-use-within-a-tenant"></a>Uso sugerido dentro de um locatário

Os administradores de domínios federados devem configurar esta seção da política HRD em um plano de quatro fases. O objetivo desse plano é eventualmente fazer com que todos os usuários em um locatário tenham a oportunidade de usar suas credenciais gerenciadas, independentemente do domínio ou do aplicativo, salvar os aplicativos que têm dependências rígidas sobre o `domain_hint` uso.  Esse plano ajuda os administradores a encontrar esses aplicativos, isentam-os da nova política e continuam a distribuir a alteração para o restante do locatário.

1. Escolha um domínio para o qual essa alteração será revertida inicialmente.  Esse será o seu domínio de teste, então escolha um que possa ser mais enganoso para alterações na UX (ou seja, ver uma página de logon diferente).  Isso irá ignorar todas as dicas de domínio de todos os aplicativos que usam esse nome de domínio. [Defina](#configuring-policy-through-graph-explorer) essa política em seu locatário-política de HRD padrão:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Reúna comentários dos usuários do domínio de teste. Coletar detalhes de aplicativos que são quebrados como resultado dessa alteração-eles têm uma dependência de uso de dicas de domínio e devem ser atualizados. Por enquanto, adicione-os à `RespectDomainHintForApps` seção:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. Continue expandindo a distribuição da política para novos domínios, coletando mais comentários.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. Conclua a distribuição-direcionar todos os domínios, isentando aqueles que devem continuar a ser acelerados:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

Após a etapa 4 concluir todos os usuários, exceto aqueles no `guestHandlingDomain.com` , o pode entrar na página de entrada do Azure ad mesmo quando as dicas de domínio, caso contrário, causarão uma aceleração automática para um IDP federado.  A exceção a isso é se o aplicativo que está solicitando a entrada for um dos aplicativos isentos-para os mesmos, todas as dicas de domínio ainda serão aceitas.

## <a name="configuring-policy-through-graph-explorer"></a>Configurando a política por meio do explorador do Graph

Defina a [política HRD](https://docs.microsoft.com/graph/api/resources/homeRealmDiscoveryPolicy) como de costume, usando Microsoft Graph.  

1. Conceda a permissão Policy. ReadWrite. ApplicationConfiguration no [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).  
1. Usar a URL `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. POSTE a nova política para esta URL ou aplique um PATCH para `/policies/homerealmdiscoveryPolicies/{policyID}` ela se estiver substituindo uma existente.

Conteúdo de POSTAgem ou PATCH:

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

Certifique-se de usar barras para escapar a `Definition` seção JSON ao usar o grafo.  

`isOrganizationDefault` deve ser true, mas o displayName e a definição podem ser alterados.

## <a name="next-steps"></a>Próximas etapas

* [Habilitar entrada de chave de segurança sem senha](../authentication/howto-authentication-passwordless-security-key.md)
* [Habilitar a entrada sem senha com o aplicativo Microsoft Authenticator](../authentication/howto-authentication-passwordless-phone.md)
