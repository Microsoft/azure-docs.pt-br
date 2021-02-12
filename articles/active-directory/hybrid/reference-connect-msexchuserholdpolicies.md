---
title: 'Azure AD Connect: msExchUserHoldPolicies e cloudMsExchUserHoldPolicies | Microsoft Docs'
description: Este tópico descreve o comportamento de atributo dos atributos msExchUserHoldPolicies e cloudMsExchUserHoldPolicies
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fbda588d99de44c77118586519055a8fc474104
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861758"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect-msExchUserHoldPolicies e cloudMsExchUserHoldPolicies
O documento de referência a seguir descreve esses atributos usados pelo Exchange e a maneira apropriada de editar as regras de sincronização padrão.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>O que são msExchUserHoldPolicies e cloudMsExchUserHoldPolicies?
Há dois tipos de [suspensões](/Exchange/policy-and-compliance/holds/holds) disponíveis para um servidor Exchange: suspensão de litígio e In-Place. Quando a suspensão de litígio está habilitada, todas as caixas de correio todos os itens são colocados em espera.  Um In-Place Hold é usado para preservar somente os itens que atendem aos critérios de uma consulta de pesquisa que você definiu usando a ferramenta de eDiscovery In-Place.

Os atributos MsExchUserHoldPolcies e cloudMsExchUserHoldPolicies permitem o AD local e o Azure AD para determinar quais usuários estão em uma suspensão, dependendo se eles estão usando o Exchange ou o Exchange Online no local.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>fluxo de sincronização do msExchUserHoldPolicies
Por padrão, os MsExchUserHoldPolcies são sincronizados por Azure AD Connect diretamente para o atributo msExchUserHoldPolicies no metaverso e, em seguida, para o atributo msExchUserHoldPolicies no Azure AD

As tabelas a seguir descrevem o fluxo:

Entrada do Active Directory local:

|Active Directory atributo|Nome do atributo|Tipo de fluxo|Atributo de metaverso|Regra de sincronização|
|-----|-----|-----|-----|-----|
|Active Directory local|msExchUserHoldPolicies|Direto|msExchUserHoldPolicies|Entrada do AD – usuário Exchange|

Saída para o Azure AD:

|Atributo de metaverso|Nome do atributo|Tipo de fluxo|Atributo do Azure AD|Regra de sincronização|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direto|msExchUserHoldPolicies|Saída para o AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>fluxo de sincronização do cloudMsExchUserHoldPolicies
Por padrão, os cloudMsExchUserHoldPolicies são sincronizados por Azure AD Connect diretamente ao atributo cloudMsExchUserHoldPolicies no metaverso. Em seguida, se msExchUserHoldPolicies não for nulo no metaverso, o atributo em fluirá para Active Directory.

As tabelas a seguir descrevem o fluxo:

Entrada do Azure AD:

|Active Directory atributo|Nome do atributo|Tipo de fluxo|Atributo de metaverso|Regra de sincronização|
|-----|-----|-----|-----|-----|
|Active Directory local|cloudMsExchUserHoldPolicies|Direto|cloudMsExchUserHoldPolicies|Entrada do AAD – troca de usuário|

Saída para Active Directory local:

|Atributo de metaverso|Nome do atributo|Tipo de fluxo|Atributo do Azure AD|Regra de sincronização|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF (NOT NULL)|msExchUserHoldPolicies|Saída para AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Informações sobre o comportamento do atributo
O msExchangeUserHoldPolicies são um atributo de autoridade única.  Um atributo de autoridade única pode ser definido em um objeto (nesse caso, objeto de usuário) no diretório local ou no diretório na nuvem.  As regras de início de autoridade ditam que, se o atributo for sincronizado do local, o Azure AD não terá permissão para atualizar esse atributo.

Para permitir que os usuários definam uma política de retenção em um objeto de usuário na nuvem, o atributo cloudMSExchangeUserHoldPolicies é usado. Esse atributo é usado porque o Azure AD não pode definir msExchangeUserHoldPolicies diretamente com base nas regras explicadas acima.  Esse atributo será então sincronizado de volta para o diretório local se, o msExchangeUserHoldPolicies não for nulo e substituirá o valor atual de msExchangeUserHoldPolicies.

Em determinadas circunstâncias, por exemplo, se ambas foram alteradas localmente e no Azure ao mesmo tempo, isso pode causar alguns problemas.  

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
