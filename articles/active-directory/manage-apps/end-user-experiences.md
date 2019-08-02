---
title: Experiências do usuário final para aplicativos - Active Directory Domain Service | Microsoft Docs
description: O Microsoft Azure Active Directory (Azure AD) fornece várias maneiras personalizáveis de implantar aplicativos para usuários finais em sua organização.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/03/2019
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74c6787068cf8ba1e86cbf43955d0ac995aa8de1
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702104"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Experiências do usuário final para aplicativos no Active Directory Domain Services do Azure

Azure Active Directory Domain Services (Azure AD) fornece várias maneiras personalizáveis para implantar aplicativos para usuários finais em sua organização:

* Painel de acesso do AD do Azure
* Iniciador de aplicativos do Office 365
* Logon direto a aplicativos federados
* Links profundos a aplicativos federados, baseado em senha, ou existentes

A escolha dos métodos para implantação de sua organização fica seu critério.

## <a name="azure-ad-access-panel"></a>Painel de acesso do AD do Azure

O Painel de Acesso em https://myapps.microsoft.com é um portal baseado na Web que permite a um usuário final com conta organizacional no Azure Active Directory exibir e inicializar aplicativos baseados em nuvem aos quais tenham recebido acesso pelo administrador do Microsoft Azure AD. Se for um usuário final com o [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), você também poderá utilizar recursos de gerenciamento do grupo de autoatendimento por meio do Painel de acesso.

![Captura de tela mostra o portal do painel de acesso do AD do Azure](media/what-is-single-sign-on/azure-ad-access-panel.png)

O Painel de acesso é separado do portal do Azure e não requer que os usuários tenham uma assinatura do Azure ou do Office 365.

Para obter mais informações sobre o painel de acesso do AD do Azure, consulte a [Introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="office-365-application-launcher"></a>Iniciador de aplicativos do Office 365

Para organizações que implantaram o Office 365, aplicativos atribuídos aos usuários por meio do Azure AD também aparecerão no portal do Office 365 em [ https://portal.office.com/myapps ](https://portal.office.com/myapps). Isso torna simples e fácil para os usuários em uma organização para iniciar seus aplicativos sem precisar usar um segundo portal e é a solução de inicialização do aplicativo recomendada para organizações que usam o Office 365.

![Captura de tela mostra o portal do Office 365](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Para obter mais informações sobre o iniciador do aplicativo Office 365, consulte [Faça o seu aplicativo ser exibido no iniciador do aplicativo do Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Logon direto a aplicativos federados

A maioria dos aplicativos federados que oferecem suporte à conexão SAML 2.0, WS-Federation ou OpenID também oferecem suporte à capacidade dos usuários de iniciar o aplicativo e, em seguida, obter conexão por meio do AD do Azure pelo redirecionamento automático ou clicando em um link para entrar. Isso é conhecido como serviço de logon iniciado pelo provedor e a maioria dos aplicativos federados na Galeria de aplicativos do Azure AD oferece suporte a eles (consulte a documentação vinculada do Assistente de configuração de logon único do aplicativo no portal do Azure para obter detalhes).

![Exemplo de uma página de entrada do aplicativo móvel](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Links diretos logon

O Microsoft Azure Active Directory também oferece suporte a links de logon únicos diretos para aplicativos individuais que oferecem suporte baseado em senha de logon único, logon único vinculado e qualquer forma de logon único federado.

Esses links são URLs criadas especificamente que enviam um usuário por meio do processo de entrada do Azure AD para um aplicativo específico sem exigir que o usuário as inicie do painel de acesso do Azure AD ou do Office 365. Eles **URLs de acesso do usuário** podem ser encontradas nas propriedades de aplicativos corporativos disponíveis. No portal do Azure, selecione **Azure Active Directory** > **aplicativos empresariais**. Selecione o aplicativo e, em seguida, selecione **propriedades**.

![Exemplo de como a URL de acesso do usuário nas propriedades do Twitter](media/end-user-experiences/direct-sign-on-link.png)

Esses links podem ser copiados e colados em qualquer lugar em que você deseja fornecer um link de entrada para o aplicativo selecionado. Essa entrada poderia ser em um email ou em qualquer portal personalizado baseado na Web que você configurou o aplicativo para acesso de usuário. Aqui está um exemplo de uma URL de logon único direto do AD do Azure para o Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Semelhante a URLs específicas da organização para o painel de acesso, você pode personalizar ainda mais essa URL adicionando um dos domínios para seu diretório ativos ou verificados após o domínio myapps.microsoft.com. Isso garante que qualquer identidade visual organizacional seja carregada imediatamente na página de entrada sem que o usuário precise inserir sua ID de usuário pela primeira vez:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Quando um usuário autorizado clica em um desses links específicos do aplicativo, ele primeiro consulta sua página de entrada organizacional (supondo que eles não ainda tenha entrados) e depois de entrada é redirecionado para seu aplicativo sem parar no primeiro painel de acesso. Se o usuário não tiver os pré-requisitos para acessar o aplicativo, como a extensão de navegador de logon único baseado em senha, o link solicitará que o usuário instale a extensão que está faltando. A URL do link também permanece constante se a configuração de logon único para o aplicativo for alterada.

Esses links usam os mesmos mecanismos de controle de acesso que o painel de acesso e o Office 365, e apenas esses usuários ou grupos que foram atribuídos ao aplicativo no portal do Azure serão capazes de autenticar com êxito. No entanto, todos os usuários não autorizados receberão uma mensagem explicando que não foi concedido acesso e receberão um link para carregar o painel de acesso para exibir os aplicativos disponíveis para os quais têm acesso.

## <a name="next-steps"></a>Próximas etapas

Para planos de implantação, consulte [planos de implantação do Azure Active Directory Domain Services](../fundamentals/active-directory-deployment-plans.md)
