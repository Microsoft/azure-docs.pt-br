---
title: 'Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft | Azure'
description: Neste início rápido, você aprenderá a registrar um aplicativo na plataforma de identidade da Microsoft.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperf-fy21q1, contperf-fy21q2
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 6052e2243f8b31aa57d4abf27c24a4b045c9eae2
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754634"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft

Neste guia de início rápido, você registra um aplicativo no portal do Azure para que a plataforma de identidade da Microsoft possa fornecer serviços de autenticação e autorização para seu aplicativo e seus usuários.

Cada aplicativo para o qual você deseja que a plataforma de identidade da Microsoft execute o IAM (gerenciamento de identidade e acesso) deve estar registrado. Seja um aplicativo cliente como um aplicativo móvel ou Web, ou uma API Web que faz backup de um aplicativo cliente, registrá-lo estabelece uma relação de confiança entre seu aplicativo e o provedor de identidade, a plataforma de identidade da Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa – [criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Conclusão do [Início Rápido: Configurar um locatário](quickstart-create-new-tenant.md)

## <a name="register-an-application"></a>Registrar um aplicativo

Registrar seu aplicativo estabelece uma relação de confiança entre seu aplicativo e a plataforma de identidade da Microsoft. A confiança é unidirecional: seu aplicativo confia na plataforma de identidade da Microsoft, e não o contrário.

Siga estas etapas para criar o registro do aplicativo:

1. Entre no <a href="https://portal.azure.com/" target="_blank">Portal do Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
1. Insira um **Nome** para seu aplicativo. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
1. Especifique quem pode usar o aplicativo, às vezes chamado de *público-alvo de conexão*.

    | Tipos de conta com suporte | Descrição |
    |-------------------------|-------------|
    | **Contas somente neste diretório organizacional** | Selecione essa opção se você está criando um aplicativo para uso somente por usuários (ou convidados) no *seu* locatário.<br><br>Geralmente chamado de aplicativo LOB (*linha de negócios*), é um aplicativo de **locatário único** na plataforma de identidade da Microsoft. |
    | **Contas em qualquer diretório organizacional** | Selecione essa opção se você quer que os usuários em *qualquer* locatário do Azure AD possam usar seu aplicativo. Essa opção é apropriada se, por exemplo, você está criando um aplicativo SaaS (software como serviço) que quer fornecer a várias organizações.<br><br>Isso é conhecido como um aplicativo **multilocatário** na plataforma de identidade da Microsoft. |
    | **Contas em qualquer diretório organizacional e contas pessoais da Microsoft** | Selecione essa opção a fim de direcionar para o conjunto mais amplo de clientes.<br><br>Ao selecionar essa opção, você está registrando um aplicativo **multilocatário** que também pode dar suporte a usuários com MSA (**contas Microsoft**) pessoais. |
    | **Contas Microsoft pessoais** | Selecione esta opção se você está criando um aplicativo para uso somente por usuários com contas Microsoft pessoais. As contas Microsoft pessoais incluem contas do Skype, Xbox, Live e Hotmail. |

1. Não insira nada para **URI de redirecionamento (opcional)** , você vai configurar um na próxima seção.
1. Selecione **Registrar** para concluir o registro inicial do aplicativo.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Captura de tela do portal do Azure em um navegador da Web mostrando o painel Registrar um aplicativo.":::

Quando o registro for concluído, o portal do Azure exibirá o painel **Visão geral** do registro de aplicativo, que inclui sua **ID de Aplicativo (cliente)** . Também conhecido como apenas *ID do cliente*, esse valor identifica exclusivamente seu aplicativo na plataforma de identidade da Microsoft.

O código do aplicativo ou, mais tipicamente, uma biblioteca de autenticação usada em seu aplicativo, também usa a ID do cliente como um aspecto na validação dos tokens de segurança que ele recebe da plataforma de identidade.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Captura de tela do portal do Azure em um navegador da Web mostrando um painel Visão Geral do registro do aplicativo.":::

## <a name="add-a-redirect-uri"></a>Adicionar um URI de redirecionamento

Um URI de redirecionamento é o local para o qual a plataforma de identidade da Microsoft redireciona o cliente de um usuário e envia tokens de segurança após a autenticação.

Em um aplicativo Web de produção, por exemplo, o URI de redirecionamento geralmente é um ponto de extremidade público em que seu aplicativo está em execução, como `https://contoso.com/auth-response`. Durante o desenvolvimento, é comum também adicionar o ponto de extremidade em que você executa o aplicativo localmente, como `https://127.0.0.1/auth-response` ou `http://localhost/auth-response`.

Você adiciona e modifica URIs de redirecionamento para seus aplicativos registrados definindo suas [configurações de plataforma](#configure-platform-settings).

### <a name="configure-platform-settings"></a>Definir configurações de plataforma

As configurações para cada tipo de aplicativo, incluindo URIs de redirecionamento, são definidas em **Configurações de plataforma** no portal do Azure. Algumas plataformas, como **Web** e **Aplicativos de página única**, exigem que você especifique manualmente um URI de redirecionamento. Para outras plataformas, como dispositivos móveis e desktop, você pode selecionar entre URIs de redirecionamento gerados para você ao definir suas outras configurações.

Para definir as configurações do aplicativo com base na plataforma ou no dispositivo que você está direcionando:

1. Selecione seu aplicativo em **Registros de aplicativo** no portal do Azure.
1. Em **Gerenciar**, selecione **Autenticação**.
1. Em **Configurações da plataforma**, selecione **Adicionar uma plataforma**.
1. Em **Configurar plataformas**, selecione o bloco para o tipo de aplicativo (plataforma) para definir suas configurações.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Captura de tela do painel Configuração da plataforma no portal do Azure" border="false":::

    | Plataforma | Definições de configuração |
    | -------- | ---------------------- |
    | **Web** | Insira um **URI de redirecionamento** para seu aplicativo, a localização para a qual a plataforma de identidade da Microsoft redireciona o cliente do usuário e envia tokens de segurança após a autenticação.<br/><br/>Selecione essa plataforma para aplicativos Web padrão que são executados em um servidor. |
    | **Aplicativo de página única** | Insira um **URI de redirecionamento** para seu aplicativo, a localização para a qual a plataforma de identidade da Microsoft redireciona o cliente do usuário e envia tokens de segurança após a autenticação.<br/><br/>Selecione esta plataforma se você está criando um aplicativo Web do lado do cliente em JavaScript ou com uma estrutura como Angular, Vue.js, React.js ou Blazor WebAssembly. |
    | **iOS/macOS** | Insira a **ID do Pacote** do aplicativo, encontrada no XCode em *Info.plist* ou Configurações de Build.<br/><br/>Um URI de redirecionamento é gerado para você quando você especifica uma ID de pacote. |
    | **Android** | Insira o **Nome do pacote** do aplicativo, que pode ser encontrado no arquivo *AndroidManifest.xml*, e gere e insira o **Hash de assinatura**.<br/><br/>Um URI de redirecionamento é gerado para você quando você especifica essas configurações. |
    | **Aplicativos móveis e para desktop** | Selecione um dos **URIs de redirecionamento sugeridos** ou especifique um **URI de redirecionamento personalizado**.<br/>Para aplicativos de desktop, recomendamos:<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>Selecionar essa plataforma para aplicativos móveis que não estão usando a MSAL (Biblioteca de Autenticação da Microsoft) mais recente ou que não estão usando um agente. Selecione também essa plataforma para aplicativos de desktop. |
1. Selecione **Configurar** para concluir a configuração da plataforma.

### <a name="redirect-uri-restrictions"></a>Restrições do URI de redirecionamento

Há algumas restrições ao formato dos URIs de redirecionamento que você adiciona a um registro de aplicativo. Confira os detalhes dessas restrições em [Restrições e limitações do URI de redirecionamento (URL de resposta)](reply-url.md).

## <a name="add-credentials"></a>Adicionar credenciais

As credenciais são usadas por [aplicativos cliente confidenciais](msal-client-applications.md) que acessam uma API Web. Exemplos de clientes confidenciais são [aplicativos Web](scenario-web-app-call-api-overview.md), outras [APIs Web](scenario-protected-web-api-overview.md) ou [aplicativos dos tipos serviço e daemon](scenario-daemon-overview.md). As credenciais permitem que seu aplicativo se autentique como ele mesmo, sem nenhuma interação com um usuário no runtime. 

Você pode adicionar certificados e segredos do cliente (uma cadeia de caracteres) como credenciais ao registro do aplicativo cliente confidencial.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Captura de tela do portal do Azure mostrando o painel Certificados e segredos em um Registro de aplicativo":::

### <a name="add-a-certificate"></a>Adicionar um certificado

Às vezes chamados de *chave pública*, os certificados são o tipo de credencial recomendado, pois fornecem um nível mais alto de garantia do que um segredo do cliente. Para obter mais informações sobre como usar o certificado como um método de autenticação em seu aplicativo, confira [Credenciais do certificado de autenticação de aplicativo da plataforma de identidade da Microsoft](active-directory-certificate-credentials.md)

1. Selecione seu aplicativo em **Registros de aplicativo** no portal do Azure.
1. Selecione **Certificados e segredos** > **Carregar certificado**.
1. Selecione o arquivo que você gostaria de carregar. Ele deve ser um dos seguintes tipos de arquivo: .cer, .pem, .crt.
1. Selecione **Adicionar**.

### <a name="add-a-client-secret"></a>Adicionar um segredo do cliente

O segredo do cliente, também conhecido como *senha do aplicativo*, é um valor de cadeia de caracteres que seu aplicativo pode usar no lugar de um certificado para a própria identidade. É o mais fácil de usar dos dois tipos de credencial e é usado com frequência durante o desenvolvimento, mas é considerado menos seguro do que um certificado. Você deve usar certificados em seus aplicativos executados em produção. Para obter mais informações sobre as recomendações de segurança de aplicativo, confira [Melhores práticas e recomendações da plataforma de identidade da Microsoft](identity-platform-integration-checklist.md#security)

1. Selecione seu aplicativo em **Registros de aplicativo** no portal do Azure.
1. Selecione **Certificados e segredos** >  **Novo segredo do cliente**.
1. Adicione uma descrição para o segredo do cliente.
1. Selecione uma duração.
1. Selecione **Adicionar**.
1. **Registre o valor do segredo** para uso no código do aplicativo cliente. Ele *nunca será exibido novamente* depois que você sair dessa página.

**Observação:** a ID gerada com o valor do segredo é a ID do segredo, que é diferente da ID do Aplicativo.

## <a name="next-steps"></a>Próximas etapas

Aplicativos cliente normalmente precisam acessar recursos em uma API Web. Além de proteger seu aplicativo cliente com a plataforma de identidade da Microsoft, você pode usar a plataforma para autorizar o acesso baseado em permissões com escopo à sua API Web.

Vá para o próximo início rápido da série para criar outro registro de aplicativo para sua API Web e expor seus escopos.

> [!div class="nextstepaction"]
> [Configurar um aplicativo para expor uma API Web](quickstart-configure-app-expose-web-apis.md)
