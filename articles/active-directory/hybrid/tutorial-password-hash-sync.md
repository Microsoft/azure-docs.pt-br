---
title: 'Tutorial:  Integrar uma única floresta do AD ao Azure usando o PHS'
description: Demonstra como configurar um ambiente de identidade híbrido usando a sincronização de senha.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b82dcc633f0f2bb13cb84f80873117f9eb94a24f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96176113"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>Tutorial:  Integrar uma única floresta do AD usando a sincronização de hash da senha (PHS)

![Criar](media/tutorial-password-hash-sync/diagram.png)

O tutorial a seguir guiará você pela criação de um ambiente de identidade híbrido usando a sincronização de senha hash.  Esse ambiente pode ser usado para testes ou para familiarizar-se mais com o funcionamento de uma identidade híbrida.

## <a name="prerequisites"></a>Pré-requisitos
A seguir estão os pré-requisitos necessários para concluir este tutorial
- Um computador com [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) instalado.  É recomendável fazer isso em um computador [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) ou um computador [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- Um [adaptador de rede externa](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) para permitir que a máquina virtual comunique-se com a Internet.
- Uma [assinatura do Azure](https://azure.microsoft.com/free)
- Uma cópia do Windows Server 2016

> [!NOTE]
> Este tutorial usa scripts do PowerShell para que você possa criar o ambiente do tutorial no menor tempo possível.  Cada um dos scripts usa variáveis que são declaradas no início dos scripts.  Você pode e deve alterar as variáveis para refletir seu ambiente.
>
>Os scripts usados criam um ambiente geral do Active Directory antes de instalar o Azure AD Connect.  Eles são relevantes para todos os tutoriais.
>
> Cópias dos scripts do PowerShell que são usados neste tutorial estão disponíveis no GitHub [aqui](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
A primeira etapa necessária para colocar o ambiente de identidade híbrida em funcionamento é criar uma máquina virtual que será usada como o servidor local do Active Directory.  Faça o seguinte:

1. Abra o ISE do PowerShell como Administrador.
2. Execute o seguinte script.

```powershell
#Declare variables
$VMName = 'DC1'
$Switch = 'External'
$InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
$Path = 'D:\VM'
$VHDPath = 'D:\VM\DC1\DC1.vhdx'
$VHDSize = '64424509440'

#Create New Virtual Machine
New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

#Set the memory to be non-dynamic
Set-VMMemory $VMName -DynamicMemoryEnabled $false

#Add DVD Drive to Virtual Machine
Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

#Mount Installation Media
$DVDDrive = Get-VMDvdDrive -VMName $VMName

#Configure Virtual Machine to Boot from DVD
Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
```

## <a name="complete-the-operating-system-deployment"></a>Concluir a implantação de sistema operacional
Para concluir a criação da máquina virtual, é necessário concluir a instalação do sistema operacional.

1. Gerenciador Hyper-V, clique duas vezes na máquina virtual
2. Clique no botão Iniciar.
3. Você será solicitado a "Pressionar qualquer tecla para inicializar a partir de um CD ou DVD". Prossiga e faça isso.
4. Na tela de inicialização do Windows Server, selecione o idioma e clique em **Avançar**.
5. Clique em **Instalar Agora**.
6. Insira a chave de licença e clique em **Avançar**.
7. Marque **Eu aceito os termos da licença e clique em **Avançar**.
8. Selecione **Personalizado:  Instalar somente o Windows (Avançado)**
9. Clique em **Avançar**.
10. Quando a instalação estiver concluída, reinicie a máquina virtual, entre e execute as atualizações do Windows para garantir que a VM seja a mais atualizada.  Instale as últimas atualizações.

## <a name="install-active-directory-prerequisites"></a>Instalar os pré-requisitos do Active Directory
Agora que temos uma máquina virtual ativa, precisamos executar algumas etapas antes de instalar o Active Directory.  Ou seja, precisamos renomear a máquina virtual, definir um endereço IP estático e informações de DNS e instalar as ferramentas de Administração de Servidor Remoto.   Faça o seguinte:

1. Abra o ISE do PowerShell como Administrador.
2. Execute o seguinte script.

```powershell
#Declare variables
$ipaddress = "10.0.1.117" 
$ipprefix = "24" 
$ipgw = "10.0.1.1" 
$ipdns = "10.0.1.117"
$ipdns2 = "8.8.8.8" 
$ipif = (Get-NetAdapter).ifIndex 
$featureLogPath = "c:\poshlog\featurelog.txt" 
$newname = "DC1"
$addsTools = "RSAT-AD-Tools" 

#Set static IP address
New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

# Set the DNS servers
Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

#Rename the computer 
Rename-Computer -NewName $newname -force 

#Install features 
New-Item $featureLogPath -ItemType file -Force 
Add-WindowsFeature $addsTools 
Get-WindowsFeature | Where installed >>$featureLogPath 

#Restart the computer 
Restart-Computer
```

## <a name="create-a-windows-server-ad-environment"></a>Criar um ambiente AD do Windows Server
Agora que temos a VM criada e ela foi renomeada e tem um endereço IP estático, podemos instalar e configurar o Active Directory Domain Services.  Faça o seguinte:

1. Abra o ISE do PowerShell como Administrador.
2. Execute o seguinte script.

```powershell 
#Declare variables
$DatabasePath = "c:\windows\NTDS"
$DomainMode = "WinThreshold"
$DomainName = "contoso.com"
$DomaninNetBIOSName = "CONTOSO"
$ForestMode = "WinThreshold"
$LogPath = "c:\windows\NTDS"
$SysVolPath = "c:\windows\SYSVOL"
$featureLogPath = "c:\poshlog\featurelog.txt" 
$Password = "Pass1w0rd"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>Criar um usuário do AD do Windows Server
Agora que temos o ambiente do Active Directory, precisamos de uma conta de teste.  Essa conta será criada no ambiente AD local e, em seguida, sincronizada com o Azure AD.  Faça o seguinte:

1. Abra o ISE do PowerShell como Administrador.
2. Execute o seguinte script.

```powershell 
#Declare variables
$Givenname = "Allie"
$Surname = "McCray"
$Displayname = "Allie McCray"
$Name = "amccray"
$Password = "Pass1w0rd"
$Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


#Create the user
New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

#Set the password to never expire
Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
```

## <a name="create-an-azure-ad-tenant"></a>Criar um locatário do Azure AD
Agora, é necessário criar um locatário do Azure AD para que seja possível sincronizar os usuários com a nuvem.  Para criar um novo locatário do Azure AD, faça o seguinte.

1. Navegue até o [portal do Azure](https://portal.azure.com) e entre com uma conta que tenha uma assinatura do Azure.
2. Selecione o **ícone adição (+)** e pesquise **Azure Active Directory**.
3. Selecione **Azure Active Directory** nos resultados da pesquisa.
4. Selecione **Criar**.</br>
![Captura de tela que mostra como criar um locatário do Azure AD.](media/tutorial-password-hash-sync/create1.png)</br>
5. Forneça um **nome para a organização** juntamente com o **nome de domínio inicial**. Em seguida, selecione **Criar**. Isso criará criar o diretório.
6. Depois que for concluído, clique no link **aqui** para gerenciar o diretório.

## <a name="create-a-global-administrator-in-azure-ad"></a>Criar um administrador global no Azure AD
Agora que temos um locatário do Azure AD, criaremos uma conta de administrador global.  Essa conta é usada para criar a conta do Azure AD Connector durante a instalação do Azure AD Connect.  A conta do Azure AD Connector é usada para gravar informações no Azure AD.   Para criar a conta de administrador global, faça o seguinte.

1.  Em **Gerenciar**, selecione **Usuários**.</br>
![Captura de tela que mostra a opção Usuário selecionada na seção Gerenciar, em que você cria um Administrador global no Azure AD.](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Selecione **Todos os usuários** e, em seguida, selecione **+ Novo usuário**.
3.  Forneça um nome e um nome de usuário para esse usuário. Este será o Administrador Global para o locatário. Convém também alterar a **função do diretório** para **Administrador global.** Também é possível mostrar a senha temporária. Ao concluir, selecione **Criar**.</br>
![Captura de tela que mostra o botão Criar que você seleciona quando cria um Administrador global no Azure AD.](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Depois que isso for concluído, abra um novo navegador da Web e entre em myapps.microsoft.com usando a nova conta de administrador global e a senha temporária.
5. Altere a senha do administrador global para algo que você irá lembrar.

## <a name="download-and-install-azure-ad-connect"></a>Baixe e instale o Azure AD Connect
Agora é hora de baixar e instalar o Azure AD Connect.  Uma vez instalado, vamos executar a instalação expressa.  Faça o seguinte:

1. Baixe o [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Navegue até **AzureADConnect.msi** e clique duas vezes nessa opção.
3. Na tela de boas-vindas, marque a caixa de concordar com os termos da licença e clique em **Continuar**.  
4. Na tela de configurações expressas, clique em **Usar configurações expressas**.</br>  
![Captura de tela que mostra as configurações do Expresso e o botão Usar configurações expressas.](media/tutorial-password-hash-sync/express1.png)</br>
5. Na tela do Azure AD a conectar, digite o nome de usuário e a senha do administrador global do Azure AD. Clique em **Próximo**.  
6. Na tela Conectar AD DS, digite o nome de usuário e senha para uma conta de administrador corporativa. Clique em **Próximo**.  
7. Na tela Pronto para configurar, clique em **Instalar**.
8. Quando a instalação for concluída, clique em **Sair**.
9. Após a conclusão da instalação, saia e entre novamente antes de usar o Synchronization Service Manager ou o Editor de Regras de Sincronização.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Verificar se os usuários foram criados e a sincronização está ocorrendo
Nesse momento, verificaremos se os usuários que estavam no diretório local foram sincronizados e agora existem no locatário do Azure AD.  Esteja ciente de que isso pode levar algumas horas para ser concluído.  Para verificar se os usuários estão sincronizados, faça o seguinte.


1. Navegue até o [portal do Azure](https://portal.azure.com) e entre com uma conta que tenha uma assinatura do Azure.
2. Na esquerda, selecione **Azure Active Directory**
3. Em **Gerenciar**, selecione **Usuários**.
4. Verifique se você vê os novos usuários em nosso inquilino</br>
![Sincronização](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Testar entrando com um dos usuários

1. Navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Entre com uma conta de usuário que foi criada no novo locatário.  Será necessário entrar usando o formato a seguir: (user@domain.onmicrosoft.com). Use a mesma senha que o usuário usa para entrar no local.</br>
   ![Verificar](media/tutorial-password-hash-sync/verify1.png)</br>

Agora você configurou com êxito um ambiente de identidade híbrida que pode ser usado para testar e familiarizar-se com o que o Azure tem a oferecer.

## <a name="next-steps"></a>Próximas etapas


- [Pré-requisitos e hardware](how-to-connect-install-prerequisites.md) 
- [Configurações Expressas](how-to-connect-install-express.md)
- [Sincronização de hash de senha](how-to-connect-password-hash-synchronization.md)|