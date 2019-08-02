---
title: Implantar a Sincronização de Arquivos do Azure | Microsoft Docs
description: Saiba como implantar a Sincronização de arquivos do Azure do início ao fim.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6a41830dcb7f681713db7a7802ab430581dc844f
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371162"
---
# <a name="deploy-azure-file-sync"></a>Implantar a Sincronização de Arquivos do Azure
Use a Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização em Arquivos do Azure enquanto mantém a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. A Sincronização de arquivos do Azure transforma o Windows Server em um cache rápido do compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter tantos caches quantos precisar em todo o mundo.

É altamente recomendável que você leia [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md) e [Planejando uma implantação de Sincronização de arquivos do Azure](storage-sync-files-planning.md) antes de completar as etapas descritas neste artigo.

## <a name="prerequisites"></a>Pré-requisitos
* Um compartilhamento de arquivos do Azure na mesma região que você deseja implantar Sincronização de Arquivos do Azure. Para obter mais informações, consulte:
    - [Disponibilidade de região](storage-sync-files-planning.md#region-availability) para Sincronização de arquivos do Azure.
    - Para orientações passo a passo sobre como criar uma conta de compartilhamento, consulte [Criar uma conta de compartilhamento](storage-how-to-create-file-share.md).
* Pelo menos uma instância do Windows Server ou cluster do Windows Server para sincronizar com a Sincronização de arquivos do Azure. Consulte [Interoperabilidade com o Windows Server](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability) para obter mais informações sobre versões com suporte do Windows Server.
* O módulo AZ PowerShell pode ser usado com o PowerShell 5,1 ou o PowerShell 6 +. Você pode usar o módulo AZ PowerShell para Sincronização de Arquivos do Azure em qualquer sistema com suporte, incluindo sistemas não Windows, no entanto, o cmdlet de registro do servidor sempre deve ser executado na instância do Windows Server que você está registrando (isso pode ser feito diretamente ou por meio do PowerShell comunicação remota). No Windows Server 2012 R2, você pode verificar se está executando pelo menos o PowerShell 5,1. examinando o valor da propriedade **psversion** do objeto $psversiontable:  \*

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Se o valor de PSVersion for menor que 5.1. \*, como será o caso com a maioria das novas instalações do Windows Server 2012 R2, será possível facilmente fazer upgrade, baixando e instalando o [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). O pacote apropriado para baixar e instalar o Windows Server 2012 R2 é **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**. 

    O PowerShell 6 + pode ser usado com qualquer sistema com suporte e pode ser baixado por meio de sua [página do GitHub](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Se você planeja usar a interface do usuário de registro do servidor, em vez de registrá-la diretamente do PowerShell, você deve usar o PowerShell 5,1.

* Se você tiver optado por usar o PowerShell 5,1, verifique se pelo menos o .NET 4.7.2 está instalado. Saiba mais sobre [as versões do .NET Framework e as dependências](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) em seu sistema.

    > [!Important]  
    > Se você estiver instalando o .NET 4.7.2 + no Windows Server Core, será necessário instalar `quiet` o `norestart` com os sinalizadores e ou a instalação falhará. Por exemplo, se estiver instalando o .NET 4,8, o comando seria semelhante ao seguinte:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* O módulo AZ PowerShell, que pode ser instalado seguindo as instruções aqui: [Instale e configure o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > O módulo AZ. StorageSync agora é instalado automaticamente quando você instala o módulo AZ PowerShell.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Preparar Servidores Windows para uso com Sincronização de arquivos do Azure
Para cada servidor que você pretende usar com a Sincronização de Arquivos do Azure, incluindo cada nó de servidor em um Cluster de Failover, desabilite a **Configuração de Segurança Reforçada do Internet Explorer**. Isso é necessário apenas para o registro inicial do servidor. Você pode habilitá-la novamente depois que o servidor foi registrado.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Você pode ignorar esta etapa se estiver implantando Sincronização de Arquivos do Azure no Windows Server Core.

1. Abra o Gerenciador de Servidor.
2. Clique em **Servidor Local**:  
    !["Servidor Local" no lado esquerdo da interface do usuário do Gerenciador do Servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. No subpainel **Propriedades**, selecione o link de **Configuração de Segurança Reforçada do IE**.  
    ![O painel "Configuração de Segurança Reforçada do IE" na interface do usuário do Gerenciador do Servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Na caixa de diálogo **Configuração de Segurança Reforçada do Internet Explorer**, selecione **Desativado** para **Administradores** e **Usuários**:  
    ![A janela pop-up Configuração de Segurança Reforçada do Internet Explorer com "Desativado" selecionado](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Para desabilitar a Configuração de Segurança Reforçada do Internet Explorer, execute o seguinte em uma sessão do PowerShell elevada:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

---

## <a name="deploy-the-storage-sync-service"></a>Implantar o Serviço de Sincronização de Armazenamento 
A implantação da Sincronização de Arquivos do Azure começa com a colocação de um recurso do **Serviço de Sincronização de Armazenamento** em um grupo de recursos da assinatura selecionada. É recomendável provisionar alguns desse, conforme necessário. Você criará uma relação de confiança entre os servidores e esse recurso e um servidor somente poderá ser registrado em um Serviço de Sincronização de Armazenamento. Como resultado, é recomendável implantar quantos serviços de sincronização de armazenamento forem necessários para separar grupos de servidores. Tenha em mente que os servidores de diferentes serviços de sincronização de armazenamento não podem sincronizar entre si.

> [!Note]
> O Serviço de Sincronização de Armazenamento herdou as permissões de acesso da assinatura e do grupo de recursos em que foi implantado. É recomendável verificar cuidadosamente quem tem acesso a ele. Entidades com acesso de gravação podem começar a sincronizar novos conjuntos de arquivos de servidores registrados nesse serviço de sincronização de armazenamento e fazer com que os dados fluam para o armazenamento do Azure que está acessível para elas.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Para implantar um serviço de sincronização de armazenamento, vá para o [portal do Azure](https://portal.azure.com/), clique em *criar um recurso* e procure por sincronização de arquivos do Azure. Nos resultados da pesquisa, selecione **Sincronização de Arquivos do Azure** e então **Criar** para abrir a guia **Implantar Sincronização de Armazenamento**.

No novo painel que será aberta, insira as seguintes informações:

- **Nome**: Um nome exclusivo (por assinatura) para o Serviço de Sincronização de Armazenamento.
- **Assinatura**: A assinatura na qual você deseja criar o Serviço de Sincronização de Armazenamento. Dependendo da estratégia de configuração da sua empresa, você pode ter acesso a uma ou mais assinaturas. Uma Assinatura do Azure é o contêiner mais básico de cobrança para cada serviço de nuvem (como Arquivos do Azure).
- **Grupo de recursos**: Um grupo de recursos é um grupo lógico de recursos do Azure, como uma conta de armazenamento ou um Serviço de Sincronização de Armazenamento. Você pode criar um novo grupo de recursos ou usar um grupo de recursos existente para a Sincronização de arquivos do Azure. (Recomendamos o uso de grupos de recursos como contêineres para isolar logicamente recursos para sua organização, como agrupamento de recursos para um projeto específico ou HR.)
- **Localização**: A região na qual você deseja implantar a Sincronização de Arquivos do Azure. Somente as regiões com suporte estão disponíveis nessa lista.

Quando terminar, selecione **Criar** para implantar o Serviço De Sincronização De Armazenamento.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Substitua **< Az_Region >** , **< RG_Name >** e **< my_storage_sync_service >** pelos seus próprios valores e, em seguida, use o CMDS a seguir para criar e implantar um serviço de sincronização de armazenamento:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

---

## <a name="install-the-azure-file-sync-agent"></a>Instalar o agente de Sincronização de Arquivo do Azure
O agente de Sincronização de arquivos do Azure é um pacote baixável que permite que o Windows Server seja sincronizado com um compartilhamento de arquivos do Azure. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Você pode baixar o agente do [Centro de Download da Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Após fazer o download, clique duas vezes no pacote MSI para iniciar a instalação do agente de Sincronização de arquivos do Azure.

> [!Important]  
> Se você pretende usar a Sincronização de arquivos do Azure com um Cluster de Failover, o agente de Sincronização de Arquivo do Azure precisa ser instalado em cada nó no cluster. Cada nó no cluster devem ser registrados para trabalhar com a sincronização de arquivos do Azure.

Recomendamos que você faça o seguinte:
- Deixe o caminho de instalação padrão (C:\Program Files\Azure\StorageSyncAgent), para simplificar a manutenção do servidor e solução de problemas.
- Habilite o Microsoft Update para manter a Sincronização de arquivos do Azure atualizada. Todas as atualizações, incluindo hotfixes e atualizações de recursos, para o agente de Sincronização de arquivos do Azure, ocorrerão por meio do Microsoft Update. É sempre recomendável fazer a atualização mais recente para a Sincronização de arquivos do Azure. Para mais informações, consulte a [Política de atualização da Sincronização de arquivos do Azure](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Quando a instalação do agente de Sincronização de arquivos do Azure tiver acabado, a interface do usuário de Registro do Servidor abrirá automaticamente. Para fazer o registro é necessário que haja um Serviço de Sincronização de Armazenamento. Veja como criar um Serviço de Sincronização de Armazenamento na próxima seção.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Execute o código a seguir do PowerShell para baixar a versão apropriada do agente da Sincronização de Arquivos do Azure para o sistema operacional e instalar no sistema.

> [!Important]  
> Se você pretende usar a Sincronização de arquivos do Azure com um Cluster de Failover, o agente de Sincronização de Arquivo do Azure precisa ser instalado em cada nó no cluster. Cada nó no cluster devem ser registrados para trabalhar com a sincronização de arquivos do Azure.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registrar o Windows Server com o Serviço de Sincronização de Armazenamento
Registrar o Windows Server com um Serviço de Sincronização de Armazenamento estabelece uma relação de confiança entre o servidor (ou cluster) e o Serviço de Sincronização de Armazenamento. Um servidor somente pode ser registrado em um Serviço de Sincronização de Armazenamento e sincronizar com outros servidores e compartilhamentos de arquivos do Azure associados ao mesmo Serviço de Sincronização de Armazenamento.

> [!Note]
> O registro do servidor usa suas credenciais do Azure para criar uma relação de confiança entre o Serviço de Sincronização de Armazenamento e o Windows Server, mas o servidor cria e usa sua própria identidade, desde que o servidor permaneça registrado e o token de assinatura de acesso compartilhado (Armazenamento SAS) é válido. Um novo token de SAS não poderá ser emitido para o servidor depois que o servidor for cancelado, removendo, assim, a capacidade do servidor de acessar os compartilhamentos de arquivos do Azure, interrompendo qualquer sincronização.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
A interface do usuário de Registro do Servidor deve abrir automaticamente após a instalação do agente de Sincronização de arquivos do Azure. Caso contrário, abra-a manualmente na localização do arquivo: C:\Arquivos de Programas\Azure\StorageSyncAgent\ServerRegistration.exe. Quando a interface do usuário de Registro do Servidor abrir, clique em **Entrar** para começar.

Depois de entrar, você deverá fornecer as seguintes informações:

![Uma captura de tela da interface do usuário de Registro do Servidor](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Assinatura do Azure**: A assinatura que contém o Serviço de Sincronização de Armazenamento (confira [Implantar o Serviço de Sincronização de Armazenamento](#deploy-the-storage-sync-service)). 
- **Grupo de recursos**: O grupo de recursos que contém o Serviço de Sincronização de Armazenamento.
- **Serviço de Sincronização de Armazenamento**: O nome do Serviço de Sincronização de Armazenamento com o qual você deseja se registrar.

Depois de selecionar as informações apropriadas nos menus suspensos, clique em **Registrar** para concluir o registro do servidor. Como parte do processo de registro, você será solicitado a realizar uma entrada adicional.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Criar um grupo de sincronização e um ponto de extremidade de nuvem
Um grupo de sincronização define a topologia de sincronização para um conjunto de arquivos. Os pontos de extremidade em um grupo de sincronização são mantidos em sincronização entre si. Um grupo de sincronização precisa conter um ponto de extremidade de nuvem que represente um compartilhamento de arquivos do Azure e um ou mais pontos de extremidade do servidor. Um ponto de extremidade do servidor representa um caminho no servidor registrado. Um servidor pode ter pontos de extremidade do servidor em vários grupos de sincronização. É necessário criar tantos grupos de sincronização quantos você precisar para descrever adequadamente sua topologia de sincronização desejada.

Um ponto de extremidade de nuvem é um ponteiro para um compartilhamento de arquivos do Azure. Todos os pontos de extremidade do servidor serão sincronizados com um ponto de extremidade de nuvem, tornando o ponto de extremidade de nuvem o hub. A conta de armazenamento do compartilhamento de arquivos do Azure deve estar localizada na mesma região que o Serviço de Sincronização de Armazenamento. Todo o compartilhamento de arquivos do Azure será sincronizado, com uma exceção: Uma pasta especial, comparável à pasta oculta "System Volume Information" em um volume NTFS, será provisionada. Este diretório é chamado ".SystemShareInformation". Ele contém importantes metadados de sincronização que não serão sincronizados com outros pontos de extremidade. Não utilize nem exclua-o!

> [!Important]  
> Você pode fazer alterações a qualquer Ponto de extremidade de Nuvem ou de Servidor no Grupo de sincronização e ter seus arquivos sincronizados com os outros pontos de extremidade no Grupo de sincronização. Se você fizer uma alteração diretamente no Ponto de extremidade de nuvem (compartilhamento de Arquivos do Azure), observe que as alterações devem primeiro ser descobertas por um trabalho de detecção de alteração de sincronização de arquivos do Azure. Um trabalho de detecção de alteração é iniciado para um ponto de extremidade da nuvem apenas uma vez a cada 24 horas. Para obter mais informações, consulte [Perguntas frequentes sobre os Arquivos do Azure](storage-files-faq.md#afs-change-detection).

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Para criar um Grupo de Sincronização, navegue até o Serviço de Sincronização de Armazenamento no [Portal do Azure](https://portal.azure.com/) e clique em **+ Grupo de sincronização**:

![Criar um novo Grupo de Sincronização no Portal do Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

No painel que abrir, insira as informações a seguir para criar um Grupo de Sincronização com um ponto de extremidade de nuvem:

- **Nome do grupo de sincronização**: O nome do grupo de sincronização a ser criado. Esse nome deve ser exclusivo dentro do Serviço de Sincronização de Armazenamento, mas pode ser qualquer nome lógico para você.
- **Assinatura**: A assinatura na qual você implantou o Serviço de Sincronização de Armazenamento em [Implantar o Serviço de Sincronização de Armazenamento](#deploy-the-storage-sync-service).
- **Conta de armazenamento**: Se você escolher **Selecionar conta de armazenamento**, outro painel será exibido, no qual você poderá selecionar a conta de armazenamento que tem o compartilhamento de arquivos do Azure com o qual você deseja fazer a sincronização.
- **Compartilhamento de arquivos do Azure**: O nome do compartilhamento de arquivos do Azure com o qual você deseja sincronizar.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Para criar o grupo de sincronização, execute o PowerShell a seguir. Lembre-se de substituir `<my-sync-group>` com o nome desejado do grupo de sincronização.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Depois que o grupo de sincronização tiver sido criado com êxito, será possível criar o ponto de extremidade de nuvem. Certifique-se de substituir `<my-storage-account>` e `<my-file-share>` pelos valores esperados.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Criar um ponto de extremidade do servidor
Um ponto de extremidade do servidor representa um local específico em um servidor registrado, como uma pasta em um volume do servidor. Um ponto de extremidade do servidor deve ser um caminho em um servidor registrado (em vez de um compartilhamento montado) e, para usar a classificação em camadas de nuvem, o caminho deve estar em um volume que não seja do sistema. Não há suporte para Armazenamento NAS (Network Attached Storage).

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Para adicionar um ponto de extremidade do servidor, navegue até o Grupo de Sincronização recém-criado e clique em **Adicionar ponto de extremidade do servidor**.

![Adicionar um novo ponto de extremidade do servidor no painel Grupo de Sincronização](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

No painel **Adicionar ponto de extremidade do servidor**, insira as seguintes informações para criar um ponto de extremidade do servidor:

- **Servidor registrado**: O nome do servidor ou cluster no qual você deseja criar o ponto de extremidade de servidor.
- **Caminho**: O caminho do Windows Server a ser sincronizado como parte do grupo de sincronização.
- **Camada de nuvem**: Uma opção para habilitar ou desabilitar a camada de nuvem. Com nuvem em camadas, arquivos raramente usados ou acessados de nuvem podem ter várias camada para arquivos do Azure.
- **Espaço Livre no Volume**: A quantidade de espaço livre a ser reservada no volume, no qual o ponto de extremidade de servidor está localizado. Por exemplo, se o espaço livre do volume estiver definido como 50% em um volume com um único ponto de extremidade do servidor, aproximadamente metade da quantidade de dados será disposta em camadas para os Arquivos do Azure. Independentemente de as camadas na nuvem estarem habilitadas, o Compartilhamento de Arquivos do Azure sempre terá uma cópia completa dos dados no Grupo de Sincronização.

Selecione **Criar** para adicionar o ponto de extremidade do servidor. Os arquivos agora serão mantidos em sincronia entre o Compartilhamento de Arquivos do Azure e o Windows Server. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Execute os comandos a seguir do PowerShell para criar o ponto de extremidade do servidor e substitua `<your-server-endpoint-path>` e `<your-volume-free-space>` pelos valores desejados.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Definir configurações de rede virtual e de firewall

### <a name="portal"></a>Portal
Se você quiser configurar a sincronização de arquivos do Azure para trabalhar com as configurações de firewall e rede virtual, faça o seguinte:

1. No portal do Azure, navegue até a conta de armazenamento que você deseja proteger.
1. Selecione o botão **firewalls e redes virtuais** no menu à esquerda.
1. Selecione **redes selecionadas** em **permitir acesso de**.
1. Verifique se os servidores IP ou rede virtual estão listados na seção apropriada.
1. Certifique-se **de que permitir que serviços da Microsoft confiáveis acessem esta conta de armazenamento** esteja marcado.
1. Selecione **salvar** para salvar suas configurações.

![Definindo configurações de rede virtual e de firewall para trabalhar com a sincronização de arquivos do Azure](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Integração com a Sincronização de arquivos do Azure
As etapas recomendadas para se integrar à Sincronização de arquivos do Azure pela primeira vez com zero tempo de inatividade e ainda preservar a fidelidade do arquivo completo e a lista de controle de acesso (ACL) são as seguintes:
 
1. Implantar um Serviço de sincronização de armazenamento.
2. Criar um grupo de sincronização.
3. Instalar o agente de Sincronização de arquivos do Azure no servidor com o conjunto de dados completo.
4. Registrar esse servidor e criar um ponto de extremidade do servidor no compartilhamento. 
5. Permitir que a sincronização faça o upload completo ao compartilhamento de arquivos do Azure (ponto de extremidade de nuvem).  
6. Após a conclusão do upload inicial, instalar o agente de Sincronização de arquivos do Azure em cada um dos servidores restantes.
7. Criar novos compartilhamentos de arquivo em cada um dos servidores restantes.
8. Criar pontos de extremidade do servidor em novos compartilhamentos de arquivo com a política de camadas de nuvem, se desejado. (Essa etapa requer que haja armazenamento adicional disponível para a configuração inicial.)
9. Permitir que o agente de Sincronização de arquivos do Azure faça uma restauração rápida do namespace completo sem a transferência de dados reais. Após a sincronização completa de namespace, o mecanismo de sincronização preencherá o espaço de disco local com base na política de camadas de nuvem do ponto de extremidade do servidor. 
10. Certifique-se de que a sincronização seja concluída e teste a sua topologia conforme desejado. 
11. Redirecionar usuários e aplicativos para esse novo compartilhamento.
12. Outra opção é excluir compartilhamentos duplicados nos servidores.
 
Caso não tenha armazenamento extra para a integração inicial e gostaria de anexar aos compartilhamentos existentes, você pode pré-propagar os dados nos compartilhamentos de arquivos do Azure. Essa abordagem é sugerida se e somente se você puder aceitar o tempo de inatividade e não garante absolutamente nenhuma alteração de dados em compartilhamentos do servidor durante o processo de migração inicial. 
 
1. Verifique se os dados em qualquer um dos servidores não podem ser alterados durante o processo de integração.
2. Pré-propagar compartilhamentos de arquivos do Azure com os dados do servidor usando qualquer ferramenta de transferência de dados no SMB, por exemplo, Robocopy, Direct SMB Copy. Como o AzCopy não baixa dados pelo SMB, ele não pode ser usado para pré-propagação.
3. Crie a topologia da Sincronização de arquivos do Azure com os pontos de extremidade desejados do servidor apontando para os compartilhamentos existentes.
4. Deixe a sincronização concluir o processo de reconciliação em todos os pontos de extremidade. 
5. Assim que a reconciliação for concluída, você pode abrir compartilhamentos para alterações.
 
Atualmente, a abordagem de pré-propagação tem algumas limitações – 
- A fidelidade total nos arquivos não é preservada. Por exemplo, arquivos perdem ACLs e carimbo de data/hora.
- Alterações de dados no servidor antes de a topologia de sincronização estar totalmente ativa e em execução podem causar conflitos nos pontos de extremidade do servidor.  
- Depois que o ponto de extremidade da nuvem for criado, a Sincronização de arquivos do Azure executa um processo para detectar os arquivos na nuvem antes de iniciar a sincronização inicial. O tempo necessário para concluir esse processo varia dependendo de vários fatores, como velocidade da rede, largura de banda disponível e quantidade de arquivos e pastas. Para obter uma estimativa aproximada na versão prévia, o processo de detecção é executado a aproximadamente 10 arquivos por segundo. Portanto, mesmo se a pré-propagação for executada rapidamente, o tempo geral para obter um sistema totalmente em execução pode ser significativamente maior quando os dados forem pré-propagados na nuvem.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrar uma implantação de replicação do DFS (DFS-R) para sincronização de arquivos do Azure
Para migrar uma implantação de DFS-R para sincronização de arquivos do Azure:

1. Crie um grupo de sincronização para representar a topologia de DFS-R que você estiver substituindo.
2. Inicialize no servidor que tiver o conjunto completo de dados em sua topologia de DFS-R para migrar. Instale a sincronização de arquivos do Azure nesse servidor.
3. Registre o servidor e crie um ponto de extremidade do servidor para o primeiro servidor a ser migrado. Não habilite a camada de nuvem.
4. Permita que todos os dados se sincronizem ao seu compartilhamento de arquivos do Azure (ponto de extremidade de nuvem).
5. Instale e registre o agente de sincronização de arquivos do Azure em cada um dos servidores restantes do DFS-R.
6. Desabilite o DFS-R. 
7. Crie um ponto de extremidade do servidor em cada um dos servidores do DFS-R. Não habilite a camada de nuvem.
8. Certifique-se de que a sincronização seja concluída e teste a sua topologia conforme desejado.
9. Desative o DFS-R.
10. Agora, a camada de nuvem pode ser ativada em qualquer ponto de extremidade do servidor, conforme desejado.

Para obter mais informações, consulte [Interoperabilidade de sincronização de arquivos do Azure com o sistema de arquivos distribuído (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Próximas etapas
- [Adicionar/remover um ponto de extremidade do Servidor de Sincronização de arquivos do Azure](storage-sync-files-server-endpoint.md)
- [Registrar/cancelar o registro de um servidor com a Sincronização de arquivos do Azure](storage-sync-files-server-registration.md)
- [Monitorar a Sincronização de Arquivos do Azure](storage-sync-files-monitoring.md)
