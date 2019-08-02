---
title: Configurar dispositivos ingressados no Azure Active Directory híbrido manualmente | Microsoft Docs
description: Saiba como configurar manualmente dispositivos ingressados no Azure Active Directory híbrido.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8babf2a6a4f4a15c6d2979ea0d5ce558dfb0cd6a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052139"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Tutorial: configurar manualmente dispositivos ingressados no Azure Active Directory híbrido

Com o gerenciamento de dispositivos no Azure AD (Azure Active Directory), você pode garantir que os usuários acessem recursos usando dispositivos que atendam aos padrões de segurança e conformidade. Para obter mais informações, confira [Introdução ao gerenciamento de dispositivos no Azure Active Directory](overview.md).

> [!TIP]
> Se usar o Azure AD Connect for uma opção para você, confira os tutoriais relacionados para os domínios [gerenciados](hybrid-azuread-join-managed-domains.md) ou [federados](hybrid-azuread-join-federated-domains.md). Usando o Azure AD Connect, é possível simplificar significativamente a configuração de ingresso do Azure AD híbrido.

Caso tenha um ambiente local do Active Directory e queira ingressar dispositivos adicionados ao domínio no Azure AD, você poderá fazer isso configurando dispositivos adicionados ao Azure AD híbrido. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar manualmente o ingresso no Azure AD híbrido
> * Configurar um ponto de conexão de serviço
> * Configurar a emissão de declarações
> * Habilitar dispositivos de nível inferior do Windows
> * Verificar dispositivos ingressados
> * Solucionar problemas de implementação

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial presume que você já esteja familiarizado com:

* [Introdução ao gerenciamento de dispositivos no Azure Active Directory](../device-management-introduction.md)
* [Planejar sua implementação de junção do Azure Active Directory híbrido](hybrid-azuread-join-plan.md)
* [Controlar ingresso no Azure AD híbrido de seus dispositivos](hybrid-azuread-join-control.md)

Antes de começar a habilitar dispositivos ingressados no Azure AD híbrido em sua organização, verifique se:

* você está executando uma versão atualizada do Azure AD Connect.
* o Azure AD Connect sincronizou os objetos de computador dos dispositivos que você deseja que sejam ingressados no Azure AD pelo Azure AD híbrido. Se os objetos de computador pertencem a UOs (unidades organizacionais) específicas, essas UOs também precisam ser configuradas para sincronização no Azure AD Connect.

Azure AD Connect:

* Mantém a associação entre a conta do computador na instância local do Active Directory e o objeto de dispositivo no Azure AD.
* Habilita outros recursos relacionados ao dispositivo, como o Windows Hello para Empresas.

Verifique se as seguintes URLs podem ser acessadas pelos computadores na rede da organização para o registro dos computadores no Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com`
* o STS da organização (para domínios federados) devem ser incluídos nas configurações de intranet local do usuário

Se a organização está planejando usar o SSO Contínuo, as URLs a seguir precisam estar acessíveis aos computadores da organização. Elas também precisam ser adicionadas à zona de intranet local do usuário.

* `https://autologon.microsoftazuread-sso.com`

Além disso, a seguinte configuração deve ser habilitada na zona de intranet do usuário: "Permitir atualizações da barra de status por meio de script".

Se sua organização usar a instalação gerenciada (não federada) com o Active Directory local e não usar os Serviços de Federação do Active Directory (AD FS) para federar ao Azure AD, o ingresso no Azure AD híbrido no Windows 10 dependerá de objetos de computador no Active Directory para ser sincronizado com o Azure AD. Verifique se as UOs que contêm os objetos de computador que precisam ser ingressados no Azure AD híbrido estão habilitadas para sincronização na configuração de sincronização do Azure AD Connect.

Para dispositivos Windows 10 na versão 1703 ou anterior, se a sua organização exigir acesso à Internet por meio de um proxy de saída, será necessário implementar a WPAD (Descoberta Automática de Proxy da Web) para permitir que computadores com Windows 10 sejam registrados no Azure AD.

A partir do Windows 10 1803, mesmo se a tentativa de ingresso no Azure AD híbrido por um dispositivo em um domínio federado usando o AD FS falhar e se o Azure AD Connect estiver configurado para sincronizar os objetos de computador/dispositivo com o Azure AD, o dispositivo tentará concluir o ingresso no Azure AD híbrido usando o computador/dispositivo sincronizado.

## <a name="verify-configuration-steps"></a>Etapas para verificar a configuração

