---
title: "Como configurar dispositivos adicionados ao Azure Active Directory híbrido | Microsoft Docs"
description: "Saiba como configurar dispositivos adicionados ao Azure Active Directory híbrido."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 3325aa48db36426a8cf1c9859390c357267ee5fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-hybrid-azure-active-directory-joined-devices"></a>Como configurar dispositivos adicionados ao Azure Active Directory híbrido

Com o gerenciamento de dispositivos no Azure AD (Azure Active Directory), você pode garantir que os usuários acessem recursos usando dispositivos que atendam aos padrões de segurança e conformidade. Para obter mais detalhes, confira [Introdução ao gerenciamento de dispositivos no Azure Active Directory](device-management-introduction.md).

Caso tenha um ambiente local do Active Directory e queira ingressar dispositivos adicionados ao domínio no Azure AD, você poderá fazer isso configurando dispositivos adicionados ao Azure AD híbrido. O tópico fornece as etapas relacionadas. 


## <a name="before-you-begin"></a>Antes de começar

Antes de começar a configurar dispositivos adicionados ao Azure AD híbrido no seu ambiente, você deve se familiarizar com os cenários com suporte e as restrições.  

Para melhorar a legibilidade das descrições, este tópico usa o termo a seguir: 

- **Dispositivos atuais do Windows** - este termo refere-se aos dispositivos associados ao domínio que executam o Windows 10 ou o Windows Server 2016.
- **Dispositivos de baixo nível do Windows** - este termo refere-se a todos os dispositivos do Windows associados ao domínio **com suporte** que não estão executando o Windows 10 nem o Windows Server 2016.  


### <a name="windows-current-devices"></a>Dispositivos atuais do Windows

- Para os dispositivos que executam o sistema operacional da área de trabalho do Windows, é recomendável usar a Atualização de Aniversário do Windows 10 (versão 1607) ou posterior. 
- O registro de dispositivos atuais do Windows **tem** suporte em ambientes não-federados, como configurações de sincronização de hash de senha.  


### <a name="windows-down-level-devices"></a>Dispositivos de nível inferior do Windows

- Há suporte para os seguintes dispositivos de nível inferior do Windows:
    - Windows 8.1
    - Windows 7
    - Windows Server 2012 R2
    - Windows Server 2012
    - Windows Server 2008 R2
