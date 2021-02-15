---
title: Proteja o back-end SPA com o OAuth 2,0 usando o Azure Active Directory B2C e o gerenciamento de API do Azure.
description: Proteja uma API com o OAuth 2,0 usando o Azure Active Directory B2C, o gerenciamento de API do Azure e a autenticação fácil para serem chamados de um JavaScript SPA.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new, devx-track-js
ms.openlocfilehash: b157200b03bdc89e00bfa5c8264d78baf24875c1
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609115"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Proteger o back-end SPA com OAuth 2,0, Azure Active Directory B2C e gerenciamento de API do Azure

Este cenário mostra como configurar sua instância do gerenciamento de API do Azure para proteger uma API.
Usaremos o protocolo OAuth 2,0 com Azure AD B2C, junto com o gerenciamento de API para proteger um back-end Azure Functions usando o EasyAuth.

## <a name="aims"></a>Visa
Vamos ver como o gerenciamento de API pode ser usado em um cenário simplificado com Azure Functions e Azure AD B2C. Você criará um aplicativo JavaScript (JS) chamando uma API, que conecta os usuários com Azure AD B2C. Em seguida, você usará os recursos da política Validate-JWT do gerenciamento de API para proteger a API de back-end.

Para uma defesa profunda, usamos EasyAuth para validar o token novamente dentro da API de back-end.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir as etapas deste artigo, você precisa ter:
* Uma conta de armazenamento do Azure (StorageV2) Uso Geral v2 para hospedar o aplicativo de página única do front-end JS
* Uma instância de gerenciamento de API do Azure 
* Um aplicativo de funções do Azure vazio (executando o tempo de execução do .NET Core v2, em um plano de consumo do Windows) para hospedar a API chamada
* Um locatário Azure AD B2C, vinculado a uma assinatura 

Embora na prática você usaria recursos na mesma região em cargas de trabalho de produção, neste artigo de instruções, a região de implantação não é importante.

