---
title: Configurar a inscrição e a entrada com o OpenID Connect
titleSuffix: Azure AD B2C
description: Configure a inscrição e a entrada com qualquer IdP (provedor de identidade do OpenID Connect) no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9434bd4042798dc05a33401e1884e11a73774936
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448329"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a OpenID Connect usando o Azure Active Directory B2C

O [OpenID Connect](openid-connect.md) é um protocolo de autenticação criado com base no OAuth 2,0 que pode ser usado para a entrada segura do usuário. A maioria dos provedores de identidade que usam esse protocolo tem suporte no Azure AD B2C. Este artigo explica como você pode adicionar provedores de identidade do OpenID Connect personalizados em seus fluxos de usuário.

## <a name="add-the-identity-provider"></a>Adicionar o provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C clicando no filtro **diretório + assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **Provedores de identidade** e **Novo provedor do OpenID Connect**.
1. Insira um **Nome**. Por exemplo, digite *contoso*.

## <a name="configure-the-identity-provider"></a>Configurar o provedor de identidade

Cada provedor de identidade do OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para executar a entrada. Isso inclui informações como as URLs a serem usadas e o local das chaves de assinatura públicas do serviço. O documento de metadados do OpenID Connect sempre está localizado em um ponto de extremidade que termina com `.well-known/openid-configuration`. Insira a URL de metadados do provedor de identidade do OpenID Connect que você deseja adicionar.

## <a name="client-id-and-secret"></a>ID do cliente e segredo

Para permitir que os usuários entrem, o provedor de identidade exige que os desenvolvedores registrem um aplicativo em seus serviços. Este aplicativo tem uma ID que é conhecida como o **ID do cliente** e um **segredo do cliente**. Copie esses valores do provedor de identidade e insira-os nos campos correspondentes.

> [!NOTE]
> O segredo do cliente é opcional. No entanto, você deve inserir um segredo do cliente se desejar usar o [fluxo do código de autorização](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), que usa o segredo para trocar o código do token.

## <a name="scope"></a>Escopo

O escopo define as informações e as permissões que você deseja coletar do provedor de identidade personalizado. As solicitações do OpenID Connect precisam conter o valor de escopo do `openid` para receber o token de ID do provedor de identidade. Sem o token de ID, os usuários não conseguem entrar no Azure AD B2C usando o provedor de identidade personalizado. É possível acrescentar outros escopos separados por espaço. Veja a documentação do provedor de identidade personalizado para saber quais outros escopos podem estar disponíveis.

## <a name="response-type"></a>Tipo de resposta

O tipo de resposta descreve que tipo de informação é retornada na chamada inicial para o `authorization_endpoint` do provedor de identidade personalizado. Os seguintes tipos de resposta podem ser usados:

* `code`: conforme o [fluxo de código de autorização](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), um código será retornado ao Azure AD B2C. O Azure AD B2C prossegue para chamar o `token_endpoint` para trocar o código pelo token.
* `id_token`: um token de ID é devolvido do provedor de identidade personalizado para o Azure AD B2C.

## <a name="response-mode"></a>Modo de resposta

O modo de resposta define o método que deve ser usado para retornar os dados do provedor de identidade personalizado para o Azure AD B2C. Os seguintes modos de resposta podem ser usados:

* `form_post`: esse modo de resposta é recomendado para obter a melhor segurança possível. A resposta é transmitida por meio do método HTTP `POST`, com o código ou o token codificado no corpo usando o formato `application/x-www-form-urlencoded`.
* `query`: o código ou o token é devolvido como um parâmetro de consulta.

## <a name="domain-hint"></a>Dica de domínio

A dica de domínio pode ser usada para pular diretamente para a página de entrada do provedor de identidade especificado, em vez de fazer com que o usuário faça uma seleção na lista de provedores de identidade disponíveis. Para permitir esse tipo de comportamento, insira um valor para a dica de domínio. Para pular para o provedor de identidade personalizado, acrescente o parâmetro `domain_hint=<domain hint value>` ao final da solicitação ao chamar o Azure AD B2C para entrar.

## <a name="claims-mapping"></a>Mapeamento de declarações

Depois que o provedor de identidade personalizado envia um token de ID para o Azure AD B2C, o Azure AD B2C precisa ser capaz de mapear as declarações do token recebido para as declarações que ele reconhece e usa. Para cada um dos seguintes mapeamentos, veja a documentação do provedor de identidade personalizado para entender as declarações que são retornadas nos tokens do provedor de identidade:

* **ID de usuário**: Insira a declaração que fornece o *identificador exclusivo* para o usuário conectado.
* **Nome de exibição**: Insira a declaração que fornece o *nome de exibição* ou o *nome completo* do usuário.
* **Nome fornecido**: Insira a declaração que fornece o *nome* do usuário.
* **Sobrenome**: Insira a declaração que fornece o *último nome* do usuário.
* **Email**: Insira a declaração que fornece o *endereço de email* do usuário.

## <a name="add-the-identity-provider-to-a-user-flow"></a>Adicionar o provedor de identidade a um fluxo de usuário 

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Clique no fluxo de usuário para o qual você deseja adicionar o provedor de identidade. 
1. Em **provedores de identidade social**, selecione o provedor de identidade que você adicionou. Por exemplo, *contoso*.
1. Clique em **Salvar**.
1. Para testar sua política, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar fluxo de usuário** .
1. Na página inscrever-se ou entrar, selecione o provedor de identidade que você deseja entrar. Por exemplo, *contoso*.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms` , que exibe o conteúdo do token retornado por Azure ad B2C.
