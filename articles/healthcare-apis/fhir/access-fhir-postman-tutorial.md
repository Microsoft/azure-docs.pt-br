---
title: FHIR servidor do Postman no Azure – API do Azure para FHIR
description: Neste tutorial, veremos as etapas necessárias para usar o Postman para acessar um servidor FHIR. O Postman é útil para depurar aplicativos que acessam APIs.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 02/01/2021
ms.openlocfilehash: aa0b18b701c573d4b2542359cb45b2d7694e78bd
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017468"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Acessar a API do Azure para FHIR com o Postman

Um aplicativo cliente acessaria uma API FHIR por meio de uma [API REST](https://www.hl7.org/fhir/http.html). Talvez você também queira interagir diretamente com o servidor FHIR à medida que cria aplicativos, por exemplo, para depuração. Neste tutorial, veremos as etapas necessárias para usar o [Postman](https://www.getpostman.com/) para acessar um servidor FHIR. O Postman é uma ferramenta geralmente usada para depuração ao criar aplicativos que acessam APIs.

## <a name="prerequisites"></a>Pré-requisitos

- Um ponto de extremidade do FHIR no Azure. Você pode configurar isso usando a API do Azure para FHIR gerenciada ou o servidor FHIR de software livre para o Azure. Configure a API do Azure para FHIR gerenciada usando o [portal do Azure](fhir-paas-portal-quickstart.md), o [PowerShell](fhir-paas-powershell-quickstart.md) ou a [CLI do Azure](fhir-paas-cli-quickstart.md).
- Um [aplicativo cliente](register-confidential-azure-ad-client-app.md) que você usará para acessar o serviço FHIR.
- Você concedeu permissões, por exemplo, "Colaborador de dados do FHIR", ao aplicativo cliente para acessar o serviço FHIR. Mais informações em [Configurar o RBAC do Azure para FHIR](configure-azure-rbac.md)
- Postman instalado. Você pode obtê-lo em [https://www.getpostman.com](https://www.getpostman.com)

## <a name="fhir-server-and-authentication-details"></a>Servidor FHIR e detalhes de autenticação

Para usar o Postman, os seguintes detalhes são necessários:

- A URL do servidor FHIR, por exemplo `https://MYACCOUNT.azurehealthcareapis.com`
- O provedor de identidade `Authority` para seu servidor FHIR, por exemplo, `https://login.microsoftonline.com/{TENANT-ID}`
- O `audience` configurado. Normalmente é a URL do servidor FHIR, por exemplo, `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` ou apenas `https://azurehealthcareapis.com`.
- O `client_id` (ou a ID do aplicativo) do [aplicativo cliente](register-confidential-azure-ad-client-app.md) você usará para acessar o serviço FHIR.
- O `client_secret` (ou o segredo do aplicativo) do aplicativo cliente.

Por fim, você deve verificar se `https://www.getpostman.com/oauth2/callback` é uma URL de resposta registrada para seu aplicativo cliente.

## <a name="connect-to-fhir-server"></a>Conectar ao servidor do FHIR

Usando o Postman, faça uma solicitação de `GET` para `https://fhir-server-url/metadata`:

![Instrução de capacidade de metadados do Postman](media/tutorial-postman/postman-metadata.png)

A URL de metadados para a API do Azure para FHIR é `https://MYACCOUNT.azurehealthcareapis.com/metadata`. Neste exemplo, a URL do servidor FHIR é `https://fhirdocsmsft.azurewebsites.net` e a instrução de capacidade do servidor está disponível em `https://fhirdocsmsft.azurewebsites.net/metadata`. Esse ponto de extremidade deve estar acessível sem autenticação.

Se você tentar acessar recursos restritos, deverá obter uma resposta de "Falha na autenticação":

![Falha na autenticação](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Como obter um token de acesso

Para obter um token de acesso válido, selecione "Autorização" e escolha o tipo "OAuth 2.0":

![Definir o OAuth 2.0](media/tutorial-postman/postman-select-oauth2.png)

Pressione "Obter Novo Token de Acesso" e uma caixa de diálogo será exibida:

![Solicitar Novo Token de Acesso](media/tutorial-postman/postman-request-token.png)

Você precisará de alguns detalhes:

| Campo                 | Exemplo de valor                                                                                                   | Comentário                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Nome do Token            | MYTOKEN                                                                                                         | Um nome de sua escolha          |
| Tipo de concessão            | Código de Autorização                                                                                              |                            |
| URL de retorno de chamada          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| URL de autenticação              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` é `https://MYACCOUNT.azurehealthcareapis.com` para a API do Azure para FHIR |
| URL do Token de Acesso      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| ID do Cliente             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | ID do aplicativo             |
| Segredo do cliente         | `XXXXXXXX`                                                                                                        | Chave do cliente secreta          |
| Escopo | `<Leave Blank>` |
| Estado                 | `1234`                                                                                                            |                            |
| Autenticação de cliente | Enviar credenciais do cliente no corpo                                                                                 |                 

Pressione "Token de Solicitação" e você será guiado pelo fluxo de autenticação do Azure Active Directory e um token será retornado para o Postman. Se você tiver problemas, abra o console do Postman (no item de menu "Exibir->Mostrar Console do Postman").

Role para baixo na tela de token retornada e pressione "Usar Token":

![Usar Token](media/tutorial-postman/postman-use-token.png)

O token agora deve ser preenchido no campo "Token de Acesso" e você pode selecionar tokens em "Tokens Disponíveis". Se você "Enviar" novamente para repetir a pesquisa de recursos `Patient`, deverá obter um status `200 OK`:

![200 OK](media/tutorial-postman/postman-200-OK.png)

Nesse caso, não há pacientes no banco de dados e a pesquisa está vazia.

Se você inspecionar o token de acesso com uma ferramenta como [https://jwt.ms](https://jwt.ms), deverá ver o conteúdo como:

```jsonc
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

Em situações de solução de problemas, a validação de que você tem o público-alvo correto (declaração `aud`) é um bom ponto de partida. Se o token for do emissor correto (declaração `iss`) e tiver o público-alvo correto (declaração `aud`), mas você ainda não conseguir acessar a API do FHIR, é provável que o usuário ou a entidade de serviço (declaração `oid`) não tenha acesso ao plano de dados do FHIR. Recomendamos [usar o Azure RBAC (controle de acesso baseado em função do Azure)](configure-azure-rbac.md) para atribuir funções de plano de dados aos usuários. Se você estiver usando um locatário do Azure Active Directory secundário e externo para seu plano de dados, será necessário [configurar as atribuições de RBAC locais](configure-local-rbac.md).

Também é possível [obter um token para a API do Azure para FHIR usando a CLI do Azure](get-healthcare-apis-access-token-cli.md). Se você estiver usando um token obtido com a CLI do Azure, deverá usar o tipo de autorização "Token do Portador" e colar o token diretamente.

## <a name="inserting-a-patient"></a>Como inserir um paciente

Agora que você tem um token de acesso válido, pode inserir um novo paciente. Alterne para o método "POST" e adicione o seguinte documento JSON ao corpo da solicitação:

[!code-json[](../samples/sample-patient.json)]

Pressione "Enviar" e poderá ver que o paciente foi criado com êxito:

![Captura de tela que mostra como o paciente foi criado com êxito.](media/tutorial-postman/postman-patient-created.png)

Se você repetir a pesquisa de pacientes, agora deverá ver o registro de pacientes:

![Paciente Criado](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você acessou uma API do FHIR usando o Postman. Leia sobre os recursos de API com suporte em nossa seção de recursos com suporte.
 
>[!div class="nextstepaction"]
>[Recursos compatíveis](fhir-features-supported.md)