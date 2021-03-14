---
title: Proteger o back-end de SPA no gerenciamento de API do Azure com Active Directory B2C
description: Proteja uma API com o OAuth 2,0 usando o Azure Active Directory B2C, o gerenciamento de API do Azure e a autenticação fácil para serem chamados de um SPA JavaScript usando o fluxo de autenticação SPA habilitado para PKCE.
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
ms.date: 02/18/2021
ms.author: wieastbu
ms.custom: fasttrack-new, fasttrack-update, devx-track-js
ms.openlocfilehash: 812b54d10ea3cc3c405f534e36ac66abf3466808
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449281"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Proteger o back-end SPA com OAuth 2,0, Azure Active Directory B2C e gerenciamento de API do Azure

Este cenário mostra como configurar sua instância do gerenciamento de API do Azure para proteger uma API.
Usaremos o fluxo Azure AD B2C SPA (código de autenticação + PKCE) para adquirir um token, junto com o gerenciamento de API para proteger um back-end Azure Functions usando o EasyAuth.

## <a name="aims"></a>Visa

Vamos ver como o gerenciamento de API pode ser usado em um cenário simplificado com Azure Functions e Azure AD B2C. Você criará um aplicativo JavaScript (JS) chamando uma API, que conecta os usuários com Azure AD B2C. Em seguida, você usará o gerenciamento de API Validate-JWT, CORS e o limite de taxa por recursos de política de chave para proteger a API de back-end.

Para uma defesa profunda, usamos EasyAuth para validar o token novamente dentro da API de back-end e garantir que o gerenciamento de API seja o único serviço que pode chamar o back-end de Azure Functions.

## <a name="what-will-you-learn"></a>O que você aprenderá

> [!div class="checklist"]
> * Configuração de um aplicativo de página única e API de back-end no Azure Active Directory B2C
> * Criação de uma API de back-end Azure Functions
> * Importação de uma API de Azure Functions para o gerenciamento de API do Azure
> * Protegendo a API no gerenciamento de API do Azure
> * Chamando os pontos de extremidade de autorização do Azure Active Directory B2C por meio das bibliotecas da plataforma Microsoft Identity (MSAL.js)
> * Armazenando um aplicativo de página única de HTML/baunilha JS e servindo-o de um ponto de extremidade de armazenamento de BLOBs do Azure

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas deste artigo, você precisa ter:

* Uma conta de armazenamento do Azure (StorageV2) Uso Geral v2 para hospedar o aplicativo de página única do front-end JS.
* Uma instância de gerenciamento de API do Azure (qualquer camada funcionará, incluindo o ' consumo ', mas determinados recursos aplicáveis ao cenário completo não estão disponíveis nessa camada (taxa-limite por chave e IP virtual dedicado), essas restrições são indicadas abaixo no artigo onde apropriado).
* Um aplicativo de funções do Azure vazio (executando o tempo de execução do V 3.1 .NET Core, em um plano de consumo) para hospedar a API chamada
* Um locatário Azure AD B2C, vinculado a uma assinatura.

Embora na prática você usaria recursos na mesma região em cargas de trabalho de produção, neste artigo de instruções, a região de implantação não é importante.

## <a name="overview"></a>Visão geral