Você pode configurar dispositivos adicionados ao Azure AD híbrido para vários tipos de plataforma de dispositivo Windows. Este tópico inclui as etapas necessárias para todos os cenários de configuração típicos.  

Use a tabela a seguir para ter uma visão geral das etapas necessárias para o seu cenário:  

| Etapas | Sincronização de hash de senha e do Windows atual | Windows atual e a federação | Nível inferior do Windows |
| :--- | :---: | :---: | :---: |
| Configurar o ponto de conexão de serviço | ![Verificação][1] | ![Verificação][1] | ![Verificação][1] |
| Configurar a emissão de declarações |     | ![Verificação][1] | ![Verificação][1] |
| Permitir que dispositivos não Windows 10 |       |        | ![Verificação][1] |
| Verificar dispositivos ingressados | ![Verificação][1] | ![Verificação][1] | [Marca de seleção][1] |

## <a name="configure-a-service-connection-point"></a>Configurar um ponto de conexão de serviço

O objeto de SCP (ponto de conexão de serviço) é usado pelos seus dispositivos durante o registro para descobrir informações de locatário do Azure AD. Na instância local do Active Directory, o objeto SCP para dispositivos ingressados no Azure AD híbrido precisa existir na partição do contexto de nomenclatura da configuração da floresta do computador. Há apenas um contexto de nomenclatura de configuração por floresta. Em uma configuração de várias florestas do Active Directory, o ponto de conexão de serviço precisa existir em todas as florestas que contêm computadores ingressados no domínio.

Você pode usar o cmdlet [ **Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) para recuperar o contexto de nomenclatura de configuração da sua floresta.  

Para uma floresta com um nome de domínio do Active Directory *fabrikam.com*, o contexto de nomenclatura de configuração é:

`CN=Configuration,DC=fabrikam,DC=com`

Na sua floresta, o objeto de SCP para o registro automático de dispositivos associados ao domínio está localizado em:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Dependendo de como você implantou o Azure AD Connect, o objeto SCP poderá ter já ter sido configurado.
Você pode verificar a existência do objeto e recuperar os valores da descoberta usando o seguinte script do Windows PowerShell:

   ```PowerShell
   $scp = New-Object System.DirectoryServices.DirectoryEntry;

   $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

   $scp.Keywords;
   ```

A saída **$scp.Keywords** mostra as informações de locatário do Azure AD. Aqui está um exemplo:

   ```
   azureADName:microsoft.com
   azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47
   ```

Se o ponto de conexão do serviço não existir, você poderá criá-lo executando o cmdlet `Initialize-ADSyncDomainJoinedComputerSync` no seu servidor do Azure AD Connect. As credenciais do admin corporativo são necessárias para executar este cmdlet.  

O cmdlet:

* cria o ponto de conexão de serviço na floresta do Active Directory à qual o Azure AD Connect está conectado.
* Exige que você especifique o parâmetro `AdConnectorAccount`. Essa conta é configurada como a conta do conector do Active Directory no Azure AD Connect.


O script a seguir mostra um exemplo de uso do cmdlet. Nesse script, `$aadAdminCred = Get-Credential` exige que você digite um nome de usuário. Você precisa fornecer o nome de usuário no formato de nome principal (UPN) do usuário (`user@example.com`).

   ```PowerShell
   Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

   $aadAdminCred = Get-Credential;

   Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;
   ```

O cmdlet `Initialize-ADSyncDomainJoinedComputerSync`:

* usa o módulo do PowerShell do Active Directory e as ferramentas do Azure AD DS (Azure Active Directory Domain Services). Essas ferramentas dependem de serviços Web do Active Directory em execução em um controlador de domínio. Os Serviços Web do Active Directory têm suporte em controladores de domínio executando o Windows Server 2008 R2 e posterior.
* Somente há suporte para a versão do módulo 1.1.166.0 do MSOnline PowerShell. Para baixar este módulo, use [este link](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).
* Se as ferramentas do AD DS não estiverem instaladas, o `Initialize-ADSyncDomainJoinedComputerSync` falhará. Você pode instalar as ferramentas do AD DS por meio do Gerenciador do Servidor em **Recursos** > **Ferramentas de Administração de Servidor Remoto** > **Ferramentas de Administração de Funções**.

