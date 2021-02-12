---
title: Crie resiliência na autenticação de usuário externa com o Azure Active Directory
description: Um guia para administradores de ti e arquitetos para a criação de autenticação resiliente para usuários externos
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
ms.openlocfilehash: 487efce1fe57413dda740c42a7fd3d5ea91cfa49
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724952"
---
# <a name="build-resilience-in-external-user-authentication"></a>Criar resiliência na autenticação de usuário externa

[Azure Active Directory colaboração B2B](../external-identities/what-is-b2b.md) (Azure ad B2B) é um recurso de [identidades externas](../external-identities/delegate-invitations.md) que permite a colaboração com outras organizações e indivíduos. Ele permite a integração segura de usuários convidados em seu locatário do Azure AD sem precisar gerenciar suas credenciais. Os usuários externos trazem sua identidade e suas credenciais com eles de um provedor de identidade externo (IdP), para que eles não precisem se lembrar de uma nova credencial. 

## <a name="ways-to-authenticate-external-users"></a>Maneiras de autenticar usuários externos

Você pode escolher os métodos de autenticação de usuário externo para seu diretório. Você pode usar o Microsoft IdPs ou outros IdPs.

Com cada IdP externo, você assume uma dependência da disponibilidade desse IdP. Com alguns métodos de conexão ao IdPs, há coisas que você pode fazer para aumentar sua resiliência.

> [!NOTE] 
> O Azure AD B2B tem a capacidade interna de autenticar qualquer usuário de qualquer [Azure Active Directory](../index.yml) locatário ou com uma conta pessoal da [Microsoft](https://account.microsoft.com/account). Você não precisa fazer nenhuma configuração com essas opções internas.

### <a name="considerations-for-resilience-with-other-idps"></a>Considerações sobre resiliência com outros IdPs

Ao usar o IdPs externo para autenticação de usuário convidado, há certas configurações que você deve garantir para evitar interrupções.

| Método de autenticação| Considerações sobre resiliência |
| - | - |
| Federação com IDPs social, como [Facebook](../external-identities/facebook-federation.md) ou [Google](../external-identities/google-federation.md).| Você deve manter sua conta com o IdP e configurar a ID do cliente e o segredo do cliente. |
| [Federação direta com os provedores de identidade SAML e WS-Federation](../external-identities/direct-federation.md)| Você deve colaborar com o proprietário do IdP para acessar seus pontos de extremidade, nos quais você é dependente. <br>Você deve manter os metadados que contêm os certificados e pontos de extremidade. |
| [Senha de uso único de email](../external-identities/one-time-passcode.md)| Com esse método, você é dependente do sistema de email da Microsoft, do sistema de email do usuário e do cliente de email do usuário. |


 

## <a name="self-service-sign-up-preview"></a>Inscrição de autoatendimento (versão prévia)

Como alternativa ao envio de convites ou links, você pode habilitar [a inscrição de autoatendimento](../external-identities/self-service-sign-up-overview.md).  Isso permite que usuários externos solicitem acesso a um aplicativo. Você deve criar um [conector de API](../external-identities/self-service-sign-up-add-api-connector.md) e associá-lo a um fluxo de usuário. Você associa fluxos de usuário que definem a experiência do usuário com um ou mais aplicativos. 

É possível usar [conectores de API](../external-identities/api-connectors-overview.md) para integrar o fluxo de usuário de inscrição de autoatendimento com as APIs de sistemas externos. Essa integração de API pode ser usada para [fluxos de trabalho de aprovação personalizados](../external-identities/self-service-sign-up-add-approvals.md), [execução de verificação de identidade](../external-identities/code-samples-self-service-sign-up.md)e outras tarefas, como substituição de atributos de usuário. O uso de APIs requer que você gerencie as dependências a seguir.

* **Autenticação do conector de API**: a configuração de um conector requer uma URL de ponto de extremidade, um nome de usuário e uma senha. Configure um processo pelo qual essas credenciais sejam mantidas e trabalhe com o proprietário da API para garantir que você saiba qualquer agendamento de expiração.

* **Resposta do conector de API**: os conectores de API de design no fluxo de inscrição para falharem normalmente se a API não estiver disponível. Examine e forneça aos seus desenvolvedores de API essas [respostas de API de exemplo](../external-identities/self-service-sign-up-add-api-connector.md) e as [práticas recomendadas para solução de problemas](../external-identities/self-service-sign-up-add-api-connector.md). Trabalhe com a equipe de desenvolvimento de API para testar todos os cenários de resposta possíveis, incluindo respostas de continuação, validação/erro e bloqueio. 

## <a name="next-steps"></a>Próximas etapas
Recursos de resiliência para administradores e arquitetos
 
* [Crie resiliência com o gerenciamento de credenciais](resilience-in-credentials.md)

* [Crie resiliência com os Estados do dispositivo](resilience-with-device-states.md)

* [Criar resiliência usando a Continuous Access Evaluation (CAE)](resilience-with-continuous-access-evaluation.md)

* [Crie resiliência em sua autenticação híbrida](resilience-in-hybrid.md)

* [Crie resiliência no acesso do aplicativo com o proxy de aplicativo](resilience-on-premises-access.md)

Recursos de resiliência para desenvolvedores

* [Crie resiliência IAM em seus aplicativos](resilience-app-development-overview.md)

* [Crie resiliência em seus sistemas CIAM](resilience-b2c.md)
