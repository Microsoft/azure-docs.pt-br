---
title: Experiências do usuário final para aplicativos-Azure Active Directory
description: O Microsoft Azure Active Directory (Azure AD) fornece várias maneiras personalizáveis de implantar aplicativos para usuários finais em sua organização.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.reviewer: arvindh
ms.openlocfilehash: 0d3f1cab0637c7409cbc45f4f5a5374b2b5b6a77
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259246"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Experiências do usuário final para aplicativos no Active Directory Domain Services do Azure

Azure Active Directory Domain Services (Azure AD) fornece várias maneiras personalizáveis para implantar aplicativos para usuários finais em sua organização:

* Meus aplicativos do Azure AD
* Microsoft 365 iniciador de aplicativo
* Logon direto a aplicativos federados
* Links profundos a aplicativos federados, baseado em senha, ou existentes

A escolha dos métodos para implantação de sua organização fica seu critério.

## <a name="azure-ad-my-apps"></a>Meus aplicativos do Azure AD

Meus aplicativos em https://myapps.microsoft.com é um portal baseado na Web que permite que um usuário final com uma conta institucional no Azure Active Directory exiba e inicie aplicativos aos quais eles receberam acesso pelo administrador do Azure AD. Se você for um usuário final com [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), também poderá utilizar os recursos de gerenciamento de grupo de autoatendimento por meio de meus aplicativos.

Por padrão, todos os aplicativos são listados juntos em uma única página. Mas você pode usar coleções para agrupar aplicativos relacionados e apresentá-los em uma guia separada, facilitando sua localização. Por exemplo, você pode usar coleções para criar agrupamentos lógicos de aplicativos para funções de trabalho específicas, tarefas, projetos e assim por diante. Para obter informações, consulte [criar coleções no portal meus aplicativos](access-panel-collections.md). 

Meus aplicativos é separado da portal do Azure e não exige que os usuários tenham uma assinatura do Azure ou Microsoft 365 assinatura.

Para obter mais informações sobre meus aplicativos do Azure AD, consulte a [introdução aos meus aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="microsoft-365-application-launcher"></a>Microsoft 365 iniciador de aplicativo

Para organizações que implantaram Microsoft 365, os aplicativos atribuídos aos usuários por meio do Azure AD também aparecerão no portal do Office 365 em [https://portal.office.com/myapps](https://portal.office.com/myapps) . Isso torna fácil e conveniente para os usuários em uma organização iniciarem seus aplicativos sem precisar usar um segundo portal e é a solução de inicialização de aplicativo recomendada para organizações que usam Microsoft 365.

Para obter mais informações sobre o iniciador do aplicativo Office 365, consulte [Faça o seu aplicativo ser exibido no iniciador do aplicativo do Office 365](/previous-versions/office/office-365-api/).

## <a name="direct-sign-on-to-federated-apps"></a>Logon direto a aplicativos federados

A maioria dos aplicativos federados que oferecem suporte à conexão SAML 2.0, WS-Federation ou OpenID também oferecem suporte à capacidade dos usuários de iniciar o aplicativo e, em seguida, obter conexão por meio do AD do Azure pelo redirecionamento automático ou clicando em um link para entrar. Isso é conhecido como logon iniciado pelo provedor de serviços e a maioria dos aplicativos federados na Galeria de aplicativos do Azure AD dá suporte a isso (consulte a documentação vinculada do assistente de configuração de logon único do aplicativo no portal do Azure para obter detalhes).

## <a name="direct-sign-on-links"></a>Links diretos logon

O Microsoft Azure Active Directory também oferece suporte a links de logon únicos diretos para aplicativos individuais que oferecem suporte baseado em senha de logon único, logon único vinculado e qualquer forma de logon único federado.

Esses links são URLs criadas especificamente que enviam um usuário por meio do processo de entrada do Azure AD para um aplicativo específico sem exigir que o usuário os inicie do Azure AD meus aplicativos ou Microsoft 365. Essas **URLs de acesso do usuário** podem ser encontradas nas propriedades de aplicativos empresariais disponíveis. No portal do Azure, selecione **Azure Active Directory** > **Aplicativos empresariais**. Selecione o aplicativo e, em seguida, selecione **Propriedades**.

![Exemplo da URL de acesso do usuário nas propriedades do Twitter](media/end-user-experiences/direct-sign-on-link.png)

Esses links podem ser copiados e colados em qualquer lugar em que você deseja fornecer um link de entrada para o aplicativo selecionado. Essa entrada poderia ser em um email ou em qualquer portal personalizado baseado na Web que você configurou o aplicativo para acesso de usuário. Aqui está um exemplo de uma URL de logon único direto do AD do Azure para o Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Semelhante às URLs específicas da organização para meus aplicativos, você pode personalizar ainda mais essa URL adicionando um dos domínios ativos ou verificados ao seu diretório após o domínio *myapps.Microsoft.com* . Isso garante que qualquer identidade visual organizacional seja carregada imediatamente na página de entrada sem que o usuário precise inserir sua ID de usuário pela primeira vez:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Quando um usuário autorizado clica em um desses links específicos do aplicativo, ele primeiro vê sua página de entrada organizacional (pressupondo que eles ainda não estejam conectados) e, depois que a entrada é redirecionada para seu aplicativo sem parar em meus aplicativos primeiro. Se o usuário não tiver os pré-requisitos para acessar o aplicativo, como a extensão de navegador de logon único baseado em senha, o link solicitará que o usuário instale a extensão que está faltando. A URL do link também permanece constante se a configuração de logon único para o aplicativo for alterada.

Esses links usam os mesmos mecanismos de controle de acesso que meus aplicativos e Microsoft 365, e somente os usuários ou grupos que foram atribuídos ao aplicativo no portal do Azure poderão se autenticar com êxito. No entanto, qualquer usuário não autorizado verá uma mensagem explicando que não recebeu acesso e receberá um link para carregar meus aplicativos para exibir os aplicativos disponíveis aos quais eles têm acesso.

## <a name="next-steps"></a>Próximas etapas

* [Série de Início Rápido no Gerenciamento de Aplicativos](view-applications-portal.md)
* [O que é logon único?](what-is-single-sign-on.md)
* [Guia de introdução: integrando o Active Directory do Azure com aplicativos](plan-an-application-integration.md)