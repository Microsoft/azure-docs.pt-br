---
title: Permissões no Azure Active Directory | Microsoft Docs
description: Saiba mais sobre permissões no Microsoft Azure Active Directory e como usá-las.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jesakowi
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 2b85115d905cb6a7eb7c6aed64a4834425d2f1d7
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366387"
---
# <a name="permissions-and-consent-in-the-azure-active-directory-v10-endpoint"></a>Permissões e consentimento no ponto de extremidade v1.0 do Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O Microsoft Azure Active Directory (Azure AD) faz uso extensivo de permissões para fluxos OAuth e OpenID Connect (OIDC). Quando seu aplicativo receber um token de acesso do Azure AD, o token de acesso incluirá declarações que descrevem as permissões que seu aplicativo tem em relação a um recurso específico.

As *permissões*, também conhecidas como *escopos*, facilitam a autorização para o recurso porque o recurso só precisa verificar se o token contém a permissão apropriada para a API que está chamando o aplicativo.

## <a name="types-of-permissions"></a>Tipos de permissões

O Microsoft Active Directory do Azure define dois tipos de permissões:

* **Permissões delegadas** - são usadas por aplicativos que têm um usuário conectado presente. Para esses aplicativos, o usuário ou administrador consente as permissões solicitadas pelo aplicativo e este recebe permissão para agir como o usuário conectado na hora de fazer chamadas à API. Dependendo da API, o usuário pode não ser capaz de consentir diretamente com a API e, em vez disso, [exigiria que um administrador fornecesse "consentimento do administrador"](../develop/howto-convert-app-to-be-multi-tenant.md).
* **Permissões de aplicativo** - são usadas por aplicativos executados sem um usuário conectado presente; por exemplo, aplicativos executados como serviços em segundo plano ou daemons. As permissões de aplicativo só podem ser [consentidas por administradores](../develop/v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) , pois normalmente são poderosas e permitem o acesso a dados entre os limites do usuário ou dados que, de outra forma, seriam restritos aos administradores. Os usuários que são definidos como proprietários do aplicativo de recurso (ou seja, a API que publica as permissões) também têm permissão para conceder permissões de aplicativo para as APIs que eles possuem.

Permissões efetivas são as permissões que seu aplicativo terá ao fazer solicitações para uma API. 

* Para obter permissões delegadas, as permissões efetivas do seu aplicativo será a interseção menos privilegiadas das permissões delegadas que tiver recebido o aplicativo (por meio de consentimento) e os privilégios do usuário conectado no momento. Seu aplicativo não pode ter mais privilégios que o usuário conectado. Dentro das organizações, os privilégios do usuário conectado podem ser determinados pela política ou por associação em uma ou mais funções de administrador. Para saber quais funções de administrador podem consentir as permissões delegadas, consulte [Permissões da função de administrador no Microsoft Azure Active Directory](../roles/permissions-reference.md).
    Por exemplo, suponha que seu aplicativo tenha recebido a `User.ReadWrite.All` a permissão delegada no Microsoft Graph. Essa permissão concede uma permissão ao seu aplicativo para ler e atualizar o perfil de todos os usuários em uma organização. Se o usuário conectado for um administrador global, seu aplicativo poderá atualizar o perfil de todos os usuários na organização. No entanto, se o usuário conectado não estiver em uma função de administrador, seu aplicativo poderá atualizar apenas o perfil do usuário conectado. Não poderá atualizar os perfis de outros usuários na organização porque o que ele tem permissão para agir em nome de usuário não tem os privilégios.
* Para permissões de aplicativo, as permissões efetivas do seu aplicativo são o nível completo de privilégios indicado pela permissão. Por exemplo, um aplicativo que tenha o `User.ReadWrite.All` permissão de aplicativo pode atualizar o perfil de todos os usuários na organização.

## <a name="permission-attributes"></a>Atributos de permissão
As permissões no AD do Azure têm um número de propriedades que ajudam, administradores ou desenvolvedores de aplicativo a tomarem decisões conscientes sobre o que a permissão concede acesso. 

> [!NOTE]
> Você pode exibir as permissões de um aplicativo do Azure AD ou a Entidade de Serviço expõe usando o portal do Azure ou o PowerShell. Tente este script para exibir as permissões expostas pelo Microsoft Graph.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| Nome da propriedade | Descrição | Exemplo |
| --- | --- | --- |
| `ID` | É um valor de GUID que identifica exclusivamente esta permissão. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca |
| `IsEnabled` | Indica se essa permissão está disponível para uso. | true |
| `Type` | Indica se essa permissão requer o consentimento do usuário ou consentimento do administrador. | Usuário |
| `AdminConsentDescription` | É uma descrição que é exibida para os administradores durante as experiências de consentimento do administrador | Permite que o aplicativo leia emails nas caixas de entrada dos usuários.  |
| `AdminConsentDisplayName` | É um nome fácil exibido aos administradores durante as experiências de consentimento do administrador. | Ler email de usuário |
| `UserConsentDescription` | É uma descrição que é exibida aos usuários durante a experiência de consentimento do usuário. |  Permite que o aplicativo leia emails em sua caixa de email.  |
| `UserConsentDisplayName` | É um nome fácil que é exibido aos usuários durante uma experiência de consentimento do usuário. | Leia seu email |
| `Value` | É a cadeia de caracteres que é usada para identificar a permissão durante os fluxos de autorização OAuth 2.0. `Value` também pode ser combinado com a cadeia de caracteres do URI da ID do aplicativo para formar um nome totalmente qualificado de permissão. | `Mail.Read` |

