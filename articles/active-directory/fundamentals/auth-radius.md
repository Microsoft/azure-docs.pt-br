---
title: Autenticação RADIUS com Azure Active Directory
description: Diretrizes arquitetônicas sobre a obtenção de autenticação RADIUS com Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97c524e1f4c05787f1dd61dea5a463e8fa83511a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96168620"
---
# <a name="radius-authentication-with-azure-active-directory"></a>Autenticação RADIUS com Azure Active Directory

O serviço RADIUS (RADIUS) é um protocolo de rede que protege uma rede habilitando a autenticação centralizada e a autorização de usuários de discagem. Muitos aplicativos ainda dependem do protocolo RADIUS para autenticar usuários.

O Microsoft Windows Server tem uma função chamada NPS (servidor de diretivas de rede), que pode atuar como um servidor RADIUS e oferecer suporte à autenticação RADIUS.

O Azure Active Directory (AD do Azure) permite a autenticação multifator com sistemas baseados em RADIUS. Se um cliente quiser aplicar a autenticação multifator do Azure AD a qualquer uma das cargas de trabalho RADIUS mencionadas anteriormente, ele poderá instalar a extensão NPS da autenticação multifator do Azure AD no servidor NPS do Windows. 

O servidor NPS do Windows autentica as credenciais de um usuário em relação à Active Directory e, em seguida, envia a solicitação de autenticação multifator para o Azure. O usuário recebe um desafio em seu autenticador móvel. Uma vez bem-sucedido, o aplicativo cliente tem permissão para se conectar ao serviço. 

## <a name="use-when"></a>Use quando: 

Você precisa adicionar a autenticação multifator a aplicativos como
* uma VPN (rede virtual privada)
* Acesso WiFi
* Área de Trabalho Remota gateway (RDG)
* Virtual Desktop Infrastructure (VDI)
* Qualquer outro que dependa do protocolo RADIUS para autenticar usuários no serviço. 

> [!NOTE]
> Em vez de depender do RADIUS e da extensão NPS da autenticação multifator do Azure AD para aplicar a autenticação multifator do Azure AD a cargas de trabalho de VPN, recomendamos que você atualize suas VPN para SAML e Federate diretamente sua VPN com o Azure AD. Isso dá à sua VPN toda a abrangência da proteção do Azure AD, incluindo o acesso condicional, a autenticação multifator, a conformidade do dispositivo e a proteção de identidade.

![diagrama arquitetônico](./media/authentication-patterns/radius-auth.png)


## <a name="components-of-the-system"></a>Componentes do sistema 

* **Aplicativo cliente (cliente VPN)**: envia a solicitação de autenticação para o cliente RADIUS.

* **Cliente RADIUS**: converte solicitações do aplicativo cliente e as envia ao servidor RADIUS que tem a extensão NPS instalada.

* **Servidor RADIUS**: conecta-se com Active Directory para executar a autenticação primária para a solicitação RADIUS. Após o êxito, passa a solicitação para a extensão NPS da autenticação multifator do Azure AD.

* **Extensão do NPS**: dispara uma solicitação para a autenticação multifator do Azure ad para uma autenticação secundária. Se for bem-sucedido, a extensão do NPS concluirá a solicitação de autenticação fornecendo o servidor RADIUS com tokens de segurança que incluem a declaração de autenticação multifator, emitida pelo serviço de token de segurança do Azure.

* **Autenticação multifator do Azure ad**: comunica-se com o Azure ad para recuperar os detalhes do usuário e executa uma autenticação secundária usando um método de verificação configurado pelo usuário.

## <a name="implement-radius-with-azure-ad"></a>Implementar RADIUS com o Azure AD 

* [Fornecer recursos de autenticação multifator do Azure AD usando o NPS](../authentication/howto-mfa-nps-extension.md) 

* [Configurar a extensão NPS da autenticação multifator do Azure AD](../authentication/howto-mfa-nps-extension-advanced.md) 

* [VPN com a autenticação multifator do Azure AD usando a extensão NPS](../authentication/howto-mfa-nps-extension-vpn.md) 

  
‎ 