## <a name="overview"></a>Visão geral
Aqui está uma ilustração dos componentes em uso e o fluxo entre eles quando esse processo for concluído.
![Componentes em uso e fluxo](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Componentes em uso e fluxo")

A seguir é apresentada uma visão geral das etapas:

1. Criar a chamada de Azure AD B2C (front-end, gerenciamento de API) e aplicativos de API com escopos e conceder acesso à API
1. Crie as políticas de inscrição ou de entrada para permitir que os usuários entrem com Azure AD B2C 
1. Configurar o gerenciamento de API com as novas IDs de cliente Azure AD B2C e chaves para habilitar a autorização de usuário OAuth2 no console do desenvolvedor
1. Criar a API de função
1. Configure a API de função para habilitar EasyAuth com as novas e as chaves de ID de cliente Azure AD B2C e bloquear para APIM VIP 
1. Criar a definição de API no gerenciamento de API
1. Configurar o Oauth2 para a configuração da API de gerenciamento de API
1. Configurar a política **CORS** e adicionar a política **Validate-JWT** para validar o token OAuth para cada solicitação de entrada
1. Compilar o aplicativo de chamada para consumir a API
1. Carregar o exemplo de SPA do JS
1. Configurar o aplicativo de cliente JS de exemplo com o novo Azure AD B2C ID do cliente e as chaves 
1. Testar o aplicativo cliente

## <a name="configure-azure-ad-b2c"></a>Configurar Azure AD B2C 
Abra a folha Azure AD B2C no portal e execute as etapas a seguir.
1. Selecione a guia **aplicativos** 
1. Clique no botão ' Adicionar ' e crie três aplicativos para as seguintes finalidades
   * O cliente de front-end.
   * A API da função de back-end.
   * Adicional O portal do desenvolvedor de gerenciamento de API (a menos que você esteja executando o gerenciamento de API do Azure na camada de consumo, mais sobre esse cenário posteriormente).
1. Defina a API Web/WebApp para todos os 3 aplicativos e defina ' permitir fluxo implícito ' como Sim somente para o cliente de front-end.
1. Agora, defina o URI da ID do aplicativo, escolha algo exclusivo e relevante para o serviço que está sendo criado.
1. Use espaços reservados para as URLs de resposta por enquanto https://localhost , atualizaremos essas URLs mais tarde.
1. Clique em ' criar ' e repita as etapas de 3 a 2-5 para cada um dos três aplicativos acima, registrando o URI de AppID, o nome e a ID do aplicativo para uso posterior para todos os três aplicativos.
1. Abra o aplicativo portal do desenvolvedor do gerenciamento de API na lista de aplicativos e selecione a guia *chaves* (em geral) e clique em ' gerar chave ' para gerar uma chave de autenticação
1. Ao clicar em salvar, registre a chave em um local seguro para uso posterior – Observe que esse local é a única chance de exibir e copiar essa chave.
1. Agora, selecione a guia *escopos publicados* (em acesso à API)
1. Crie e nomeie um escopo para sua API de função e registre o escopo e o valor de escopo completo preenchido e clique em ' salvar '.
   > [!NOTE]
   > Os escopos de Azure AD B2C são efetivamente permissões em sua API para que outros aplicativos possam solicitar acesso por meio da folha de acesso à API de seus aplicativos, efetivamente você acabou de criar permissões de aplicativo para sua API chamada.
1. Abra os outros dois aplicativos e, em seguida, examine a guia *acesso à API* .
1. Conceda a eles acesso ao escopo da API de back-end e ao padrão que já estava lá ("acessar o perfil do usuário").
1. Gere uma chave a cada selecionando a guia *chaves* em ' geral ' para gerar uma chave de autenticação e registre essas chaves em um local seguro para mais tarde.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Criar um fluxo de usuário "inscrever-se ou entrar"
1. Retornar à raiz (ou ' visão geral ') da folha Azure AD B2C 
1. Em seguida, selecione "Fluxos dos Usuários (políticas)" e clique em "novo fluxo de usuário"
1. Escolha o tipo de fluxo de usuário "inscrever-se e entrar"
1. Dê um nome à política e registre-a para mais tarde.
1. Em seguida, em ' provedores de identidade ', marque ' inscrição de ID de usuário ' (isso pode indicar ' inscrição de email ') e clique em OK. 
1. Em ' atributos e declarações de usuário ', clique em ' mostrar mais... ' em seguida, escolha as opções de declaração que você deseja que os usuários insiram e que retornaram no token. Verifique pelo menos ' nome de exibição ' e ' endereço de email ' para coletar e retornar e clique em ' OK ' e clique em ' criar '.
1. Selecione a política que você criou na lista e clique no botão ' executar fluxo de usuário '.
1. Essa ação abrirá a folha executar fluxo de usuário, selecionará o aplicativo frontend e registrará o endereço do domínio b2clogin.com mostrado sob a lista suspensa para ' selecionar domínio '.
1. Clique no link na parte superior para abrir o "ponto de extremidade de configuração do OpenID conhecido" e registre os valores de authorization_endpoint e de token_endpoint, bem como o valor do próprio link como o ponto de extremidade de configuração do OpenID conhecido.

   > [!NOTE]
   > As políticas B2C permitem que você exponha os pontos de extremidade de logon Azure AD B2C para poder capturar diferentes componentes de dados e conectar usuários de maneiras diferentes. Nesse caso, configuramos um ponto de extremidade de inscrição ou de entrada, que expôs um ponto de extremidade de configuração conhecido, especificamente, a nossa política criada foi identificada na URL pelo parâmetro p =.
   > 
   > Quando isso for feito, agora você terá uma empresa funcional para a plataforma de identidade do consumidor que conectará os usuários em vários aplicativos. 
   > Se desejar, você pode clicar no botão ' executar fluxo de usuário ' aqui (para passar pelo processo de inscrição ou entrada) e ter uma ideia do que ele fará na prática, mas a etapa de redirecionamento no final falhará, pois o aplicativo ainda não foi implantado.

## <a name="build-the-function-api"></a>Criar a API de função
1. Voltar para seu locatário do Azure AD padrão no portal do Azure para que possamos configurar itens em sua assinatura novamente 
1. Vá para a folha aplicativos de funções do portal do Azure, abra o aplicativo de funções vazio e crie uma nova função de In-Portal "webhook + API" por meio do início rápido.
1. Cole o código de exemplo abaixo em Run. CSX sobre o código existente que aparece.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!NOTE]
   > O código da função script c# que você acabou de colar simplesmente registra uma linha nos logs de funções e retorna o texto "Olá, Mundo" com alguns dados dinâmicos (data e hora).

