---
title: 'Sincronização do Azure AD Connect: compreender e personalizar a sincronização | Microsoft Docs'
description: Explica como funciona a sincronização do Azure AD Connect e como personalizá-lo.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cea26cb119f64679807bc6c5eaadb41b341e5d5a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89662389"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Sincronização do Azure AD Connect: compreender e personalizar a sincronização
Os serviços de sincronização do Azure Active Directory Connect (sincronização do Azure AD Connect) são um componente principal do Azure AD Connect. Eles cuidam de todas as operações relacionadas à sincronização de dados de identidade entre seu ambiente local e o Azure AD. O serviço de sincronização do Azure AD Connect é o sucessor do DirSync, do AD do Azure Sync e do Forefront Identity Manager com o Azure Active Directory Connector configurado.

Este tópico é a base da **sincronização do Azure AD Connect** (também chamada de **mecanismo de sincronização**) e relaciona links para todos os outros tópicos relacionados a ela. Para obter links para Azure AD Connect, consulte [integrando suas identidades locais com Azure Active Directory](whatis-hybrid-identity.md).

O serviço de sincronização consiste em dois componentes, o componente local **Sincronização do Azure AD Connect** e o lado do serviço no Azure AD, chamado **Serviço de sincronização do Azure AD Connect**.

## <a name="azure-ad-connect-sync-topics"></a>Tópicos da sincronização do Azure AD Connect
| Tópico | O que ele abrange e quando deve ser lido |
| --- | --- |
| **Noções básicas sobre a sincronização do Azure AD Connect** | |
| [Compreendendo a arquitetura](concept-azure-ad-connect-sync-architecture.md) |Para aqueles que ainda não conhecem o mecanismo de sincronização e querem saber mais sobre a arquitetura e os termos usados. |
| [Conceitos técnicos](how-to-connect-sync-technical-concepts.md) |Uma versão abreviada do tópico sobre arquitetura e que explica brevemente os termos usados. |
| [Topologias para o Azure AD Connect](plan-connect-topologies.md) |Descreve as diferentes topologias e os cenários com suporte no mecanismo de sincronização. |
| **Configuração personalizada** | |
| [Executando o assistente de instalação novamente](how-to-connect-installation-wizard.md) |Explica as opções disponíveis para quando você executar o assistente de instalação do Azure AD Connect novamente. |
| [Noções básicas sobre o provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning.md) |Descreve o modelo de configuração chamado provisionamento declarativo. |
| [Noções básicas sobre expressões de provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |Descreve a sintaxe para a linguagem de expressão usada no provisionamento declarativo. |
| [Noções básicas sobre a configuração padrão](concept-azure-ad-connect-sync-default-configuration.md) |Descreve as regras prontas e a configuração padrão. Também descreve como as regras trabalham juntas para que os cenários prontos funcionem. |
| [Noções básicas sobre usuários e contatos](concept-azure-ad-connect-sync-user-and-contacts.md) |Continua do tópico anterior e descreve como a configuração para usuários e contatos funciona em conjunto, especialmente em um ambiente de várias florestas. |
| [Como fazer uma alteração na configuração padrão](how-to-connect-sync-change-the-configuration.md) |Explica como alterar uma configuração comum para fluxos de atributo. |
| [Práticas recomendadas para alterar a configuração padrão](how-to-connect-sync-best-practices-changing-default-configuration.md) |Limitações de suporte e para fazer alterações na configuração de integração. |
| [Configurar a filtragem](how-to-connect-sync-configure-filtering.md) |Descreve as diferentes opções para limitar quais objetos serão sincronizados com o Azure AD e o passo a passo de como configurá-los. |
| **Recursos e cenários** | |
| [Impedir exclusões acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md) |Descreve o recurso para *impedir exclusões acidentais* e como configurá-lo. |
| [Agendador](how-to-connect-sync-feature-scheduler.md) |Descreve o agendador interno que está importando, sincronizando e exportando dados. |
| [Implementar a sincronização de senha hash](how-to-connect-password-hash-synchronization.md) |Descreve o funcionamento da sincronização de senha, como implementá-la, como operá-la e como solucionar problemas. |
| [Write-back de dispositivo](how-to-connect-device-writeback.md) |Descreve como o write-back de dispositivo funciona no Azure AD Connect. |
| [Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md) |Descreve como estender o esquema do Azure AD com seus próprios atributos personalizados. |
| [Microsoft 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) |Descreve como colocar os recursos de Microsoft 365 do usuário na mesma região que o usuário. |
| **Serviço de sincronização** | |
| [Recursos do serviço de sincronização do Azure AD Connect](how-to-connect-syncservice-features.md) |Descreve o serviço de sincronização e como alterar as configurações de sincronização no Azure AD. |
| [Duplicar a resiliência do atributo](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |Descreve como habilitar e usar a resiliência dos valores de atributos duplicados **userPrincipalName** e **proxyAddresses**. |
| **Operações e interface do usuário** | |
| [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) |Descreve a interface do usuário do Synchronization Service Manager, incluindo as guias [Operações](how-to-connect-sync-service-manager-ui-operations.md), [Conectores](how-to-connect-sync-service-manager-ui-connectors.md), [Designer de Metaverso](how-to-connect-sync-service-manager-ui-mvdesigner.md) e [Pesquisa de Metaverso](how-to-connect-sync-service-manager-ui-mvsearch.md). |
| [Considerações e tarefas operacionais](./how-to-connect-sync-staging-server.md) |Descreve os problemas operacionais, como a recuperação de desastre. |
| **Como...** | |
| [Redefinir a conta do Azure AD](how-to-connect-azureadaccount.md) |Como redefinir as credenciais da conta de serviço usada para conectar da sincronização do Azure AD Connect ao Azure AD. |
| **Mais informações e referências** | |
| [Portas](reference-connect-ports.md) |Lista as portas que você precisa abrir entre o mecanismo de sincronização e os diretórios locais e o AD do Azure. |
| [Atributos sincronizados com o Active Directory do Azure](reference-connect-sync-attributes-synchronized.md) |Lista todos os atributos que estão sendo sincronizados entre o AD local e o AD do Azure. |
| [Referência de funções](reference-connect-sync-functions-reference.md) |Lista todas as funções disponíveis no provisionamento declarativo. |

## <a name="additional-resources"></a>Recursos adicionais
* [Integração de suas identidades locais com o Active Directory do Azure](whatis-hybrid-identity.md)