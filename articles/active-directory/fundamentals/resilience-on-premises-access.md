---
title: Crie resiliência no acesso do aplicativo com o proxy de aplicativo
description: Um guia para arquitetos e administradores de ti sobre como usar o proxy de aplicativo para acesso resiliente a aplicativos locais
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fed78d7d2250d749ced7fe343689df76329b60d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724653"
---
# <a name="build-resilience-in-application-access-with-application-proxy"></a>Crie resiliência no acesso do aplicativo com o proxy de aplicativo

O Proxy de Aplicativo é um recurso do Azure AD que permite que os usuários acessem aplicativos Web locais de um cliente remoto. O proxy de aplicativo inclui o serviço de proxy de aplicativo na nuvem e os conectores de proxy de aplicativo, que são executados em um servidor local. 

Os usuários acessam recursos locais por meio de uma URL publicada por meio do proxy de aplicativo. Eles são redirecionados para a página de entrada do Azure AD. O serviço de proxy de aplicativo no Azure AD envia um token para o conector de proxy de aplicativo na rede corporativa, que passa o token para o local Active Directory o usuário autenticado pode acessar o recurso local. No diagrama a seguir, os [conectores](../manage-apps/application-proxy-connectors.md) são mostrados em um [grupo de conectores](../manage-apps/application-proxy-connector-groups.md).

> [!IMPORTANT]
> Ao publicar seus aplicativos por meio do proxy de aplicativo, você deve implementar o [planejamento de capacidade e a redundância apropriada para os conectores de proxy de aplicativo](../manage-apps/application-proxy-connectors.md#capacity-planning).

![Diagrama de arquitetura do aplicativo y](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## <a name="how-do-i-implement-application-proxy"></a>Como fazer implementar o proxy de aplicativo?

Para implementar o acesso remoto com o Azure Proxy de Aplicativo do AD, consulte os recursos a seguir.

* [Como planejar uma implantação do Proxy de Aplicativo](../manage-apps/application-proxy-deployment-plan.md)

* [Práticas recomendadas de alta disponibilidade e balanceamento de carga](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [Configurar servidores proxy](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [Criar uma estratégia de controle de acesso resiliente](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Próximas etapas
Recursos de resiliência para administradores e arquitetos
 
* [Crie resiliência com o gerenciamento de credenciais](resilience-in-credentials.md)

* [Crie resiliência com os Estados do dispositivo](resilience-with-device-states.md)

* [Criar resiliência usando a Continuous Access Evaluation (CAE)](resilience-with-continuous-access-evaluation.md)

* [Criar resiliência na autenticação de usuário externa](resilience-b2b-authentication.md)

* [Crie resiliência em sua autenticação híbrida](resilience-in-hybrid.md)

Recursos de resiliência para desenvolvedores

* [Crie resiliência IAM em seus aplicativos](resilience-app-development-overview.md)

* [Crie resiliência em seus sistemas CIAM](resilience-b2c.md)