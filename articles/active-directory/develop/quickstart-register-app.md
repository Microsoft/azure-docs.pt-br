---
title: Registrar um aplicativo na plataforma de identidade da Microsoft – plataforma de identidade da Microsoft
description: Saiba como adicionar e registrar um aplicativo na Microsoft Identity Platform.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541253cf3a8f2628ea0e76f1115ddc4a290daf62
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655491"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft

Os desenvolvedores corporativos e provedores de SaaS (software como serviço) podem desenvolver serviços de nuvem comerciais ou aplicativos de linha de negócios que podem ser integrados à Microsoft Identity Platform para fornecer conexão segura e autorização para seus serviços.

Este Início Rápido mostra como adicionar e registrar um aplicativo usando a experiência **Registros de aplicativo** do portal do Azure para que o seu aplicativo possa ser integrado à plataforma de identidade da Microsoft. Para saber mais sobre os novos recursos e aprimoramentos na nova experiência de registros de aplicativo, veja [esta postagem de blog](https://developer.microsoft.com/graph/blogs/new-app-registration/).

## <a name="register-a-new-application-using-the-azure-portal"></a>Registrar um novo aplicativo usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** e, em seguida, **Registros de aplicativo > Novo registro**.
1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:

   - **Nome**: insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo.
   - **Tipos de conta com suporte**: selecione as contas às quais você gostaria que seu aplicativo desse suporte.

       | Tipos de conta com suporte | DESCRIÇÃO |
       |-------------------------|-------------|
       | **Contas somente neste diretório organizacional** | Selecione esta opção se você está criando um aplicativo de linha de negócios (LOB). Essa opção não estará disponível se você não estiver registrando o aplicativo em um diretório.<br><br>Essa opção mapeia para o único locatário somente do Azure AD.<br><br>Essa é a opção padrão, a menos que você esteja registrando o aplicativo fora de um diretório. Quando o aplicativo é registrado fora de um diretório, o padrão é contas da Microsoft pessoais e de vários locatários do Azure AD. |
       | **Contas em qualquer diretório organizacional** | Selecione essa opção se você deseja direcionar para todos os clientes comerciais e educacionais.<br><br>Essa opção mapeia para vários locatários somente do Azure AD.<br><br>Se você registrou o aplicativo como único locatário somente do Azure AD, pode atualizá-lo para ser multilocatário e voltar a ser locatário único na folha**Autenticação**. |
       | **Contas em qualquer diretório organizacional e contas pessoais da Microsoft** | Selecione essa opção a fim de direcionar para o conjunto mais amplo de clientes.<br><br>Essa opção mapeia para contas da Microsoft pessoais e multilocatário do Azure AD.<br><br>Se você registrou o aplicativo como contas da Microsoft pessoais e multilocatário do Azure AD, não poderá alterar isso na interface do usuário. Em vez disso, use o editor de manifesto do aplicativo para alterar os tipos de conta com suporte. |

   - **URI de redirecionamento (opcional)** : selecione o tipo de aplicativo que você está compilando, **Web** ou **Cliente público (dispositivos móvel e desktop)** e insira o URI de redirecionamento (ou a URL de resposta) para o aplicativo.
       - Para aplicativos Web, informe a URL base do aplicativo. Por exemplo, `http://localhost:31544` pode ser uma URL para um aplicativo Web em execução no seu computador local. Os usuários usariam essa URL para entrar em um aplicativo cliente Web.
       - Para aplicativos cliente públicos, informe o URI usado pelo Azure AD para retornar respostas de token. Insira um valor específico para o aplicativo, por exemplo, `myapp://auth`.

     Para ver exemplos específicos de aplicativos da Web ou aplicativos nativos, confira nossos [inícios rápidos](https://docs.microsoft.com/azure/active-directory/develop).

1. Ao terminar, selecione **Registrar**.

    [![Mostra a tela para registrar um novo aplicativo no portal do Azure](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

O Azure AD atribui uma ID exclusiva do aplicativo (cliente) para seu aplicativo e você é redirecionado para a página **Visão geral** do aplicativo. Para adicionar mais recursos ao aplicativo, você pode selecionar outras opções de configuração, incluindo identidade visual, certificados e segredos, permissões de API e muito mais.

[![Exemplo da página de visão geral de um aplicativo registrado recentemente](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [permissões e consentimento](v2-permissions-and-consent.md).
- Para habilitar recursos adicionais de configuração em seu registro de aplicativo, como credenciais e permissões, e habilitar entrada para usuários de outros locatários, confira estes inícios rápidos:
    - [Configurar um aplicativo cliente para acessar APIs Web](quickstart-configure-app-access-web-apis.md)
    - [Configurar um aplicativo para expor APIs Web](quickstart-configure-app-expose-web-apis.md)
    - [Modificar as contas que têm suporte por um aplicativo](quickstart-modify-supported-accounts.md)
- Escolha um [início rápido](https://docs.microsoft.com/azure/active-directory/develop) para criar rapidamente um aplicativo e adicionar funcionalidades como obtenção de tokens, atualização de tokens, conexão do usuário, exibição de algumas informações do usuário e muito mais.
- Saiba mais sobre os dois objetos do Azure AD que representam um aplicativo registrado e a relação entre eles, Veja [Objetos de aplicativo e objetos de entidade de serviço](app-objects-and-service-principals.md).
- Saiba mais sobre as diretrizes de identidade visual que você deve usar ao desenvolver aplicativos. Confira [Diretrizes de identidade visual para aplicativos](howto-add-branding-in-azure-ad-apps.md).
