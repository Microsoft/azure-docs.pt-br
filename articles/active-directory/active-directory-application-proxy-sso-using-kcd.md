---
title: "Logon único com o Proxy de Aplicativo | Microsoft Docs"
description: "Aborda como fornecer o logon único usando o Proxy de Aplicativo Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: ded0d9c9-45f6-47d7-bd0f-3f7fd99ab621
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017, it-pro
ms.openlocfilehash: 149af1f68e574f78127a9c2de8a0e79ed8774d29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Delegação restrita de Kerberos para logon único em seus aplicativos com o Proxy de Aplicativo

Você pode fornecer o logon único para aplicativos locais, publicados por meio do Proxy do Aplicativo, que sejam protegidos com a Autenticação Integrada do Windows. Esses aplicativos exigem um tíquete Kerberos para acessar. O Proxy do Aplicativo usa a KCD (Delegação restrita de Kerberos) para dar suporte a esses aplicativos. 

Você pode habilitar o logon único para seus aplicativos usando a IWA (Autenticação Integrada do Windows) concedendo permissão aos conectores do Proxy de Aplicativo no Active Directory para representar usuários. Os conectores usam essa permissão para enviar e receber tokens em seu nome.

## <a name="how-single-sign-on-with-kcd-works"></a>Como funciona o logon único com a KCD
Este diagrama explica o fluxo de quando um usuário tenta acessar um aplicativo local que usa IWA.

