---
title: Como fornecer acesso remoto seguro a aplicativos locais
description: Aborda como usar o Proxy de Aplicativo do AD do Azure para fornecer acesso remoto seguro aos seus aplicativos locais.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 426056d394af0a9ded28202615cb80c7b50e59fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>Como fornecer acesso remoto seguro a aplicativos locais

Os funcionários de hoje querem ser produtivos em qualquer lugar, a qualquer momento e com qualquer dispositivo. Desejam trabalhar em seus próprios dispositivos, sejam eles laptops, telefones ou tablets. Além disso, eles esperam para poder acessar todos os seus aplicativos, tanto aplicativos SaaS na nuvem como aplicativo corporativos no local. O fornecimento de acesso a aplicativos locais sempre envolveu VPNs (redes virtuais privadas) ou DMZs (zonas desmilitarizadas). Essas soluções são complexas e difíceis de proteger, além de serem caras para configurar e gerenciar.

Há uma opção melhor!

Uma força de trabalho moderna no mundo que prioriza a nuvem e a mobilidade precisa de uma solução moderna de acesso remoto. O Proxy de Aplicativo do Azure AD é um recurso do Azure Active Directory que oferece acesso remoto como um serviço. Isso significa que é fácil de implantar, usar e gerenciar.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="what-is-azure-active-directory-application-proxy"></a>O que é o Proxy de Aplicativo do Active Directory do Azure?
O Proxy de Aplicativo Azure AD fornece SSO (logon único) e acesso remoto seguro para aplicativos da Web hospedados no local. Alguns aplicativos que você talvez queira publicar incluem sites do SharePoint, o Outlook Web Access ou qualquer outro aplicativo Web de LOB que você tenha. Esses aplicativos Web locais são integrados ao Azure AD, a mesma plataforma de identidade e controle que é usada pelo O365. Os usuários finais podem acessar seus aplicativos locais da mesma forma que acessam o O365 e outros aplicativos SaaS integrados com o Azure AD. Você não precisa alterar a infraestrutura de rede nem necessita VPN para fornecer essa solução para seus usuários.

## <a name="why-is-application-proxy-a-better-solution"></a>Por que o Proxy de Aplicativo é uma solução melhor?
O Proxy de Aplicativo Azure AD fornece uma solução de acesso remoto simples, segura e econômica para todos os aplicativos locais.

O Proxy de Aplicativo do Azure AD é:

* **Simples**
   * Você não precisa alterar ou atualizar seus aplicativos para que funcionem com o Proxy de Aplicativo. 
   * Os usuários obtêm uma experiência de autenticação consistente. Eles podem usar o portal MyApps para obter logon único tanto aos aplicativos SaaS na nuvem quanto aos seus aplicativos locais. 
* **Proteger**
   * Ao publicar aplicativos usando o Proxy de Aplicativo Azure AD, você pode tirar proveito dos controles de autorização avançados e da análise de segurança no Azure. Você obtém segurança de escala de nuvem e recursos de segurança do Azure como verificação em duas etapas e acesso condicional.
   * Você não precisa abrir conexões de entrada através do firewall para dar acesso remoto aos usuários. 
* **Econômico**
   * O Proxy de Aplicativo funciona na nuvem, assim você pode economizar tempo e dinheiro. As soluções locais geralmente exigem que você configure e mantenha DMZs, servidores de borda ou outras infraestruturas complexas.  

## <a name="what-kind-of-applications-work-with-application-proxy"></a>Quais tipos de aplicativos funcionam com o Proxy de Aplicativo?
Com o Proxy de Aplicativo Azure AD, você pode acessar diferentes tipos de aplicativos internos:

* Aplicativos Web que usam a [Autenticação Integrada do Windows](active-directory-application-proxy-sso-using-kcd.md) para autenticação  
* Aplicativos Web que usam o acesso baseado em formulário ou [baseado em cabeçalho](application-proxy-ping-access.md)  
* APIs Web que você deseja expor a aplicativos avançados em diferentes dispositivos  
* Aplicativos hospedados por trás de um [Gateway de Área de Trabalho Remota](application-proxy-publish-remote-desktop.md)  
* Aplicativos cliente avançados que são integrados com a ADAL (Biblioteca de autenticação do Active Directory)