Aqui está uma ilustração dos componentes em uso e o fluxo entre eles quando esse processo for concluído.
![Componentes em uso e fluxo](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Componentes em uso e fluxo")

Aqui está uma rápida visão geral das etapas:

1. Criar a chamada de Azure AD B2C (front-end, gerenciamento de API) e aplicativos de API com escopos e conceder acesso à API
1. Crie as políticas de inscrição e entrada para permitir que os usuários entrem com Azure AD B2C 
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

   > [!TIP]
   > Vamos capturar algumas informações e chaves, etc. à medida que examinamos este documento, talvez seja útil ter um editor de texto aberto para armazenar os seguintes itens de configuração temporariamente.  
   >
   > ID DO CLIENTE DE BACK-END B2C:  
   > CHAVE SECRETA DO CLIENTE DE BACK-END B2C:  
   > URI DO ESCOPO DA API DE BACK-END B2C:  
   > ID DO CLIENTE B2C FRONTEND:  
   > URI DO PONTO DE EXTREMIDADE DO FLUXO DO USUÁRIO B2C:  
   > PONTO DE EXTREMIDADE DE OPENID BEM CONHECIDO DO B2C:   
   > NOME da política do B2C: Frontendapp_signupandsignin URL da função:  
   > URL BASE DA API DO APIM: URL DO PONTO DE EXTREMIDADE PRIMÁRIO DO ARMAZENAMENTO:  

## <a name="configure-the-backend-application"></a>Configurar o aplicativo de back-end

Abra a folha Azure AD B2C no portal e execute as etapas a seguir.

1. Selecione a guia **registros do aplicativo**
1. Clique no botão ' novo registro '.
1. Escolha ' Web ' na caixa de seleção Redirecionar URI.
1. Agora, defina o nome de exibição, escolha algo exclusivo e relevante para o serviço que está sendo criado. Neste exemplo, usaremos o nome "aplicativo de back-end".
1. Use espaços reservados para as URLs de resposta, como ' https://jwt.ms ' (um site de decodificação de token de propriedade da Microsoft), atualizaremos essas URLs mais tarde.
1. Verifique se você selecionou a opção "contas em qualquer provedor de identidade ou diretório organizacional (para autenticar usuários com fluxos de usuário)"
1. Para este exemplo, desmarque a caixa "conceder consentimento de administrador", pois não precisaremos de offline_access permissões hoje.
1. Clique em "Registrar".
1. Registre a ID do cliente do aplicativo de back-end para uso posterior (mostrado em ' ID do aplicativo (cliente) ').
1. Selecione a guia *certificados e segredos* (em gerenciar) e clique em ' novo segredo do cliente ' para gerar uma chave de autenticação (aceite as configurações padrão e clique em ' Adicionar ').
1. Ao clicar em ' Adicionar ', copie a chave (em ' valor ') em algum lugar seguro para uso posterior como ' segredo do cliente de back-end '-Observe que essa caixa de diálogo é a única chance de que você precise copiar essa chave.
1. Agora, selecione a guia *expor uma API* (em gerenciar).
1. Você será solicitado a definir o URI AppID, selecionar e registrar o valor padrão.
1. Criar e nomear o escopo "Olá" para sua API de função, você pode usar a frase ' Olá ' para todas as opções que podem ser inseridas, gravando o URI de valor completo do escopo preenchido e, em seguida, clique em ' Adicionar escopo '.
1. Retorne à raiz da folha Azure AD B2C selecionando a trilha ' Azure AD B2C ' na parte superior esquerda do Portal.

   > [!NOTE]
   > Os escopos de Azure AD B2C são efetivamente permissões em sua API para que outros aplicativos possam solicitar acesso por meio da folha de acesso à API de seus aplicativos, efetivamente você acabou de criar permissões de aplicativo para sua API chamada.

## <a name="configure-the-frontend-application"></a>Configurar o aplicativo frontend

1. Selecione a guia **registros do aplicativo**
1. Clique no botão ' novo registro '.
1. Escolha "aplicativo de página única (SPA)" na caixa de seleção Redirecionar URI.
1. Agora, defina o nome de exibição e o URI de AppID, escolha algo exclusivo e relevante para o aplicativo frontend que usará esse AAD B2C registro de aplicativo. Neste exemplo, você pode usar "aplicativo de front-end"
1. De acordo com o primeiro registro do aplicativo, deixe a seleção de tipos de conta com suporte como padrão (autenticando usuários com fluxos de usuário)
1. Use espaços reservados para as URLs de resposta, como ' https://jwt.ms ' (um site de decodificação de token de propriedade da Microsoft), atualizaremos essas URLs mais tarde.
1. Deixe a caixa de consentimento conceder administrador em escala
1. Clique em "Registrar".
1. Registre a ID do cliente do aplicativo frontend para uso posterior (mostrado em ' ID do aplicativo (cliente) ').
1. Alterne para a guia *permissões de API* .
1. Conceda acesso ao aplicativo de back-end clicando em ' adicionar uma permissão ', depois em ' minhas APIs ', selecione ' aplicativo de back-end ', selecione ' permissões ', selecione o escopo que você criou na seção anterior e clique em ' adicionar permissões '
1. Clique em ' conceder consentimento de administrador para {Tenant} ' e clique em ' Sim ' na caixa de diálogo pop-up. Esse pop-up consentiu o "aplicativo frontend" para usar a permissão "Hello" definida no "aplicativo de back-end" criado anteriormente.
1. Todas as permissões agora devem ser mostradas para o aplicativo como uma marca de verde na coluna status

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Criar um fluxo de usuário "inscrever-se e entrar"

1. Retorne à raiz da folha B2C selecionando o Azure AD B2C breadcrumb.
1. Alterne para a guia ' Fluxos dos Usuários ' (em políticas).
1. Clique em "novo fluxo de usuário"
1. Escolha o tipo de fluxo de usuário "inscrever-se e entrar" e selecione "recomendado" e, em seguida, "criar"
1. Dê um nome à política e registre-a para mais tarde. Para este exemplo, você pode usar "Frontendapp_signupandsignin", observe que isso será prefixado com "B2C_1_" para tornar "B2C_1_Frontendapp_signupandsignin"
1. Em "provedores de identidade" e "contas locais", marque "inscrição de email" (ou "inscrição de ID de usuário", dependendo da configuração de seu locatário B2C) e clique em OK. Essa configuração ocorre porque iremos registrar contas locais do B2C, não se desferindo a outro provedor de identidade (como um provedor de identidade social) para usar a conta de mídia social existente de um usuário.
1. Deixe as configurações de acesso condicional e MFA em seus padrões.
1. Em ' atributos e declarações de usuário ', clique em ' mostrar mais... ' em seguida, escolha as opções de declaração que você deseja que os usuários insiram e que retornaram no token. Verifique pelo menos ' nome de exibição ' e ' endereço de email ' para coletar, com ' nome de exibição ' e ' endereços de email ' para retornar (preste atenção ao fato de que você está coletando EmailAddress, singular e pedindo para retornar endereços de email, vários) e clique em ' OK ' e clique em ' criar '.
1. Clique no fluxo de usuário que você criou na lista e clique no botão ' executar fluxo de usuário '.
1. Essa ação abrirá a folha executar fluxo de usuário, selecionará o aplicativo frontend, copiará o ponto de extremidade do fluxo do usuário e o salvará para mais tarde.
1. Copie e armazene o link na parte superior, registrando como o "ponto de extremidade de configuração do OpenID conhecido" para uso posterior.

   > [!NOTE]
   > As políticas B2C permitem que você exponha os pontos de extremidade de logon Azure AD B2C para poder capturar diferentes componentes de dados e conectar usuários de maneiras diferentes.
   > 
   > Nesse caso, configuramos um fluxo de inscrição ou de entrada (política). Isso também expôs um ponto de extremidade de configuração bem conhecido, em ambos os casos em que nossa política criada foi identificada na URL pelo parâmetro de cadeia de caracteres de consulta "p =".  
   >
   > Quando isso for feito, agora você terá uma empresa funcional para a plataforma de identidade do consumidor que conectará os usuários em vários aplicativos.

## <a name="build-the-function-api"></a>Criar a API de função

1. Volte para o seu locatário do Azure AD padrão no portal do Azure para que possamos configurar itens em sua assinatura novamente.
1. Vá para a folha aplicativos de funções do portal do Azure, abra o aplicativo de funções vazio e clique em ' funções ' e clique em ' Adicionar '.
1. No submenu que aparece, escolha ' desenvolver no portal ', em ' selecionar um modelo ' e escolha ' gatilho HTTP ', em detalhes do modelo nomeie-o como ' Olá ' com o nível de autorização ' função ' e selecione Adicionar.
1. Alterne para a folha código + teste e copie e cole o código de exemplo abaixo *sobre o código existente* que aparece.
1. Selecione Salvar.

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

   > [!TIP]
   > O código da função script c# que você acabou de colar simplesmente registra uma linha nos logs de funções e retorna o texto "Olá, Mundo" com alguns dados dinâmicos (data e hora).

1. Selecione "integração" na folha à esquerda e clique no link http (req) dentro da caixa "gatilho".
1. Na lista suspensa ' métodos HTTP selecionados ', desmarque o método HTTP POST, deixando apenas obter selecionado e, em seguida, clique em salvar.
1. Volte para a guia código + teste, clique em "obter URL da função", em seguida, copie a URL que aparece e salve-a para mais tarde.

   > [!NOTE]
   > As associações que você acabou de criar simplesmente informam funções para responder a solicitações HTTP GET anônimas para a URL que você acabou de copiar ( `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` ). Agora temos uma API https sem servidor escalonável, que é capaz de retornar uma carga muito simples.
   >
   > Agora você pode testar a chamada dessa API de um navegador da Web usando sua versão da URL acima que você acabou de copiar e salvar. Você também pode remover a parte dos parâmetros da cadeia de caracteres de consulta "? Code = secretkey" da URL e testar novamente para provar que Azure Functions retornará um erro 401.

## <a name="configure-and-secure-the-function-api"></a>Configurar e proteger a API de função

1. Duas áreas extras no aplicativo de funções precisam ser configuradas (restrições de rede e autorização).
1. Em primeiro lugar, vamos configurar a autenticação/autorização, portanto, navegue de volta para a folha raiz do aplicativo de funções por meio de breadcrumb.
1. Em seguida, selecione "autenticação/autorização" (em "configurações").
1. Ative o recurso de autenticação do serviço de aplicativo.
1. Defina a ação a ser tomada quando a solicitação não for autenticada na lista suspensa para "fazer logon com Azure Active Directory".
1. Em ' provedores de autenticação ', escolha ' Azure Active Directory '.
1. Escolha ' avançado ' na opção de modo de gerenciamento.
1. Cole a ID do cliente [Application] do aplicativo back-end (de Azure AD B2C) na caixa ' ID do cliente '
1. Cole o ponto de extremidade de configuração do Open-ID bem conhecido da política inscrever-se e entrar na caixa URL do emissor (registramos essa configuração anteriormente).
1. Clique em ' Mostrar segredo ' e cole o segredo do cliente do aplicativo de back-end na caixa apropriada.
1. Selecione OK, que o levará de volta à folha/tela de seleção do provedor de identidade.
1. Deixe o [repositório de token](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#token-store) habilitado em configurações avançadas (padrão).
1. Clique em ' salvar ' (na parte superior esquerda da folha).

   > [!IMPORTANT]
   > Agora, sua API de função será implantada e deverá lançar 401 respostas se o JWT correto não for fornecido como uma autorização: cabeçalho de portador e deverá retornar dados quando uma solicitação válida for apresentada.  
   > Você adicionou segurança adicional de defesa aprofundada no EasyAuth Configurando a opção ' fazer logon com o Azure AD ' para lidar com solicitações não autenticadas. Lembre-se de que isso alterará o comportamento de solicitação não autorizado entre o Aplicativo de funções de back-end e o SPA como o EasyAuth emitirá um redirecionamento de 302 para o AAD em vez de uma resposta 401 não autorizada, corrigiremos isso usando o gerenciamento de API mais tarde.  
   >
   > Ainda não temos nenhuma segurança IP aplicada, se você tiver uma chave válida e um token OAuth2, qualquer pessoa poderá chamá-la de qualquer lugar-idealmente, queremos forçar todas as solicitações a surgirem por meio do gerenciamento de API.  
   > 
   > Se você estiver usando o tipo de consumo APIM, não [haverá um IP virtual de gerenciamento de API do Azure dedicado](./api-management-howto-ip-addresses.md#ip-addresses-of-consumption-tier-api-management-service) para permitir-List com as funções de restrições de acesso. No SKU padrão do gerenciamento de API do Azure e acima [do VIP há um único locatário e pelo tempo de vida do recurso](./api-management-howto-ip-addresses.md#changes-to-the-ip-addresses). Para a camada de consumo do gerenciamento de API do Azure, você pode bloquear suas chamadas de API por meio da chave de função secreta compartilhada na parte do URI que você copiou acima. Além disso, para o nível de consumo – as etapas 12-17 abaixo não se aplicam.

1. Fechar a folha "autenticação/autorização" 
1. Abra a *folha gerenciamento de API do portal* e, em seguida, abra *sua instância* do.
1. Registre o VIP privado mostrado na guia Visão geral.
1. Retorne à *folha Azure Functions do portal* e abra *a instância* novamente.
1. Selecione ' rede ' e, em seguida, selecione ' configurar restrições de acesso '
1. Clique em ' Adicionar regra ' e insira o VIP copiado na etapa 3 acima no formato XX. XX. XX. XX/32.
1. Se você quiser continuar a interagir com o portal do Functions e executar as etapas opcionais abaixo, você também deverá adicionar seu próprio endereço IP público ou intervalo CIDR aqui.
1. Quando houver uma entrada de permissão na lista, o Azure adicionará uma regra de negação implícita para bloquear todos os outros endereços.

Você precisará adicionar blocos de endereços formatados por CIDR ao painel restrições de IP. Quando você precisar adicionar um único endereço, como o VIP de gerenciamento de API, será necessário adicioná-lo no formato XX. XX. XX. XX/32.

   > [!NOTE]
   > Agora, sua API de função não deve ser chamável de qualquer lugar além do gerenciamento de API ou seu endereço.

1. Abra a *folha gerenciamento de API* e, em seguida, abra *sua instância do*.
1. Selecione a folha APIs (em APIs).
1. No painel ' adicionar uma nova API ', escolha ' Aplicativo de funções ' e, em seguida, selecione ' completo ' na parte superior do pop-up.
1. Clique em procurar, escolha o aplicativo de funções no qual você está hospedando a API e clique em selecionar. Em seguida, clique em selecionar novamente.
1. Dê ao API um nome e uma descrição para o uso interno do gerenciamento de API e adicione-o ao produto ' ilimitado '.
1. Copie e registre a ' URL base ' da API e clique em ' criar '.
1. Clique na guia "configurações" e, em assinatura – alternar para a caixa de seleção "assinatura necessária", pois usaremos o token JWT OAuth nesse caso para obter o limite de taxa. Observe que, se você estiver usando a camada de consumo, isso ainda seria necessário em um ambiente de produção. 

   > [!TIP]
   > Se estiver usando a camada de consumo de APIM, o produto ilimitado não estará disponível como um pronto para uso. Em vez disso, navegue até "produtos" em "APIs" e clique em "Adicionar".  
   > Digite "ilimitado" como o nome do produto e a descrição e selecione a API que você acabou de adicionar do texto explicativo de APIs "+" na parte inferior esquerda da tela. Marque a caixa de seleção "publicado". Deixe o restante como padrão. Por fim, pressione o botão "criar". Isso criou o produto "ilimitado" e o atribuiu à sua API. Você pode personalizar o novo produto mais tarde.

## <a name="configure-and-capture-the-correct-storage-endpoint-settings"></a>Configurar e capturar as configurações corretas do ponto de extremidade de armazenamento

1. Abra a folha contas de armazenamento na portal do Azure 
1. Selecione a conta que você criou e selecione a folha "site estático" na seção Configurações (se você não vir uma opção "site estático", verifique se você criou uma conta v2).
1. Defina o recurso de hospedagem da Web estática como ' habilitado ' e defina o nome do documento de índice como ' index.html ' e clique em ' salvar '.
1. Anote o conteúdo do ' ponto de extremidade primário ' para mais tarde, pois esse local é onde o site de front-end será hospedado.

   > [!TIP]
   > Você pode usar o armazenamento de BLOBs do Azure + a regravação de CDN ou Azure App serviço para hospedar o recurso de Hospedagem de site estático do SPA, mas o armazenamento de BLOBs nos oferece um contêiner padrão para fornecer conteúdo da Web estático/HTML/js/CSS do armazenamento do Azure e inferirá uma página padrão para o trabalho de zero.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Configurar as políticas **CORS** e **Validate-JWT**

> As seções a seguir devem ser seguidas, independentemente da camada APIM que está sendo usada. A URL da conta de armazenamento é da conta de armazenamento que você terá disponibilizado nos pré-requisitos na parte superior deste artigo.
1. Alterne para a folha gerenciamento de API do portal e abra sua instância do.
1. Selecione APIs e, em seguida, selecione "todas as APIs".
1. Em "processamento de entrada", clique no botão de exibição de código "</>" para mostrar o editor de políticas.
1. Edite a seção de entrada e cole o XML abaixo para que ele seja lido da seguinte maneira.
1. Substituir os seguintes parâmetros na política
1. {PrimaryStorageEndpoint} (O ' ponto de extremidade de armazenamento primário ' copiado na seção anterior), {b2cpolicy-well-known-OpenID} (o ' ponto de extremidade de configuração do OpenID conhecido ' que você copiou anteriormente) e {backend-API-Application-Client-ID} (a ID do aplicativo/cliente B2C para a **API de back-end**) com os valores corretos salvos anteriormente.
1. Se você estiver usando a camada de consumo do gerenciamento de API, deverá remover a política de limite de taxa por chave, pois essa política não está disponível ao usar a camada de consumo do gerenciamento de API do Azure.

   ```xml
   <inbound>
      <cors allow-credentials="true">
            <allowed-origins>
                <origin>{PrimaryStorageEndpoint}</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="120">
                <method>GET</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
            <expose-headers>
                <header>*</header>
            </expose-headers>
        </cors>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid." require-expiration-time="true" require-signed-tokens="true" clock-skew="300">
         <openid-config url="{b2cpolicy-well-known-openid}" />
         <required-claims>
            <claim name="aud">
               <value>{backend-api-application-client-id}</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <rate-limit-by-key calls="300" renewal-period="120" counter-key="@(context.Request.IpAddress)" />
      <rate-limit-by-key calls="15" renewal-period="60" counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
   </inbound>
   ```

   > [!NOTE]
   > Agora, o gerenciamento de API do Azure é capaz de responder a solicitações de origem cruzada de seus aplicativos JavaScript SPA, e ele executará a limitação, limitação de taxa e validação prévia do token de autenticação JWT que está sendo passado antes de encaminhar a solicitação para a API de função.
   > 
   > Parabéns, agora você tem Azure AD B2C, gerenciamento de API e Azure Functions trabalhando juntos para publicar, proteger e consumir uma API!

   > [!TIP]
   > Se você estiver usando a camada de consumo do gerenciamento de API, em vez da limitação de taxa pelo assunto de JWT ou endereço IP de entrada (limitar a taxa de chamada por política de chave não tem suporte atualmente para a camada de "consumo"), você pode limitar por cota de taxa de chamada [aqui](./api-management-access-restriction-policies.md#LimitCallRate).  
   > Como este exemplo é um aplicativo de página única JavaScript, usamos a chave de gerenciamento de API somente para chamadas de cobrança e limitação de taxa. A autorização e a autenticação reais são manipuladas pelo Azure AD B2C e são encapsuladas no JWT, que é validado duas vezes, uma vez por gerenciamento de API e, em seguida, pelo Azure function de back-end.

## <a name="upload-the-javascript-spa-sample-to-static-storage"></a>Carregar o exemplo de SPA do JavaScript no armazenamento estático

1. Ainda na folha da conta de armazenamento, selecione a folha ' contêineres ' na seção serviço BLOB e clique no contêiner $web que aparece no painel à direita.
1. Salve o código abaixo em um arquivo localmente em seu computador como index.html e, em seguida, carregue o arquivo index.html para o contêiner $web.

   ```html
    <!doctype html>
    <html lang="en">
    <head>
         <meta charset="utf-8">
         <meta http-equiv="X-UA-Compatible" content="IE=edge">
         <meta name="viewport" content="width=device-width, initial-scale=1">
         <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.11.1/js/msal-browser.min.js"></script>
    </head>
    <body>
         <div class="container-fluid">
             <div class="row">
                 <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
                        <div class="container-fluid">
                            <a class="navbar-brand" href="#">Azure Active Directory B2C with Azure API Management</a>
                            <div class="navbar-nav">
                                <button class="btn btn-success" id="signinbtn"  onClick="login()">Sign In</a>
                            </div>
                        </div>
                    </nav>
                 </div>
             </div>
             <div class="row">
                 <div class="col-md-12">
                     <div class="card" >
                        <div id="cardheader" class="card-header">
                            <div class="card-text"id="message">Please sign in to continue</div>
                        </div>
                        <div class="card-body">
                            <button class="btn btn-warning" id="callapibtn" onClick="getAPIData()">Call API</a>
                            <div id="progress" class="spinner-border" role="status">
                                <span class="visually-hidden">Loading...</span>
                            </div>
                        </div>
                     </div>
                 </div>
             </div>
         </div>
         <script lang="javascript">
                // Just change the values in this config object ONLY.
                var config = {
                    msal: {
                        auth: {
                            clientId: "{CLIENTID}", // This is the client ID of your FRONTEND application that you registered with the SPA type in AAD B2C
                            authority:  "{YOURAUTHORITYB2C}", // Formatted as https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantguid or full tenant name including onmicrosoft.com}/{signuporinpolicyname}
                            redirectUri: "{StoragePrimaryEndpoint}", // The storage hosting address of the SPA, a web-enabled v2 storage account - recorded earlier as the Primary Endpoint.
                            knownAuthorities: ["{B2CTENANTDOMAIN}"] // {b2ctenantname}.b2clogin.com
                        },
                        cache: {
                            cacheLocation: "sessionStorage",
                            storeAuthStateInCookie: false 
                        }
                    },
                    api: {
                        scopes: ["{BACKENDAPISCOPE}"], // The scope that we request for the API from B2C, this should be the backend API scope, with the full URI.
                        backend: "{APIBASEURL}/hello" // The location that we will call for the backend api, this should be hosted in API Management, suffixed with the name of the API operation (in the sample this is '/hello').
                    }
                }
                document.getElementById("callapibtn").hidden = true;
                document.getElementById("progress").hidden = true;
                const myMSALObj = new msal.PublicClientApplication(config.msal);
                myMSALObj.handleRedirectPromise().then((tokenResponse) => {
                    if(tokenResponse !== null){
                        console.log(tokenResponse.account);
                        document.getElementById("message").innerHTML = "Welcome, " + tokenResponse.account.name;
                        document.getElementById("signinbtn").hidden = true;
                        document.getElementById("callapibtn").hidden = false;
                    }}).catch((error) => {console.log("Error Signing in:" + error);
                });
                function login() {
                    try {
                        myMSALObj.loginRedirect({scopes: config.api.scopes});
                    } catch (err) {console.log(err);}
                }
                function getAPIData() {
                    document.getElementById("progress").hidden = false; 
                    document.getElementById("message").innerHTML = "Calling backend ... "
                    document.getElementById("cardheader").classList.remove('bg-success','bg-warning','bg-danger');
                    myMSALObj.acquireTokenSilent({scopes: config.api.scopes, account: getAccount()}).then(tokenResponse => {
                        const headers = new Headers();
                        headers.append("Authorization", `Bearer ${tokenResponse.accessToken}`);
                        fetch(config.api.backend, {method: "GET", headers: headers})
                            .then(async (response)  => {
                                if (!response.ok)
                                {
                                    document.getElementById("message").innerHTML = "Error: " + response.status + " " + JSON.parse(await response.text()).message;
                                    document.getElementById("cardheader").classList.add('bg-warning');
                                }
                                else
                                {
                                    document.getElementById("cardheader").classList.add('bg-success');
                                    document.getElementById("message").innerHTML = await response.text();
                                }
                                }).catch(async (error) => {
                                    document.getElementById("cardheader").classList.add('bg-danger');
                                    document.getElementById("message").innerHTML = "Error: " + error;
                                });
                    }).catch(error => {console.log("Error Acquiring Token Silently: " + error);
                        return myMSALObj.acquireTokenRedirect({scopes: config.api.scopes, forceRefresh: false})
                    });
                    document.getElementById("progress").hidden = true;
             }
            function getAccount() {
                var accounts = myMSALObj.getAllAccounts();
                if (!accounts || accounts.length === 0) {
                    return null;
                } else {
                    return accounts[0];
                }
            }
        </script>
     </body>
    </html>
   ```

1. Navegue até o ponto de extremidade primário do site estático que você armazenou anteriormente na última seção.

   > [!NOTE]
   > Parabéns, você acabou de implantar um aplicativo de página única JavaScript na Hospedagem de conteúdo estático do armazenamento do Azure.  
   > Como ainda não configuramos o aplicativo JS com seus Azure AD B2C detalhes – a página ainda não funcionará se você abri-lo.

## <a name="configure-the-javascript-spa-for-azure-ad-b2c"></a>Configurar o JavaScript SPA para Azure AD B2C

1. Agora sabemos onde tudo está: podemos configurar o SPA com o endereço apropriado da API de gerenciamento de API e as IDs de aplicativo/cliente Azure AD B2C corretas.
1. Voltar para a folha armazenamento portal do Azure 
1. Selecione ' contêineres ' (em ' configurações ') 
1. Selecione o contêiner ' $web ' na lista
1. Selecione index.htmblob l na lista 
1. Clique em ' Editar ' 
1. Atualize os valores de autenticação na seção de configuração MSAL para corresponder ao seu aplicativo de *front-end* registrado no B2C anteriormente. Use os comentários de código para obter dicas sobre como os valores de configuração devem ser examinados.
O valor de *Authority* deve estar no formato:-https://{b2ctenantname}. b2clogin. com/TFP/{b2ctenantname}. onmicrosoft. com}/{signupandsigninpolicyname}, se você tiver usado nossos nomes de exemplo e seu locatário B2C for chamado de "contoso", então você esperaria que a autoridade fosse " https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com}/Frontendapp_signupandsignin ".
1. Defina os valores da API para corresponder ao seu endereço de back-end (a URL base da API que você registrou anteriormente e os valores ' b2cScopes ' foram registrados anteriormente para o *aplicativo de back-end*).
1. Clique em Salvar

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Definir os URIs de redirecionamento para o aplicativo de front-end Azure AD B2C

1. Abra a folha Azure AD B2C e navegue até o registro do aplicativo para o aplicativo de frontend do JavaScript.
1. Clique em ' redirecionar URIs ' e exclua o espaço reservado ' https://jwt.ms ' que inserimos anteriormente.
1. Adicione um novo URI para o ponto de extremidade primário (armazenamento) (menos a barra à direita).

   > [!NOTE]
   > Essa configuração fará com que um cliente do aplicativo de front-end receba um token de acesso com declarações apropriadas de Azure AD B2C.  
   > O SPA poderá adicionar isso como um token de portador no cabeçalho HTTPS na chamada para a API de back-end.  
   > 
   > O gerenciamento de API validará previamente o token, as chamadas de limite de taxa para o ponto de extremidade pelo assunto do JWT emitido pela ID do Azure (o usuário) e pelo endereço IP do chamador (dependendo da camada de serviço do gerenciamento de API, consulte a observação acima), antes de passar pela solicitação para a API de recebimento da Azure Function, adicionando a chave de segurança functions.  
   > O SPA renderizará a resposta no navegador.
   >
   > *Parabéns, você configurou Azure AD B2C, gerenciamento de API do Azure Azure Functions Azure App autorização de serviço para trabalhar em harmonia perfeito!*

Agora temos um aplicativo simples com uma API segura simples, vamos testá-lo.

## <a name="test-the-client-application"></a>Testar o aplicativo cliente

1. Abra a URL do aplicativo de exemplo que você anotou na conta de armazenamento que você criou anteriormente.
1. Clique em "entrar" no canto superior direito, esse clique abrirá seu perfil de Azure AD B2C inscrever-se ou entrar.
1. O aplicativo deve ser bem-vindo a você pelo nome do perfil B2C.
1. Agora, clique em "chamar API" e a página deverá ser atualizada com os valores enviados de volta de sua API protegida.
1. Se você clicar *repetidamente* no botão chamar API e estiver executando na camada de desenvolvedor ou acima do gerenciamento de API, você deve observar que sua solução começará a classificar o limite da API e esse recurso deverá ser relatado no aplicativo com uma mensagem apropriada.

## <a name="and-were-done"></a>E pronto

As etapas acima podem ser adaptadas e editadas para permitir muitos usos diferentes de Azure AD B2C com o gerenciamento de API.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Azure Active Directory e o OAuth2.0](../active-directory/develop/authentication-vs-authorization.md).
* Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre o Gerenciamento de API.
* Para outras maneiras de proteger seu serviço de back-end, confira [Autenticação de certificado mútuo](api-management-howto-mutual-certificates.md).
* [Crie uma instância de serviço de gerenciamento de API](get-started-create-service-instance.md).
* [Gerencie sua primeira API](import-and-publish.md).