![Diagrama de fluxo de autenticação do Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. O usuário insere a URL para acessar o aplicativo local por meio do Proxy de Aplicativo.
2. O Proxy de Aplicativo redireciona a solicitação para serviços de autenticação do AD do Azure para pré-autenticação. Neste ponto, o AD do Azure se aplica a qualquer política de autenticação e autorização aplicável, tal como autenticação multifator. Se o usuário for validado, o AD do Azure cria um token e o envia para o usuário.
3. O usuário passa o token para o Proxy de Aplicativo.
4. O Proxy de Aplicativo valida o token e recupera o nome UPN (nome UPN), enviando em seguida a solicitação, o UPN e o SPN (Nome da Entidade de Serviço) para o Conector por meio de um canal de segurança duplamente autenticado.
5. O conector realiza a negociação do KCD (Delegação Restrita de Kerberos) com o AD local, representando o usuário para obter um token Kerberos para o aplicativo.
6. O Active Directory envia o token Kerberos para o aplicativo para o Conector.
7. O Conector envia a solicitação original para o servidor de aplicativos usando o token Kerberos recebido do AD.
8. O aplicativo envia a resposta para o Conector, que é retornada para o serviço de Proxy de Aplicativo e, finalmente, para o usuário.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar com o logon único para aplicativos da IWA, verifique se seu ambiente está preparado com as seguintes configurações e definições:

* Seus aplicativos, como os aplicativos Web do SharePoint, são definidos para usar a Autenticação Integrada do Windows. Para sabe rmais, veja [Habilitar suporte para autenticação Kerberos](https://technet.microsoft.com/library/dd759186.aspx), ou para o SharePoint, consulte [Planejar a autenticação Kerberos no SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Todos os seus aplicativos têm [nome da entidade de serviço](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* O servidor que executa o Conector e o servidor que executa o aplicativo que você está publicando são ingressados em domínio e fazem parte desse mesmo domínio ou em domínios confiáveis. Para obter mais informações sobre o ingresso no domínio, consulte [Ingressar um computador em um domínio](https://technet.microsoft.com/library/dd807102.aspx).
* O servidor que executa o conector tem acesso de leitura ao atributo TokenGroupsGlobalAndUniversal para usuários. Essa configuração padrão pode ter sido afetada pelo aumento das restrições de segurança que protegem o ambiente.

### <a name="configure-active-directory"></a>Configurar o Active Directory
A configuração do Active Directory varia, dependendo de se o conector do Proxy do Aplicativo e o servidor de aplicativos estão no mesmo domínio ou não.

#### <a name="connector-and-application-server-in-the-same-domain"></a>O conector e o servidor de aplicativos estão no mesmo domínio
1. No Active Directory, vá para **Ferramentas** > **Usuários e Computadores**.
2. Selecione o servidor que executa o conector.
3. Clique com o botão direito do mouse e selecione **Propriedades** > **Delegação**.
4. Selecione **Confiar no computador para delegação apenas a serviços especificados**. 
5. Em **Serviços aos quais esta conta pode apresentar credenciais delegadas**, adicione o valor da identidade SPN do servidor de aplicativos. Isso permite que o Conector do Proxy de Aplicativo represente usuários no AD para os aplicativos definidos na lista.

   ![Captura de tela da janela Propriedades do Conector SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>O conector e o servidor de aplicativos estão em domínios diferentes
1. Para obter uma lista de pré-requisitos para trabalhar com o KCD entre domínios, consulte [Delegação restrita de Kerberos nos domínios](https://technet.microsoft.com/library/hh831477.aspx).
2. Use a propriedade `principalsallowedtodelegateto` no servidor do conector para habilitar o Proxy do Aplicativo a delegar para o servidor do conector. O servidor de aplicativos é o `sharepointserviceaccount` e o servidor de delegação é o `connectormachineaccount`. Para o Windows 2012 R2, use esse código como um exemplo:

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

Sharepointserviceaccount pode ser a conta do computador do SPS ou uma conta de serviço sob a qual o pool de aplicativos do SPS está sendo executado.

## <a name="configure-single-sign-on"></a>Configurar o logon único 
1. Publique seu aplicativo seguindo as instruções descritas em [Publicar aplicativos com o Proxy de Aplicativo](application-proxy-publish-azure-portal.md). Certifique-se de selecionar **Azure Active Directory** como o **Método de Pré-autenticação**.
2. Depois que o aplicativo aparecer na lista de aplicativos empresariais, selecione-o e clique em **Logon único**.
3. Defina o modo de logon único como **Autenticação Integrada do Windows**.  
4. Insira o **SPN do Aplicativo Interno** do servidor de aplicativos. Neste exemplo, o SPN para nosso aplicativo publicado é http/www.contoso.com. Esse SPN precisa estar na lista de serviços aos quais o conector pode apresentar credenciais delegadas. 
5. Escolha a **Identidade de Logon Delegada** para que o conector use em nome de seus usuários. Para obter mais informações, consulte [Trabalhando com identidades diferentes de nuvem e local](#Working-with-different-on-premises-and-cloud-identities)

   ![Configuração de Aplicativo Avançada](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>SSO para aplicativos não Windows
O fluxo de delegação de Kerberos no Proxy de Aplicativo do AD do Azure é iniciado quando o AD do Azure autentica o usuário na nuvem. Depois que a solicitação chega localmente, o conector do Proxy de Aplicativo Azure AD emite um tíquete do Kerberos em nome do usuário interagindo com o Active Directory local. Esse processo é conhecido como KCD (delegação restrita do Kerberos). Na próxima fase, uma solicitação é enviada ao aplicativo de back-end com esse tíquete Kerberos. Há vários protocolos que definem como enviar essas solicitações. A maioria dos servidores não Windows espera Negotiate/SPNego, que agora tem suporte no Proxy de Aplicativo do AD do Azure.

Para obter mais informações sobre o Kerberos, consulte [Tudo o que você deseja saber sobre a delegação restrita de Kerberos (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Os aplicativos que não são do Windows normalmente utilizam nomes de usuário ou nomes de conta SAM em vez de endereços de email de domínio. Se essa situação se aplicar aos seus aplicativos, você precisará configurar o campo de identificação de logon delegada para conectar as identidades de nuvem às identidades de aplicativo. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Trabalhando com identidades diferentes de nuvem e local
O Proxy do Aplicativo pressupõe que os usuários têm a mesma identidade na nuvem e localmente. Se esse não for o caso, você pode ainda poderá usar a KCD para o logon único. Configure uma **Identificação de logon delegada** para cada aplicativo a fim de especificar qual identidade deve ser usada ao realizar o logon único.  

Essa capacidade permite que muitas organizações com identidades diferentes localmente e na nuvem usem o SSO da nuvem para aplicativos locais, sem exigir que os usuários insiram senhas e nomes de usuários diferentes. Isso inclui as organizações que:

* Têm vários domínios internamente (joe@us.contoso.com, joe@eu.contoso.com) e um único domínio na nuvem (joe@contoso.com).
* Têm um nome de domínio não roteável internamente (joe@contoso.usa) e um nome legal na nuvem.
* Não usem nomes de domínio internamente (joe)
* Usem aliases diferentes no local e na nuvem. Por exemplo, joe-johns@contoso.com versus joej@contoso.com  

Com o Proxy de Aplicativo, você pode selecionar qual identidade deve ser usada para obter o tíquete Kerberos. Essa configuração é por aplicativo. Algumas dessas opções são adequadas para sistemas que não aceitam o formato de endereço de email, e outras são desenvolvidas para logon alternativo.

![Captura de tela de parâmetro de identidade de logon delegada](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Se a identidade de logon delegada for usada, o valor não poderá ser exclusivo em todos os domínios ou florestas em sua organização. Você pode evitar esse problema publicando esses aplicativos duas vezes com dois grupos diferentes de Conectores. Como cada aplicativo tem um público de usuários diferente, é possível ingressar seus Conectores em um domínio diferente.

### <a name="configure-sso-for-different-identities"></a>Configurar o SSO para diferentes identidades
1. Defina as configurações do Azure AD Connect para que a identidade principal seja o endereço de email (email). Isso é feito como parte do processo de personalização, alterando o campo **Nome UPN** nas configurações de sincronização. Essas configurações também determinam como os usuários fazem logon no Office365, em dispositivos Windows10 e outros aplicativos que usam o Azure AD como seu armazenamento de identidade.  
   ![Identificando a captura de tela de usuários - lista suspensa Nome UPN](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Nas definições de configuração de aplicativo para o aplicativo que você deseja modificar, selecione a **Identidade de Logon Delegada** a ser usada:

   * Nome UPN (por exemplo, joe@contoso.com)
   * Nome UPN alternativo (por exemplo, joed@contoso.local)
   * Nome de usuário que faz parte do nome UPN (por exemplo, joe)
   * Nome de usuário que faz parte do nome UPN alternativo (por exemplo, joed)
   * Nome da conta SAM local (depende da configuração do controlador de domínio)

### <a name="troubleshooting-sso-for-different-identities"></a>Solucionando problemas de SSO para diferentes identidades
Se houver um erro no processo de SSO, ele aparecerá no log de eventos do computador do conector, conforme explicado na [Solução de problemas](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Porém, em alguns casos, a solicitação será enviada com êxito para o aplicativo de back-end embora este aplicativo responderá em várias outras respostas HTTP. Nesses casos, a solução de problemas deve começar examinando o número de evento 24029 no computador do conector, no log de eventos da sessão do Proxy do Aplicativo. A identidade do usuário que foi usada para delegação será exibida no campo "usuário" nos detalhes do evento. Para ativar o log de sessão, selecione **Mostrar logs analíticos e de depuração** no menu de exibição do visualizador de eventos.

## <a name="next-steps"></a>Próximas etapas

* [Como configurar um aplicativo de Proxy de Aplicativo para usar a Delegação restrita de Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Solucionar problemas que surgirem com o Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)


Para obter as últimas notícias e atualizações, confira o [blog do Proxy de Aplicativo](http://blogs.technet.com/b/applicationproxyblog/)