- O registro de dispositivos de nível inferior do Windows **tem** suporte em ambientes não federados por meio do Logon Único Contínuo [Logon Único Contínuo do Azure Active Directory](https://aka.ms/hybrid/sso).
- O registro de dispositivos de nível inferior do Windows **não tem** suporte para dispositivos que utilizam perfis móveis. Se você depender da mobilidade de perfis ou configurações, use o Windows 10.



## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a habilitar dispositivos adicionados ao Azure AD híbrido em sua organização, você precisa se certificar de que esteja executando uma versão atualizada do Azure AD Connect.

Azure AD Connect:

- Mantém a associação entre a conta do computador no seu Active Directory (AD) local e o objeto do dispositivo no Azure AD. 
- Permite outros recursos relacionados ao dispositivo, como o Windows Hello for Business.



## <a name="configuration-steps"></a>Etapas da configuração

Você pode configurar dispositivos adicionados ao Azure AD híbrido para vários tipos de plataforma de dispositivo Windows. Este tópico inclui as etapas necessárias para todos os cenários de configuração típicos.  

Use a tabela a seguir para ter uma visão geral das etapas necessárias para o seu cenário:  



| Etapas                                      | Sincronização de hash de senha e do Windows atual | Windows atual e a federação | Nível inferior do Windows |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Etapa 1: configurar o ponto de conexão de serviço | ![Verificação][1]                            | ![Verificação][1]                    | ![Verificação][1]        |
| Etapa 2: configurar a emissão de declarações           |                                        | ![Verificação][1]                    | ![Verificação][1]        |
| Etapa 3: habilitar dispositivos não Windows 10      |                                        |                                | ![Verificação][1]        |
| Etapa 4: implantação de controle e distribuição     | ![Verificação][1]                            | ![Verificação][1]                    | ![Verificação][1]        |
| Etapa 5: Verificar os dispositivos adicionados          | ![Verificação][1]                            | ![Verificação][1]                    | ![Verificação][1]        |



## <a name="step-1-configure-service-connection-point"></a>Etapa 1: configurar o ponto de conexão de serviço

O objeto de ponto de conexão de serviço (SCP) é usado por seus dispositivos durante o registro para descobrir informações de locatário do Azure AD. No seu AD (Active Directory) local, o objeto SCP para dispositivos adicionados ao Azure AD híbrido deve existir na partição do contexto de nomenclatura da configuração da floresta do computador. Há apenas um contexto de nomenclatura de configuração por floresta. Em uma configuração de várias florestas do Active Directory, deverá haver um ponto de conexão de serviço em todas as florestas que tiverem computadores associados ao domínio.

Você pode usar o cmdlet [ **Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) para recuperar o contexto de nomenclatura de configuração da sua floresta.  

Para uma floresta com um nome de domínio do Active Directory *fabrikam.com*, o contexto de nomenclatura de configuração é:

`CN=Configuration,DC=fabrikam,DC=com`

Na sua floresta, o objeto de SCP para o registro automático de dispositivos associados ao domínio está localizado em:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Dependendo de como você implantou o Azure AD Connect, o objeto SCP pode ter já ter sido configurado.
Você pode verificar a existência do objeto e recuperar os valores da descoberta usando o seguinte script do Windows PowerShell: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

A saída **$scp.Keywords** mostra as informações de locatário do Azure AD, por exemplo:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Se o ponto de conexão do serviço não existir, você poderá criá-lo executando o cmdlet `Initialize-ADSyncDomainJoinedComputerSync` no seu servidor do Azure AD Connect. As credenciais do admin corporativo são necessárias para executar este cmdlet.  
O cmdlet:

- Cria o ponto de conexão de serviço na floresta do Active Directory à qual o Azure AD Connect está conectado. 
- Exige que você especifique o parâmetro `AdConnectorAccount`. Essa é a conta que está configurada como a conta do conector do Active Directory no Azure AD Connect. 


O script a seguir mostra um exemplo de uso do cmdlet. Nesse script, `$aadAdminCred = Get-Credential` exige que você digite um nome de usuário. Você precisa fornecer o nome de usuário no formato de nome principal (UPN) do usuário (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

O cmdlet `Initialize-ADSyncDomainJoinedComputerSync`:

- Usa o módulo do Active Directory PowerShell e Ferramentas do AD DS, que se baseiam nos Serviços Web do Active Directory em execução em um controlador de domínio. Os Serviços Web do Active Directory têm suporte em controladores de domínio executando o Windows Server 2008 R2 e posterior.
- Há suporte somente para a **versão do módulo 1.1.166.0 do MSOnline PowerShell**. Para baixar este módulo, use este [link](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).   
- Se as ferramentas do AD DS não estiverem instaladas, o `Initialize-ADSyncDomainJoinedComputerSync` falhará.  As ferramentas do AD DS podem ser instaladas pelo Gerenciador do Servidor em Recursos – Ferramentas de Administração de Servidor Remoto – Ferramentas de Administração de Função.

Para controladores de domínio executando o Windows Server 2008 ou versões anteriores, use o script abaixo para criar o ponto de conexão de serviço.

Em uma configuração de várias florestas, você deve usar o script a seguir para criar o ponto de conexão de serviço em cada floresta onde existam computadores:
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your AD configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC

    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()


## <a name="step-2-setup-issuance-of-claims"></a>Etapa 2: configurar a emissão de declarações

Em uma configuração do Azure AD federada, os dispositivos confiam nos Serviços de Federação do Active Directory (AD FS) ou no serviço de federação local de um terceiro para se autenticar no Azure AD. Os dispositivos são autenticados para obter um token de acesso para se registrar com o Serviço de Registro de Dispositivo (Azure DRS) do Azure Active Directory.

Os dispositivos atuais do Windows são autenticados usando a Autenticação Integrada do Windows para um ponto de extremidade WS-Trust ativo (versões 1.3 ou 2005) hospedado pelo serviço de federação local.

> [!NOTE]
> Ao usar o AD FS, **adfs/services/trust/13/windowstransport** ou **adfs/services/trust/2005/windowstransport** devem ser habilitados. Se você estiver usando o proxy Web de autenticação, verifique também se esse ponto de extremidade pode ser publicado por meio do proxy. Você pode ver quais pontos de extremidade são habilitados por meio do console de gerenciamento do AD FS em **Serviço > pontos de extremidade**.
>
>Se você não tiver o AD FS como seu serviço de federação local, siga as instruções do seu fornecedor para garantir que ele ofereça suporte aos pontos de extremidade WS-Trust 1.3 ou 2005 e que eles sejam publicados por meio do Arquivo de Troca de Metadados (MEX).

As seguintes declarações devem existir no token recebido pelo DRS do Azure para que o registro do dispositivo seja concluído. O DRS do Azure criará um objeto de dispositivo no Azure AD com algumas dessas informações que são usadas pelo Azure AD Connect para associar o objeto de dispositivo recém-criado à conta do computador local.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Se você tiver mais de um nome de domínio verificado, precisará fornecer a seguinte declaração para os computadores:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Se você já estiver emitindo uma declaração ImmutableID (por exemplo, a ID de logon alternativo), precisará fornecer uma declaração correspondente para os computadores:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

Nas seções a seguir, você encontrará informações sobre:
 
- Os valores de que cada declaração deve ter
- Como uma definição se pareceria no AD FS

A definição ajuda você a verificar se os valores estão presentes ou se você precisa criá-los.

> [!NOTE]
> Se você não usar o AD FS para o servidor de federação local, siga as instruções do fornecedor para criar a configuração adequada para emitir essas declarações.

### <a name="issue-account-type-claim"></a>Emitir declaração de tipo de conta

**`http://schemas.microsoft.com/ws/2012/01/accounttype`** - Essa declaração deve conter um valor de **DJ**, que identifica o dispositivo como um computador associado ao domínio. No AD FS, você pode adicionar uma regra de transformação de emissão que se parece com o seguinte:

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

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Emita o objectGUID da conta do computador local

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`** - Essa declaração deve conter o valor do **objectGUID** da conta do computador local. No AD FS, você pode adicionar uma regra de transformação de emissão que se parece com o seguinte:

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
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Emita o objectSID da conta do computador local

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** - Essa declaração deve conter o valor do **objectSid** da conta do computador local. No AD FS, você pode adicionar uma regra de transformação de emissão que se parece com o seguinte:

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

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Emita o issuerID de computador quando houver vários nomes de domínio verificados no Azure AD

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`** - Essa declaração deve conter o Uniform Resource Identifier (URI) de qualquer um dos nomes de domínio verificados que se conectam ao serviço de federação local (AD FS ou terceiro) que emite o token. No AD FS, você pode adicionar regras de transformação de emissão parecidas com aquelas abaixo na ordem específica após as mostradas acima. Observe que é necessária uma regra para emitir explicitamente a regra para usuários. Nas regras abaixo, é adicionada uma primeira regra de identificação do usuário vs. a autenticação do computador.

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


Na declaração acima,

- `$<domain>` é a URL de serviço do AD FS
- `<verified-domain-name>` é um espaço reservado que você precisa substituir por um de seus nomes de domínio verificado no Azure AD



Para obter mais detalhes sobre nomes de domínio verificados, confira [Adicionar um nome de domínio ao Azure Active Directory](active-directory-add-domain.md).  
Para obter uma lista dos domínios da empresa verificados, você pode usar o cmdlet [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0). 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Emitir o ImmutableID de computador quando houver um para o usuário (por exemplo, a ID de login alternativa é definida)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`** - Essa declaração deve conter um valor válido para computadores. No AD FS, você pode criar uma regra de transformação de emissão da seguinte maneira:

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

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Script auxiliar para criar regras de transformação de emissão do AD FS

O script a seguir o ajudará com a criação das regras de transformação de emissão descritas acima.

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

### <a name="remarks"></a>Comentários 

- Esse script acrescenta as regras às regras existentes. Não execute o script duas vezes porque o conjunto de regras seria adicionado duas vezes. Certifique-se de que não exista nenhuma regra correspondente para essas declarações (sob as condições correspondentes) antes de executar o script novamente.

- Se você tiver vários nomes de domínio verificados (conforme mostrado no portal do Azure AD ou por meio do cmdlet Get-MsolDomains), defina o valor de **$multipleVerifiedDomainNames** no script para **$true**. Além disso, certifique-se de remover qualquer declaração de issuerid existente que possa ter sido criada pelo Azure AD Connect ou por outros meios. Aqui está um exemplo dessa regra:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Se você já tiver emitido uma declaração **ImmutableID** para contas de usuário, defina o valor de **$immutableIDAlreadyIssuedforUsers** no script para **$true**.

## <a name="step-3-enable-windows-down-level-devices"></a>Etapa 3: habilitar dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos associados ao domínio forem dispositivos de nível inferior do Windows, você precisa:

- Definir uma política no Azure AD para permitir que os usuários registrem dispositivos.
 
- Configurar o serviço de federação local para emitir declarações para dar suporte à **autenticação integrada do Windows (IWA)** para o registro de dispositivos.
 
- Adicionar o ponto de extremidade de autenticação de dispositivo do Azure AD às zonas da Intranet local para evitar prompts de certificação ao autenticar o dispositivo.

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Definir uma política no Azure AD para permitir que os usuários registrem dispositivos

Para registrar dispositivos de nível inferior do Windows, você precisa garantir que a configuração para permitir que os usuários registrem dispositivos no Azure AD esteja definida. Você pode encontrar essa configuração no Portal do Azure em:

`Azure Active Directory > Users and groups > Device settings`
    
A política a seguir deve ser definida como **Todos** : **os usuários podem registrar seus dispositivos com o Azure AD**

![Registrar dispositivos](./media/active-directory-conditional-access-automatic-device-registration-setup/23.png)


### <a name="configure-on-premises-federation-service"></a>Configurar o serviço de federação local 

O serviço de federação local deve oferecer suporte ao emitir as declarações **authenticationmehod** e **wiaormultiauthn** ao receber uma solicitação de autenticação para a terceira parte confiável do Azure AD com um parâmetro resouce_params com um valor codificado como mostrado abaixo:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Quando essa solicitação chega, o serviço de federação local deve autenticar o usuário usando a Autenticação Integrada do Windows e, se ela for bem-sucedida, ele deverá emitir as duas declarações a seguir:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

No AD FS, você precisa adicionar uma regra de transformação de emissão que passe pelo método de autenticação.  

**Para adicionar essa regra:**

1. No console de gerenciamento do AD FS, vá para `AD FS > Trust Relationships > Relying Party Trusts`.
2. Clique com o botão direito do mouse no objeto de confiança de terceira parte confiável da Plataforma de Identidade do Microsoft Office 365 e selecione **Editar Regras de Declaração**.
3. Na guia **Regras de Transformação de Emissão**, selecione **Adicionar Regra**.
4. Na lista de modelos **Regra de declaração**, selecione **Enviar Declarações Usando uma Regra Personalizada**.
5. Selecione **Avançar**.
6. Na caixa **Nome da regra de declaração**, digite **Regra de Declaração de Método de Autenticação**.
7. Na caixa **Regra de declaração** , digite a seguinte regra:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. No servidor de federação, digite o comando do PowerShell abaixo após substituir  **\<RPObjectName\>**  pelo nome do objeto da terceira parte confiável para o seu objeto de terceira parte confiável do Azure AD. Geralmente, este objeto é nomeado como **Plataforma de Identidade do Microsoft Office 365**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Adicione o ponto de extremidade de autenticação de dispositivo do Azure AD para Zonas da Intranet Local

Para evitar prompts de certificado quando os usuários dos dispositivos de registro fizerem a autenticação no Azure AD, você poderá enviar uma política para os seus dispositivos associados ao domínio para adicionar a seguinte URL na zona da Intranet Local no Internet Explorer:

`https://device.login.microsoftonline.com`

## <a name="step-4-control-deployment-and-rollout"></a>Etapa 4: implantação de controle e distribuição

Quando você tiver concluído as etapas necessárias, os dispositivos adicionados ao domínio estarão prontos para ingressar automaticamente no Azure AD:

- Todos os dispositivos associados ao domínio que executarem a Atualização de Aniversário do Windows 10 e o Windows Server 2016 serão registrados automaticamente no Azure AD na reinicialização do dispositivo ou no login. 

- Novos dispositivos serão registrados com o Azure AD quando o dispositivo reiniciar depois que a operação de ingresso no domínio for concluída.

- Os dispositivos que foram anteriormente registrados no Azure AD (por exemplo, no Intune) fazem a transição para "*Adicionado ao Domínio, Registrado no AAD*". No entanto, levará algum tempo para que esse processo seja concluído em todos os dispositivos devido ao fluxo normal de atividade do usuário e do domínio.

### <a name="remarks"></a>Comentários

- Você pode usar um objeto de Política de Grupo para controlar a distribuição de registro automático de computadores associados ao domínio do Windows 10 e do Windows Server 2016.

- A Atualização de 10 de novembro de 2015 do Windows será adicionada automaticamente ao Azure AD **apenas** se o objeto da Política de Grupo de distribuição for definido.

- Para distribuição dos computadores de nível inferior do Windows, você pode implantar um [pacote do Windows Installer](#windows-installer-packages-for-non-windows-10-computers) nos computadores que selecionar.

- Se você efetuar push do objeto de Política de Grupo em dispositivos adicionados ao domínio do Windows 8.1, será feita uma tentativa de ingresso. No entanto, é recomendável usar o [pacote do Windows Installer](#windows-installer-packages-for-non-windows-10-computers) para ingressar todos os seus dispositivos de nível inferior do Windows. 

### <a name="create-a-group-policy-object"></a>Criar um objeto de Política de Grupo 

Para controlar a distribuição dos computadores atuais com Windows, você deve implantar o objeto de Política de Grupo **Registrar computadores adicionados ao domínio como dispositivos** nos dispositivos que deseja registrar. Por exemplo, você pode implantar a política em uma unidade organizacional ou um grupo de segurança.

**Para definir a política:**

1. Abra o **Gerenciador do Servidor** e depois vá para `Tools > Group Policy Management`.
2. Vá para o nó de domínio que corresponde ao domínio em que você deseja ativar o registro automático de computadores atuais do Windows.
3. Clique com o botão direito do mouse em **Objetos de Política de Grupo** e selecione **Novo**.
4. Insira um nome para o objeto de Política de Grupo. Por exemplo, *Ingresso no Azure AD híbrido. 
5. Clique em **OK**.
6. Clique com o botão direito do mouse em seu novo objeto de Política de Grupo e selecione **Editar**.
7. Vá para **Configuração do Computador** > **Políticas** > **Modelos Administrativos** > **Componentes do Windows** > **Registro de Dispositivo**. 
8. Clique com o botão direito do mouse em **Registrar computadores associados ao domínio como dispositivos** e selecione **Editar**.
   
   > [!NOTE]
   > Esse modelo de Política de Grupo foi renomeado de versões anteriores do console de Gerenciamento de Política de Grupo. Se você estiver usando uma versão anterior do console, vá para `Computer Configuration > Policies > Administrative Templates > Windows Components > Workplace Join > Automatically workplace join client computers`. 

7. Selecione **Habilitado** e clique em **Aplicar**.
8. Clique em **OK**.
9. Vincule o objeto da Política de Grupo a um local de sua escolha. Por exemplo, você pode vinculá-lo a uma unidade organizacional específica. Você também pode vinculá-lo a um grupo específico de computadores de segurança que ingressam automaticamente no Azure AD. Para definir essa política para todos os computadores com Windows 10 e Windows Server 2016 ingressados no domínio em sua organização, vincule o objeto de Política de Grupo ao domínio.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Pacotes do Windows Installer para computadores que não são do Windows 10

Para ingressar computadores de nível inferior do Windows adicionados ao domínio em um ambiente federado, você pode baixar e instalar esses pacotes do Windows Installer (.msi) do Centro de Download na página [Microsoft Workplace Join para computador sem o Windows 10](https://www.microsoft.com/en-us/download/details.aspx?id=53554).

Você pode implantar o pacote usando um sistema de distribuição de software como o System Center Configuration Manager. O pacote dá suporte às opções de instalação silenciosa padrão com o parâmetro *quiet*. O System Center Configuration Manager Current Branch oferece benefícios adicionais em relação às versões anteriores, como a capacidade de monitorar registros concluídos. Para obter mais informações, consulte [System Center 2016](https://www.microsoft.com/cloud-platform/system-center-configuration-manager).

O instalador cria uma tarefa agendada no sistema que é executada no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa ingressa silenciosamente o dispositivo no Azure AD com as credenciais do usuário após a autenticação usando a Autenticação Integrada do Windows. Para ver a tarefa agendada, no dispositivo, vá para o **Microsoft** > **Workplace Join** e depois para a biblioteca do Agendador de Tarefas.

## <a name="step-5-verify-joined-devices"></a>Etapa 5: Verificar os dispositivos adicionados

Você pode verificar os dispositivos adicionados com sucesso à sua organização usando o cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) no [módulo PowerShell do Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

A saída desse cmdlet mostra os dispositivos que foram registrados e adicionados no Azure AD. Para obter todos os dispositivos, use o parâmetro **-Todos** e, em seguida, filtre-os usando a propriedade **deviceTrustType**. Dispositivos associados ao domínio possuem um valor de **Associado ao domínio**.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao gerenciamento de dispositivos no Azure Active Directory](device-management-introduction.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