3. Selecione "integrar" na folha à esquerda e, em seguida, selecione ' Editor Avançado ' no canto superior direito do painel.
4. Cole o código de exemplo abaixo sobre o JSON existente.

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. Volte para a guia HttpTrigger1, clique em ' obter URL da função ' e copie a URL que aparece.

   > [!NOTE]
   > As associações que você acabou de criar simplesmente informam as funções para responder em solicitações HTTP GET anônimas para a URL que você acabou de copiar. ( `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` ) Agora temos uma API https sem servidor escalonável, que é capaz de retornar uma carga muito simples.
   > Agora você pode testar a chamada dessa API de um navegador da Web usando a URL acima, você também pode remover a parte? código = segredo da URL e provar que Azure Functions retornará um erro 401.

## <a name="configure-and-secure-the-function-api"></a>Configurar e proteger a API de função
1. Duas áreas extras no aplicativo de funções precisam ser configuradas (restrições de autenticação e de rede).
1. Em primeiro lugar, vamos configurar a autenticação/autorização, portanto, clique no nome do aplicativo de funções (ao lado do &lt; &gt; ícone de funções Z) para mostrar a página Visão geral.
1. Em seguida, selecione a guia ' recursos da plataforma ' e selecione ' autenticação/autorização '.
1. Ative o recurso de autenticação do serviço de aplicativo.
1. Em ' provedores de autenticação ', escolha ' Azure Active Directory ' e escolha ' avançado ' na opção de modo de gerenciamento.
1. Cole a ID do aplicativo da API da função de back-end (de Azure AD B2C na caixa ' ID do cliente ')
1. Cole o ponto de extremidade de configuração do Open-ID bem conhecido da política inscrever-se ou entrar na caixa URL do emissor (registramos essa configuração anteriormente).
1. Selecione OK.
1. Defina a ação a ser tomada quando a solicitação não for autenticada na lista suspensa para "fazer logon com Azure Active Directory" e clique em salvar.

   > [!NOTE]
   > Agora, sua API de função será implantada e deverá lançar 401 respostas se a chave correta não for fornecida e deverá retornar dados quando uma solicitação válida for apresentada.
   > Você adicionou segurança adicional de defesa aprofundada no EasyAuth Configurando a opção ' fazer logon com o Azure AD ' para lidar com solicitações não autenticadas. Lembre-se de que isso alterará o comportamento de solicitação não autorizado entre o Aplicativo de funções de back-end e o SPA como o EasyAuth emitirá um redirecionamento de 302 para o AAD em vez de uma resposta 401 não autorizada, corrigiremos isso usando o gerenciamento de API mais tarde.
   > Ainda não temos nenhuma segurança IP aplicada, se você tiver uma chave válida e um token OAuth2, qualquer pessoa poderá chamá-la de qualquer lugar-idealmente, queremos forçar todas as solicitações a surgirem por meio do gerenciamento de API.
   > Se você estiver usando a camada de consumo do gerenciamento de API, não poderá executar esse bloqueio pelo VIP, pois não há um IP estático dedicado para essa camada, você precisará contar com o método de bloquear suas chamadas de API por meio da chave de função secreta compartilhada, portanto, as etapas 11-13 não serão possíveis.

1. Fechar a folha "autenticação/autorização" 
1. Selecione ' rede ' e, em seguida, selecione ' restrições de acesso '
1. Em seguida, bloqueie os IPs do aplicativo de funções permitidos para o VIP da instância de gerenciamento de API. Esse VIP é mostrado na seção Gerenciamento de API – visão geral do Portal.
1. Se você quiser continuar a interagir com o portal do Functions e executar as etapas opcionais abaixo, você também deverá adicionar seu próprio endereço IP público ou intervalo CIDR aqui.
1. Quando houver uma entrada de permissão na lista, o Azure adicionará uma regra de negação implícita para bloquear todos os outros endereços. 

Você precisará adicionar blocos de endereços formatados por CIDR ao painel restrições de IP. Quando você precisar adicionar um único endereço, como o VIP de gerenciamento de API, será necessário adicioná-lo no formato XX. XX. XX.

   > [!NOTE]
   > Agora, sua API de função não deve ser chamável de qualquer lugar além do gerenciamento de API ou seu endereço.
   
