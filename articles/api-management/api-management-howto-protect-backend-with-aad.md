---
title: Proteger um back-end de API da Web com o Azure Active Directory e o Gerenciamento de API | Microsoft Docs
description: Saiba como proteger um back-end de API da Web com o Active Directory do Azure e Gerenciamento de API
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: f856ff03-64a1-4548-9ec4-c0ec4cc1600f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 0dfb4102904c2e972e6617fd3851fb1c50147357
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Como proteger um back-end de API da Web com o Active Directory do Azure e Gerenciamento de API
O vídeo a seguir mostra como criar um back-end de API da Web e protegê-lo usando o protocolo OAuth 2.0 com o Active Directory do Azure e Gerenciamento de API.  Este artigo fornece uma visão geral e informações adicionais sobre as etapas no vídeo. Este vídeo de 24 minutos mostra como:

* Criar um back-end de API da Web e protegê-lo com o AAD - iniciando em 1:30
* Importar a API para o Gerenciamento de APIs - iniciando em 7:10
* Configurar o portal do Desenvolvedor para chamar a API - iniciando em 9:09
* Configurar um aplicativo de área de trabalho para chamar a API - iniciando em 18:08
* Configurar uma política de validação de JWT para pré-autorizar solicitações - iniciando em 20:47

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Protecting-Web-API-Backend-with-Azure-Active-Directory-and-API-Management/player]
> 
> 

