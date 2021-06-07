---
title: FHIR servidor do Postman no Azure – API do Azure para FHIR
description: Neste tutorial, vamos percorrer as etapas necessárias para usar o postmaster para acessar um servidor FHIR. O Postman é útil para depurar aplicativos que acessam APIs.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 03/26/2021
ms.openlocfilehash: 59847f745037acec47415489cdf61d119a7807af
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936267"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Acessar a API do Azure para FHIR com o Postman

Um aplicativo cliente pode acessar a API do Azure para FHIR por meio de uma [API REST](https://www.hl7.org/fhir/http.html). Para enviar solicitações, exibir respostas e depurar seu aplicativo à medida que ele está sendo criado, use uma ferramenta de teste de API de sua escolha. Neste tutorial, vamos orientá-lo pelas etapas de acesso ao servidor FHIR usando o [postmaster](https://www.getpostman.com/). 

## <a name="prerequisites"></a>Pré-requisitos

- Um ponto de extremidade do FHIR no Azure. 

  Para implantar a API do Azure para FHIR (um serviço gerenciado), você pode usar o [portal do Azure](fhir-paas-portal-quickstart.md), o [PowerShell](fhir-paas-powershell-quickstart.md)ou o [CLI do Azure](fhir-paas-cli-quickstart.md).

- Um [aplicativo cliente confidencial](register-confidential-azure-ad-client-app.md) registrado para acessar o serviço FHIR.
- Você concedeu permissões ao aplicativo cliente confidencial, por exemplo, "FHIR data Contributor", para acessar o serviço FHIR. Para obter mais informações, consulte [Configurar o RBAC do Azure para FHIR](./configure-azure-rbac.md).
- Postman instalado. 
    
  Para obter mais informações sobre o postmaster, consulte Introdução [ao postmaster](https://www.getpostman.com).

## <a name="fhir-server-and-authentication-details"></a>Servidor FHIR e detalhes de autenticação

Para usar o postmaster, os seguintes parâmetros de autenticação são necessários:

- A URL do servidor FHIR, por exemplo, `https://MYACCOUNT.azurehealthcareapis.com`

- O provedor de identidade `Authority` para seu servidor FHIR, por exemplo, `https://login.microsoftonline.com/{TENANT-ID}`

- O configurado `audience` que geralmente é a URL do servidor FHIR, por exemplo, `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` ou `https://azurehealthcareapis.com` .

- A `client_id` ID do aplicativo ou o aplicativo do [cliente confidencial](register-confidential-azure-ad-client-app.md) usado para acessar o serviço FHIR.

- O `client_secret` ou o segredo do aplicativo do cliente confidencial.

Por fim, você deve verificar se `https://www.getpostman.com/oauth2/callback` é uma URL de resposta registrada para seu aplicativo cliente.

## <a name="connect-to-fhir-server"></a>Conectar ao servidor do FHIR

Abra o postmaster e selecione **Get** para fazer uma solicitação para `https://fhir-server-url/metadata` .

![Instrução de capacidade de metadados do Postman](media/tutorial-postman/postman-metadata.png)

A URL de metadados para a API do Azure para FHIR é `https://MYACCOUNT.azurehealthcareapis.com/metadata`. 

Neste exemplo, a URL do servidor FHIR é `https://fhirdocsmsft.azurewebsites.net` , e a declaração de funcionalidade do servidor está disponível em `https://fhirdocsmsft.azurewebsites.net/metadata` . Esse ponto de extremidade é acessível sem autenticação.

Se você tentar acessar recursos restritos, ocorrerá uma resposta "falha na autenticação".

![Falha na autenticação](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Como obter um token de acesso
Selecione **Obter novo token de acesso**.

Para obter um token de acesso válido, selecione **autorização** e selecione **OAuth 2,0** no menu suspenso **tipo** .

![Definir o OAuth 2.0](media/tutorial-postman/postman-select-oauth2.png)

Selecione **Obter novo token de acesso**.

![Solicitar Novo Token de Acesso](media/tutorial-postman/postman-request-token.png)

Na caixa de diálogo **obter novo token de acesso** , insira os seguintes detalhes:

| Campo                 | Exemplo de valor                                                                                                   | Comentário                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Nome do Token            | MYTOKEN                                                                                                         | Um nome de sua escolha          |
| Tipo de concessão            | Código de Autorização                                                                                              |                            |
| URL de retorno de chamada          | `https://www.getpostman.com/oauth2/callback`                                                                    |                            |
| URL de autenticação              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` é `https://MYACCOUNT.azurehealthcareapis.com` para a API do Azure para FHIR |
| URL do Token de Acesso      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                    |                            |
| ID do Cliente             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                           | ID do aplicativo             |
| Segredo do cliente         | `XXXXXXXX`                                                                                                      | Chave do cliente secreta          |
| Escopo | `<Leave Blank>` | O escopo não é usado; Portanto, pode ser deixado em branco.  
| Estado                 | `1234`     | [State](https://learning.postman.com/docs/sending-requests/authorization/) é um valor opaco para impedir falsificação de solicitação entre sites. É opcional e pode usar um valor arbitrário, como ' 1234 '.                           |
| Autenticação de cliente | Enviar credenciais do cliente no corpo                                                                                 |                 

Selecione o **token de solicitação** a ser guiado por meio do fluxo de autenticação Azure Active Directory e um token será retornado para o postmaster. Se ocorrer uma falha de autenticação, consulte o console do postmaster para obter mais detalhes. **Observação**: na faixa de opções, selecione **Exibir** e, em seguida, selecione **Mostrar console do postmaster**. O atalho de teclado para o console do postmaster é **ALT-CTRL + C**.

Role para baixo para exibir a tela token retornado e, em seguida, selecione **usar token**.

![Usar Token](media/tutorial-postman/postman-use-token.png)

Consulte o campo **token de acesso** para exibir o token populado recentemente. Se você selecionar **Enviar** para repetir a `Patient` pesquisa de recursos, um **status** `200 OK` será retornado. Um status retornado `200 OK` indica uma solicitação HTTP bem-sucedida.

![200 OK](media/tutorial-postman/postman-200-OK.png)

No exemplo de *pesquisa de paciente* , não há pacientes no banco de dados, de modo que o resultado da pesquisa esteja vazio.

Você pode inspecionar o token de acesso usando uma ferramenta como [JWT.ms](https://jwt.ms). Um exemplo do conteúdo é mostrado abaixo.

```json
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

Em situações de solução de problemas, a validação de que você tem o público-alvo correto (declaração `aud`) é um bom ponto de partida. Se o token for do emissor correto ( `iss` declaração) e tiver o público correto ( `aud` declaração), mas você ainda não conseguir acessar a API FHIR, é provável que o usuário ou a entidade de serviço ( `oid` declaração) não tenha acesso ao plano de dados FHIR. Recomendamos que você use o [controle de acesso baseado em função do Azure (RBAC do Azure)](configure-azure-rbac.md) para atribuir funções de plano de dados aos usuários. Se você estiver usando um locatário do Azure Active Directory secundário e externo para seu plano de dados, será necessário [Configurar o RBAC local para atribuições de FHIR](configure-local-rbac.md) .

Também é possível obter um token para a API do [Azure para FHIR usando o CLI do Azure](get-healthcare-apis-access-token-cli.md). Se você estiver usando um token obtido com o CLI do Azure, deverá usar o token de *portador* do tipo de autorização. Cole o token diretamente.

## <a name="inserting-a-patient"></a>Como inserir um paciente

Com um token de acesso válido, agora você pode inserir um novo paciente. No postmaster, altere o método selecionando **post** e, em seguida, adicione o seguinte documento JSON no corpo da solicitação.

[!code-json[](../samples/sample-patient.json)]

Selecione **Enviar** para determinar se o paciente foi criado com êxito.

![Captura de tela que mostra como o paciente foi criado com êxito.](media/tutorial-postman/postman-patient-created.png)

Se você repetir a pesquisa de pacientes, agora você deverá ver o registro de pacientes.

![Paciente Criado](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você acessou a API do Azure para FHIR usando o postmaster. Para obter mais informações sobre a API do Azure para recursos do FHIR, consulte
 
>[!div class="nextstepaction"]
>[Recursos compatíveis](fhir-features-supported.md)