## <a name="import-the-function-app-definition"></a>Importar a definição do aplicativo de funções
1. Abra a *folha gerenciamento de API* e, em seguida, abra *sua instância do*.
1. Selecione a folha APIs na seção Gerenciamento de API da sua instância.
1. No painel ' adicionar uma nova API ', escolha ' Aplicativo de funções ' e, em seguida, selecione ' completo ' na parte superior do pop-up.
1. Clique em procurar, escolha o aplicativo de funções no qual você está hospedando a API e clique em selecionar.
1. Dê ao API um nome e uma descrição para o uso interno do gerenciamento de API e adicione-o ao produto ' ilimitado '.
1. Certifique-se de registrar a URL base para uso posterior e, em seguida, clique em criar.

## <a name="configure-oauth2-for-api-management"></a>Configurar o Oauth2 para gerenciamento de API

1. Em seguida, selecione a folha OAuth 2,0 na guia Segurança e clique em ' Adicionar '
1. Forneça valores para *nome de exibição* e *Descrição* para o ponto de extremidade OAuth adicionado (esses valores serão mostrados na próxima etapa como um ponto de extremidade Oauth2).
1. Você pode inserir qualquer valor na URL da página de registro do cliente, pois esse valor não será usado.
1. Verifique o tipo de concessão de *autenticação implícita* e deixe o tipo de concessão de código de autorização marcado.
1. Mova para os campos de ponto de extremidade de *autorização* e *token* e insira os valores que você capturou do documento XML de configuração conhecido anteriormente.
1. Role para baixo e preencha um *parâmetro de corpo adicional* chamado ' Resource ' com a ID de cliente da API da função de back-end do registro do aplicativo Azure ad B2C
1. Selecione ' credenciais do cliente ', defina a ID do cliente para a ID do aplicativo do aplicativo de console do desenvolvedor-ignore esta etapa se estiver usando o modelo de gerenciamento de API de consumo.
1. Defina o segredo do cliente para a chave que você registrou anteriormente-ignore esta etapa se estiver usando o modelo de gerenciamento de API de consumo.
1. Por fim, agora Registre a redirect_uri da concessão do código de autenticação do gerenciamento de API para uso posterior.

## <a name="set-up-oauth2-for-your-api"></a>Configurar o Oauth2 para sua API
1. Sua API será exibida no lado esquerdo do portal na seção ' todas as APIs ', abra sua API clicando nela.
1. Selecione a guia ' configurações '.
1. Atualize suas configurações selecionando "OAuth 2,0" no botão de opção de autorização do usuário.
1. Selecione o servidor OAuth que você definiu anteriormente.
1. Marque a caixa de seleção "substituir escopo" e insira o escopo que você registrou para a chamada à API de back-end anteriormente.

   > [!NOTE]
   > Agora temos uma instância de gerenciamento de API que sabe como obter tokens de acesso de Azure AD B2C para autorizar solicitações e compreende nossa configuração de Azure Active Directory B2C de Oauth2.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Configurar as políticas **CORS** e **Validate-JWT**

> As seções a seguir devem ser seguidas, independentemente da camada APIM que está sendo usada. 

