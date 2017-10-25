---
title: "Azure Active Directory B2C: protocolos de autenticação | Microsoft Docs"
description: Como compilar aplicativos usando os protocolos com suporte do Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5e407d0a-73a2-4d74-ac81-3aa6c31ddcee
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.openlocfilehash: 8e7e7bc7633370057f8dc596ad04a3f1d796a7d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# Azure AD B2C: protocolos de autenticação
O Azure AD B2C (Azure Active Directory B2C) fornece identidade como um serviço para seus aplicativos, com suporte a dois protocolos padrão de mercado, o OpenID Connect e o OAuth 2.0. O serviço está em conformidade com o padrão, mas pode haver diferenças sutis entre duas implementações quaisquer desses protocolos. 

As informações neste guia são úteis se você escreve seu código enviando e manipulando solicitações HTTP diretamente, em vez de usar uma biblioteca de software livre. Recomendamos a leitura desta página antes de se aprofundar nos detalhes de cada protocolo específico. Mas se você já estiver familiarizado com o Azure AD B2C, acesse diretamente [os guias de referência de protocolo](#protocols).

<!-- TODO: Need link to libraries above -->

## Noções básicas
Todo aplicativo que usa o Azure AD B2C precisa ser registrado em seu diretório B2C no [Portal do Azure](https://portal.azure.com). O processo de registro do aplicativo coleta e atribui alguns valores ao seu aplicativo:

* Uma **ID de aplicativo** que identifica exclusivamente o aplicativo.
* Um **URI de Redirecionamento** ou **Identificador de Pacote** que pode ser usado para direcionar as respostas de volta ao aplicativo.
* Alguns outros valores específicos de cenário. Para obter mais informações, saiba [como registrar seu aplicativo](active-directory-b2c-app-registration.md).

Depois de registrar seu aplicativo, ele se comunicará com o Azure AD (Azure Active Directory), enviando solicitações para o ponto de extremidade:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Em quase todos os fluxos do OAuth e do OpenID Connect, há quatro partes envolvidas na troca:

![Funções do OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* O **servidor de autorização** é o ponto de extremidade do Azure AD. Ele trata de forma segura tudo o que for relacionado ao acesso e informações do usuário. Ele também manipula as relações de confiança entre as partes em um fluxo. Ele é responsável pela verificação da identidade do usuário, concessão e revogação do acesso a recursos e emissão de tokens. Ele também é conhecido como o provedor de identidade.

* O **Proprietário do Recurso** normalmente é o usuário final. Ele é a parte que possui os dados e tem o poder de permitir que terceiros acessem esses dados ou recurso.

* O **cliente OAuth** é seu aplicativo. Ele é identificado pela ID do aplicativo. Geralmente é a parte com a qual os usuários finais interagem. Ele também solicita tokens do servidor de autorização. O proprietário do recurso deve conceder ao cliente a permissão para acessar o recurso.

* O **Servidor de Recurso** é o local no qual o recurso ou os dados residem. Ele confia no servidor de autorização para autenticar e autorizar o cliente OAuth com segurança. Ele também usa os tokens de acesso de portador para garantir a concessão a um recurso.

## Políticas
Sem dúvida, as políticas do AD B2C do Azure são os recursos mais importantes do serviço. O AD B2C do Azure amplia os protocolos padrão do OAuth 2.0 e do OpenID Connect ao introduzir as políticas. Elas permitem que o AD B2C do Azure execute muito mais do que uma simples autenticação e uma autorização. 

As políticas descrevem totalmente as experiências de identidade do consumidor, incluindo inscrição, entrada ou edição de perfil. As políticas podem ser definidas em uma interface do usuário administrativa. Elas podem ser executadas usando um parâmetro de consulta especial nas solicitações de autenticação HTTP. 

As políticas não são um recurso padrão do OAuth 2.0 e do OpenID Connect; portanto, você deve reservar um tempo para entendê-las. Para saber mais, confira o [guia de referência de política do Azure AD B2C](active-directory-b2c-reference-policies.md).

## Tokens
A implementação do AD B2C do Azure do OAuth 2.0 e do OpenID Connect faz amplo uso de tokens de portador, incluindo os tokens de portador representados como JWTs (tokens Web JSON). Um token de portador é um token de segurança leve que concede ao "portador" acesso a um recurso protegido.

O portador é qualquer parte que possa apresentar o token. Primeiro, o Azure AD deve autenticar uma parte para que ela possa receber um token de portador. Porém, se as medidas necessárias não forem tomadas para proteger o token durante a transmissão e o armazenamento, ele poderá ser interceptado e usado por uma parte não planejada.

Alguns tokens de segurança têm um mecanismo interno que impede que partes não autorizadas os usem, mas os tokens de portador não têm esse mecanismo. Eles devem ser transportados em um canal seguro, como HTTPS (protocolo TLS). 

Se um token de portador for transmitido fora de um canal seguro, uma parte mal-intencionada poderá usar um ataque por parte de intermediários para adquirir o token e usá-lo para acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança são aplicáveis ao se armazenar ou manter em cache tokens de portador para uso posterior. Sempre se certifique de que seu aplicativo transmita e armazene tokens de portador de maneira segura.

Para obter mais considerações de segurança sobre tokens de portador, confira [RFC 6750 seção 5](http://tools.ietf.org/html/rfc6750).

Há mais informações sobre os diferentes tipos de tokens usados no Azure AD B2C disponíveis na [referência de token do Azure AD](active-directory-b2c-reference-tokens.md).

## Protocolos
Quando você estiver pronto para ver algumas solicitações de exemplo, inicie com um dos seguintes tutoriais. Cada um corresponde a um cenário de autenticação específico. Se precisar de ajuda para determinar qual é o fluxo certo para você, confira [os tipos de aplicativos que você pode compilar usando o Azure AD B2C](active-directory-b2c-apps.md).

* [Compilar aplicativos nativos e móveis com o OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
* [Compilar aplicativos Web usando o OpenID Connect](active-directory-b2c-reference-oidc.md)
* [Criar aplicativos de página única com o fluxo implícito do OAuth 2.0](active-directory-b2c-reference-spa.md)