## <a name="types-of-consent"></a>Tipos de consentimento

Os aplicativos no Microsoft Azure Active Directory dependem de autorização para acessar os recursos necessários ou APIs. Há um número de tipos de autorização que seu aplicativo precisa saber para ser bem-sucedido. Se você estiver definindo permissões, também precisará entender como seus usuários terá acesso ao seu aplicativo ou a API.

* **Consentimento do usuário estático** - ocorre automaticamente durante o [OAuth 2.0 autorizar fluxo](v1-protocols-oauth-code.md#request-an-authorization-code) quando você especifica o recurso que deseja que seu aplicativo interaja. No cenário de consentimento do usuário estático, seu aplicativo deve ter já especificado todas as permissões necessárias na configuração do aplicativo no portal do Azure. Se o usuário (ou um administrador, conforme apropriado) não concedeu permissão para este aplicativo, em seguida, o Microsoft Active Directory solicitará ao usuário para fornecer consentimento neste momento. 

    Saiba mais sobre como registrar um aplicativo do Microsoft Azure Active Directory que solicita acesso a um conjunto estático de APIs.
* **Consentimento do usuário dinâmico** - é um recurso do modelo de aplicativo do Microsoft Azure Active Directory e v2. Nesse cenário, o aplicativo solicita um conjunto de permissões que precisa do [fluxo de autorização OAuth 2.0 para aplicativos v2](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent). Se o usuário não aceitou, serão solicitados a consentir neste momento. [Saiba mais sobre o consentimento dinâmico](./azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

    > [!IMPORTANT]
    > O consentimento dinâmico pode ser conveniente, mas apresenta um grande desafio para permissões que exigem o consentimento do administrador, desde que a experiência de consentimento do administrador não saiba sobre essas permissões no momento da autorização. Se você precisar de permissões de administrador com privilégios ou se seu aplicativo usar o consentimento dinâmico, você deverá registrar todas as permissões no portal do Azure (não apenas o subconjunto de permissões que exigem o consentimento do administrador). Isso permite que os administradores de locatários deem consentimento em nome de todos os seus usuários.
  
* **Consentimento do administrador** - é necessário quando seu aplicativo precisa ter acesso a determinadas permissões com alto privilégio. O consentimento do administrador faz com que os administradores tenham alguns controles adicionais antes de autorizar aplicativos ou usuários a acessar dados altamente privilegiados da organização. [Saiba mais sobre como conceder consentimento do administrador](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>Práticas recomendadas

### <a name="client-best-practices"></a>Práticas recomendadas de cliente

- Solicite apenas permissões de que seu aplicativo precisa. Aplicativos com um número excessivo de permissões estão em risco de exposição de dados de usuário se eles forem comprometidos.
- Escolha entre permissões delegadas e permissões de aplicativo com base no cenário compatível com seu aplicativo.
    - Use sempre permissões delegadas se a chamada estiver sendo feita em nome do usuário.
    - Use somente permissões de aplicativo se o aplicativo for não interativo e não estiver fazendo chamadas em nome de um usuário específico. As permissões de aplicativo são altamente privilegiadas e devem ser usadas apenas quando absolutamente necessário.
- Ao usar um aplicativo com base no ponto de extremidade v 2.0, defina sempre as permissões estáticas (as especificadas no registro de aplicativo) como o superconjunto das permissões dinâmicas solicitadas no runtime (aquelas especificadas no código e enviados como parâmetros de consulta na solicitação de autorização) para que os cenários, como o administrador de consentimento, funcionem corretamente.

### <a name="resourceapi-best-practices"></a>Melhores práticas de API/recurso

- Recursos que expõem as APIs devem definir permissões que são específicas para os dados ou ações que estão protegendo. Essa melhor prática ajuda a fazer com que os clientes não acabem tendo permissão para acessar os dados desnecessários e que os usuários sejam bem informados sobre os dados que estão aceitando.
- Recursos devem definir explicitamente `Read` e `ReadWrite` permissões separadamente.
- Recursos deverão marcar quaisquer permissões que permitam o acesso a dados entre limites de usuário como `Admin` permissões.
- Os recursos devem seguir o padrão de nomenclatura `Subject.Permission[.Modifier]`, em que:
  - `Subject` corresponde ao tipo de dados disponível
  - `Permission` corresponde à ação que um usuário pode realizar em relação aos dados
  - `Modifier` é usado, opcionalmente, para descrever especializações de outra permissão
    
    Por exemplo:
  - Mail.Read - permite que os usuários leiam o email.
  - Mail.Read - permite que os usuários leiam o email.
  - Mail.ReadWrite.All - permite que um administrador ou usuário acesse todos os emails da organização.