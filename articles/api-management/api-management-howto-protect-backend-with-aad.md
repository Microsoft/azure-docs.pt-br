---
title: Proteger uma API usando OAuth 2.0 com o Azure Active Directory e o Gerenciamento de API | Microsoft Docs
description: Saiba como proteger um back-end da API da Web com o Azure Active Directory e Gerenciamento de API.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2019
ms.author: apimpm
ms.openlocfilehash: 72cffea3e5d42210bffbdbeef94c475cc8bdebf4
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312093"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Proteger uma API usando OAuth 2.0 com o Azure Active Directory e o Gerenciamento de API

Este guia mostra como configurar sua instância do Gerenciamento de API do Azure para proteger uma API usando o protocolo OAuth 2.0 com o Microsoft Azure Active Directory (Azure AD). 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir as etapas deste artigo, você precisa ter:
* Uma instância de Gerenciamento de API
* Uma API sendo publicada que use a instância de Gerenciamento de API
* Um locatário do Azure AD

## <a name="overview"></a>Visão geral

A seguir é apresentada uma visão geral das etapas:

1. Registrar um aplicativo (aplicativo de back-end) no Azure AD para representar a API.
2. Registrar outro aplicativo (aplicativo cliente) no Azure AD para representar um aplicativo cliente que precisa chamar a API.
3. No Azure AD, conceder permissões para permitir que o aplicativo cliente chame o aplicativo de back-end.
4. Configurar o Console do Desenvolvedor para usar a autorização de usuário OAuth 2.0.
5. Adicionar política **validate-jwt** para validar o token OAuth para cada solicitação de entrada.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrar um aplicativo no Azure AD para representar a API

Para proteger uma API com o Azure AD, a primeira etapa é registrar no Azure AD um aplicativo que represente a API. 

1. Navegue até a página [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) . 

2. Selecione **Novo registro**. 

1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo: 
    - Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo, por exemplo, `backend-app`. 
    - Na seção **tipos de conta com suporte** , selecione **contas em qualquer diretório organizacional**. 

1. Deixe a seção redirecionar **URI** vazia por enquanto.

1. Selecione **Registrar** para criar o aplicativo. 

1. Na página **Visão geral** do aplicativo, localize o valor de **ID do aplicativo (cliente)** e registre-o para uso posterior.

Quando o aplicativo for criado, anote a **ID do aplicativo** para uso em uma etapa posterior. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrar outro aplicativo no Azure AD para representar um aplicativo cliente

Todos os aplicativos cliente que chamam a API também precisam ser registrados como aplicativos no Microsoft Azure Active Directory. Neste exemplo, o aplicativo cliente de exemplo é o Console do Desenvolvedor no Portal do Desenvolvedor do Gerenciamento de API. É assim que se registra outro aplicativo no Microsoft Azure Active Directory para representar o Console do Desenvolvedor.

1. Navegue até a página [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) . 

1. Selecione **Novo registro**.

1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo: 
    - Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo, por exemplo, `client-app`. 
    - Na seção **tipos de conta com suporte** , selecione **contas em qualquer diretório organizacional**. 

1. Na seção **URI** de redirecionamento `Web` , selecione e insira a URL`https://contoso5.portal.azure-api.net/signin`

1. Selecione **Registrar** para criar o aplicativo. 

1. Na página **Visão geral** do aplicativo, localize o valor de **ID do aplicativo (cliente)** e registre-o para uso posterior.

Agora, crie um segredo do cliente para esse aplicativo, para uso em uma etapa posterior.

1. Na lista de páginas para seu aplicativo cliente, selecione **certificados & segredos**e selecione **novo segredo do cliente**.

2. Em **Adicionar um segredo do cliente**, forneça uma **Descrição**. Escolha quando a chave deve expirar e selecione **Adicionar**.

Anote o valor da chave. 

## <a name="grant-permissions-in-azure-ad"></a>Conceder permissões no Azure AD

Agora que você já registrou um aplicativo para representar a API e outro para representar o Console do Desenvolvedor, é preciso conceder permissões para permitir que o aplicativo de cliente chame o aplicativo de back-end.  

1. Navegue até **registros de aplicativo**. 

2. Selecione `client-app`e, na lista de páginas para o aplicativo, acesse **permissões de API**.

3. Selecione **Adicionar uma permissão**.

4. Em **selecionar uma API**, localize e selecione `backend-app`.

5. Em **permissões delegadas**, selecione as permissões apropriadas para `backend-app`.

6. Selecione **adicionar permissões** 

> [!NOTE]
> Se o **Microsoft Azure Active Directory** não estiver nas permissões para outros aplicativos, clique em **Adicionar** para adicioná-lo a partir da lista.

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Habilitar a autorização de usuário OAuth 2.0 no Console do Desenvolvedor

Você já criou seus aplicativos no Azure AD e concedeu as permissões adequadas para permitir que o aplicativo cliente chame o aplicativo back-end. 

Neste exemplo, o Console do Desenvolvedor é o aplicativo cliente. As etapas a seguir descrevem como habilitar a autorização de usuário OAuth 2.0 no Console do Desenvolvedor. 

1. Em portal do Azure, navegue até sua instância de gerenciamento de API.

2. Selecione **OAuth 2.0** > **Adicionar**.

3. Forneça um **Nome de exibição** e uma **Descrição**.

4. Para **URL da Página de registro do cliente**, insira um valor de espaço reservado como `http://localhost`. A **URL da página de registro do cliente** aponta para a página que os usuários podem usar para criar e configurar contas próprias para os provedores OAuth 2.0 que dão suporte a isso. Neste exemplo, como os usuários não criam nem configuram contas próprias, é usado um espaço reservado.

