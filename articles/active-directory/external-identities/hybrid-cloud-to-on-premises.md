---
title: Conceder aos usuários B2B acesso aos seus aplicativos locais-Azure AD
description: Mostra como fornecer aos usuários B2B de nuvem acesso a aplicativos locais com a colaboração B2B do Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd91d1d2c9f5a4a413f9ea64cfdef649823d0f09
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131013"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Conceder aos usuários B2B do Microsoft Azure AD acesso aos aplicativos locais

Como uma organização que usa recursos de colaboração B2B do Microsoft Azure AD (Azure Active Directory) para convidar usuários convidados de organizações parceiras para o Microsoft Azure AD, agora é possível fornecer a esses usuários B2B acesso a aplicativos locais. Esses aplicativos locais podem usar a autenticação baseada em SAML ou a IWA (Autenticação Integrada do Windows) com KCD (delegação restrita de Kerberos).

## <a name="access-to-saml-apps"></a>Acesso a aplicativos SAML

Se o aplicativo local usar a autenticação baseada em SAML, você poderá disponibilizar facilmente esses aplicativos aos usuários de colaboração do Microsoft Azure AD B2B por meio do Portal do Azure.

Para isso, será necessário fazer o seguinte:

- Integre o aplicativo usando o SAML conforme descrito em [Configurar logon único baseado em SAML](../manage-apps/configure-saml-single-sign-on.md). Certifique-se de anotar o que é utilizado para o valor da **URL de logon** .
-  Use o Proxy de Aplicativo do Azure Active Directory para publicar o aplicativo local com o **Azure Active Directory** configurado como a fonte de autenticação. Para obter instruções, consulte [Publicar aplicativos usando o Proxy de Aplicativo do Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md). 

   Ao configurar a configuração **Internal Url** , use a URL de logon especificada no modelo de aplicativo inexistente na galeria. Dessa forma, os usuários podem acessar o aplicativo fora do limite da organização. O Proxy de Aplicativo realiza o logon único do SAML para o aplicativo local.
 
   ![Mostra a URL interna e a autenticação das configurações do aplicativo local](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Acesso a aplicativos IWA e KCD

Para fornecer aos usuários B2B acesso a aplicativos locais que são protegidos com a delegação restrita de Autenticação Integrada do Windows e Kerberos, serão necessários os componentes a seguir:

- **Autenticação por meio do Proxy de Aplicativo do Azure Active Directory** . Os usuários B2B devem autenticar-se no aplicativo local. Para fazer isso, é necessário publicar o aplicativo local por meio do Proxy de Aplicativo do Azure Active Directory. Para obter mais informações, consulte [tutorial: adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo](../manage-apps/application-proxy-add-on-premises-application.md).
- **Autorização por meio de um objeto de usuário B2B no diretório local** . O aplicativo deve executar verificações de acesso do usuário e conceder acesso aos recursos corretos. A IWA e KCD exigem um objeto de usuário no Active Directory do Windows Server local para concluir essa autorização. Conforme descrito em [Como o logon único com KCD funciona](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works), o Proxy de aplicativo precisa desse objeto de usuário para representar o usuário e obter um token do Kerberos no aplicativo. 

   > [!NOTE]
   > Ao configurar o Proxy de Aplicativo do AD do Azure, verifique se a **identidade de logon delegada** está definida como **nome principal do usuário** (padrão) na configuração de logon único para IWA (autenticação integrada do Windows).

   Para o cenário de usuário B2B, há dois métodos disponíveis que você pode usar para criar os objetos de usuário convidado que são necessários para autorização no diretório local:

   - MIM (Microsoft Identity Manager) e agente de gerenciamento MIM para Microsoft Graph. 
   - [Um script do PowerShell](#create-b2b-guest-user-objects-through-a-script-preview). Usar o script é uma solução mais leve que não requer o MIM. 

O diagrama a seguir fornece uma visão geral de alto nível de como o Proxy de Aplicativo do Azure Active Directory e a geração do objeto de usuário B2B no diretório local trabalham em conjunto para conceder aos usuários B2B acesso aos aplicativos IWA e KCD locais. As etapas numeradas são descritas em detalhes no diagrama abaixo.

![Diagrama de soluções de script MIM e B2B](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Um usuário de uma organização parceira (o locatário Fabrikam) é convidado para o locatário Contoso.
2.  Um objeto de usuário convidado é criado no locatário Contoso (por exemplo, um objeto de usuário com um UPN de deguest_fabrikam.com#EXT#@contoso.onmicrosoft.com).
3.  O convidado Fabrikam é importado da Contoso por meio do MIM ou script do PowerShell B2B.
4.  Uma representação ou “footprint” do objeto de usuário convidado Fabrikam (Guest#EXT#) é criado no diretório local, Contoso.com, por meio do MIM ou por meio do script PowerShell B2B.
5.  O usuário convidado acessa o aplicativo local, app.contoso.com.
6.  A solicitação de autenticação é autorizada por meio do Proxy de Aplicativo, usando a delegação restrita de Kerberos. 
7.  Como o objeto de usuário convidado existe localmente, a autenticação é com êxito.

### <a name="lifecycle-management-policies"></a>Políticas de gerenciamento do ciclo de vida

É possível gerenciar os objetos de usuário B2B local por meio das políticas de gerenciamento do ciclo de vida. Por exemplo: 

- É possível configurar políticas de MFA (Autenticação Multifator) para o usuário Convidado, de modo que a MFA seja utilizada durante a autenticação do Proxy de Aplicativos. Para obter mais informações, consulte [acesso condicional para usuários de colaboração B2B](conditional-access.md).
- Quaisquer patrocínios, revisões de acesso, verificações de conta e etc. executadas no usuário B2B de nuvem aplicam-se aos usuários locais. Por exemplo, se o usuário de nuvem for excluído por meio de suas políticas de gerenciamento do ciclo de vida, o usuário local também será excluído pela sincronização do MIM ou por meio de sincronização de Azure AD Connect. Para obter mais informações, consulte [gerenciar o acesso de convidado com revisões de acesso do Azure ad](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Criar objetos de usuários convidados B2B através do MIM

Para obter informações sobre como usar o MIM 2016 Service Pack 1 e o agente de gerenciamento do MIM para Microsoft Graph para criar os objetos de usuário convidado no diretório local, consulte [Colaboração B2B (entre empresas) do Microsoft Azure AD com MIM (Microsoft Identity Manager) 2016 SP1 com Azure Proxy de Aplicativo](/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Criar objetos de usuário convidado B2B por meio de um script (versão prévia)

Há um script de amostra do PowerShell disponível que pode ser utilizado como ponto de partida para criar os objetos de usuário convidado no Active Directory local.

Você pode baixar o script e o arquivo Leiame dos [conectores para Microsoft Identity Manager 2016 e o Forefront Identity Manager 2010 R2](https://www.microsoft.com/download/details.aspx?id=51495). No pacote de download, escolha o **script e Leiame para extrair os usuários de on-prem.zipdo Azure ad B2B** .

Antes de usar o script, certifique-se de revisar os pré-requisitos e as considerações importantes no arquivo Leiame associado. Além disso, entenda que o script é disponibilizado apenas como um exemplo. Sua equipe de desenvolvimento ou um parceiro deverá personalizar e revisar o script antes de executá-lo.

## <a name="license-considerations"></a>Considerações sobre licença

Certifique-se de que você possui as CALs (Licenças de Acesso para Cliente) corretas para usuários convidados externos que acessam aplicativos locais. Para obter mais informações, consulte a seção "Conectores Externos" das [Licenças de Acesso para Cliente e Licenças de Gerenciamento](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx). Consulte o seu representante da Microsoft ou revendedor local em relação às suas necessidades específicas de licenciamento.

## <a name="next-steps"></a>Próximas etapas

- [Colaboração do Azure Active Directory B2B para organizações híbridas](hybrid-organizations.md)

- Para uma visão geral do Azure AD Connect, consulte [Integrar seus diretórios locais ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md).