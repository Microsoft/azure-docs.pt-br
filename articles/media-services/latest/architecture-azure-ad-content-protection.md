---
title: Proteção de conteúdo de ponta a ponta usando o Azure AD
description: Este artigo ensina a proteger seu conteúdo com os Serviços de Mídia do Azure e o Azure Active Directory
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 9c81a9b48ff9fa305385c45266d88deb4047f70f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599461"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>Tutorial: Proteção de conteúdo de ponta a ponta usando o Azure AD

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Com este tutorial e o exemplo de player fornecido, você pode configurar um subsistema de proteção de conteúdo de mídia de ponta a ponta no AMS (Serviços de Mídia do Azure) e no ADD (Azure Active Directory) para transmitir conteúdo de mídia com todos os formatos de contêiner, codec, protocolos de streaming e DRM/AES-128 compatíveis do AMS. O exemplo é genérico o suficiente para acesso seguro a qualquer API REST protegida pelo OAuth 2 por meio do fluxo de código de autorização com PKCE (chave de prova para troca de código). (O serviço de entrega de licença dos Serviços de Mídia do Azure é apenas um deles.) Ele também funciona para a API do Microsoft Graph ou qualquer API REST desenvolvida personalizada protegida com o Fluxo de Código de Autorização do OAuth 2. Este é o documento complementar para o [código de exemplo](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).

Neste tutorial, você irá:

> [!div class="checklist"]
>
> * Considere os requisitos de autenticação
> * Entenda como o aplicativo funciona
> * Registrar um aplicativo de recurso de back-end
> * Registrar um aplicativo cliente
> * Configurar a política de chave de conteúdo da conta dos Serviços de Mídia e as políticas de streaming
> * Configurar o aplicativo de player

