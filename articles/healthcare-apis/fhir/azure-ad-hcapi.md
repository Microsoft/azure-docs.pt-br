---
title: Configuração de identidade Azure Active Directory para a API do Azure para FHIR
description: Conheça os princípios de identidade, autenticação e autorização para servidores FHIR do Azure.
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 277838e3ce870b528f986292f88ad2b2b20f42b1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017585"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>Configuração de identidade Azure Active Directory para a API do Azure para FHIR

Uma parte importante ao trabalhar com dados de saúde é garantir que os dados sejam seguros e não possam ser acessados por usuários ou aplicativos não autorizados. Os servidores FHIR usam o [OAuth 2,0](https://oauth.net/2/) para garantir essa segurança de dados. A [API do Azure para FHIR](https://azure.microsoft.com/services/azure-api-for-fhir/) é protegida usando [Azure Active Directory](../../active-directory/index.yml), que é um exemplo de um provedor de identidade OAuth 2,0. Este artigo fornece uma visão geral da autorização do FHIR Server e das etapas necessárias para obter um token para acessar um servidor FHIR. Embora essas etapas sejam aplicadas a qualquer servidor FHIR e qualquer provedor de identidade, veremos a API do Azure para FHIR como o servidor FHIR e o Azure AD como nosso provedor de identidade neste artigo.

## <a name="access-control-overview"></a>Visão geral do controle de acesso

Para que um aplicativo cliente acesse a API do Azure para FHIR, ele deve apresentar um token de acesso. O token de acesso é uma coleção de Propriedades (declarações) codificada em [Base64](https://en.wikipedia.org/wiki/Base64) que transmite informações sobre a identidade do cliente e as funções e privilégios concedidos ao cliente.

Há várias maneiras de obter um token, mas a API do Azure para FHIR não importa como o token é obtido desde que seja um token assinado adequadamente com as declarações corretas. 

Usando o [fluxo de código de autorização](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) como exemplo, o acesso a um servidor FHIR passa pelas quatro etapas abaixo:

![Autorização de FHIR](media/azure-ad-hcapi/fhir-authorization.png)

1. O cliente envia uma solicitação ao `/authorize` ponto de extremidade do Azure AD. O AD do Azure redirecionará o cliente para uma página de entrada na qual o usuário será autenticado usando as credenciais apropriadas (por exemplo, nome de usuário e senha ou autenticação de dois fatores). Veja detalhes sobre [como obter um código de autorização](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code). Após a autenticação bem-sucedida, um *código de autorização* é retornado para o cliente. O Azure AD permitirá que esse código de autorização seja retornado a uma URL de resposta registrada configurada no registro do aplicativo cliente (veja abaixo).
1. O aplicativo cliente troca o código de autorização para um *token de acesso* no `/token` ponto de extremidade do Azure AD. Ao solicitar um token, o aplicativo cliente pode precisar fornecer um segredo do cliente (a senha dos aplicativos). Consulte os detalhes sobre [como obter um token de acesso](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).
1. O cliente faz uma solicitação para a API do Azure para FHIR, por exemplo, `GET /Patient` para pesquisar todos os pacientes. Ao fazer a solicitação, ele inclui o token de acesso em um cabeçalho de solicitação HTTP, por exemplo `Authorization: Bearer eyJ0e...` , onde `eyJ0e...` representa o token de acesso codificado em base64.
1. A API do Azure para FHIR valida que o token contém declarações apropriadas (Propriedades no token). Se tudo fizer check-out, ele concluirá a solicitação e retornará um pacote FHIR com os resultados para o cliente.

É importante observar que a API do Azure para FHIR não está envolvida na validação de credenciais de usuário e não emite o token. A autenticação e a criação de tokens são feitas pelo Azure AD. A API do Azure para FHIR simplesmente valida que o token está assinado corretamente (é autêntico) e que tem declarações apropriadas.

## <a name="structure-of-an-access-token"></a>Estrutura de um token de acesso

O desenvolvimento de aplicativos FHIR geralmente envolve a depuração de problemas de acesso. Se um cliente tiver acesso negado à API do Azure para FHIR, será útil entender a estrutura do token de acesso e como ele pode ser decodificado para inspecionar o conteúdo (as declarações) do token. 

Os servidores FHIR normalmente esperam um [token Web JSON](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT, às vezes, pronuncia-se "jot"). Ele consiste em três partes:

1. Um cabeçalho, que poderia ser semelhante a:
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
1. A carga (as declarações), por exemplo:
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```
1. Uma assinatura, que é calculada concatenando o conteúdo codificado em base64 do cabeçalho e a carga e calculando um hash criptográfico deles com base no algoritmo ( `alg` ) especificado no cabeçalho. Um servidor poderá obter chaves públicas do provedor de identidade e validar que esse token foi emitido por um provedor de identidade específico e que ele não foi violado.

O token completo consiste nas versões codificadas em Base64 (na verdade, codificadas para URL Base64) desses três segmentos. Os três segmentos são concatenados e separados por um `.` (ponto).

Um token de exemplo é visto abaixo:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

O token pode ser decodificado e inspecionado com ferramentas como [https://jwt.ms](https://jwt.ms) . O resultado da decodificação do token é:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>Como obter um token de acesso

Conforme mencionado acima, há várias maneiras de obter um token do Azure AD. Elas são descritas em detalhes na [documentação do desenvolvedor do Azure ad](../../active-directory/develop/index.yml).

O Azure AD tem duas versões diferentes dos pontos de extremidade do OAuth 2,0, que são chamados de `v1.0` e `v2.0` . Essas duas versões são pontos de extremidade do OAuth 2,0, e `v1.0` as `v2.0` designações são relacionadas às diferenças em como o Azure ad implementa esse padrão. 

Ao usar um servidor FHIR, você pode usar os `v1.0` `v2.0` pontos de extremidade ou. A escolha pode depender das bibliotecas de autenticação que você está usando em seu aplicativo cliente.

As seções pertinentes da documentação do Azure AD são:

* Ponto de extremidade `v1.0`:
    * [Fluxo de código de autorização](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).
    * [Fluxo de credenciais do cliente](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md).
* Ponto de extremidade `v2.0`:
    * [Fluxo de código de autorização](../../active-directory/develop/v2-oauth2-auth-code-flow.md).
    * [Fluxo de credenciais do cliente](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md).

Há outras variações (por exemplo, em nome do fluxo) para obter um token. Verifique a documentação do Azure AD para obter detalhes. Ao usar a API do Azure para FHIR, também há alguns atalhos para obter um token de acesso (para fins de depuração) [usando o CLI do Azure](get-healthcare-apis-access-token-cli.md).

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu alguns dos conceitos básicos envolvidos na proteção do acesso à API do Azure para FHIR usando o Azure AD. Para saber como implantar uma instância da API do Azure para FHIR, prossiga para o guia de início rápido de implantação.

>[!div class="nextstepaction"]
>[Implantar a API do Azure para FHIR](fhir-paas-portal-quickstart.md)