Para controladores de domínio que executam o Windows Server 2008 ou versões anteriores, use o script abaixo para criar o ponto de conexão de serviço. Em uma configuração de várias florestas, use o script a seguir para criar o ponto de conexão de serviço em cada floresta em que exista computadores.

   ```PowerShell
   $verifiedDomain = "contoso.com" # Replace this with any of your verified domain names in Azure AD
   $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47" # Replace this with you tenant ID
   $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com" # Replace this with your Active Directory configuration naming context

   $de = New-Object System.DirectoryServices.DirectoryEntry
   $de.Path = "LDAP://CN=Services," + $configNC
   $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
   $deDRC.CommitChanges()

   $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
   $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
   $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

   $deSCP.CommitChanges()
   ```

No script anterior, `$verifiedDomain = "contoso.com"` é um espaço reservado. Substitua-o por um de seus nomes de domínio verificados no Azure AD. Você precisa ter o domínio antes de poder usá-lo.

Para obter mais informações sobre nomes de domínio verificados, confira [Adicionar um nome de domínio ao Azure Active Directory](../active-directory-domains-add-azure-portal.md).

Para obter uma lista dos domínios da empresa verificados, use o cmdlet [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0).

![Lista de domínios da empresa](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Configurar a emissão de declarações

Em uma configuração do Azure AD federada, os dispositivos dependem do AD FS ou de um servidor de federação local de um parceiro da Microsoft para autenticarem-se no Azure AD. Os dispositivos são autenticados para obter um token de acesso para se registrar com o Serviço de Registro de Dispositivo (Azure DRS) do Azure Active Directory.

Os dispositivos atuais do Windows são autenticados usando a Autenticação Integrada do Windows com um ponto de extremidade WS-Trust ativo (versões 1.3 ou 2005) hospedado pelo serviço de federação local.

> [!NOTE]
> Se você estiver usando o AD FS, o **adfs/services/trust/13/windowstransport** ou o **adfs/services/trust/2005/windowstransport** precisará ser habilitado. Se você estiver usando o Proxy de Autenticação na Web, verifique também se esse ponto de extremidade foi publicado por meio do proxy. Veja quais pontos de extremidade estão habilitados por meio do console de gerenciamento do AD FS em **Serviço** > **Pontos de extremidade**.
>
> Se o AD FS não for seu serviço de federação local, siga as instruções do fornecedor para garantir que ele dê suporte aos pontos de extremidade WS-Trust 1.3 ou 2005 e que sejam publicados por meio do MEX (Arquivo de Troca de Metadados).

Para que o registro do dispositivo seja concluído, as seguintes declarações precisam existir no token recebido pelo DRS do Azure. O DRS do Azure criará um objeto de dispositivo no Azure AD com algumas dessas informações. Em seguida, o Azure AD Connect usará essas informações para associar o objeto de dispositivo recém-criado à conta de computador local.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Se você tiver mais de um nome de domínio verificado, precisará fornecer a seguinte declaração para os computadores:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Se já estiver emitindo uma declaração ImmutableID (por exemplo, a ID de logon alternativa), você precisará fornecer uma declaração correspondente para os computadores:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

Nas seções a seguir, você encontrará informações sobre:

* Os valores que cada declaração deve ter.
* Como uma definição seria no AD FS.

A definição ajuda você a verificar se os valores estão presentes ou se você precisa criá-los.

> [!NOTE]
> Se você não usar o AD FS para o servidor de federação local, siga as instruções do fornecedor para criar a configuração adequada para emitir essas declarações.

### <a name="issue-account-type-claim"></a>Emitir declaração de tipo de conta

A declaração `http://schemas.microsoft.com/ws/2012/01/accounttype` precisa conter um valor de **DJ**, que identifica o dispositivo como um computador ingressado no domínio. No AD FS, você pode adicionar uma regra de transformação de emissão que se parece com o seguinte:

   ```
   @RuleName = "Issue account type for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "DJ"
   );
   ```

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Emita o objectGUID da conta do computador local

A declaração `http://schemas.microsoft.com/identity/claims/onpremobjectguid` precisa conter o valor de **objectGUID** da conta do computador local. No AD FS, você pode adicionar uma regra de transformação de emissão que se parece com o seguinte:

   ```
   @RuleName = "Issue object GUID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$", 
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"),
      query = ";objectguid;{0}",
      param = c2.Value
   );
   ```

### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Emita o objectSID da conta do computador local

A declaração `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` precisa conter o valor de **objectSid** da conta do computador local. No AD FS, você pode adicionar uma regra de transformação de emissão que se parece com o seguinte:

   ```
   @RuleName = "Issue objectSID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(claim = c2);
   ```

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>Emita a issuerID do computador quando houver vários nomes de domínio verificados no Azure AD

A declaração `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` precisa conter o URI (Uniform Resource Identifier) de qualquer um dos nomes de domínio verificados que se conectam ao serviço de federação local (AD FS ou terceiros) que emite o token. No AD FS, você pode adicionar regras de transformação de emissão como as seguintes na ordem específica, após as mostradas acima. Observe que é necessária uma regra para emitir explicitamente a regra para usuários. Nas regras a seguir, é adicionada uma primeira regra que identifica o usuário, em vez da autenticação do computador.

   ```
   @RuleName = "Issue account type with the value User when its not a computer"
   NOT EXISTS(
   [
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "DJ"
   ]
   )
   => add(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "User"
   );

   @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
   c1:[
      Type == "http://schemas.xmlsoap.org/claims/UPN"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "User"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = regexreplace(
      c1.Value,
      ".+@(?<domain>.+)",
      "http://${domain}/adfs/services/trust/"
      )
   );

   @RuleName = "Issue issuerID for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = "http://<verified-domain-name>/adfs/services/trust/"
   );
   ```

Na declaração anterior, `<verified-domain-name>` é um espaço reservado. Substitua-o por um de seus nomes de domínio verificados no Azure AD. Por exemplo, use `Value = "http://contoso.com/adfs/services/trust/"`.

Para obter mais informações sobre nomes de domínio verificados, confira [Adicionar um nome de domínio ao Azure Active Directory](../active-directory-domains-add-azure-portal.md).  

Para obter uma lista dos domínios da empresa verificados, você pode usar o cmdlet [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0).

![Lista de domínios da empresa](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-an-alternate-login-id-is-set"></a>Emitir a ImmutableID para o computador quando existe uma para os usuários (por exemplo, está definida uma ID de logon alternativa)

A declaração `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` precisa conter um valor válido para computadores. No AD FS, você pode criar uma regra de transformação de emissão da seguinte maneira:

   ```
   @RuleName = "Issue ImmutableID for computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"),
      query = ";objectguid;{0}",
      param = c2.Value
   );
   ```

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Script auxiliar para criar regras de transformação de emissão do AD FS

O script a seguir ajuda você com a criação das regras de transformação de emissão descritas anteriormente.

   ```
   $multipleVerifiedDomainNames = $false
   $immutableIDAlreadyIssuedforUsers = $false
   $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains

   $rule1 = '@RuleName = "Issue account type for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "DJ"
   );'

   $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"),
      query = ";objectguid;{0}",
      param = c2.Value
   );'

   $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(claim = c2);'

   $rule4 = ''
   if ($multipleVerifiedDomainNames -eq $true) {
   $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
   NOT EXISTS(
   [
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "DJ"
   ]
   )
   => add(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "User"
   );

   @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
   c1:[
      Type == "http://schemas.xmlsoap.org/claims/UPN"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "User"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = regexreplace(
      c1.Value,
      ".+@(?<domain>.+)",
      "http://${domain}/adfs/services/trust/"
      )
   );

   @RuleName = "Issue issuerID for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
   );'
   }

   $rule5 = ''
   if ($immutableIDAlreadyIssuedforUsers -eq $true) {
   $rule5 = '@RuleName = "Issue ImmutableID for computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"),
      query = ";objectguid;{0}",
      param = c2.Value
   );'
   }

   $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

   $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

   $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

   Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString
   ```

#### <a name="remarks"></a>Comentários

* Esse script acrescenta as regras às regras existentes. Não execute o script duas vezes porque o conjunto de regras seria adicionado duas vezes. Certifique-se de que não exista nenhuma regra correspondente para essas declarações (sob as condições correspondentes) antes de executar o script novamente.
* Se houver vários nomes de domínio verificados (conforme mostrado no portal do Azure AD ou por meio do cmdlet **Get-MsolDomain**), defina o valor de **$multipleVerifiedDomainNames** no script como **$true**. Além disso, remova qualquer declaração **issuerid** existente que possa ter sido criada pelo Azure AD Connect ou por outros meios. Aqui está um exemplo dessa regra:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
   => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 
   ```

Se você já tiver emitido uma declaração **ImmutableID** para contas de usuário, defina o valor de **$immutableIDAlreadyIssuedforUsers** no script para **$true**.

## <a name="enable-windows-down-level-devices"></a>Habilitar dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos ingressados no domínio forem dispositivos de nível inferior do Windows, será necessário:

* Definir uma política no Azure AD para permitir que os usuários registrem dispositivos.
* Configurar o serviço de federação local para emitir declarações para dar suporte à IWA (Autenticação Integrada do Windows) para o registro de dispositivos.
* Adicione o ponto de extremidade de autenticação de dispositivo do Azure AD às zonas da intranet local para evitar prompts de certificado ao autenticar o dispositivo.
* Controle dispositivos de nível inferior do Windows.

### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Definir uma política no Azure AD para permitir que os usuários registrem dispositivos

Para registrar dispositivos de nível inferior do Windows, verifique se a configuração para permitir que os usuários registrem dispositivos no Azure AD está habilitada. No portal do Azure, você pode encontrar essa configuração em **Azure Active Directory** > **Usuários e grupos** > **Configurações de dispositivo**.

A política a seguir deve ser definida como **Tudo**: **Os usuários podem registrar seus dispositivos no Azure AD**.

![O botão Tudo que permite que os usuários registrem dispositivos](./media/hybrid-azuread-join-manual/23.png)

### <a name="configure-the-on-premises-federation-service"></a>Configurar o serviço de federação local

O serviço de federação local precisa dar suporte à emissão das declarações **authenticationmethod** e **wiaormultiauthn** quando recebe uma solicitação de autenticação para a terceira parte confiável do Azure AD que contém um parâmetro resource_params com o seguinte valor codificado:

   ```
   eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

   which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}
   ```

Quando essa solicitação chega, o serviço de federação local precisa autenticar o usuário usando a autenticação integrada do Windows. Quando a autenticação for bem-sucedida, o serviço de federação precisará emitir as duas declarações a seguir:

   `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows` `http://schemas.microsoft.com/claims/wiaormultiauthn`

No AD FS, você precisa adicionar uma regra de transformação de emissão que passe pelo método de autenticação. Para adicionar essa regra:

1. No console de gerenciamento do AD FS, vá para **AD FS** > **Relações de Confiança** > **Terceiras Partes Confiáveis**.
1. Clique com o botão direito do mouse no objeto de confiança de terceira parte confiável da Plataforma de Identidade do Microsoft Office 365 e selecione **Editar Regras de Declaração**.
1. Na guia **Regras de Transformação de Emissão**, selecione **Adicionar Regra**.
1. Na lista de modelos **Regra de declaração**, selecione **Enviar Declarações Usando uma Regra Personalizada**.
1. Selecione **Avançar**.
1. No **Nome da regra de declaração**, digite **Regra de Declaração de Método de Autenticação**.
1. Na caixa **Regra de declaração**, insira a seguinte regra:

   `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

1. No servidor de federação, digite o seguinte comando do PowerShell. Substitua **\<RPObjectName\>** pelo nome do objeto de terceira parte confiável do objeto de confiança de terceira parte confiável do Azure AD. Geralmente, este objeto é nomeado como **Plataforma de Identidade do Microsoft Office 365**.

   `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Adicionar o ponto de extremidade de autenticação de dispositivo do Azure AD nas zonas da intranet local

Para evitar prompts de certificado quando os usuários de dispositivos registrados fizerem a autenticação no Azure AD, você poderá enviar uma política por push aos seus dispositivos ingressados no domínio para adicionar a seguinte URL na zona da intranet local no Internet Explorer:

`https://device.login.microsoftonline.com`

### <a name="control-windows-down-level-devices"></a>Controlar os dispositivos de nível inferior do Windows

Para registrar os dispositivos de nível inferior do Windows, é necessário baixar e instalar um pacote do Windows Installer (.msi) do Centro de Download. Para obter mais informações, confira a seção [Validação controlada do ingresso do Azure AD híbrido em dispositivos de nível inferior do Windows](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices).

## <a name="verify-joined-devices"></a>Verificar dispositivos ingressados

Você pode verificar os dispositivos ingressados com sucesso à sua organização usando o cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) no [módulo do PowerShell do Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

A saída desse cmdlet mostra os dispositivos que foram registrados e adicionados no Azure AD. Para obter todos os dispositivos, use o parâmetro **-All** e, em seguida, filtre-os usando a propriedade **deviceTrustType**. Dispositivos ingressados no domínio possuem um valor de **Ingressado no domínio**.

## <a name="troubleshoot-your-implementation"></a>Solucionar problemas de implementação

Se estiver com problemas para concluir o ingresso no Azure AD híbrido de dispositivos Windows ingressados no domínio, confira:

* [Solucionar problemas de ingresso no Azure AD Híbrido para dispositivos atuais do Windows](troubleshoot-hybrid-join-windows-current.md)
* [Solucionar problemas de ingresso no Azure AD Híbrido para dispositivos de nível inferior do Windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao gerenciamento de dispositivos no Azure Active Directory](overview.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
