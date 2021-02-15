---
title: Autenticação e autorização para Aplicativos Web Estáticos do Azure
description: Aprenda a usar diferentes provedores de autorização para proteger seu aplicativo estático.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: e95cd313d341844eabf4f5c5feae8a8ca3dc9c2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826551"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps-preview"></a>Autenticação e autorização para Versão Prévia do serviço Aplicativos Web Estáticos do Azure

O serviço Aplicativos Web Estáticos do Azure simplifica a experiência de autenticação gerenciando esta com os seguintes provedores:

- Azure Active Directory
- GitHub
- Facebook
- Google<sup>1</sup>
- Twitter

Os [convites](#invitations) específicos do provedor associam usuários a funções, e os usuários autorizados recebem acesso a [rotas](routes.md) por regras definidas no arquivo _routes.json_.

Todos os provedores de autenticação são habilitados por padrão. Para restringir um provedor de autenticação, [bloqueie o acesso](#block-an-authorization-provider) com uma regra de rota personalizada.

O tópico de autenticação e autorização é bastante semelhante aos conceitos de roteamento. Certifique-se de ler o [guia de roteamento](routes.md) juntamente com este artigo.

## <a name="roles"></a>Funções

Cada usuário que acessa um aplicativo Web estático pertence a uma ou mais funções.  Há duas funções internas às quais os usuários podem pertencer:

- **anônimo**: Todos os usuários pertencem automaticamente à função _anônimo_.
- **autenticado**: Todos os usuários que estão conectados pertencem à função _autenticado_.

Além das funções internas, você pode criar novas funções, atribuí-las aos usuários por meio de convites e referenciá-las no arquivo _routes.json_.

## <a name="role-management"></a>Gerenciamento de funções

### <a name="add-a-user-to-a-role"></a>Adicionar um usuário a uma função

Para adicionar usuários ao seu site, você gera convites que permitem associar usuários a funções específicas. As funções são definidas e mantidas no arquivo _routes.json_.

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>Criar um convite

Os convites são específicos para provedores de autorização individuais, portanto, considere as necessidades do seu aplicativo à medida que você seleciona quais provedores serão compatíveis. Alguns provedores expõem o endereço de email de um usuário, enquanto outros fornecem apenas o nome de usuário do site.

<a name="provider-user-details" id="provider-user-details"></a>

| Provedor de autorização | Expõe a seguinte informação do usuário  |
| ---------------------- | ----------------- |
| Azure Active Directory | endereço de email     |
| Facebook               | endereço de email     |
| GitHub                 | Nome de Usuário          |
| Google<sup>1</sup>     | endereço de email     |
| Twitter                | Nome de Usuário          |

1. Navegue até um recurso do serviço Aplicativos Web Estáticos no [portal do Azure](https://portal.azure.com).
1. Nas _Configurações_, clique em **Gerenciamento de funções**.
1. Clique no botão **Convidar**.
1. Selecione um _Provedor de autorização_ na lista de opções.
1. Adicione o nome de usuário ou o endereço de email do destinatário na caixa _Detalhes do convidado_.
    - Para o GitHub e o Twitter, você insere o nome de usuário. Para todos os outros, insira o endereço de email do destinatário.
1. Selecione o domínio do seu site estático na lista suspensa de _Domínio_.
    - O domínio que você selecionar é o domínio que aparece no convite. Se você tiver um domínio personalizado associado ao seu site, provavelmente é melhor escolher o domínio personalizado.
1. Adicione uma lista separada por vírgulas de nomes de função na caixa _Função_.
1. Insira o número máximo de horas que você deseja que o convite permaneça válido.
    - O limite máximo possível é de 168 horas, que corresponde a 7 dias.
1. Clique no botão **Gerar**.
1. Copie o link da caixa _Link do convite_.
1. Envie por email o link do convite para a pessoa que você está concedendo acesso ao seu aplicativo.

Quando o usuário clica no link no convite, ele é solicitado a fazer logon com sua conta correspondente. Uma vez conectado com êxito, o usuário é associado às funções selecionadas.

> [!CAUTION]
> Certifique-se de que suas regras de rota não entrem em conflito com os provedores de autenticação selecionados. O bloqueio de um provedor com uma regra de rota impediria que os usuários aceitassem convites.

### <a name="update-role-assignments"></a>Atualizar atribuições de função

1. Navegue até um recurso do serviço Aplicativos Web Estáticos no [portal do Azure](https://portal.azure.com).
1. Nas _Configurações_, clique em **Gerenciamento de funções**.
1. Clique no usuário na lista.
1. Edite a lista de funções na caixa _Função_.
1. Clique no botão **Atualizar**.

### <a name="remove-user"></a>Remover usuário

1. Navegue até um recurso do serviço Aplicativos Web Estáticos no [portal do Azure](https://portal.azure.com).
1. Nas _Configurações_, clique em **Gerenciamento de funções**.
1. Localize o usuário na lista.
1. Marque a caixa de seleção na linha do usuário.
1. Clique no botão **Excluir** .

Quando remover um usuário, tenha em mente os seguintes itens:

1. A remoção de um usuário invalida suas permissões.
1. A propagação em todo o mundo poderá levar alguns minutos.
1. Se o usuário for adicionado de volta ao aplicativo, a [`userId` é alterada](user-information.md).

## <a name="remove-personal-identifying-information"></a>Remover informações de identificação pessoal

Quando você concede consentimento a um aplicativo como um usuário final, o aplicativo tem acesso ao seu endereço de email ou ao seu nome de usuário, dependendo do provedor de identidade. Depois que essas informações são fornecidas, o proprietário do aplicativo decide como gerenciar informações de identificação pessoal.

Os usuários finais precisam contatar os administradores de aplicativos Web individuais para revogar essas informações de seus sistemas.

Para remover informações de identificação pessoal da plataforma de Aplicativos Web Estáticos do Azure e impedir que a plataforma forneça essas informações em solicitações futuras, envie uma solicitação usando a URL:

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

Para impedir que a plataforma forneça essas informações em solicitações futuras para aplicativos individuais, envie uma solicitação para a seguinte URL:

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

## <a name="system-folder"></a>Pasta do sistema

O serviço Aplicativos Web Estáticos do Azure usam a pasta do sistema `/.auth` para fornecer acesso a APIs relacionadas à autorização. Em vez de expor qualquer uma das rotas na pasta `/.auth` diretamente aos usuários finais, considere criar [regras de roteamento](routes.md) para criar URLs amigáveis.

## <a name="login"></a>Logon

Use a tabela a seguir para localizar a rota de logon específica do provedor.

| Provedor de autorização | Rota de logon             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| Facebook               | `/.auth/login/facebook` |
| GitHub                 | `/.auth/login/github`   |
| Google<sup>1</sup>     | `/.auth/login/google`   |
| Twitter                | `/.auth/login/twitter`  |

Por exemplo, para fazer logon com o GitHub, você pode incluir um link de logon como o trecho de código a seguir:

```html
<a href="/.auth/login/github">Login</a>
```

Se você optar por oferecer suporte a mais de um provedor, precisará expor um link específico do provedor para cada provedor em seu site.

Você pode usar uma [regra de rota](routes.md) para mapear um provedor padrão para uma rota amigável como _/login_.

```json
{
  "route": "/login",
  "serve": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>Redirecionamento pós-logon

Se você quiser que um usuário retorne a uma página específica após o logon, forneça uma URL no parâmetro de cadeia de caracteres de consulta `post_login_redirect_uri`.


## <a name="logout"></a>Logout

A rota `/.auth/logout` registra os usuários fora do site. Você pode adicionar um link para a navegação do site para permitir que o usuário faça logoff, conforme mostrado no exemplo a seguir.

```html
<a href="/.auth/logout">Log out</a>
```

Você pode usar uma [regra de rota](routes.md) para mapear uma rota amigável como _/logout_.

```json
{
  "route": "/logout",
  "serve": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>Redirecionamento pós-logoff

Se você quiser que um usuário retorne a uma página específica após o logoff, forneça uma URL no parâmetro de cadeia de caracteres de consulta `post_logout_redirect_uri`.

## <a name="block-an-authorization-provider"></a>Bloquear um provedor de autorização

Talvez você queira impedir que seu aplicativo use um provedor de autorização. Por exemplo, seu aplicativo pode querer padronizar apenas para [provedores que expõem endereços de email](#provider-user-details).

Para bloquear um provedor, você pode criar [regras de rota](routes.md) para retornar um erro 404 para solicitações para a rota específica do provedor bloqueado. Por exemplo, para restringir o Twitter como provedor, adicione a regra de rota a seguir.

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": "404"
}
```

## <a name="restrictions"></a>Restrições

Consulte o [artigo cotas](quotas.md) para restrições gerais e limitações.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Acessar dados de autenticação e autorização do usuário](user-information.md)

<sup>1</sup> Certificação externa pendente.