1. Volte para a guia Design e escolha "todas as APIs" e clique no botão exibição de código para mostrar o editor de política.
1. Edite a seção de entrada e cole o XML abaixo para que ele seja lido da seguinte maneira.

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. Edite a URL de OpenID-config para corresponder ao ponto de extremidade de Azure AD B2C conhecido para a política de inscrição ou entrada.
1. Edite o valor da declaração para corresponder à ID de aplicativo válida, também conhecida como ID do cliente para o aplicativo de API de back-end e salvar.

   > [!NOTE]
   > Agora, o gerenciamento de API é capaz de responder a solicitações de origem cruzada para aplicativos de SPA do JS e realizará limitação, limitação de taxa e pré-validação do token de autenticação JWT que está sendo passado antes de encaminhar a solicitação para a API de função.

   > [!NOTE]
   > A seção a seguir é opcional e não se aplica à camada de **consumo** , que não dá suporte ao portal do desenvolvedor.
   > Se você não pretende usar o portal do desenvolvedor ou não pode usá-lo já que está usando a camada de consumo, ignore esta etapa e vá diretamente para ["criar o JavaScript Spa para consumir a API"](#build-the-javascript-spa-to-consume-the-api).

## <a name="optional-configure-the-developer-portal"></a>Adicional Configurar o portal do desenvolvedor

1. Abra a folha Azure AD B2C e navegue até o registro do aplicativo para o portal do desenvolvedor
1. Defina a entrada ' URL de resposta ' como a que você anotou quando configurou o redirect_uri da concessão de código de autenticação no gerenciamento de API anteriormente.

   Agora que a autorização de usuário do OAuth 2,0 está habilitada no `Echo API` , o console do desenvolvedor Obtém um token de acesso para o usuário, antes de chamar a API.

1. Navegue até qualquer operação no `Echo API` portal do desenvolvedor e selecione Experimente para levá **-lo** para o console do desenvolvedor.
1. Observe um novo item na seção **autorização** , correspondente ao servidor de autorização que você acabou de adicionar.
1. Selecione **código de autorização** na lista suspensa autorização e você será solicitado a entrar no locatário do Azure AD. Se você já tiver entrado com a conta, talvez não seja solicitado.
1. Após a entrada bem-sucedida, um `Authorization: Bearer` cabeçalho é adicionado à solicitação, com um token de acesso de Azure ad B2C codificado em base64. 
1. Selecione **Enviar** e você pode chamar a API com êxito.

   > [!NOTE]
   > Agora, o gerenciamento de API é capaz de adquirir tokens para o portal do desenvolvedor testar sua API e é capaz de entender sua definição e renderizar a página de teste apropriada no portal de desenvolvimento.

1. Na folha visão geral do portal de gerenciamento de API, clique em ' portal do desenvolvedor ' para entrar como um administrador da API.
1. Aqui, você e outros consumidores selecionados de sua API podem testá-los e chamá-los de um console do.
1. Selecione ' produtos ', escolha ' ilimitado ' e escolha a API criada anteriormente e clique em ' experimentar '
1. Reexiba a chave de assinatura de API e copie-a em algum lugar seguro junto com a URL de solicitação que você precisará mais tarde.
1. Além disso, selecione implícito, na lista suspensa Autenticação OAuth, e talvez você precise se autenticar aqui com um pop-up.
1. Clique em ' enviar ' e, se tudo estiver bem, seu Aplicativo de funções deverá responder com uma mensagem de saudação por meio do gerenciamento de API com uma mensagem 200 OK e algum JSON.

   > [!NOTE]
   > Parabéns, agora você tem Azure AD B2C, gerenciamento de API e Azure Functions trabalhando juntos para publicar, proteger e consumir uma API. Talvez você tenha notado que a API está, na verdade, protegida duas vezes usando esse método, uma vez com o cabeçalho de API OCP-Subscription-Key e uma vez com a autorização: JWT de portador.
   > Você estaria correto, pois este exemplo é um aplicativo de página única JavaScript, usamos a chave de gerenciamento de API somente para chamadas de cobrança e limitação de taxa.
   > A autorização e a autenticação reais são manipuladas pelo Azure AD B2C e são encapsuladas no JWT, que é validado duas vezes, uma vez por gerenciamento de API e, em seguida, por Azure Functions.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>Criar o JavaScript SPA para consumir a API
1. Abra a folha contas de armazenamento na portal do Azure 
1. Selecione a conta que você criou e selecione a folha "site estático" na seção Configurações (se você não vir uma opção "site estático", verifique se você criou uma conta v2).
1. Defina o recurso de hospedagem da Web estática como ' habilitado ' e defina o nome do documento de índice como ' index.html ' e clique em ' salvar '.
1. Anote o conteúdo do ponto de extremidade primário, pois esse local é onde o site de front-end será hospedado. 

   > [!NOTE]
   > Você pode usar o armazenamento de BLOBs do Azure + a reescrita de CDN ou Azure App serviço-mas o recurso de Hospedagem de site estático do armazenamento de BLOBs nos dá um contêiner padrão para fornecer conteúdo da Web estático/HTML/js/CSS do armazenamento do Azure e inferirá uma página padrão para o trabalho de zero.

## <a name="upload-the-js-spa-sample"></a>Carregar o exemplo de SPA do JS
1. Ainda na folha da conta de armazenamento, selecione a folha ' BLOBs ' na seção serviço BLOB e clique no contêiner $web que aparece no painel à direita.
1. Salve o código abaixo em um arquivo localmente em seu computador como index.html e, em seguida, carregue o arquivo index.html para o contêiner $web.

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},
                    url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. Navegue até o ponto de extremidade primário do site estático que você armazenou anteriormente na última seção.

   > [!NOTE]
   > Parabéns, você acabou de implantar um aplicativo de página única JavaScript no armazenamento do Azure, pois não configuramos o aplicativo JS com suas chaves para a API ou configuramos o aplicativo JS com seus Azure AD B2C detalhes ainda – a página ainda não funcionará se você abri-la.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Configurar o SPA do JS para Azure AD B2C
