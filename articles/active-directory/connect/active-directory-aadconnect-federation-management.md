---
title: "Gerenciamento e personalização dos Serviços de Federação do Active Directory com o Azure AD Connect | Microsoft Docs"
description: "Gerenciamento do AD FS com o Azure AD Connect e personalização da experiência de entrada do AD FS do usuário com o Azure AD e o PowerShell."
keywords: "AD FS, ADFS, gerenciamento do AD FS, AAD Connect, Conectar, entrada, personalização do AD FS, reparar confiança, O365, federação, terceira parte confiável"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 0cab39bb3ce5c42fac543171baea498eb894c502
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Gerenciar e personalizar os Serviços de Federação do Active Directory usando o Azure AD Connect
Este artigo descreve como gerenciar e personalizar os Serviços de Federação do Active Directory (AD FS) usando o Azure Active Directory (Azure AD) Connect. Ele também inclui outras tarefas comuns do AD FS que você pode precisar realizar para obter uma configuração completa de um farm do AD FS.

| Tópico | O que ele abrange |
|:--- |:--- |
| **Gerenciar o AD FS** | |
| [Reparar a relação de confiança](#repairthetrust) |Como reparar a confiança de federação com o Office 365. |
| [Federar ao Azure AD usando uma ID de logon alternativa ](#alternateid) | Configurar a federação usando uma ID de logon alternativa  |
| [Adicionar um servidor do AD FS](#addadfsserver) |Como expandir o farm do AD FS com um servidor adicional do AD FS. |
| [Adicionar um Servidor Proxy de Aplicativo Web do AD FS](#addwapserver) |Como expandir um farm do AD FS com um servidor WAP (Proxy de Aplicativo Web) adicional. |
| [Adicionar um domínio federado](#addfeddomain) |Como adicionar um domínio federado. |
| [Atualizar o certificado SSL](active-directory-aadconnectfed-ssl-update.md)| Como atualizar o certificado SSL para um farm do AD FS. |
| **Personalizar o AD FS** | |
| [Adicionar uma ilustração ou um logotipo da empresa personalizado](#customlogo) |Como personalizar uma página de entrada do AD FS com um logotipo e uma ilustração da empresa. |
| [Adicionar uma descrição de entrada](#addsignindescription) |Como adicionar uma descrição de página de entrada. |
| [Modificar regras de declaração do AD FS](#modclaims) |Como modificar declarações do AD FS para vários cenários de federação. |

## <a name="manage-ad-fs"></a>Gerenciar o AD FS
Você pode realizar várias tarefas relacionadas ao AD FS no Azure AD com mínima intervenção do usuário usando o assistente do Azure AD Connect. Após instalar o Azure AD Connect executando o assistente, você pode executar o assistente novamente para realizar tarefas adicionais.

## <a name="repairthetrust"></a>Reparar a confiança 
Você pode usar o Azure AD Connect para verificar a integridade atual da confiança do AD FS e Azure AD e tomar as medidas apropriadas para reparar a relação de confiança. Siga estas etapas para reparar a confiança do Azure AD e AD FS.

1. Selecione **Reparar a relação de confiança do AAD e do ADFS** na lista de tarefas adicionais.
   ![Reparar a relação de confiança do AAD e do ADFS](media/active-directory-aadconnect-federation-management/RepairADTrust1.PNG)

2. Na página **Conectar ao Azure AD**, forneça suas credenciais de administrador global do Azure AD e clique em **Avançar**.
   ![Conecte-se ao AD do Azure](media/active-directory-aadconnect-federation-management/RepairADTrust2.PNG)

3. Na página **Credenciais de acesso remoto** , digite as credenciais de administrador de domínio.

   ![Credenciais de acesso remoto](media/active-directory-aadconnect-federation-management/RepairADTrust3.PNG)

    Depois que você clicar em **Avançar**, o Azure AD Connect verificará a integridade do certificado e mostrará eventuais problemas.

    ![Estado de certificados](media/active-directory-aadconnect-federation-management/RepairADTrust4.PNG)

    A página **Pronto para configurar** mostra a lista de ações que serão executadas para reparar a confiança.

    ![Pronto para configurar](media/active-directory-aadconnect-federation-management/RepairADTrust5.PNG)

4. Clique em **Instalar** para reparar a relação de confiança.

> [!NOTE]
> O Azure AD Connect só pode reparar ou agir em relação a certificados autoassinados. O Azure AD Connect não pode reparar certificados de terceiros.

## <a name="alternateid"></a>Federar com o Azure AD usando uma AlternateID 
É recomendável que o nome UPN local e o nome UPN na nuvem sejam mantidos iguais. Se o UPN local usar um domínio não roteável (por exemplo, Contoso.local) ou não puder ser alterado devido a dependências do aplicativo local, recomendamos configurar uma ID de logon alternativa. A ID de logon alternativa permite configurar uma experiência de logon na qual os usuários podem se conectar com um atributo que não seja o UPN, como o email. A opção para o nome UPN no Azure AD Connect usa como padrão o atributo userPrincipalName no Active Directory. Se você escolher qualquer outro atributo para o nome UPN e estiver federando com o uso do AD FS, o Azure AD Connect configurará o AD FS para a ID de logon alternativa. Um exemplo de escolha de um atributo diferente para o nome UPN é mostrado abaixo:

![Seleção de atributo de ID alternativa](media/active-directory-aadconnect-federation-management/attributeselection.png)

A configuração da ID de logon alternativa do AD FS consiste em duas etapas principais:
1. **Configurar o conjunto certo de declarações de emissão**: as regras de declaração de emissão no objeto de confiança de terceira parte confiável do Azure AD são modificadas para usar o atributo UserPrincipalName selecionado como a ID alternativa do usuário.
2. **Habilitar a ID de logon alternativa na configuração do AD FS**: a configuração do AD FS é atualizada, de forma que o AD FS possa consultar usuários nas florestas apropriadas usando a ID alternativa. Há suporte para essa configuração no AD FS no Windows Server 2012 R2 (com KB2919355) ou posterior. Se os servidores do AD FS forem 2012 R2, o Azure AD Connect verificará a presença do KB obrigatório. Se o KB não for detectado, um aviso será exibido após a conclusão da configuração, conforme mostrado abaixo:

    ![Aviso de ausência de KB no 2012R2](media/active-directory-aadconnect-federation-management/kbwarning.png)

    Para corrigir a configuração no caso de um KB ausente, instale o [KB2919355](http://go.microsoft.com/fwlink/?LinkID=396590) obrigatório e repare a relação de confiança usando [Reparar relação de confiança entre o AAD e o AD FS](#repairthetrust).

> [!NOTE]
> Para obter mais informações sobre alternateID e as etapas para configurá-la manualmente, leia [Configurando a ID de logon alternativa](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="addadfsserver"></a>Adicionar um servidor do AD FS 

> [!NOTE]
> Para adicionar um servidor do AD FS, o Azure AD Connect requer o arquivo de certificado PFX. Portanto, você só poderá executar essa operação se tiver configurado o farm do AD FS usando o Azure AD Connect.

1. Selecione **Implantar um Servidor de Federação adicional** e clique em **Avançar**.

   ![Servidor de federação adicional](media/active-directory-aadconnect-federation-management/AddNewADFSServer1.PNG)

2. Na página **Conectar ao Azure AD**, digite suas credenciais de administrador global do Azure AD e clique em **Avançar**.

   ![Conecte-se ao AD do Azure](media/active-directory-aadconnect-federation-management/AddNewADFSServer2.PNG)

3. Forneça as credenciais de administrador de domínio.

   ![Credenciais de administrador de domínio](media/active-directory-aadconnect-federation-management/AddNewADFSServer3.PNG)

4. O Azure AD Connect solicita a senha do arquivo PFX que você forneceu ao configurar o novo farm do AD FS com o Azure AD Connect. Clique em **Digitar Senha** para fornecer a senha para o arquivo PFX.

   ![Senha de certificado](media/active-directory-aadconnect-federation-management/AddNewADFSServer4.PNG)

    ![Especificar certificado SSL](media/active-directory-aadconnect-federation-management/AddNewADFSServer5.PNG)

5. Na página **Servidores do AD FS** , insira o nome do servidor ou o endereço IP a ser adicionado ao farm do AD FS.

   ![Servidores do AD FS](media/active-directory-aadconnect-federation-management/AddNewADFSServer6.PNG)

6. Clique em **Avançar** e passe pela página **Configurar** final. Após o Azure AD Connect terminar de adicionar os servidores no farm do AD FS, você terá a opção de verificar a conectividade.

   ![Pronto para configurar](media/active-directory-aadconnect-federation-management/AddNewADFSServer7.PNG)

    ![Instalação concluída](media/active-directory-aadconnect-federation-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>Adicionar um servidor WAP do AD FS 

> [!NOTE]
> Para adicionar um servidor WAP, o Azure AD Connect requer o arquivo de certificado PFX. Portanto, você só poderá executar essa operação se tiver configurado o farm do AD FS usando o Azure AD Connect.

1. Selecione **Implantar Proxy de Aplicativo Web** na lista de tarefas disponíveis.

   ![Implantar o proxy de aplicativo Web](media/active-directory-aadconnect-federation-management/WapServer1.PNG)

2. Forneça as credenciais de administrador global do Azure.

   ![Conecte-se ao AD do Azure](media/active-directory-aadconnect-federation-management/wapserver2.PNG)

3. Na página **Especificar certificado SSL**, forneça a senha para o arquivo PFX que você forneceu ao configurar o farm do AD FS com o Azure AD Connect.
   ![Senha de certificado](media/active-directory-aadconnect-federation-management/WapServer3.PNG)

    ![Especificar certificado SSL](media/active-directory-aadconnect-federation-management/WapServer4.PNG)

4. Adicione o servidor a ser adicionado como um servidor WAP. Já que o servidor WAP pode não estar ingressado no domínio, o assistente solicita as credenciais administrativas para o servidor que está sendo adicionado.

   ![Credenciais administrativas de servidor](media/active-directory-aadconnect-federation-management/WapServer5.PNG)

5. Na página **Credenciais de confiança de proxy** , forneça as credenciais administrativas para configurar a confiança de proxy e acessar o servidor primário no farm do AD FS.

   ![Credenciais de confiança de proxy](media/active-directory-aadconnect-federation-management/WapServer6.PNG)

6. Na página **Pronto para configurar** , o assistente mostra a lista de ações que serão executadas.

   ![Pronto para configurar](media/active-directory-aadconnect-federation-management/WapServer7.PNG)

7. Clique em **Instalar** para concluir a configuração. Depois que a configuração for concluída, o assistente lhe dará a opção para verificar a conectividade com os servidores. Clique em **Verificar** para verificar a conectividade.

   ![Instalação concluída](media/active-directory-aadconnect-federation-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Adicionar um domínio federado 

É fácil adicionar um domínio a ser federado com o Azure AD usando o Azure AD Connect. O Azure AD Connect adiciona o domínio para federação e modifica as regras de declaração para refletir corretamente o emissor quando você tem vários domínios federados com o Azure AD.

1. Para adicionar um domínio federado, selecione a tarefa **Adicionar um domínio do Azure AD adicional**.

   ![Domínio do Azure AD adicional](media/active-directory-aadconnect-federation-management/AdditionalDomain1.PNG)

2. Na próxima página do assistente, forneça as credenciais de administrador global do Azure AD.

   ![Conecte-se ao AD do Azure](media/active-directory-aadconnect-federation-management/AdditionalDomain2.PNG)

3. Na página **Credenciais de acesso remoto** , forneça as credenciais do administrador de domínio.

   ![Credenciais de acesso remoto](media/active-directory-aadconnect-federation-management/additionaldomain3.PNG)

4. Na próxima página, o assistente fornece uma lista de domínios do Azure AD com os quais você pode federar seu diretório local. Escolha o domínio na lista.

   ![Domínio do Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain4.PNG)

    Depois de escolher o domínio, o assistente fornece as informações apropriadas sobre outras ações que o assistente realizará e o impacto da configuração. Em alguns casos, se você selecionar um domínio que ainda não seja verificado no Azure AD, o assistente fornecerá informações para ajudá-lo a verificar o domínio. Confira [Adicionar seu nome de domínio personalizado ao Azure Active Directory](../active-directory-add-domain.md) para obter mais detalhes.

5. Clique em **Avançar**. A página **Pronto para configurar** mostra a lista de ações que o Azure AD Connect executará. Clique em **Instalar** para concluir a configuração.

   ![Pronto para configurar](media/active-directory-aadconnect-federation-management/AdditionalDomain5.PNG)

> [!NOTE]
> Os usuários do domínio federado adicionado devem ser sincronizados antes que eles possam fazer logon no Azure AD.

## <a name="ad-fs-customization"></a>Personalização do AD FS
As seções a seguir fornecem detalhes sobre algumas das tarefas comuns que talvez você precise executar ao personalizar a página de entrada do AD FS.

## <a name="customlogo"></a>Adicionar uma ilustração ou um logotipo da empresa personalizado 
Para alterar o logotipo da empresa que é exibido na página de **Entrada**, use o cmdlet e sintaxe do Windows PowerShell a seguir.

> [!NOTE]
> As dimensões recomendadas para o logotipo são 260x35 a 96 dpi com um tamanho do arquivo máximo de 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> O parâmetro *TargetName* é obrigatório. O tema padrão liberado com o AD FS é denominado Default.

## <a name="addsignindescription"></a>Adicionar uma descrição de entrada 
Para adicionar uma descrição à página de **Entrada**, use o seguinte cmdlet e sintaxe do Windows PowerShell.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>Modificar regras de declaração do AD FS 
O AD FS dá suporte a uma linguagem de declarações avançada que você pode usar para criar regras de declaração personalizada. Para obter mais informações, confira [A função da linguagem de regras de declaração](https://technet.microsoft.com/library/dd807118.aspx).

As seções a seguir descrevem como você pode escrever regras personalizadas para alguns cenários relacionadas à federação do Azure AD e AD FS.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Condicional de ID imutável em um valor presente no atributo
O Azure AD Connect permite que você especifique um atributo a ser usado como âncora de origem quando objetos são sincronizados ao Azure AD. Se o valor do atributo personalizado não estiver vazio, convém emitir uma declaração de ID imutável.

Por exemplo, você pode selecionar **ms-ds-consistencyguid** como o atributo de âncora de origem e talvez deseje emitir **ImmutableID** como **ms-ds-consistencyguid**, caso o atributo tenha um valor em relação a ele. Se não houver um valor com o atributo, atribua **objectGuid** como a ID imutável. Você pode construir o conjunto de regras de declaração personalizada, conforme descrito na seção a seguir.

**Regra 1: consultar atributos**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Nessa regra, você simplesmente consulta os valores de **ms-ds-consistencyguid** e **objectGuid** do usuário do Active Directory. Altere o nome do repositório para um nome de armazenamento apropriado em sua implantação do AD FS. Também altere o tipo de declarações para um tipo apropriado para a federação, conforme definido para **objectGuid** e **ms-ds-consistencyguid**.

Além disso, usando **add** e não **issue**, você evita adicionar uma emissão de saída à entidade e pode usar os valores como valores intermediários. Você emitirá a declaração em uma regra mais recente depois de estabelecer o valor a ser usado como a ID imutável.

**Regra 2: verificar se ms-ds-consistencyguid existe para o usuário**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Essa regra define um sinalizador temporário chamado **idflag** que é definido como **useguid** se não há nenhum **ms-ds-consistencyguid** populado para o usuário. A lógica por trás disso é o fato de que o AD FS não permite declarações vazias. Assim, quando você adiciona as declarações http://contoso.com/ws/2016/02/identity/claims/objectguid e http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid à Regra 1, você só acabará com uma declaração **msdsconsistencyguid** se o valor for populado para o usuário. Se ele não estiver populado, o AD FS verá que ele terá um valor vazio e o descartará imediatamente. Todos os objetos terão **objectGuid**. Portanto, essa declaração sempre estará presente após a Regra 1 ser executada.

**Regra 3: emitir ms-ds-consistencyguid como a ID imutável, se estiver presente**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Essa é uma verificação **Exist** implícita. Se o valor para a declaração existir, emita-o como a ID imutável. O exemplo anterior usa a declaração **nameidentifier** . Você precisará alterar isso para o tipo de declaração adequado para a ID imutável no seu ambiente.

**Regra 4: emitir o objectGuid como a ID imutável se ms-ds-consistencyGuid não estiver presente**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

Nessa regra, você simplesmente verifica o sinalizador temporário **idflag**. Você decide se deve emitir a declaração com base em seu valor.

> [!NOTE]
> A sequência dessas regras é importante.

### <a name="sso-with-a-subdomain-upn"></a>SSO com um subdomínio UPN
Você pode adicionar mais de um domínio a ser federado usando o Azure AD Connect, conforme descrito em [Adicionar um novo domínio federado](active-directory-aadconnect-federation-management.md#addfeddomain). Você deve modificar a declaração de nome UPN para que a ID do emissor corresponda ao domínio raiz e não ao subdomínio, pois o domínio raiz federado também abrange o filho.

Por padrão, a regra da declaração da ID do emissor é definida como:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Declaração de ID do emissor padrão](media/active-directory-aadconnect-federation-management/issuer_id_default.png)

A regra padrão simplesmente usa o sufixo do UPN na declaração da ID do emissor. Por exemplo, John é um usuário em sub.contoso.com e contoso.com é federado com o Azure AD. João insere john@sub.contoso.com como o nome de usuário ao entrar no Azure AD. A regra de declaração de ID de emissor padrão no AD FS lida com isso da seguinte maneira:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Valor de declaração:**  http://sub.contoso.com/adfs/services/trust/

Para ter apenas o domínio raiz no valor de declaração do emissor, altere a regra de declaração para corresponder ao seguinte:

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre as [opções de entrada do usuário](active-directory-aadconnect-user-signin.md).