## <a name="how-does-application-proxy-work"></a>Como o Proxy de Aplicativo funciona?
Há dois componentes que você precisa configurar para fazer com que o Proxy de Aplicativo funcione: um conector e um ponto de extremidade externo. 

O conector é um agente leve que se encontra em um Windows Server na sua rede. O conector facilita o fluxo de tráfego desde o serviço do Proxy de Aplicativo na nuvem até seu aplicativo local. Ele usa apenas conexões de saída, portanto você não precisa abrir portas de entrada nem colocar nada na DMZ. Os conectores são sem monitoração de estado e efetuam pull de informações da nuvem conforme necessário. Para obter mais informações sobre conectores, como eles fazem o balanceamento de carga e a autenticação, consulte [Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-understand-connectors.md). 

O ponto de extremidade externo é como os usuários acessam seus aplicativos enquanto estão fora da sua rede. Eles podem acessar diretamente uma URL externa que você determinar ou podem acessar o aplicativo por meio do portal MyApps. Quando os usuários acessam um desses pontos de extremidade, eles são autenticados no Azure AD e, em seguida, são direcionados por meio do conector até o aplicativo local.

 ![Diagrama do Proxy de Aplicativo do Azure](./media/active-directory-application-proxy-get-started/azureappproxxy.png)

1. O usuário acessa o aplicativo usando o serviço Proxy de Aplicativo e é direcionado para a página de entrada do Azure AD para autenticação.
2. Após uma entrada bem-sucedida, um token é gerado e enviado ao dispositivo cliente.
3. O cliente agora envia o token para o serviço Proxy de Aplicativo que irá recuperar o nome de usuário principal (UPN) e o nome da entidade de segurança (SPN) do token e direcionar a solicitação para o conector do Proxy de Aplicativo.
4. Se você tiver configurado o logon único, o conector não realizará nenhuma autenticação adicional necessária em nome do usuário.
5. O conector envia a solicitação para o aplicativo no local.  
6. A resposta será enviada pelo serviço Proxy de Aplicativo e o conector para o usuário.

### <a name="single-sign-on"></a>Logon único
O Proxy de Aplicativo Azure AD oferece SSO (logon único ) a aplicativos que usam a IWA (Autenticação Integrada do Windows) ou aplicativos com reconhecimento de declaração. Se seu aplicativo usa a IWA, o Proxy de Aplicativo representa o usuário usando a delegação restrita de Kerberos para fornecer o SSO. Se você tem um aplicativo com reconhecimento de declaração que confia no Active Directory do Azure, o SSO funciona porque o usuário já foi autenticado pelo AD do Azure.

Para obter mais informações sobre o Kerberos, consulte [Tudo o que você deseja saber sobre a delegação restrita de Kerberos (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

### <a name="managing-apps"></a>Gerenciando aplicativos
Assim que o aplicativo for publicado com o Proxy de Aplicativo, será possível gerenciá-lo como qualquer outro aplicativo corporativo no Portal Azure. Você pode usar recursos de segurança do Azure Active Directory como a verificação em duas etapas e o acesso condicional, controlar permissões de usuário e personalizar a identidade visual do seu aplicativo. 

## <a name="get-started"></a>Introdução

Antes de configurar o Proxy de Aplicativo, verifique se você tem uma [edição do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) com suporte e um diretório do Azure AD do qual você é um administrador global.

Introdução ao Proxy de Aplicativo em duas etapas:

1. [Habilitar o Proxy de Aplicativo e configurar o conector](active-directory-application-proxy-enable.md).    
2. [Publicar aplicativos](active-directory-application-proxy-publish.md) - use o assistente rápido e fácil para publicar seus aplicativos locais e torná-los acessíveis remotamente.

## <a name="whats-next"></a>O que vem a seguir?
Depois de publicar seu primeiro aplicativo, há muito mais você pode fazer com o Proxy de Aplicativo:

* [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
* [Publicar aplicativos usando seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
* [Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-understand-connectors.md)
* [Trabalhar com servidores proxy locais existentes](application-proxy-working-with-proxy-servers.md) 
* [Definir uma home page personalizada](application-proxy-office365-app-launcher.md)

Para obter as últimas notícias e atualizações, confira o [blog do Proxy de Aplicativo](http://blogs.technet.com/b/applicationproxyblog/)

