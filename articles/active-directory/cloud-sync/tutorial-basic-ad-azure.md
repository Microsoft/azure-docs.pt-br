---
title: Tutorial – Ambiente do Azure AD e local do Active Directory básico.
services: active-directory
description: Saiba como criar um ambiente básico do AD e do Azure AD.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: de59b3f26a9a4696c721cab94cd5b96bd0200e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98611835"
---
# <a name="tutorial-basic-active-directory-environment"></a>Tutorial: Ambiente básico do Active Directory

Este tutorial orienta você na criação de um ambiente básico do Active Directory. 

![Diagrama que mostra um ambiente básico do Azure AD.](media/tutorial-single-forest/diagram-2.png)

Você pode usar o ambiente criado no tutorial para testar vários aspectos de cenários de identidade híbrida e será um pré-requisito para alguns dos tutoriais.  Se você já tiver um ambiente do Active Directory, poderá usá-lo como substituto.  Essas informações são fornecidas para pessoas que estão começando desde o princípio.

Este tutorial consiste em
## <a name="prerequisites"></a>Pré-requisitos
A seguir estão os pré-requisitos necessários para concluir este tutorial
- Um computador com [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) instalado.  É recomendável fazer isso em um computador [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) ou um computador [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- Um [adaptador de rede externa](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) para permitir que a máquina virtual comunique-se com a Internet.
- Uma [assinatura do Azure](https://azure.microsoft.com/free)
- Uma cópia do Windows Server 2016
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)

> [!NOTE]
> Este tutorial usa scripts do PowerShell para que você possa criar o ambiente do tutorial no menor tempo possível.  Cada um dos scripts usa variáveis que são declaradas no início dos scripts.  Você pode e deve alterar as variáveis para refletir seu ambiente.
>
>Os scripts usados criam um ambiente do Active Directory geral antes de instalar o agente de provisionamento de nuvem do Azure AD Connect.  Eles são relevantes para todos os tutoriais.
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
Agora que você tem uma máquina virtual ativa, precisa executar algumas etapas antes de instalar o Active Directory.  Ou seja, precisa renomear a máquina virtual, definir um endereço IP estático e informações de DNS e instalar as Ferramentas de Administração de Servidor Remoto.   Faça o seguinte:

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
Agora que você tem a VM criada e ela foi renomeada e tem um endereço IP estático, pode instalar e configurar o Active Directory Domain Services.  Faça o seguinte:

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
Agora que você tem o ambiente do Active Directory, precisa de uma conta de teste.  Essa conta será criada no ambiente AD local e, em seguida, sincronizada com o Azure AD.  Faça o seguinte:

1. Abra o ISE do PowerShell como Administrador.
2. Execute o seguinte script.

    ```powershell 
    # Filename:    4_CreateUser.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
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
![Captura de tela que mostra a página do Azure Active Directory no portal do Azure.](media/tutorial-single-forest/create-1.png)</br>
5. Forneça um **nome para a organização** juntamente com o **nome de domínio inicial**. Em seguida, selecione **Criar**. Isso criará criar o diretório.
6. Depois que for concluído, clique no link **aqui** para gerenciar o diretório.

## <a name="create-a-global-administrator-in-azure-ad"></a>Criar um administrador global no Azure AD
Agora que você tem um locatário do Azure AD, criará uma conta de administrador global.  Para criar a conta de administrador global, faça o seguinte.

1.  Em **Gerenciar**, selecione **Usuários**.</br>
![Captura de tela que mostra o menu "Visão geral" com "Usuários" selecionado.](media/tutorial-single-forest/administrator-1.png)</br>
2.  Selecione **Todos os usuários** e, em seguida, selecione **+ Novo usuário**.
3.  Forneça um nome e um nome de usuário para esse usuário. Este será o Administrador Global para o locatário. Convém também alterar a **função do diretório** para **Administrador global.** Também é possível mostrar a senha temporária. Ao concluir, selecione **Criar**.</br>
![Criar](media/tutorial-single-forest/administrator-2.png)</br>
4. Depois que isso for concluído, abra um novo navegador da Web e entre em myapps.microsoft.com usando a nova conta de administrador global e a senha temporária.
5. Altere a senha do administrador global para algo que você irá lembrar.

## <a name="optional--additional-server-and-forest"></a>Opcional:  Servidor e floresta adicionais
A seguir está uma seção opcional que fornece as etapas para criar um servidor e/ou floresta adicional.  Isso pode ser usado em alguns dos tutoriais mais avançados, como [Piloto do Azure AD Connect para a sincronização de nuvem](tutorial-pilot-aadc-aadccp.md).

Se você precisar apenas de um servidor adicional, poderá parar após a etapa **Criar a máquina virtual** e ingressar o servidor no domínio existente que foi criado acima.  

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Abra o ISE do PowerShell como Administrador.
2. Execute o seguinte script.

    ```powershell
    # Filename:    1_CreateVM_CP.ps1
    # Description: Creates a VM to be used in the tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. #This script is made available to you without any express, implied or statutory warranty, not even the implied warranty of merchantability or fitness for a particular purpose, or the warranty of title or non-infringement. The entire risk of the use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $VMName = 'CP1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\CP1\CP1.vhdx'
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

### <a name="complete-the-operating-system-deployment"></a>Concluir a implantação de sistema operacional
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

### <a name="install-active-directory-prerequisites"></a>Instalar os pré-requisitos do Active Directory
Agora que você tem uma máquina virtual ativa, precisa executar algumas etapas antes de instalar o Active Directory.  Ou seja, precisa renomear a máquina virtual, definir um endereço IP estático e informações de DNS e instalar as Ferramentas de Administração de Servidor Remoto.   Faça o seguinte:

1. Abra o ISE do PowerShell como Administrador.
2. Execute o seguinte script.

    ```powershell
    # Filename:    2_ADPrep_CP.ps1
    # Description: Prepares your environment for Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $ipaddress = "10.0.1.118" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.118"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "CP1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    #Set the DNS servers
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
### <a name="create-a-windows-server-ad-environment"></a>Criar um ambiente AD do Windows Server
Agora que você tem a VM criada e ela foi renomeada e tem um endereço IP estático, pode instalar e configurar o Active Directory Domain Services.  Faça o seguinte:

1. Abra o ISE do PowerShell como Administrador.
2. Execute o seguinte script.

    ```powershell
    # Filename:    3_InstallAD_CP.ps1
    # Description: Creates an on-premises AD environment.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "fabrikam.com"
    $DomaninNetBIOSName = "FABRIKAM"
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

### <a name="create-a-windows-server-ad-user"></a>Criar um usuário do AD do Windows Server
Agora que você tem o ambiente do Active Directory, precisa de uma conta de teste.  Essa conta será criada no ambiente AD local e, em seguida, sincronizada com o Azure AD.  Faça o seguinte:

1. Abra o ISE do PowerShell como Administrador.
2. Execute o seguinte script.

    ```powershell 
    # Filename:    4_CreateUser_CP.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Anna"
    $Surname = "Ringdal"
    $Displayname = "Anna Ringdal"
    $Name = "aringdal"
    $Password = "Pass1w0rd"
    $Identity = "CN=aringdal,CN=Users,DC=fabrikam,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```

## <a name="conclusion"></a>Conclusão
Agora você tem um ambiente que pode ser usado para os tutoriais existentes e para testar recursos adicionais fornecidos pela sincronização de nuvem.

## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem do Azure AD Connect?](what-is-cloud-sync.md)