Se você não tiver uma assinatura dos Serviços de Mídia do Azure, crie uma [conta de avaliação gratuita](https://azure.microsoft.com/free/) do Azure criar uma conta de Serviços de Mídia.

### <a name="duration"></a>Duration
O tutorial deve levar cerca de duas horas para ser concluído depois que você tiver a tecnologia de pré-requisito pronta para uso.

## <a name="prerequisites"></a>Pré-requisitos

As versões de tecnologia e os conceitos mais recentes são usados. É recomendável que você se familiarize com eles antes de iniciar este tutorial.

### <a name="prerequisite-knowledge"></a>Pré-requisito de conhecimento

É opcional, mas recomendamos que você esteja familiarizado com os seguintes conceitos antes de iniciar este tutorial:

* DRM (Digital Rights Management )
* [AMS (Serviços de Mídia do Azure) v3](./media-services-overview.md)
* As [políticas de chave de conteúdo](drm-content-key-policy-concept.md) do AMS usando a API do AMS v3, portal do Azure ou a [ferramenta do AMSE (Gerenciador de Serviços de Mídia do Azure)](https://github.com/Azure/Azure-Media-Services-Explorer)
* Pontos de extremidade do Azure AD v2 na [Plataforma de Identidade da Microsoft](../../active-directory/develop/index.yml)
* Autenticação de nuvem moderna, como [OAuth 2.0 e OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md)
  * [Fluxo de código de autorização no OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md) e por que PKCE é necessária
  * [Permissão delegada vs. permissão de aplicativo](../../active-directory/develop/developer-glossary.md#permissions)
* [Token JWT](../../active-directory/develop/access-tokens.md), suas declarações e substituição de chave de assinatura (inclusas no exemplo.)

### <a name="prerequisite-code-and-installations"></a>Código de pré-requisito e instalações

* O código de exemplo. Baixe o [código de exemplo](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).
* Uma instalação do Visual Studio Code. Baixe o Visual Studio Code aqui [https://code.visualstudio.com/download](https://code.visualstudio.com/download).
* Uma instalação do Node.js. Baixe o Node.js aqui [https://nodejs.org](https://nodejs.org). O NPM vem com a instalação.
* Uma [assinatura do Azure](https://azure.microsoft.com/free/).
* Uma conta do AMS (Serviços de Mídia do Azure).
* @azure/msal-browser v2.0, um dos membros na [MSAL (Biblioteca de Autenticação da Microsoft)](../../active-directory/develop/msal-overview.md) a família SDK para diferentes plataformas de cliente
* A versão mais recente do [Player de Mídia do Azure](https://github.com/Azure-Samples/azure-media-player-samples)(incluída no exemplo.)
* As credenciais de FPS da Apple se você quiser incluir FairPlay DRM e o certificado de aplicativo hospedado com CORS que seja acessível via JavaScript do lado do cliente.

> [!IMPORTANT]
> Este tutorial usa o .NET para criar a restrição de política de chave de conteúdo.  Se você não for um desenvolvedor do .NET, mas quiser experimentar o Node.js para se conectar aos Serviços de Mídia do Azure, leia [Conectar-se à API de Serviços de Mídia v3 – Node.js](configure-connect-nodejs-howto.md). Também há um módulo do Node.js disponível para manipular a substituição de chave automaticamente. Confira o [módulo passport-ad](https://github.com/AzureAD/passport-azure-ad) do Node.js.

## <a name="consider-the-authentication-and-authorization-requirements"></a>Considere os requisitos de autenticação e autorização

Alguns desafios são apresentados na criação do subsistema. Ele tem várias partes móveis, há restrições de aplicativo cliente e a substituição de chave do Azure AD que ocorre a cada seis semanas.

O SPA (aplicativo de página única ) usado neste tutorial leva em conta os desafios para os requisitos de autenticação e as restrições a seguir. Ele usa:

* Os pontos de extremidade do Azure AD v2 como plataforma de desenvolvedor do Azure AD (pontos de extremidade v1) estão mudando para a plataforma de identidade da Microsoft (pontos de extremidade v2).
* Fluxo do Código de Autorização porque o fluxo de concessão implícita do OAuth 2 foi preterido.
* Um aplicativo que está sujeito às seguintes restrições:
    * Um cliente público não pode ocultar o segredo do cliente.  O fluxo de código de autorização sozinho requer ocultar o segredo do cliente, portanto, o fluxo do código de autorização com PKCE é usado.
    * Um aplicativo baseado em navegador que está sujeito a uma área restrita de segurança do navegador (restrição CORS/simulação).
    * Um aplicativo baseado em navegador que usa JavaScript moderno que está sujeito às restrições de segurança do JavaScript (acessibilidade de cabeçalho personalizado, correlation-id).

## <a name="understand-the-subsystem-design"></a>Entender o design do subsistema

O design do subsistema é mostrado no diagrama a seguir.  Ele tem três camadas:

* Camada de Back Office (em preto) para configurar a política de chave de conteúdo e publicar conteúdo para streaming
* Pontos de extremidade públicos (em azul), que são pontos de extremidade voltados para o cliente/player que fornecem autenticação, autorização, licença DRM e conteúdo criptografado
* Aplicativo do player (em azul claro) que integra todos os componentes e
    * lida com a autenticação do usuário por meio do Azure AD.
    * manipula a aquisição do access_token do Azure AD.
    * recebe o manifesto e o conteúdo criptografado do AMS/CDN.
    * adquire a licença DRM dos Serviços de Mídia do Azure.
    * manipula a descriptografia, a decodificação e a exibição de conteúdo.

![tela para análise de tokens JWT](media/aad-ams-content-protection/subsystem.svg)

Leia [Design de um sistema de proteção de conteúdo de vários DRM com controle de acesso](./architecture-design-multi-drm-system.md) para obter mais detalhes sobre o subsistema.

## <a name="understand-the-single-page-app"></a>Entender o aplicativo de página única

O aplicativo de player é um aplicativo de página única (SPA), desenvolvido no Visual Studio Code usando:

* Node.js com o JavaScript ES 6
* @azure/msal-browser 2.0 beta
* SDK do Player de Mídia do Azure
* Fluxo do OAuth 2 em pontos de extremidade do Azure AD v2 (Plataforma de Identidade da Microsoft)

O aplicativo de player SPA conclui as seguintes ações:

* Autenticação de usuário para usuários nativos do locatário e usuários convidados de outros locatários do AAD ou contas do MSA. Os usuários podem optar por entrar por meio de um pop-up ou redirecionamento do navegador (para navegadores que não permitem pop-ups, como o Safari).
* Aquisição de `access_token` por meio do fluxo de código de autorização com PKCE.
* A renovação de `access_token` (tokens emitidos pelo AAD é válida por uma hora), para a qual `refresh_token` também é adquirido.
* Análise de tokens JWT (`access_token` e `id_token`) para teste/inspeção.
* Aquisição de licenças DRM para todas as três chaves de conteúdo DRMs ou AES-128.
* Streaming de conteúdo sob várias combinações de DRM vs. Protocolo de Streaming vs. Formato de Contêiner. A cadeia de caracteres de formato correta é gerada para cada combinação.
* Descriptografia, decodificação e exibição.
* Chamadas à API do Microsoft Graph para fins de solução de problemas. <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

A tela para conexão, aquisição de token, renovação de token e exibição de token:

 ![Tela para entrada, aquisição de token, renovação de token e exibição de token](media/aad-ams-content-protection/token-acquisition.png)

A tela para analisar tokens JWT (access_token ou id_token):

![Captura de tela que mostra a análise de tokens JWT.](media/aad-ams-content-protection/parsing-jwt-tokens.png)

A tela para testar o conteúdo protegido com diferentes combinações de DRM/AES vs. Protocolos de Streaming vs. Formato de Contêiner:

![Captura de tela que mostra o teste de conteúdo protegido com diferentes combinações de DRM ou AES versus Protocolos de Streaming versus Formato de Contêiner](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>Escolher um locatário do Azure AD

> [!NOTE]
> Daqui em diante, supõe-se que você tenha feito logon no portal do Azure e tenha pelo menos um locatário do Azure AD.

Escolha um locatário do Azure AD a ser usado para nosso exemplo de ponta a ponta. Você tem duas opções:

* Um locatário existente do Azure AD. Qualquer assinatura do Azure deve ter um locatário do Azure AD, mas um locatário do Azure AD pode ser usado por várias assinaturas do Azure.
* Um novo locatário do Azure AD *não* usado por nenhuma assinatura do Azure. Se você escolher a opção de novo locatário, a conta de serviço de mídia e o aplicativo de player de exemplo deverão estar em uma assinatura do Azure que usa um locatário do Azure AD separado. Isso proporciona alguma flexibilidade. Por exemplo, você pode usar seu locatário do Azure AD, mas também a conta de serviço de mídia do cliente na assinatura do Azure do cliente.

## <a name="register-the-backend-resource-app"></a>Registrar o aplicativo de recurso de back-end

1. Navegue até o locatário do Azure AD que você escolheu ou criou.
1. Selecione **Azure Active Directory** no menu.
1. Selecione **Registros de aplicativo** no menu.
1. Clique em **+ Novo Registro**.
1. Dê ao aplicativo o nome *LicenseDeliveryResource2* (em que 2 indica pontos de extremidade do AAD v2).
1. Selecione **Contas neste diretório organizacional somente ([*seu nome de locatário*] somente – locatário único)** . Se você quiser habilitar o acesso a vários locatários, selecione uma das outras opções de multilocatário.
1. O **URI de redirecionamento** é opcional e pode ser alterado posteriormente.
1. Clique em **Registrar**. A exibição Registros de aplicativo aparecerá.
1. Selecione **Manifesto** no menu. A exibição Manifesto aparecerá.
1. Altere o valor de `accessTokenAcceptedVersion` para *2* (sem aspas).
1. Altere o valor de `groupMembershipClaims` para *"SecurityGroup"* (com aspas).
1. Clique em **Save** (Salvar).
1. Selecione **Expor uma API** no menu. A exibição Adicionar um escopo aparecerá. (O Azure fornece um URI de ID do aplicativo, mas, se você quiser alterá-lo, poderá editar no campo URI da ID do aplicativo.)
1. Clique em **Salvar e continuar**. O modo de exibição será alterado. Para cada uma das configurações na coluna configuração na tabela abaixo, insira o valor na coluna valor e clique em **Adicionar escopo**.

| Configuração | Valor | Descrição |
| ------- | ----- | ----------- |
| Nome do escopo | *DRM.License.Delivery* | Como o escopo aparecerá quando o acesso a essa API estiver sendo solicitado e em tokens de acesso quando o escopo tiver sido concedido a um aplicativo cliente. Isso deve ser exclusivo em todo o aplicativo. Uma prática recomendada é usar "resource.operation.constraint" como um padrão para gerar o nome. |
| Quem pode consentir? | *Administradores e usuários* | Determina se os usuários podem consentir esse escopo em diretórios em que o consentimento do usuário está habilitado. |
| Nome de exibição de consentimento do administrador | *Entrega de licença do DRM* | Como o escopo será chamado na tela de consentimento quando os administradores consentirem com este escopo. |
| Descrição do consentimento do administrador** | *Escopo de recurso de back-end de entrega de licença DRM* | Uma descrição detalhada do escopo que é exibido quando os administradores de locatários expandem um escopo na tela de consentimento. |
| Nome de exibição do consentimento do usuário | *DRM.License.Delivery* | Como o escopo será chamado na tela consentimento quando os usuários consentirem com este escopo. |
| Descrição de consentimento do usuário | *Escopo de recurso de back-end de entrega de licença DRM* | Esta é uma descrição detalhada do escopo que é exibido quando os usuários expandem um escopo na tela de consentimento. |
| Estado | *Enabled* | Determina se esse escopo está disponível para os clientes solicitarem. Defina-o como "Desabilitado" para escopos que você não deseja que fiquem visíveis para os clientes. Somente escopos desabilitados podem ser excluídos e é recomendável aguardar pelo menos uma semana depois que um escopo tiver sido desabilitado antes de excluí-lo para garantir que nenhum cliente ainda o esteja usando. |

## <a name="register-the-client-app"></a>Registrar o aplicativo cliente

1. Navegue até o locatário do Azure AD que você escolheu ou criou.
1. Selecione **Azure Active Directory** no menu.
1. Selecione **Registros de aplicativo** no menu.
1. Clique em **+ Novo Registro**.
1. Forneça um nome ao aplicativo cliente, por exemplo, *Proteção de Conteúdo do AAD do AMS*.
1. Selecione **Contas neste diretório organizacional somente ([*seu nome de locatário*] somente – locatário único)** . Se você quiser habilitar o acesso a vários locatários, selecione uma das outras opções de multilocatário.
1. O **URI de redirecionamento** é opcional e pode ser alterado posteriormente.
1. Clique em **Registrar**.
1. Selecione **Permissões de API** no menu.
1. Clique em **+ Adicionar uma permissão**. A exibição Solicitar permissões de API será aberta.
1. Clique na guia **Minha API** e selecione o aplicativo *LicenseDeliveryResource2* criado na última seção.
1. Clique na seta DRM e marque a permissão *DRM.License.Delivery*.
1. Clique em **Adicionar permissões**. A exibição Adicionar permissões será fechada.
1. Selecione **Manifesto** no menu. A exibição Manifesto aparecerá.
1. Localize e adicione os seguintes pares de valor ao atributo `replyUrlsWithType`:

   ```json
   "replyUrlsWithType": [
        {
            "url": "https://npmwebapp.azurewebsites.net/",
            "type": "SPA"
        },
        {
            "url": "http://localhost:3000/",
            "type": "SPA"
        }
    ],
   ```

    > [!NOTE]
    > Neste ponto, você ainda não tem a URL para seu aplicativo de player.  Se você estiver executando o aplicativo de seu servidor Web localhost, poderá usar apenas o par de valores de localhost. Depois de implantar seu aplicativo de player, você poderá adicionar a entrada aqui com a URL implantada.  Se você se esquecer de fazer isso, verá uma mensagem de erro na entrada do Azure AD.

1. Clique em **Save** (Salvar).
1. Por fim, para verificar se a configuração está correta, selecione **Autenticação**.  A exibição de autenticação aparecerá. O aplicativo cliente será listado como um SPA (aplicativo de página única), o URI de redirecionamento será listado e o tipo de concessão será o fluxo do código de autorização com PKCE.

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>Configurar a política de chave de conteúdo da conta dos Serviços de Mídia e as políticas de streaming

**Esta seção pressupõe que você seja um desenvolvedor do .NET e esteja familiarizado com o uso da API do AMS v3.**

> [!NOTE]
> No momento da redação deste artigo, você não podia usar o portal do Azure para a configuração da política de chave de conta dos Serviços de Mídia porque ele não dava suporte ao uso de uma chave de assinatura de token assimétrico com OpenID-Config.  A instalação deve oferecer suporte à substituição de chave do Azure AD porque o token emitido pelo Azure AD é assinado por uma chave assimétrica e a chave é transferida a cada seis semanas. Portanto, este tutorial usa o .NET e a API do AMS v3.

A configuração da [política de chave de conteúdo](drm-content-key-policy-concept.md) e das [políticas de streaming](stream-streaming-policy-concept.md) para DRM e AES-128 se aplicam.  Altere a `ContentKeyPolicyRestriction` na política de chave de conteúdo.

Veja abaixo o código do .NET para criar a restrição de política de chave de conteúdo.

```dotnetcli
ContentKeyPolicyRestriction objContentKeyPolicyRestriction;

//use Azure Active Directory OpenId discovery document, supporting key rollover
objContentKeyPolicyRestriction = new ContentKeyPolicyTokenRestriction()
    {
        OpenIdConnectDiscoveryDocument = ConfigAccessor.AppSettings["ida_AADOpenIdDiscoveryDocument"]
    };

string audience = ConfigAccessor.AppSettings["ida_audience"];
string issuer   = ConfigAccessor.AppSettings["ida_issuer"];

ContentKeyPolicyTokenRestriction objContentKeyPolicyTokenRestriction = (ContentKeyPolicyTokenRestriction)objContentKeyPolicyRestriction;
objContentKeyPolicyTokenRestriction.Audience             = audience;
objContentKeyPolicyTokenRestriction.Issuer               = issuer;
objContentKeyPolicyTokenRestriction.RestrictionTokenType = ContentKeyPolicyRestrictionTokenType.Jwt;

objContentKeyPolicyRestriction = objContentKeyPolicyTokenRestriction;

return objContentKeyPolicyRestriction;
```

Altere os valores `ida_AADOpenIdDiscoveryDocument`, `ida_audience` e `ida_issuer` no código acima. Para localizar os valores para esses itens no portal do Azure:

1. Selecione o locatário do AAD que você usou anteriormente, clique em **Registros de aplicativo** no menu e clique no link **Pontos de extremidade**.
1. Selecione e copie o valor do campo **Documento de metadados do OpenIdConnect** e cole-o no código como o valor `ida_AADOpenIdDiscoveryDocument`.
1. O valor de `ida_audience` é a ID do aplicativo (cliente) do aplicativo registrado *LicenseDeliveryResource2*.
1. O valor `ida_issuer` é a URL `https://login.microsoftonline.com/[tenant_id]/v2.0`. Substitua [*id_de_locatário*] pela sua ID de locatário.

## <a name="set-up-the-sample-player-app"></a>Configurar o aplicativo de player de exemplo

Se você ainda não fez isso, clone ou baixe o aplicativo do repositório de origem: [https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).

Você tem duas opções para configurar o aplicativo de player:

* Personalização mínima (substituindo apenas alguns valores de cadeia de caracteres constantes, como client_id, tenant_id e URL de streaming), mas você deve usar Visual Studio Code e o Node.js.
* Se preferir usar outro IDE e outra plataforma da Web, como ASP.NET Core, você pode colocar os arquivos de página da Web, os arquivos JavaScript e o arquivo CSS em seu projeto, já que o aplicativo de player em si não usa nenhum código do servidor.

### <a name="option-1"></a>Opção 1

1. Inicie o Visual Studio Code.
1. Para abrir o projeto, clique em Arquivo -> Abrir Pasta -> navegue até a pasta pai do arquivo *package.json* e selecione-a.
1. Abra o arquivo JavaScript *public/javascript/constants.js*.
1. Substitua `OAUTH2_CONST.CLIENT_ID` pelo `client_id` do seu aplicativo cliente registrado no locatário do AAD.  Você pode encontrar o `client_id` na seção Visão geral do aplicativo registrado no portal do Azure. Observação: é a ID do cliente, não a ID de objeto.
1. Substitua `OAUTH2_CONST.TENANT_ID` pela `tenant_id` do seu locatário do Azure AD. Você pode encontrar a `tenant_id` clicando no menu Azure Active Directory. A tenant_id aparecerá na seção visão geral.
1. Substitua `OAUTH2_CONST.SCOPE` pelo escopo que você adicionou ao seu aplicativo cliente registrado. Você pode encontrar o escopo navegando até o aplicativo cliente registrado no menu **Registros de aplicativo** e selecionando seu aplicativo cliente:
    1. Selecione seu aplicativo cliente, clique no menu **Permissões de API** e selecione o escopo *DRM.License.Delivery* na permissão de API *LicenseDeliveryResource2*. A permissão deve estar em um formato como *api://df4ed433-dbf0-4da6-b328-e1fe05786db5/DRM.License.Delivery*. **Importante**: Mantenha o espaço na frente de `offline_access` no `OAUTH2_CONST.SCOPE`.
1. Substitua as duas cadeias de caracteres constantes por `AMS_CONST`, conforme mostrado abaixo. Uma é a URL de streaming protegida do seu ativo de teste e a outra é a URL do certificado de aplicativo de FPS se você quiser incluir o caso de teste FairPlay. Caso contrário, você pode deixá-lo como está para `AMS_CONST.APP_CERT_URL`. Em seguida, clique em **Salvar**.

```javascript
//defaults in ams.js
class AMS_CONST {
    static get DEFAULT_URL() {
        return "https://eventgridmediaservice-usw22.streaming.media.azure.net/9591e337-ae90-420e-be30-1da36c06665b/MicrosoftElite01.ism/manifest(format=mpd-time-csf,encryption=cenc)";
    }
    //FairPlay application cert URL
    static get APP_CERT_URL() {
     return `${window.location.href}cert/FPSAC.cer`;
    }
}
```

Para testar localmente:

1. No VSC (Visual Studio Code), selecione **Exibir** no menu principal e **Terminal**.
1. Se você ainda não tiver instalado o NPM, no prompt de comando, digite `npm install`.
1. Insira `npm start` no prompt de comando. (Se NPM não for iniciado, tente alterar o diretório para `npmweb` inserindo `cd npmweb` no prompt de comando.)
1. Use um navegador para navegar até `http://localhost:3000`.

Dependendo do navegador que você usa, escolha a combinação correta de DRM/AES, protocolo de streaming e formato de contêiner para testar após a entrada (aquisição de `access_token`). Se você estiver testando no Safari no macOS, verifique a opção Redirecionar API, pois o Safari não permite pop-ups. A maioria dos outros navegadores permite pop-ups e opções de redirecionamento.

### <a name="option-2"></a>Opção 2

Se você planeja usar outra plataforma IDE/Web e/ou um servidor Web, como o IIS em execução no computador de desenvolvimento, copie os arquivos a seguir em um novo diretório em seu servidor Web local. É nos caminhos abaixo que você os encontrará no código que você baixou.

* *views/index.ejs* (altere o sufixo para .html)
* *views/jwt.ejs* (altere o sufixo para .html)
* *views/fo.ejs* (altere o sufixo para .html)
* *public/* * (arquivos JavaScript, CSS, imagens, conforme mostrado abaixo)

1. Copie os arquivos encontrados na pasta *view* para a raiz do novo diretório.
1. Copie as *pastas* encontradas na pasta *public* para a raiz do novo diretório.
1. Altere as extensões dos arquivos de `.ejs` para `.html`. (Nenhuma variável do lado do servidor é usada, de modo que você pode alterá-la com segurança.)
1. Abra *index.html* no VSC (ou outro editor de código) e altere os caminhos `<script>` e `<link>` para que eles reflitam o local em que os arquivos estão.  Se você seguiu as etapas anteriores, basta excluir o `\` no caminho.  Por exemplo, `<script type="text/javascript" src="/javascript/constants.js"></script>` torna-se `<script type="text/javascript" src="javascript/constants.js"></script>`.
1. Personalize as constantes no arquivo *javascript/constants.js* como na opção 1.

## <a name="common-customer-scenarios"></a>Cenários comuns de clientes

Agora que você concluiu o tutorial e tem um subsistema de trabalho, pode tentar modificá-lo para os seguintes cenários de cliente:

### <a name="azure-role-based-access-control-azure-rbac-for-license-delivery-via-azure-ad-group-membership"></a>Azure RBAC (controle de acesso baseado em função do Azure) para entrega de licença por meio da associação de grupo do Azure AD

Até agora, o sistema permite que qualquer usuário que possa entrar para obter uma licença válida e reproduzir o conteúdo protegido.

É um requisito comum do cliente que um subconjunto de usuários autenticados tem permissão para assistir ao conteúdo, enquanto outros não são, por exemplo, um cliente que oferece assinaturas básicas e premium para conteúdo de vídeo. Seus clientes, que pagaram por uma assinatura básica, não devem poder assistir ao conteúdo que requer uma assinatura premium. Abaixo estão as etapas adicionais necessárias para atender a esse requisito:

#### <a name="set-up-the-azure-ad-tenant"></a>Configurar o locatário do Azure AD

1. Configure duas contas em seu locatário. Elas podem ser chamadas *premium_user* e *basic_user*;
1. Crie um grupo de usuários e chame-o de *PremiumGroup*.
1. Adicione *premium_user* ao *PremiumGroup* como um membro, mas não adicione o *basic_user* ao grupo.
1. Anote a **ID do objeto** do *PremiumGroup*.

#### <a name="set-up-the-media-services-account"></a>Configurar a conta dos Serviços de Mídia

Modifique `ContentKeyPolicyRestriction` (conforme mostrado na seção acima na configuração da conta de serviço de mídia), adicionando uma declaração denominada *Grupos*, em que `ida_EntitledGroupObjectId` tem a ID de objeto *PremiumGroup* como seu valor:

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

A declaração *groups* é um membro de um [Conjunto de Declarações Restritas](../../active-directory/develop/reference-claims-mapping-policy-type.md#claim-sets) no Azure AD.

#### <a name="test"></a>Teste

1. Entre com a conta *premium_user*. Você deve ser capaz de reproduzir o conteúdo protegido.
1. Entre com a conta *basic_user*. Você deve receber um erro indicando que o vídeo está criptografado, mas não há nenhuma chave para descriptografá-lo. Se você ver os Eventos, os erros e os downloads com o menu suspenso na parte inferior da sobreposição de diagnóstico do player, a mensagem de erro deverá indicar falha de aquisição de licença devido ao valor de declaração ausente para a declaração de grupos no JWT emitido pelo ponto de extremidade de token do Azure AD.

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>Suporte a várias contas de serviço de mídia (em várias assinaturas)

Um cliente pode ter várias contas de serviço de mídia em uma ou em várias assinaturas do Azure. Por exemplo, um cliente pode ter uma conta de serviço de mídia como produção primária, outra como secundária/backup e outra para desenvolvimento/teste.

Tudo o que você precisa fazer é usar o mesmo conjunto de parâmetros usado na seção (Configuração na Conta de Serviço de Mídia) na criação da `ContentKeyPolicyRestriction` em todas as contas de serviço de mídia.

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>Como dar suporte a um cliente, seus fornecedores e/ou subsidiárias em vários locatários do AAD

Como os usuários da solução, as subsidiárias do cliente, os fornecedores/parceiros podem residir em locatários diferentes do AAD, como `mycustomer.com`, `mysubsidiary.com` e `myparther.com`. Embora essa solução seja criada em um locatário do AAD específico, como `mycustomer.com`, você pode fazê-lo funcionar para usuários de outros locatários.

Usando `mycustomer.com` para essa solução, adicione um usuário de `mypartner.com` como um usuário convidado ao locatário do `mycustomer.com`. Verifique se o usuário `mypartner.com` ativa a conta convidado. A conta convidado pode ser de outro locatário do AAD ou de uma conta de `outlook.com`.

Observe que os usuários convidados de `mypartner.com`, depois de serem ativados no `mycustomer.com`, ainda são autenticados pelo próprio locatário do AAD/original, `mypartner.com`, mas o `access_token` é emitido pelo `mycustomer.com`.

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>Suporte a um locatário/assinatura do cliente com uma configuração em sua assinatura/locatário

Você pode usar a configuração para testar o conteúdo protegido na conta/assinatura de serviço de mídia do seu cliente. Você o configuraria com um locatário do Azure AD e uma conta de serviço de mídia na mesma assinatura. A conta de serviço de mídia do cliente estaria em sua assinatura do Azure com um locatário próprio do Azure AD.

1. Adicione a conta do cliente ao seu locatário como uma conta convidado.
1. Trabalhe com seu cliente para preparar o conteúdo protegido na conta de serviço de mídia do seu cliente fornecendo os três parâmetros listados na seção configuração da conta de serviço de mídia.

Em seguida, seu cliente pode navegar até sua configuração, entrar com a conta convidado e testar o próprio conteúdo protegido. Você também pode entrar com sua conta e testar o conteúdo do cliente.

Sua solução de exemplo pode ser configurada em um locatário da Microsoft com assinatura da Microsoft ou locatário personalizado com assinatura da Microsoft. A instância do serviço de mídia do Azure pode ser de outra assinatura com seu locatário.

## <a name="clean-up-resources"></a>Limpar os recursos

> [!WARNING]
> Se você não quiser continuar usando este aplicativo, exclua os recursos criados seguindo este tutorial. Caso contrário, você será cobrado por eles.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início Rápido: Criptografar conteúdo](drm-encrypt-content-how-to.md)