5. Para **Tipos de concessão de autorização**, selecione **Código de autorização**.

6. Especifique a **URL de ponto de extremidade de autorização** e a **URL de ponto de extremidade de token**. Recupere esses valores da página **Pontos de extremidade** no locatário do Azure AD. Volte à página **Registros de aplicativo** e clique em **Pontos de extremidade**.

7. Copie o **ponto de extremidade da autorização OAuth 2.0** e cole-o na caixa de texto **URL do Ponto de Extremidade da Autorização**.

8. Copie o **ponto de extremidade do token OAuth 2.0** e cole-o na caixa de texto **URL do Ponto de Extremidade do Token**. Além disso, adicione um parâmetro de corpo denominado **recurso**. Para o valor desse parâmetro, use a **ID do aplicativo** de back-end.

9. Em seguida, especifique as credenciais do cliente. Essas são as credenciais para o aplicativo cliente.

10. Para **ID do cliente**, use a **ID do aplicativo** do aplicativo cliente.

11. Para **Segredo do cliente**, use a chave que você criou para o aplicativo cliente anteriormente. 

12. Logo após o segredo do cliente, está a **URL de redirecionamento** para o tipo de concessão de código de autorização. Anote essa URL.

13. Clique em **Criar**.

14. Volte para a página **Configurações** do aplicativo cliente.

15. Selecione **URLs de resposta** e cole a **URL de redirecionamento** na primeira linha. Neste exemplo, você substituiu `https://localhost` pela URL na primeira linha.  

Agora que você já configurou um servidor de autorização OAuth 2.0, o Console do Desenvolvedor pode obter tokens de acesso do Azure AD. 

A próxima etapa é habilitar a autorização de usuário do OAuth 2.0 para a sua API. Isso permite que o Console do Desenvolvedor saiba o que é necessário para obter um token de acesso em nome do usuário antes de fazer chamadas à API.

1. Navegue até instância de Gerenciamento de API e vá para **APIs**.

2. Selecione a API que você deseja proteger. Neste exemplo, você usará a `Echo API`.

3. Vá para **Configurações**.

4. Em **Segurança**, escolha **OAuth 2.0** e selecione o servidor OAuth 2.0 configurado anteriormente. 

5. Clique em **Salvar**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Chamar com êxito a API do portal do desenvolvedor

> [!NOTE]
> Esta seção não se aplica para a camada **consumo**, que não oferece suporte para o portal do desenvolvedor.

Agora que a autorização de usuário OAuth 2.0 está habilitada na `Echo API`, o Console do Desenvolvedor obtém um token de acesso em nome do usuário antes de chamar a API.

1. Navegue até qualquer operação na `Echo API` no portal do desenvolvedor e clique em **Experimentar**. Isso o levará ao Console do Desenvolvedor.

2. Note que há um novo item na seção **Autorização**. Ele corresponde ao servidor de autorização que você acaba de adicionar.

3. Selecione **Código de autorização** na lista suspensa de autorização. Quando você fizer isso, será solicitado o logon no locatário do Azure AD. O logon pode não ser solicitado caso você já esteja conectado com a conta.

4. Após o logon com êxito, será adicionado um cabeçalho de `Authorization` à solicitação com um token de acesso do Azure AD. Veja abaixo um exemplo de token (com a codificação Base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Clique em **Enviar**. Pronto! Agora você já pode chamar a API com êxito.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurar uma política de validação de JWT para pré-autorizar solicitações

Neste ponto, quando um usuário tenta fazer uma chamada a partir do Console do Desenvolvedor, é solicitado o logon. O Console do Desenvolvedor obtém um token de acesso em nome do usuário.

No entanto, e se alguém chamar a API sem um token ou com um token inválido? Por exemplo, você pode chamar a API mesmo que tenha excluído o cabeçalho de `Authorization`. Isso porque o Gerenciamento de API não valida o token de acesso nesse momento. Ele simplesmente passa o cabeçalho de `Authorization` para a API de back-end.

Você pode usar a política [Validar JWT](api-management-access-restriction-policies.md#ValidateJWT) para pré-autorizar solicitações no Gerenciamento de API, validando os tokens de acesso de cada solicitação de entrada. Se uma solicitação não tiver um token válido, o Gerenciamento de API a bloqueará. Por exemplo, você pode adicionar a política a seguir à seção de política `<inbound>` da `Echo API`. Ela verifica a declaração de audiência em um token de acesso e retorna uma mensagem de erro se o token não é válido. Para obter informações sobre como configurar as políticas, confira [Definir ou editar políticas](set-edit-policies.md).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="build-an-application-to-call-the-api"></a>Criar um aplicativo para chamar a API

Neste guia, você usou o Console do Desenvolvedor no Gerenciamento de API como o aplicativo cliente de exemplo para chamar a `Echo API` protegida pelo OAuth 2.0. Para saber mais sobre como criar um aplicativo e implementar o OAuth 2.0, confira [Exemplos de código do Microsoft Azure Active Directory](../active-directory/develop/sample-v1-code.md).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [Azure Active Directory e o OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre o Gerenciamento de API.
* Para outras maneiras de proteger seu serviço de back-end, confira [Autenticação de certificado mútuo](api-management-howto-mutual-certificates.md).

* [Crie uma instância do serviço de Gerenciamento de API](get-started-create-service-instance.md).

* [Gerencie sua primeira API](import-and-publish.md).