1. Agora sabemos onde tudo está: podemos configurar o SPA com o endereço apropriado da API de gerenciamento de API e as IDs de aplicativo/cliente Azure AD B2C corretas
1. Volte para a folha de armazenamento portal do Azure e clique em index.html e escolha ' Editar BLOB ' 
1. Atualize os detalhes de autenticação para corresponder ao seu aplicativo de front-end registrado no B2C anteriormente, observando que os valores de ' b2cScopes ' são para o back-end da API.
1. A chave webApi e a URL da API podem ser encontradas no painel de teste do gerenciamento de API para a operação de API.
1. Crie uma chave de assinatura APIM por título para o gerenciamento de API de volta para a folha gerenciamento de API, selecionando "assinaturas" e clicando em "Adicionar assinatura" e salvando o registro. Clicar nas reticências (...) ao lado da linha criada permitirá que você mostre as chaves para que você possa copiar a chave primária.
1. Ele deve ser semelhante ao código abaixo:-  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. Clique em Salvar

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Definir os URIs de redirecionamento para o aplicativo de front-end Azure AD B2C
1. Abra a folha Azure AD B2C e navegue até o registro do aplicativo para o aplicativo de frontend do JavaScript
1. Defina a URL de redirecionamento para aquela que você anotou quando configurou anteriormente o ponto de extremidade primário do site estático acima

   > [!NOTE] 
   > Essa configuração fará com que um cliente do aplicativo de front-end receba um token de acesso com declarações apropriadas de Azure AD B2C.
   > O SPA poderá adicionar isso como um token de portador no cabeçalho HTTPS na chamada para a API de back-end.
   > O gerenciamento de API validará previamente o token, chamadas de limite de taxa para o ponto de extremidade pela chave do Assinante, antes de passar a solicitação para a API de recebimento da função do Azure.
   > O SPA renderizará a resposta no navegador.

   > *Parabéns, você configurou Azure AD B2C, gerenciamento de API do Azure Azure Functions Azure App autorização de serviço para trabalhar em harmonia perfeito!*

   > [!NOTE]
   > Agora temos um aplicativo simples com uma API segura simples, vamos testá-lo.

## <a name="test-the-client-application"></a>Testar o aplicativo cliente
1. Abra a URL do aplicativo de exemplo que você anotou na conta de armazenamento que você criou anteriormente
1. Clique em "entrar" no canto superior direito, esse clique abrirá seu perfil de Azure AD B2C inscrever-se ou entrar.
1. O logon de postagem na seção "conectado como" da tela será populada a partir do JWT.
1. Agora, clique em "chamar API da Web" e a página deve ser atualizada com os valores enviados de volta da sua API protegida.

## <a name="and-were-done"></a>E pronto
As etapas acima podem ser adaptadas e editadas para permitir muitos usos diferentes de Azure AD B2C com o gerenciamento de API.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [Azure Active Directory e o OAuth2.0](../active-directory/develop/authentication-vs-authorization.md).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre o Gerenciamento de API.
* Para outras maneiras de proteger seu serviço de back-end, confira [Autenticação de certificado mútuo](api-management-howto-mutual-certificates.md).
* [Crie uma instância de serviço de gerenciamento de API](get-started-create-service-instance.md).
* [Gerencie sua primeira API](import-and-publish.md).
