---
title: Problema ao salvar credenciais de administrador Configurando o aplicativo da galeria do Azure AD | Microsoft Docs
description: Como solucionar problemas comuns enfrentados ao configurar provisionamento do usuário para um aplicativo já listado na Galeria de aplicativos do Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 152b704ca597fb473a820124ee6147d6d9bc7845
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381471"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Problema para salvar as credenciais de administrador ao configurar o provisionamento do usuário para um aplicativo de galeria do Azure Active Directory 

Ao usar o portal do Azure para configurar [provisionamento automático de usuário](user-provisioning.md) para um aplicativo corporativo, você pode encontrar uma situação em que:

* As **credenciais de administrador** inseridas para o aplicativo são válidas e o botão **Conexão de teste** funciona. No entanto, não é possível salvar as credenciais e o portal do Azure retorna uma mensagem de erro genérica.

Se o logon único baseado em SAML também estiver configurado para o mesmo aplicativo, a causa mais provável do erro é que o limite de armazenamento interno por aplicativo do Azure AD para certificados e credenciais foi excedido.

Atualmente, o Azure AD tem uma capacidade máxima de armazenamento de 1024 bytes para todos os certificados, tokens secretas, credenciais e dados de configuração relacionados, associados a uma única instância de um aplicativo (também conhecido como registro de entidade de serviço no Azure AD).

Quando logon único baseado no SAML é configurado, o certificado usado para assinar os tokens SAML é armazenado aqui e geralmente consome mais 50% % do espaço.

Quaisquer tokens secretos, URIs, endereços de email de notificação, nomes de usuário e senhas que forem inseridos durante a configuração de provisionamento do usuário podem causar com que o limite de armazenamento seja excedido.

## <a name="how-to-work-around-this-issue"></a>Como resolver esse problema 

Há duas maneiras possíveis de contornar esse problema atualmente:

1. **Use duas instâncias de aplicativo da galeria, uma para logon único e outra para o provisionamento de usuário** - Tomando o aplicativo da galeria [LinkedIn Elevate](../saas-apps/linkedinelevate-tutorial.md) como exemplo, você pode adicionar LinkedIn Elevate da galeria e configurá-lo para logon único. Para provisionamento, adicione outra instância do LinkedIn Elevate da galeria de aplicativos do Azure AD e nomeie-a como "LinkedIn Elevate (Provisionamento)". Para essa segunda instância, configurar [provisionamento](../saas-apps/linkedinelevate-provisioning-tutorial.md), mas não logon único. Ao usar essa solução alternativa, os mesmos usuários e grupos precisam ser [atribuídos](assign-user-or-group-access-portal.md) para ambos os aplicativos. 

2. **Reduzir a quantidade de dados de configuração armazenados** - Todos os dados inseridos na seção [Credenciais de administrador](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) da guia de configuração são armazenados no mesmo local que o certificado SAML. Embora não seja possível reduzir o tamanho de todos esses dados, alguns campos de configuração opcionais, como o **Email de notificação** podem ser removidos.

## <a name="next-steps"></a>Próximas etapas
[Configurar o provisionamento e cancelamento de usuários para aplicativos SaaS](user-provisioning.md)