## <a name="create-an-azure-ad-directory"></a>Criar um diretório do AD do Azure
Para proteger sua API da Web usando o Active Directory do Azure, você deve primeiramente ter um locatário AAD. Neste vídeo, um locatário chamado **APIMDemo** é usado. Para criar um locatário do AAD, entre no [Portal Clássico do Azure](https://manage.windowsazure.com) e clique em **Novo**->**Serviço de Aplicativos**->**Active Directory**->**Diretório**->**Criação Personalizada**. 

![Azure Active Directory][api-management-create-aad-menu]

Neste exemplo, um diretório chamado **APIMDemo** é criado com um domínio padrão chamado **DemoAPIM.onmicrosoft.com**. Esse diretório é usado em todo o vídeo.

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Criar um serviço de API da Web protegido pelo Active Directory do Azure
Nesta etapa, um back-end de API da Web é criado usando o Visual Studio 2013. Esta etapa do vídeo começa em 1:30. Para criar o projeto de back-end de API da Web no Visual Studio clique em **Arquivo**->**Novo**->**Projeto** e escolha **Aplicativo Web ASP.NET** na lista de modelos da **Web**. Neste vídeo, o projeto é denominado **APIMAADDemo**. Clique em **OK** para criar o projeto. 

![Visual Studio][api-management-new-web-app]

Clique em **API Web** em **Selecionar uma lista de modelos** para criar um projeto de API da Web. Para configurar a Autenticação do Azure Directory clique em **Alterar Autenticação**.

![Novo Projeto][api-management-new-project]

Clique em **Contas Organizacionais** e especifique o **Domínio** do seu locatário AAD. Neste exemplo é o domínio **DemoAPIM.onmicrosoft.com**. O domínio do seu diretório pode ser obtido a partir da guia **Domínios** do seu diretório.

![Domínios][api-management-aad-domains]

Defina as configurações desejadas na caixa de diálogo **Alterar Autenticação** e clique em **OK**.

![Alterar Autenticação][api-management-change-authentication]

Quando você clica em **OK** o Visual Studio tenta registrar seu aplicativo com o diretório do AD do Azure e você talvez precise entrar pelo Visual Studio. Entre usando uma conta administrativa para seu diretório.

![Entrar no Visual Studio][api-management-sign-in-vidual-studio]

Para configurar esse projeto como uma API da Web do Azure marque a caixa de **Host na nuvem** e clique em **OK**.

![Novo Projeto][api-management-new-project-cloud]

Você pode ser solicitado a entrar no Azure e, em seguida, você pode configurar o aplicativo Web.

![Configurar][api-management-configure-web-app]

Neste exemplo, um novo **Plano do Serviço de Aplicativo** chamado **APIMAADDemo** é especificado.

Clique em **OK** para configurar o aplicativo Web e criar o projeto.

## <a name="add-the-code-to-the-web-api-project"></a>Adicione o código ao projeto de API da Web
A próxima etapa no vídeo adiciona o código no projeto de API da Web. Esta etapa começa em 4:35.

A API da Web neste exemplo implementa um serviço de calculadora básica usando um modelo e um controlador. Para adicionar o modelo para o serviço, clique com botão direito em **Modelos** no **Gerenciador de Soluções** e escolha **Adicionar**, **Classe**. Nomeie a classe como `CalcInput` e clique em **Adicionar**.

Adicione a seguinte declaração `using` no topo do arquivo `CalcInput.cs`.

```c#
using Newtonsoft.Json;
```

Substitua a classe gerada pelo seguinte código.

```c#
public class CalcInput
{
    [JsonProperty(PropertyName = "a")]
    public int a;

    [JsonProperty(PropertyName = "b")]
    public int b;
}
```

Clique com o botão direito em **Controladores** no **Gerenciador de Soluções** e escolha **Adicionar**->**Controlador**. Clique em **Controlador de API da Web 2 - Vazio**, depois clique em **Adicionar**. Digite **CalcController** para o nome do controlador e clique em **Adicionar**.

![Adicionar controlador][api-management-add-controller]

Adicione a seguinte declaração `using` no topo do arquivo `CalcController.cs`.

```c#
using System.IO;
using System.Web;
using APIMAADDemo.Models;
```

Substitua a classe do controlador gerado pelo seguinte código. Esse código implementa as operações `Add`, `Subtract`, `Multiply` e `Divide` da API básica de calculadora.

```c#
[Authorize]
public class CalcController : ApiController
{
    [Route("api/add")]
    [HttpGet]
    public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/sub")]
    [HttpGet]
    public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/mul")]
    [HttpGet]
    public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/div")]
    [HttpGet]
    public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }
}
```

Pressione **F6** para compilar e verificar a solução.

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure
Nesta etapa o projeto do Visual Studio é publicado no Azure. Esta etapa do vídeo começa em 5:45.

Para publicar o projeto no Azure, clique com o botão direito no projeto **APIMAADDemo** no Visual Studio e selecione **Publicar**. Mantenha as configurações padrão na caixa de diálogo **Publicar Web** e clique em **Publicar**.

![Publicar na Web][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Concessão de permissões para o aplicativo de serviço de back-end do AD do Azure
Um novo aplicativo do serviço de back-end é criado no diretório do AD do Azure como parte do processo de publicação e configuração do seu projeto de API da Web. Nesta etapa do vídeo, começando em 6:13, as permissões são concedidas no back-end de API da Web.

![Aplicativo][api-management-aad-backend-app]

Clique no nome do aplicativo para configurar as permissões necessárias. Navegue até a guia **Configurar** e role para baixo até a seção **permissões para outros aplicativos**. Clique na lista suspensa **Permissões de Aplicativo** ao lado do **Microsoft** **Azure Active Directory**, marque a caixa **Ler dados do diretório** e clique em **Salvar**.

![Adicionar permissões][api-management-aad-add-permissions]

> [!NOTE]
> Se o **Microsoft** **Azure Active Directory** não estiver listado em permissões para outros aplicativos, clique em **Adicionar aplicativo** e adicione-o da lista.
> 
> 

Anote a **URI da Id do aplicativo** para uso em uma etapa posterior, quando um aplicativo do AD do Azure é configurado para o portal do desenvolvedor de Gerenciamento de API.

![URI da Id do aplicativo][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Importar a API da Web para o Gerenciamento de API
APIs são configuradas no portal do publicador da API, que pode ser acessado no Portal do Azure. Para acessá-lo, clique em **Portal do publicador** na barra de ferramentas do seu serviço Gerenciamento de API. Se você ainda não tiver criado uma instância de serviço de Gerenciamento de API, veja [Criar uma instância de serviço de Gerenciamento de API][Create an API Management service instance] no tutorial [Gerenciar sua primeira API][Manage your first API].

![Portal do editor][api-management-management-console]

As operações podem ser [adicionadas manualmente às APIs](api-management-howto-add-operations.md)ou podem ser importadas. Neste vídeo, as operações são importadas no formato Swagger começando em 6:40.

Crie um arquivo chamado `calcapi.json` com o conteúdo a seguir e salve-o em seu computador. Verifique os pontos do atributo `host` para o back-end da API da Web. Neste exemplo, `"host": "apimaaddemo.azurewebsites.net"` é usado.

```json
{
  "swagger": "2.0",
  "info": {
    "title": "Calculator",
    "description": "Arithmetics over HTTP!",
    "version": "1.0"
  },
  "host": "apimaaddemo.azurewebsites.net",
  "basePath": "/api",
  "schemes": [
    "http"
  ],
  "paths": {
    "/add?a={a}&b={b}": {
      "get": {
        "description": "Responds with a sum of two numbers.",
        "operationId": "Add two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>51</code>.",
            "required": true,
            "type": "string",
            "default": "51",
            "enum": [
              "51"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>49</code>.",
            "required": true,
            "type": "string",
            "default": "49",
            "enum": [
              "49"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/sub?a={a}&b={b}": {
      "get": {
        "description": "Responds with a difference between two numbers.",
        "operationId": "Subtract two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>50</code>.",
            "required": true,
            "type": "string",
            "default": "50",
            "enum": [
              "50"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/div?a={a}&b={b}": {
      "get": {
        "description": "Responds with a quotient of two numbers.",
        "operationId": "Divide two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/mul?a={a}&b={b}": {
      "get": {
        "description": "Responds with a product of two numbers.",
        "operationId": "Multiply two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>5</code>.",
            "required": true,
            "type": "string",
            "default": "5",
            "enum": [
              "5"
            ]
          }
        ],
        "responses": { }
      }
    }
  }
}
```

Para importar a API de calculadora, clique em **APIs** no menu **Gerenciamento de API** à esquerda e depois clique em **Importar API**.

![Botão Importar API][api-management-import-api]

Execute as seguintes etapas para configurar a API de calculadora.

1. Clique em **Do arquivo**, navegue até arquivo `calculator.json` que foi salvo e clique no botão de opção **Swagger**.
2. Digite **calc** na caixa de texto **Sufixo da URL da API Web**.
3. Clique na caixa **Produtos (opcional)** e escolha **Inicial**.
4. Clique em **Salvar** para importar a API.

![Adicionar nova API][api-management-import-new-api]

Depois que a API é importada, a página de resumo para a API é exibida no portal do editor.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Chamar a API sem êxito a partir do portal do desenvolvedor
Neste ponto, a API foi importada para o Gerenciamento de API, mas não pode ainda ser chamada com êxito a partir do portal do desenvolvedor porque o serviço de back-end é protegido com a autenticação do AD do Azure. Isso é demonstrado no vídeo começando em 7:40 usando as seguintes etapas.

Clique em **Portal do desenvolvedor** do lado superior direito do portal do publicador.

![Portal do desenvolvedor][api-management-developer-portal-menu]

Clique em **APIs** e clique na API **Calculadora**.

![Portal do desenvolvedor][api-management-dev-portal-apis]

Clique em **Experimentar**.

![Experimente][api-management-dev-portal-try-it]

Clique em **Enviar** e observe o status de resposta **401 não autorizado**.

![Enviar][api-management-dev-portal-send-401]

A solicitação é não autorizada porque a API de back-end é protegida pelo Active Directory do Azure. Antes de chamar a API com êxito o portal do desenvolvedor deve ser configurado para autorizar os desenvolvedores que usam OAuth 2.0. Cada atributo é descrito nas seções a seguir.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Registrar o portal do desenvolvedor como um aplicativo AAD
A primeira etapa na configuração do portal do desenvolvedor para autorizar os desenvolvedores que usam o OAuth 2.0 é registrar o portal do desenvolvedor como um aplicativo AAD. Isso é demonstrado começando em 8:27 no vídeo.

Navegue até o locatário do Azure AD da primeira etapa deste vídeo, neste exemplo **APIMDemo** e navegue até a guia **Aplicativos**.

![Novo aplicativo][api-management-aad-new-application-devportal]

Clique no botão **Adicionar** para criar um novo aplicativo do Azure Active Directory e escolha **Adicionar um aplicativo que minha organização está desenvolvendo**.

![Novo aplicativo][api-management-new-aad-application-menu]

Escolha **Aplicativo Web e/ou API Web**, insira um nome e clique na seta Próximo. Neste exemplo, **APIMDeveloperPortal** é usado.

![Novo aplicativo][api-management-aad-new-application-devportal-1]

Para a **URL de Entrada**, insira a URL do seu serviço de Gerenciamento de API e acrescente `/signin`. Neste exemplo, `https://contoso5.portal.azure-api.net/signin` é usado.

Para a **URL da Id do aplicativo** insira a URL do seu serviço de Gerenciamento de API e acrescente alguns caracteres exclusivos. Eles podem ser qualquer caractere desejado e, neste exemplo, `https://contoso5.portal.azure-api.net/dp` é usado. Quando as **Propriedades do aplicativo** desejadas forem configuradas, clique na marca de seleção para criar o aplicativo.

![Novo aplicativo][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Configurar um servidor de autorização do OAuth 2.0 no Gerenciamento de API
A próxima etapa é configurar um servidor de autorização OAuth 2.0 no gerenciamento de API. Esta etapa é demonstrada no vídeo, começando em 9:43.

Clique em **Segurança** no menu de Gerenciamento de API à esquerda, clique em **OAuth 2.0** e em **Adicionar servidor de autorização**.

![Adicionar servidor de autorização][api-management-add-authorization-server]

Insira um nome e uma descrição opcional nos campos **Nome** e **Descrição**. Esses campos são usados para identificar o servidor de autorização OAuth 2.0 dentro da instância do serviço de Gerenciamento de API. Neste exemplo, **Demonstração de servidor de autorização** é usado. Posteriormente quando você especificar um servidor OAuth 2.0 para ser usado para autenticação para uma API, você selecionará esse nome.

Para a **URL de página de registro do cliente**, insira um valor de espaço reservado como `http://localhost`.  A **URL de página de registro do cliente** aponta para a página que os usuários podem usar para criar e configurar suas próprias contas para provedores OAuth 2.0 que dão suporte a gerenciamento de contas por usuários. Neste exemplo os usuários não criam e configuram suas próprias contas, é usado um espaço reservado.

![Adicionar servidor de autorização][api-management-add-authorization-server-1]

Em seguida, especifique a **URL de ponto de extremidade de autorização** e a **URL de ponto de extremidade de Token**.

![Servidor de autorização][api-management-add-authorization-server-1a]

Esses valores podem ser recuperados na página **Pontos de extremidade do aplicativo** do aplicativo AAD criado para o portal do desenvolvedor. Para acessar os pontos de extremidade navegue até a guia **Configurar** para o aplicativo AAD e clique em **Exibir pontos de extremidade**.

![Aplicativo][api-management-aad-devportal-application]

![Exibir pontos de extremidade][api-management-aad-view-endpoints]

Copie o **ponto de extremidade da autorização OAuth 2.0** e cole-o na caixa de texto **URL de ponto de extremidade de autorização**.

![Adicionar servidor de autorização][api-management-add-authorization-server-2]

Copie o **ponto de extremidade do token OAuth 2.0** e cole-o na caixa de texto **URL de ponto de extremidade de token**.

![Adicionar servidor de autorização][api-management-add-authorization-server-2a]

Além de colá-lo no ponto de extremidade de token, adicione um parâmetro de corpo adicional chamado **recurso** e para o valor use a **URI da Id do aplicativo** AAD do serviço de back-end criado quando o projeto do Visual Studio foi publicado.

![URI da Id do aplicativo][api-management-aad-sso-uri]

Em seguida, especifique as credenciais do cliente. Essas são as credenciais para o recurso que você quer acessar, nesse caso, o portal do desenvolvedor.

![Credenciais do cliente][api-management-client-credentials]

Para obter a **Id do Cliente**, navegue até a guia **Configurar** do aplicativo AAD para o portal de desenvolvedor e copie a **Id do Cliente**.

Para obter o **Segredo do Cliente** clique em **Selecionar duração** na seção **Chaves** e especifique um intervalo. Neste exemplo, 1 ano é usado.

![Id do Cliente][api-management-aad-client-id]

Clique em **Salvar** para salvar a configuração e exibir a chave. 

> [!IMPORTANT]
> Anote esta chave. Depois que você fechar a janela de configuração do Active Directory do Azure, a chave não pode ser exibida novamente.
> 
> 

Copie a chave para a área de transferência, alterne de volta para o portal do publicador, cole a chave na caixa de texto **Segredo do Cliente** e clique em **Salvar**.

![Adicionar servidor de autorização][api-management-add-authorization-server-3]

Imediatamente após as credenciais do cliente está uma concessão de código de autorização. Copie esse código de autorização e volte para a página de configuração do seu aplicativo de portal do desenvolvedor do Azure AD e cole a concessão de autorização no campo **URL de Resposta** e clique em **Salvar** novamente.

![URL de resposta][api-management-aad-reply-url]

A próxima etapa é configurar as permissões para o aplicativo AAD do portal do desenvolvedor. Clique em **Permissões de Aplicativo** e marque a caixa **Ler dados do diretório**. Clique em **Salvar** para salvar essa alteração e, em seguida, clique em **Adicionar aplicativo**.

![Adicionar permissões][api-management-add-devportal-permissions]

Clique no ícone de pesquisa, digite **APIM** na caixa Iniciando com, selecione **APIMAADDemo** e clique na marca de seleção para salvar.

![Adicionar permissões][api-management-aad-add-app-permissions]

Clique em **Permissões Delegadas** para **APIMAADDemo** e marque a caixa Acessar **APIMAADDemo** e clique em **Salvar**. Isso permite que o aplicativo do portal do desenvolvedor acesse o serviço de back-end.

![Adicionar permissões][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Habilitar autorização do usuário OAuth 2.0 para a API de Calculadora
Agora que o servidor OAuth 2.0 está configurado, você pode especificá-lo nas configurações de segurança para sua API. Esta etapa é demonstrada no vídeo, começando em 14:30.

Clique em **APIs** no menu à esquerda e clique em **Calculadora** para exibir e definir suas configurações.

![API de Calculadora][api-management-calc-api]

Navegue até a guia **Segurança**, marque a caixa de seleção **OAuth 2.0**, selecione o servidor de autorização desejado na lista suspensa **Servidor de autorização** e clique em **Salvar**.

![API de Calculadora][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Chamar com êxito a API de Calculadora do portal do desenvolvedor
A autorização do OAuth 2.0 já está configurado na API, suas operações podem ser chamadas com êxito no centro do desenvolvedor. Esta etapa é demonstrada no vídeo, começando em 15:00.

Navegue de volta para a operação **Adicionar dois inteiros** do serviço da calculadora no portal do desenvolvedor e clique em **Experimente**. Observe o novo item na seção **Autorização** correspondente ao servidor de autorização que você acabou de adicionar.

![API de Calculadora][api-management-calc-authorization-server]

Selecione **Código de autorização** na lista suspensa de autorização e insira as credenciais da conta a ser usada. Se você já estiver conectado com a conta pode ser que você não seja solicitado.

![API de Calculadora][api-management-devportal-authorization-code]

Clique em **Enviar** e observe o **Status da resposta** **200 OK** e os resultados da operação no conteúdo da resposta.

![API de Calculadora][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Configurar um aplicativo de área de trabalho para chamar a API
O próximo procedimento no vídeo começa em 16:30 e configura um aplicativo de área de trabalho simples para chamar a API. A primeira etapa é registrar o aplicativo de área de trabalho no AD do Azure e fornecer acesso ao diretório e ao serviço de back-end. Em 18:25 há uma demonstração do aplicativo da área de trabalho chamando uma operação na API da calculadora.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurar uma diretiva de validação de JWT para pré-autorizar solicitações
O procedimento final no vídeo começa em 20:48 e mostra como usar a diretiva [Validar JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) para pré-autorizar solicitações validando os tokens de acesso de cada solicitação de entrada. Se a solicitação não é validada pela política Validar JWT, a solicitação é bloqueada pelo Gerenciamento de API e não é passada para o back-end.

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
        </claim>
    </required-claims>
</validate-jwt>
```

Para outra demonstração de configuração e uso dessa diretiva, consulte [Abordagem da nuvem Episódio 177: Mais recursos de Gerenciamento de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avance para 13:50. Avance para 15:00 para ver as diretivas configuradas no editor de diretiva e, em seguida, 18:50 para uma demonstração de como chamar uma operação do portal do desenvolvedor com e sem o token de autorização necessário.

## <a name="next-steps"></a>Próximas etapas
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre o Gerenciamento de API.
* Para outras maneiras de proteger seu serviço de back-end, confira [Autenticação de certificado mútuo](api-management-howto-mutual-certificates.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Manage your first API]: api-management-get-started.md
