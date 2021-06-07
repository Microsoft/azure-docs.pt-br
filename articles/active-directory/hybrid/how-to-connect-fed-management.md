---
title: Azure AD Connect - Gerenciamento do AD FS e personalização | Microsoft Docs
description: Gerenciamento do AD FS com o Azure AD Connect e personalização da experiência de entrada do AD FS do usuário com o Azure AD e o PowerShell.
keywords: AD FS, ADFS, gerenciamento de AD FS, AAD Connect, conectar, entrar, AD FS personalização, reparar confiança, M365, Federação, terceira parte confiável
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc0c8c40e370579100c562e0289c97e3f5ce4236
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91274105"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Gerenciar e personalizar os Serviços de Federação do Active Directory usando o Azure AD Connect
Este artigo descreve como gerenciar e personalizar os Serviços de Federação do Active Directory (AD FS) usando o Azure Active Directory (Azure AD) Connect. Ele também inclui outras tarefas comuns do AD FS que você pode precisar realizar para obter uma configuração completa de um farm do AD FS.

| Tópico | O que ele abrange |
|:--- |:--- |
| **Gerenciar o AD FS** | |
| [Reparar a relação de confiança](#repairthetrust) |Como reparar a relação de confiança de Federação com Microsoft 365. |
| [Federar com o Azure AD usando a ID de logon alternativa](#alternateid) | Configurar a federação usando uma ID de logon alternativa  |
| [Adicionar um servidor de AD FS](#addadfsserver) |Como expandir o farm do AD FS com um servidor adicional do AD FS. |
| [Adicionar um Servidor Proxy de Aplicativo Web do AD FS](#addwapserver) |Como expandir um farm do AD FS com um servidor WAP (Proxy de Aplicativo Web) adicional. |
| [Adicionar um domínio federado](#addfeddomain) |Como adicionar um domínio federado. |
| [Atualizar o certificado TLS/SSL](how-to-connect-fed-ssl-update.md)| Como atualizar o certificado TLS/SSL para um farm de AD FS. |
| **Personalizar o AD FS** | |
| [Adicionar uma ilustração ou um logotipo da empresa personalizado](#customlogo) |Como personalizar uma página de entrada do AD FS com um logotipo e uma ilustração da empresa. |
| [Adicionar uma descrição de entrada](#addsignindescription) |Como adicionar uma descrição de página de entrada. |
| [Modificar AD FS regras de declaração](#modclaims) |Como modificar declarações do AD FS para vários cenários de federação. |

## <a name="manage-ad-fs"></a>Gerenciar o AD FS
Você pode realizar várias tarefas relacionadas ao AD FS no Azure AD com mínima intervenção do usuário usando o assistente do Azure AD Connect. Após instalar o Azure AD Connect executando o assistente, você pode executar o assistente novamente para realizar tarefas adicionais.

## <a name="repair-the-trust"></a><a name="repairthetrust"></a>Reparar a relação de confiança 
Você pode usar o Azure AD Connect para verificar a integridade atual da confiança do AD FS e Azure AD e tomar as medidas apropriadas para reparar a relação de confiança. Siga estas etapas para reparar a confiança do Azure AD e AD FS.

1. Selecione **Reparar a relação de confiança do AAD e do ADFS** na lista de tarefas adicionais.
   ![Reparar a relação de confiança do AAD e do ADFS](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. Na página **Conectar ao Azure AD**, forneça suas credenciais de administrador global do Azure AD e clique em **Avançar**.
   ![Captura de tela que mostra a página "conectar ao Azure AD" com as credenciais de exemplo inseridas.](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Na página **Credenciais de acesso remoto** , digite as credenciais de administrador de domínio.

   ![Captura de tela que mostra a página "credenciais de acesso remoto" com as credenciais de exemplo inseridas.](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Depois que você clicar em **Avançar**, o Azure AD Connect verificará a integridade do certificado e mostrará eventuais problemas.

    ![Estado de certificados](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    A página **Pronto para configurar** mostra a lista de ações que serão executadas para reparar a confiança.

    ![Captura de tela que mostra a página "pronto para configurar" com uma lista de ações.](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Clique em **Instalar** para reparar a relação de confiança.

> [!NOTE]
> O Azure AD Connect só pode reparar ou agir em relação a certificados autoassinados. O Azure AD Connect não pode reparar certificados de terceiros.

## <a name="federate-with-azure-ad-using-alternateid"></a><a name="alternateid"></a>Federar com o Azure AD usando o Alternativoid 
É recomendável que o nome UPN local e o nome UPN na nuvem sejam mantidos iguais. Se o UPN local usar um domínio não roteável (por exemplo, Contoso.local) ou não puder ser alterado devido a dependências do aplicativo local, recomendamos configurar uma ID de logon alternativa. A ID de logon alternativa permite configurar uma experiência de logon na qual os usuários podem se conectar com um atributo que não seja o UPN, como o email. A opção para o nome UPN no Azure AD Connect usa como padrão o atributo userPrincipalName no Active Directory. Se você escolher qualquer outro atributo para o nome UPN e estiver federando com o uso do AD FS, o Azure AD Connect configurará o AD FS para a ID de logon alternativa. Um exemplo de escolha de um atributo diferente para o nome UPN é mostrado abaixo:

![Seleção de atributo de ID alternativa](./media/how-to-connect-fed-management/attributeselection.png)

A configuração da ID de logon alternativa do AD FS consiste em duas etapas principais:
1. **Configurar o conjunto certo de declarações de emissão**: as regras de declaração de emissão no objeto de confiança de terceira parte confiável do Azure AD são modificadas para usar o atributo UserPrincipalName selecionado como a ID alternativa do usuário.
2. **Habilitar a ID de logon alternativa na configuração do AD FS**: a configuração do AD FS é atualizada, de forma que o AD FS possa consultar usuários nas florestas apropriadas usando a ID alternativa. Há suporte para essa configuração no AD FS no Windows Server 2012 R2 (com KB2919355) ou posterior. Se os servidores do AD FS forem 2012 R2, o Azure AD Connect verificará a presença do KB obrigatório. Se o KB não for detectado, um aviso será exibido após a conclusão da configuração, conforme mostrado abaixo:

    ![Aviso de ausência de KB no 2012R2](./media/how-to-connect-fed-management/kbwarning.png)

    Para corrigir a configuração no caso de um KB ausente, instale o [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) obrigatório e repare a relação de confiança usando [Reparar relação de confiança entre o AAD e o AD FS](#repairthetrust).

> [!NOTE]
> Para obter mais informações sobre alternateID e as etapas para configurá-la manualmente, leia [Configurando a ID de logon alternativa](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="add-an-ad-fs-server"></a><a name="addadfsserver"></a>Adicionar um servidor do AD FS 

> [!NOTE]
> Para adicionar um servidor do AD FS, o Azure AD Connect requer o arquivo de certificado PFX. Portanto, você só poderá executar essa operação se tiver configurado o farm do AD FS usando o Azure AD Connect.

1. Selecione **Implantar um Servidor de Federação adicional** e clique em **Avançar**.

   ![Servidor de federação adicional](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. Na página **Conectar ao Azure AD**, digite suas credenciais de administrador global do Azure AD e clique em **Avançar**.

   ![Captura de tela que mostra a página "conectar ao Azure AD" com as credenciais de exemplo inseridas.](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Forneça as credenciais de administrador de domínio.

   ![Credenciais de administrador de domínio](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. O Azure AD Connect solicita a senha do arquivo PFX que você forneceu ao configurar o novo farm do AD FS com o Azure AD Connect. Clique em **Digitar Senha** para fornecer a senha para o arquivo PFX.

   ![Captura de tela que mostra a página "especificar certificado SSL" com a janela "senha do certificado" aberta.](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Captura de tela que mostra a página "especificar certificado SSL" depois que uma senha para o arquivo PFX é inserida.](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Na página **Servidores do AD FS** , insira o nome do servidor ou o endereço IP a ser adicionado ao farm do AD FS.

   ![Servidores do AD FS](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Clique em **Avançar** e passe pela página **Configurar** final. Após o Azure AD Connect terminar de adicionar os servidores no farm do AD FS, você terá a opção de verificar a conectividade.

   ![Captura de tela que mostra a página "pronto para configurar" com uma lista de ações a serem concluídas depois de clicar em "instalar".](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Captura de tela que mostra a página "instalação concluída" com a mensagem "a configuração da intranet foi verificada com êxito" exibida. ](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="add-an-ad-fs-wap-server"></a><a name="addwapserver"></a>Adicionar um servidor WAP AD FS 

> [!NOTE]
> Para adicionar um servidor WAP, o Azure AD Connect requer o arquivo de certificado PFX. Portanto, você só poderá executar essa operação se tiver configurado o farm do AD FS usando o Azure AD Connect.

1. Selecione **Implantar Proxy de Aplicativo Web** na lista de tarefas disponíveis.

   ![Implantar o proxy de aplicativo Web](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Forneça as credenciais de administrador global do Azure.

   ![Captura de tela que mostra a página "conectar ao Azure AD" com um exemplo de nome de usuário e senha inseridos.](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Na página **Especificar certificado SSL**, forneça a senha para o arquivo PFX que você forneceu ao configurar o farm do AD FS com o Azure AD Connect.
   ![Senha de certificado](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![Especificar certificado TLS/SSL](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Adicione o servidor a ser adicionado como um servidor WAP. Já que o servidor WAP pode não estar ingressado no domínio, o assistente solicita as credenciais administrativas para o servidor que está sendo adicionado.

   ![Credenciais administrativas de servidor](./media/how-to-connect-fed-management/WapServer5.PNG)

5. Na página **Credenciais de confiança de proxy** , forneça as credenciais administrativas para configurar a confiança de proxy e acessar o servidor primário no farm do AD FS.

   ![Credenciais de confiança de proxy](./media/how-to-connect-fed-management/WapServer6.PNG)

6. Na página **Pronto para configurar** , o assistente mostra a lista de ações que serão executadas.

   ![Captura de tela que mostra a página "pronto para configurar" com uma lista de ações a serem executadas.](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Clique em **Instalar** para concluir a configuração. Depois que a configuração for concluída, o assistente lhe dará a opção para verificar a conectividade com os servidores. Clique em **Verificar** para verificar a conectividade.

   ![Instalação concluída](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="add-a-federated-domain"></a><a name="addfeddomain"></a>Adicionar um domínio federado 

É fácil adicionar um domínio a ser federado com o Azure AD usando o Azure AD Connect. O Azure AD Connect adiciona o domínio para federação e modifica as regras de declaração para refletir corretamente o emissor quando você tem vários domínios federados com o Azure AD.

1. Para adicionar um domínio federado, selecione a tarefa **Adicionar um domínio do Azure AD adicional**.

   ![Domínio do Azure AD adicional](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Na próxima página do assistente, forneça as credenciais de administrador global do Azure AD.

   ![Conecte-se ao AD do Azure](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Na página **Credenciais de acesso remoto** , forneça as credenciais do administrador de domínio.

   ![Credenciais de acesso remoto](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Na próxima página, o assistente fornece uma lista de domínios do Azure AD com os quais você pode federar seu diretório local. Escolha o domínio na lista.

   ![Domínio do Azure AD](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Depois de escolher o domínio, o assistente fornece as informações apropriadas sobre outras ações que o assistente realizará e o impacto da configuração. Em alguns casos, se você selecionar um domínio que ainda não seja verificado no Azure AD, o assistente fornecerá informações para ajudá-lo a verificar o domínio. Confira [Adicionar seu nome de domínio personalizado ao Azure Active Directory](../fundamentals/add-custom-domain.md) para obter mais detalhes.

5. Clique em **Próximo**. A página **Pronto para configurar** mostra a lista de ações que o Azure AD Connect executará. Clique em **Instalar** para concluir a configuração.

   ![Pronto para configurar](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Os usuários do domínio federado adicionado devem ser sincronizados antes que eles possam fazer logon no Azure AD.

## <a name="ad-fs-customization"></a>Personalização do AD FS
As seções a seguir fornecem detalhes sobre algumas das tarefas comuns que talvez você precise executar ao personalizar a página de entrada do AD FS.

## <a name="add-a-custom-company-logo-or-illustration"></a><a name="customlogo"></a>Adicionar uma ilustração ou um logotipo da empresa personalizado 
Para alterar o logotipo da empresa que é exibido na página de **Entrada**, use o cmdlet e sintaxe do Windows PowerShell a seguir.

> [!NOTE]
> As dimensões recomendadas para o logotipo são de 260 x 35 \@ 96 dpi com um tamanho de arquivo máximo de 10 KB.

```azurepowershell-interactive
Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}
```

> [!NOTE]
> O parâmetro *TargetName* é necessário. O tema padrão liberado com o AD FS é denominado Default.

## <a name="add-a-sign-in-description"></a><a name="addsignindescription"></a>Adicionar uma descrição de entrada 
Para adicionar uma descrição à página de **Entrada**, use o seguinte cmdlet e sintaxe do Windows PowerShell.

```azurepowershell-interactive
Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"
```

## <a name="modify-ad-fs-claim-rules"></a><a name="modclaims"></a>Modificar AD FS regras de declaração 
O AD FS dá suporte a uma linguagem de declarações avançada que você pode usar para criar regras de declaração personalizada. Para obter mais informações, confira [A função da linguagem de regras de declaração](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dd807118(v=ws.11)).

As seções a seguir descrevem como você pode escrever regras personalizadas para alguns cenários relacionadas à federação do Azure AD e AD FS.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Condicional de ID imutável em um valor presente no atributo
O Azure AD Connect permite que você especifique um atributo a ser usado como âncora de origem quando objetos são sincronizados ao Azure AD. Se o valor do atributo personalizado não estiver vazio, convém emitir uma declaração de ID imutável.

Por exemplo, você pode selecionar **ms-ds-consistencyguid** como o atributo de âncora de origem e talvez deseje emitir **ImmutableID** como **ms-ds-consistencyguid**, caso o atributo tenha um valor em relação a ele. Se não houver um valor com o atributo, atribua **objectGuid** como a ID imutável. Você pode construir o conjunto de regras de declaração personalizada, conforme descrito na seção a seguir.

**Regra 1: consultar atributos**

```claim-rule-language
c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
=> add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);
```

Nessa regra, você simplesmente consulta os valores de **ms-ds-consistencyguid** e **objectGuid** do usuário do Active Directory. Altere o nome do repositório para um nome de armazenamento apropriado em sua implantação do AD FS. Altere também o tipo de declarações para um tipo de declaração adequado para sua Federação, conforme definido para **objectGUID** e **MS-DS-consistencyguid**.

Além disso, usando **add** e não **issue**, você evita adicionar uma emissão de saída à entidade e pode usar os valores como valores intermediários. Você emitirá a declaração em uma regra mais recente depois de estabelecer o valor a ser usado como a ID imutável.

**Regra 2: verificar se ms-ds-consistencyguid existe para o usuário**

```claim-rule-language
NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
=> add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");
```

Essa regra define um sinalizador temporário chamado **idflag** que é definido como **useguid** se não há nenhum **ms-ds-consistencyguid** populado para o usuário. A lógica por trás disso é o fato de que o AD FS não permite declarações vazias. Portanto, quando você adicionar as declarações `http://contoso.com/ws/2016/02/identity/claims/objectguid` e `http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid` na regra 1, o resultado será uma declaração **msdsconsistencyguid** apenas se o valor for populado para o usuário. Se ele não estiver populado, o AD FS verá que ele terá um valor vazio e o descartará imediatamente. Todos os objetos terão **objectGuid**. Portanto, essa declaração sempre estará presente após a Regra 1 ser executada.

**Regra 3: emitir ms-ds-consistencyguid como a ID imutável, se estiver presente**

```claim-rule-language
c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
=> issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);
```

Essa é uma verificação **Exist** implícita. Se o valor para a declaração existir, emita-o como a ID imutável. O exemplo anterior usa a declaração **nameidentifier** . Você precisará alterar isso para o tipo de declaração adequado para a ID imutável no seu ambiente.

**Regra 4: emitir o objectGuid como a ID imutável se ms-ds-consistencyGuid não estiver presente**

```claim-rule-language
c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
&& c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
=> issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);
```

Nessa regra, você simplesmente verifica o sinalizador temporário **idflag**. Você decide se deve emitir a declaração com base em seu valor.

> [!NOTE]
> A sequência dessas regras é importante.

### <a name="sso-with-a-subdomain-upn"></a>SSO com um subdomínio UPN

Você pode adicionar mais de um domínio a ser federado usando o Azure AD Connect, conforme descrito em [Adicionar um novo domínio federado](how-to-connect-fed-management.md#addfeddomain). Azure AD Connect versão  1.1.553.0 e mais recente cria a regra de reivindicação correta para issuerID automaticamente. Se você não puder usar o Azure AD Connect versão 1.1.553.0 ou mais recente, é recomendável que a ferramenta [ Regras de Declaração do Azure AD RPT ](https://aka.ms/aadrptclaimrules) seja usada para gerar e definir regras de declaração corretas para a confiança da terceira parte do Azure AD.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre as [opções de entrada do usuário](plan-connect-user-signin.md).