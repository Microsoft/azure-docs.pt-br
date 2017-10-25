---
title: "Usar a autenticação do Azure AD para acessar a API dos Serviços de Mídia do Azure com REST | Microsoft Docs"
description: "Saiba como acessar a API dos Serviços de Mídia do Azure com a autenticação do Azure Active Directory usando REST."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: willzhan;juliako
ms.openlocfilehash: 1c62857699fb29b3583363e1c6f2dc7874635f40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Usar a autenticação do Azure AD para acessar a API dos Serviços de Mídia do Azure com REST

A equipe dos Serviços de Mídia do Azure lançou o suporte de autenticação do Azure Active Directory (Azure AD) para acesso aos Serviços de Mídia do Azure. Ela também anunciou planos para substituir a autenticação do serviço de Controle de Acesso do Azure para acesso aos Serviços de Mídia. Como cada assinatura do Azure e todas as contas dos Serviços de Mídia são anexadas a um locatário do Azure AD, o suporte de autenticação do Azure AD traz muitos benefícios de segurança. Para obter detalhes sobre essa alteração e migração (se você usar o SDK .NET dos Serviços de Mídia para seu aplicativo), consulte os seguintes artigos e postagens no blog:

- [Os Serviços de Mídia do Azure anuncia suporte para o Azure AD e substituição da autenticação de Controle de Acesso](https://azure.microsoft.com/blog/azure%20media%20service%20aad%20auth%20and%20acs%20deprecation)
- [Acessar a API dos Serviços de Mídia do Azure utilizando a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md)
- [Usar a autenticação do Azure AD para acessar a API dos Serviços de Mídia do Azure utilizando o Microsoft .NET](media-services-dotnet-get-started-with-aad.md)
- [Introdução à autenticação do Azure AD utilizando o Portal do Azure](media-services-portal-get-started-with-aad.md)

Alguns clientes precisam desenvolver suas soluções de Serviços de Mídia sob as seguintes restrições:

*   Utilizar uma linguagem de programação que não seja Microsoft .NET ou C#, ou o ambiente de tempo de execução não seja Windows.
*   Bibliotecas do Azure AD, como Bibliotecas de Autenticação do Active Directory não estão disponíveis para a linguagem de programação ou não podem ser utilizadas para seu ambiente de tempo de execução.

Alguns clientes desenvolveram aplicativos utilizando API REST tanto para autenticação de Controle de Acesso quanto para acesso aos Serviços de Mídia do Azure. Para esses clientes, você precisa de uma maneira de usar apenas a API REST para autenticação do Azure AD e o acesso subsequente aos Serviços de Mídia do Azure. Você precisa não confiar em nenhuma das bibliotecas do Azure AD ou no SDK do .NET dos Serviços de Mídia. Este artigo descreve uma solução e fornece o código de exemplo para esse cenário. Como o código são todas as chamadas de API REST, sem dependência de qualquer biblioteca dos Serviços de Mídia do Azure ou Azure AD, o código pode ser facilmente traduzido para qualquer outra linguagem de programação.

> [!IMPORTANT]
> Atualmente, os Serviços de Mídia dão suporte ao modelo de autenticação de serviços do Controle de Acesso do Azure. No entanto, a autenticação de Controle de Acesso será preterida em 1º de junho de 2018. É recomendável que você migre para o modelo de autenticação do Azure AD assim que possível.


## <a name="design"></a>Design

Neste artigo, usamos o design de autenticação e autorização a seguir:

*  Protocolo de autorização: OAuth 2.0. OAuth 2.0 é um padrão de segurança na Web que abrange a autenticação e autorização. Há suporte pela Microsoft, Google, Facebook e PayPal. Ele foi ratificado em outubro de 2012. A Microsoft suporta firmemente OAuth 2.0 e OpenID Connect. Ambos os padrões são suportados em vários serviços e bibliotecas de clientes, incluindo Azure Active Directory, Open Web Interface para Katana .NET (OWIN) e bibliotecas do Azure AD.
*  Tipo de concessão: tipo de concessão de credenciais do cliente. As credenciais do cliente são um dos quatro tipos de concessão no OAuth 2.0. Muitas vezes é utilizado para acessar API do Microsoft Azure AD Graph.
*  Modo de autenticação: entidade de serviço. O outro modo de autenticação é a autenticação interativa ou de usuário.

Um total de quatro aplicativos ou serviços estão envolvidos no fluxo de autorização e autenticação do AD Azure para utilizar os Serviços de Mídia. Os aplicativos, serviços e o fluxo estão descritos na tabela a seguir:

|Tipo de aplicativo |Aplicativo |Flow|
|---|---|---|
|Cliente | Aplicativo ou solução de cliente | Este aplicativo (efetivamente, seu proxy) é registrado no locatário do Azure AD no qual residem a assinatura do Azure e a conta de serviço de mídia. A entidade de serviço de aplicativo registrado é então concedido com a função Proprietário ou Colaborador no Controle de Acesso (IAM) da conta de serviço de mídia. A entidade de serviço é representada pelo ID do cliente do aplicativo e segredo do cliente. |
|IDP (Provedor de identidade) | Azure AD como IDP | A entidade de serviço de aplicativo registrado (ID do cliente e segredo do cliente) é autenticada pelo Azure AD como IDP. Essa autenticação é executada internamente e implicitamente. Como o fluxo de credenciais do cliente, o cliente é autenticado em vez do usuário. |
|STS (Serviço de Token de Segurança)/servidor OAuth | Azure AD como STS | Após a autenticação pelo IDP (ou servidor OAuth em termos de OAuth 2.0), um token de acesso ou token Web JSON (JWT) é emitido pelo Azure AD como STS/servidor OAuth para acesso ao recurso de camada intermediária: nesse caso, o ponto de extremidade de API REST dos Serviços de Mídia. |
|Recurso | API REST dos Serviços de Mídia | Cada chamada à API REST dos Serviços de mídia é autorizada por um token de acesso que é emitido pelo Azure AD como STS ou servidor OAuth. |

Se executar o código de exemplo e capturar um JWT ou um token de acesso, o JWT possuirá os seguintes atributos:

    aud: "https://rest.media.azure.net",

    iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    iat: 1497146280,

    nbf: 1497146280,
    exp: 1497150180,

    aio: "Y2ZgYDjuy7SptPzO/muf+uRu1B+ZDQA=",

    appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6",

    appidacr: "1",

    idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    oid: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    sub: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",

Aqui estão os mapeamentos entre os atributos no JWT e os quatro aplicativos ou serviços na tabela anterior:

|Tipo de aplicativo |Aplicativo |Atributo JWT |
|---|---|---|
|Cliente |Aplicativo ou solução de cliente |appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6". O ID do cliente de um aplicativo que será registrado no Azure AD na próxima seção. |
|IDP (Provedor de identidade) | Azure AD como IDP |idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/".  O GUID é o ID do locatário da Microsoft (microsoft.onmicrosoft.com). Cada locatário tem sua própria ID exclusiva. |
|STS (Serviço de Token de Segurança)/servidor OAuth |Azure AD como STS | iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/". O GUID é o ID do locatário da Microsoft (microsoft.onmicrosoft.com). |
|Recurso | API REST dos Serviços de Mídia |aud: "https://rest.media.azure.net". O destinatário ou audiência do token de acesso. |

## <a name="steps-for-setup"></a>Etapas para configuração

Para registrar e configurar um aplicativo do Azure AD para a autenticação do Azure AD e obter um token de acesso para chamada do ponto de extremidade de API REST dos Serviços de Mídia do Azure, conclua as seguintes etapas:

1.  No [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885), registre um aplicativo do AD (por exemplo, wzmediaservice) para o locatário do Azure AD (por exemplo, microsoft.onmicrosoft.com). Independe se foi registrado como aplicativo Web ou nativo. Além disso, é possível escolher qualquer URL de login e URL de resposta (por exemplo, http://wzmediaservice.com para ambos).
2. No [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885), vá para a guia **Configurar** de seu aplicativo. Anote o **ID do cliente**. Em seguida, em **Chaves**, gere uma **chave do cliente** (segredo do cliente). 

    > [!NOTE] 
    > Anote o segredo do cliente. Não será mostrado novamente.
    
3.  No [Portal do Azure](http://ms.portal.azure.com), vá para a conta dos Serviços de Mídia. Selecione o painel **Controle de Acesso** (IAM). Adicione um novo membro que tenha a função de Colaborador ou Proprietário. Para a entidade de segurança, procure o nome do aplicativo registrado na etapa 1 (neste exemplo, wzmediaservice).

## <a name="info-to-collect"></a>Informações para coletar

Para preparar a codificação REST, colete os seguintes pontos de dados para incluir no código:

*   Azure AD como um ponto de extremidade STS: https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token. A partir deste ponto de extremidade, um token de acesso JWT é solicitado. Além de servir como IDP, o Azure AD também serve como STS. O Azure AD emite um JWT para acesso ao recurso (um token de acesso). Um token JWT tem várias declarações.
*   API REST dos Serviços de Mídia do Azure como recurso ou audiência: https://rest.media.azure.net.
*   ID do cliente: consulte a etapa 2 em[Etapas para configuração](#steps-for-setup).
*   Segredo do cliente: veja o passo 2 em  [Etapas para configuração](#steps-for-setup).
*   O ponto de extremidade de API REST dos Serviços de Mídia no seguinte formato:

    https://[media_service_account_name].restv2.[data_center].media.azure.net/API 

    Este é o ponto de extremidade pelo qual todas as chamadas de API REST dos Serviços de Mídia em seu aplicativo são feitas. Por exemplo, https://willzhanmswjapan.restv2.japanwest.media.azure.net/API.

Você poderá então colocar esses cinco parâmetros em seu arquivo web.config ou app.config para usar em seu código.

## <a name="sample-code"></a>Exemplo de código

É possível localizar o código de exemplo em [Autenticação do Azure AD para acesso aos Serviços de Mídia do Azure: ambos via API REST](https://github.com/willzhan/WAMSRESTSoln).

O código de exemplo tem duas partes:

*   Um projeto de biblioteca DLL que tem todo o código API REST para autenticação e autorização do Azure AD. Ele também tem um método para fazer chamadas da API REST para o ponto de extremidade de API REST dos Serviços de Mídia, com o token de acesso.
*   Um cliente de teste de console que inicia a autenticação do Azure AD e chama diferentes API REST de Serviços de Mídia.

O projeto de exemplo tem três recursos:

*   Autenticações do Azure AD através das credenciais do cliente concede utilizando apenas a API REST.
*   Acesso aos Serviços de Mídia do Azure utilizando a API REST.
*   Acesso de Armazenamento do Azure utilizando a API REST (conforme usado para criar uma conta de Serviços de Mídia, usando a API REST).


## <a name="where-is-the-refresh-token"></a>Onde está o token de atualização?

Alguns leitores podem perguntar: Onde está o token de atualização? Por que não utilizar um token de atualização aqui?

A finalidade de um token de atualização não é um token de acesso de atualização. Em vez disso, ele é projetado para ignorar a autenticação do usuário final ou a intervenção do usuário e ainda obter um token de acesso válido quando um token anterior expirar. Um nome melhor para um token de atualização pode ser algo como "token de re-entada de usuário de bypass."

Se você utilizar o fluxo de concessão de autorização OAuth 2.0 (nome de usuário e senha, atuando em nome de um usuário), um token de atualização ajudará a obter um token de acesso renovado sem solicitar a intervenção do usuário. No entanto, para o fluxo de concessão de credenciais do cliente OAuth 2.0 descrito neste artigo, o cliente atua em seu próprio nome. Não é necessária a intervenção do usuário e o servidor de autorização não precisa (e não irá) fornecer um token de atualização. Se você depurar o método **GetUrlEncodedJWT** você perceberá que a resposta do ponto de extremidade do token possui um token de acesso, mas não há token de atualização.

## <a name="next-steps"></a>Próximas etapas

Comece a [carregar arquivos para sua conta](media-services-dotnet-upload-files.md).
