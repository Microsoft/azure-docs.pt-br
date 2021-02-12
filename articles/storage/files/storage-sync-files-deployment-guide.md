---
title: Implantar a Sincronização de Arquivos do Azure | Microsoft Docs
description: Saiba como implantar Sincronização de Arquivos do Azure, do início ao fim, usando o portal do Azure, o PowerShell ou o CLI do Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4814a12a870d5317ad91c3514327ba0daad7ed69
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225363"
---
# <a name="deploy-azure-file-sync"></a>Implantar a Sincronização de Arquivos do Azure
Use a Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização em Arquivos do Azure enquanto mantém a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. A Sincronização de arquivos do Azure transforma o Windows Server em um cache rápido do compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter tantos caches quantos precisar em todo o mundo.

É altamente recomendável que você leia [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md) e [Planejando uma implantação de Sincronização de arquivos do Azure](storage-sync-files-planning.md) antes de completar as etapas descritas neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Um compartilhamento de arquivos do Azure na mesma região que você deseja implantar Sincronização de Arquivos do Azure. Para obter mais informações, consulte:
    - [Disponibilidade de região](storage-sync-files-planning.md#azure-file-sync-region-availability) para Sincronização de arquivos do Azure.
    - Para orientações passo a passo sobre como criar uma conta de compartilhamento, consulte [Criar uma conta de compartilhamento](storage-how-to-create-file-share.md).
1. Pelo menos uma instância do Windows Server ou cluster do Windows Server com suporte para sincronização com o Sincronização de Arquivos do Azure. Para obter mais informações sobre as versões com suporte do Windows Server e os recursos de sistema recomendados, consulte [Considerações sobre o servidor de arquivos do Windows](storage-sync-files-planning.md#windows-file-server-considerations).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Um compartilhamento de arquivos do Azure na mesma região que você deseja implantar Sincronização de Arquivos do Azure. Para obter mais informações, consulte:
    - [Disponibilidade de região](storage-sync-files-planning.md#azure-file-sync-region-availability) para Sincronização de arquivos do Azure.
    - Para orientações passo a passo sobre como criar uma conta de compartilhamento, consulte [Criar uma conta de compartilhamento](storage-how-to-create-file-share.md).
1. Pelo menos uma instância do Windows Server ou cluster do Windows Server com suporte para sincronização com o Sincronização de Arquivos do Azure. Para obter mais informações sobre as versões com suporte do Windows Server e os recursos de sistema recomendados, consulte [Considerações sobre o servidor de arquivos do Windows](storage-sync-files-planning.md#windows-file-server-considerations).

1. O módulo AZ PowerShell pode ser usado com o PowerShell 5,1 ou o PowerShell 6 +. Você pode usar o módulo AZ PowerShell para Sincronização de Arquivos do Azure em qualquer sistema com suporte, incluindo sistemas não Windows, no entanto, o cmdlet de registro do servidor sempre deve ser executado na instância do Windows Server que você está registrando (isso pode ser feito diretamente ou por meio de comunicação remota do PowerShell). No Windows Server 2012 R2, você pode verificar se está executando pelo menos o PowerShell 5,1. \* examinando o valor da propriedade **psversion** do objeto **$psversiontable** :

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Se o valor de **psversion** for menor que 5,1. \* , como será o caso com a maioria das instalações mais recentes do Windows Server 2012 R2, você poderá atualizar facilmente baixando e instalando o [Windows Management Framework (WMF) 5,1](https://www.microsoft.com/download/details.aspx?id=54616). O pacote apropriado para baixar e instalar o Windows Server 2012 R2 é **win 8.1 andw2k12r2-KB \* \* \* \* \* \* \* -x64. msu**. 

    O PowerShell 6 + pode ser usado com qualquer sistema com suporte e pode ser baixado por meio de sua [página do GitHub](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Se você planeja usar a interface do usuário de registro do servidor, em vez de registrá-la diretamente do PowerShell, você deve usar o PowerShell 5,1.

1. Se você tiver optado por usar o PowerShell 5,1, verifique se pelo menos o .NET 4.7.2 está instalado. Saiba mais sobre [as versões do .NET Framework e as dependências](/dotnet/framework/migration-guide/versions-and-dependencies) em seu sistema.

    > [!Important]  
    > Se você estiver instalando o .NET 4.7.2 + no Windows Server Core, será necessário instalar o com os `quiet` `norestart` sinalizadores e ou a instalação falhará. Por exemplo, se estiver instalando o .NET 4,8, o comando seria semelhante ao seguinte:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. O módulo AZ PowerShell, que pode ser instalado seguindo as instruções aqui: [instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > O módulo AZ. StorageSync agora é instalado automaticamente quando você instala o módulo AZ PowerShell.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Um compartilhamento de arquivos do Azure na mesma região que você deseja implantar Sincronização de Arquivos do Azure. Para obter mais informações, consulte:
    - [Disponibilidade de região](storage-sync-files-planning.md#azure-file-sync-region-availability) para Sincronização de arquivos do Azure.
    - Para orientações passo a passo sobre como criar uma conta de compartilhamento, consulte [Criar uma conta de compartilhamento](storage-how-to-create-file-share.md).
1. Pelo menos uma instância do Windows Server ou cluster do Windows Server com suporte para sincronização com o Sincronização de Arquivos do Azure. Para obter mais informações sobre as versões com suporte do Windows Server e os recursos de sistema recomendados, consulte [Considerações sobre o servidor de arquivos do Windows](storage-sync-files-planning.md#windows-file-server-considerations).

1. [Instalar a CLI do Azure](/cli/azure/install-azure-cli)

   Se preferir, você também pode usar o Azure Cloud Shell para concluir as etapas deste tutorial.  O Azure Cloud Shell é um ambiente de shell interativo usado por meio do navegador.  Inicie o Cloud Shell usando um destes métodos:

   - Selecione **Experimente** no canto superior direito de um bloco de código. **Tente** abrir Azure cloud Shell, mas ele não copiará o código automaticamente para Cloud Shell.

   - Abra o Cloud Shell acessando [https://shell.azure.com](https://shell.azure.com)

   - Selecione o botão **Cloud Shell** na barra de menus, no canto superior direito do [portal do Azure](https://portal.azure.com)

1. Entrar.

   Entre usando o comando [az login](/cli/azure/reference-index#az-login) se estiver usando uma instalação local da CLI.

   ```azurecli
   az login
   ```

    Siga as etapas exibidas em nosso terminal para concluir o processo de autenticação.

1. Instale a extensão [AZ filesync](/cli/azure/ext/storagesync/storagesync) CLI do Azure.

   ```azurecli
   az extension add --name storagesync
   ```

   Depois de instalar a referência de extensão **storagesync** , você receberá o seguinte aviso.

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Preparar Servidores Windows para uso com Sincronização de arquivos do Azure
Para cada servidor que você pretende usar com a Sincronização de Arquivos do Azure, incluindo cada nó de servidor em um Cluster de Failover, desabilite a **Configuração de Segurança Reforçada do Internet Explorer**. Isso é necessário apenas para o registro inicial do servidor. Você pode habilitá-la novamente depois que o servidor foi registrado.

# <a name="portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Você pode ignorar esta etapa se estiver implantando Sincronização de Arquivos do Azure no Windows Server Core.

1. Abra o Gerenciador de Servidor.
2. Clique em **Servidor Local**:  
    !["Servidor Local" no lado esquerdo da interface do usuário do Gerenciador do Servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. No subpainel **Propriedades**, selecione o link de **Configuração de Segurança Reforçada do IE**.  
    ![O painel "Configuração de Segurança Reforçada do IE" na interface do usuário do Gerenciador do Servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Na caixa de diálogo **configuração de segurança reforçada do Internet Explorer** , selecione **desativado** para **Administradores** e **usuários**:  
    ![A janela pop-up Configuração de Segurança Reforçada do Internet Explorer com "Desativado" selecionado](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Siga as instruções para o portal do Azure ou o PowerShell.

---

## <a name="deploy-the-storage-sync-service"></a>Implantar o Serviço de Sincronização de Armazenamento 
A implantação da Sincronização de Arquivos do Azure começa com a colocação de um recurso do **Serviço de Sincronização de Armazenamento** em um grupo de recursos da assinatura selecionada. É recomendável provisionar alguns desse, conforme necessário. Você criará uma relação de confiança entre os servidores e esse recurso e um servidor somente poderá ser registrado em um Serviço de Sincronização de Armazenamento. Como resultado, é recomendável implantar quantos serviços de sincronização de armazenamento forem necessários para separar grupos de servidores. Tenha em mente que os servidores de diferentes serviços de sincronização de armazenamento não podem sincronizar entre si.

> [!Note]
> O serviço de sincronização de armazenamento herda as permissões de acesso da assinatura e do grupo de recursos em que foi implantado. É recomendável verificar cuidadosamente quem tem acesso a ele. Entidades com acesso de gravação podem começar a sincronizar novos conjuntos de arquivos de servidores registrados nesse serviço de sincronização de armazenamento e fazer com que os dados fluam para o armazenamento do Azure que está acessível para elas.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para implantar um serviço de sincronização de armazenamento, vá para o [portal do Azure](https://portal.azure.com/), clique em *criar um recurso* e procure por sincronização de arquivos do Azure. Nos resultados da pesquisa, selecione **sincronização de arquivos do Azure** e, em seguida, selecione **criar** para abrir a guia **implantar sincronização de armazenamento** .

No novo painel que será aberta, insira as seguintes informações:

- **Nome**: um nome exclusivo (por região) para o serviço de sincronização de armazenamento.
- **Assinatura**: A assinatura na qual você quer criar o Serviço de Sincronização de Armazenamento. Dependendo da estratégia de configuração da sua empresa, você pode ter acesso a uma ou mais assinaturas. Uma Assinatura do Azure é o contêiner mais básico de cobrança para cada serviço de nuvem (como Arquivos do Azure).
- **Grupo de recursos**: um grupo de recursos é um grupo lógico de recursos do Azure, como uma conta de armazenamento ou um Serviço de Sincronização de Armazenamento. Você pode criar um novo grupo de recursos ou usar um grupo de recursos existente para Sincronização de Arquivos do Azure. (É recomendável usar grupos de recursos como contêineres para isolar recursos logicamente para sua organização, como agrupamento de recursos ou recursos de RH para um projeto específico.)
- **Local**: a região na qual você deseja implantar sincronização de arquivos do Azure. Somente regiões com suporte estão disponíveis nesta lista.

Quando terminar, selecione **Criar** para implantar o Serviço De Sincronização De Armazenamento.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Substitua `<Az_Region>` , `<RG_Name>` e `<my_storage_sync_service>` pelos seus próprios valores e, em seguida, use os seguintes comandos para criar e implantar um serviço de sincronização de armazenamento:

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Siga as instruções para o portal do Azure ou o PowerShell.

---

## <a name="install-the-azure-file-sync-agent"></a>Instalar o agente de Sincronização de Arquivo do Azure
O agente de Sincronização de arquivos do Azure é um pacote baixável que permite que o Windows Server seja sincronizado com um compartilhamento de arquivos do Azure. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Você pode baixar o agente do [Centro de Download da Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Após fazer o download, clique duas vezes no pacote MSI para iniciar a instalação do agente de Sincronização de arquivos do Azure.

> [!Important]  
> Se você pretende usar a Sincronização de arquivos do Azure com um Cluster de Failover, o agente de Sincronização de Arquivo do Azure precisa ser instalado em cada nó no cluster. Cada nó do cluster deve ser registrado para trabalhar com Sincronização de Arquivos do Azure.

Recomendamos que você faça o seguinte:
- Deixe o caminho de instalação padrão (C:\Program Files\Azure\StorageSyncAgent), para simplificar a manutenção do servidor e solução de problemas.
- Habilite o Microsoft Update para manter a Sincronização de arquivos do Azure atualizada. Todas as atualizações, incluindo hotfixes e atualizações de recursos, para o agente de Sincronização de arquivos do Azure, ocorrerão por meio do Microsoft Update. É recomendável instalar a atualização mais recente para Sincronização de Arquivos do Azure. Para obter mais informações, consulte [sincronização de arquivos do Azure Update Policy](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Quando a instalação do agente de Sincronização de arquivos do Azure tiver acabado, a interface do usuário de Registro do Servidor abrirá automaticamente. Para fazer o registro é necessário que haja um Serviço de Sincronização de Armazenamento. Veja como criar um Serviço de Sincronização de Armazenamento na próxima seção.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
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
# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Siga as instruções para o portal do Azure ou o PowerShell.

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registrar o Windows Server com o Serviço de Sincronização de Armazenamento
Registrar o Windows Server com um Serviço de Sincronização de Armazenamento estabelece uma relação de confiança entre o servidor (ou cluster) e o Serviço de Sincronização de Armazenamento. Um servidor somente pode ser registrado em um Serviço de Sincronização de Armazenamento e sincronizar com outros servidores e compartilhamentos de arquivos do Azure associados ao mesmo Serviço de Sincronização de Armazenamento.

> [!Note]
> O registro do servidor usa suas credenciais do Azure para criar uma relação de confiança entre o Serviço de Sincronização de Armazenamento e o Windows Server, mas o servidor cria e usa sua própria identidade, desde que o servidor permaneça registrado e o token de assinatura de acesso compartilhado (Armazenamento SAS) é válido. Um novo token de SAS não poderá ser emitido para o servidor depois que o servidor for cancelado, removendo, assim, a capacidade do servidor de acessar os compartilhamentos de arquivos do Azure, interrompendo qualquer sincronização.

O administrador que registra o servidor deve ser membro do **proprietário** ou **colaborador** das funções de gerenciamento para o serviço de sincronização de armazenamento fornecido. Isso pode ser configurado no **controle de acesso (iam)** no portal do Azure para o serviço de sincronização de armazenamento.

Também é possível diferenciar os administradores capazes de registrar servidores daqueles com permissão para também configurar a sincronização em um serviço de sincronização de armazenamento. Para isso, você precisaria criar uma função personalizada na qual lista os administradores que só têm permissão para registrar servidores e dar à função personalizada as seguintes permissões:

* "Microsoft. StorageSync/storageSyncServices/registeredServers/Write"
* "Microsoft. StorageSync/storageSyncServices/Read"
* "Microsoft. StorageSync/storageSyncServices/fluxos de trabalho/leitura"
* "Microsoft. StorageSync/storageSyncServices/fluxos de trabalho/operações/leitura"

# <a name="portal"></a>[Portal](#tab/azure-portal)
A interface do usuário de Registro do Servidor deve abrir automaticamente após a instalação do agente de Sincronização de arquivos do Azure. Se não estiver, você pode abri-lo manualmente de seu local de arquivo: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Quando a interface do usuário de Registro do Servidor abrir, clique em **Entrar** para começar.

Depois de entrar, você deverá fornecer as seguintes informações:

![Uma captura de tela da interface do usuário de Registro do Servidor](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Assinatura do Azure**: a assinatura que contém o Serviço de Sincronização de Armazenamento (consulte [Implantar o Serviço de Sincronização de Armazenamento](#deploy-the-storage-sync-service)). 
- **Grupo de recursos**: o grupo de recursos que contém o Serviço de Sincronização de Armazenamento.
- **Serviço de Sincronização de Armazenamento**: o nome do Serviço de Sincronização de Armazenamento com o qual você deseja registrar.

Depois de selecionar as informações apropriadas nos menus suspensos, clique em **Registrar** para concluir o registro do servidor. Como parte do processo de registro, você será solicitado a realizar uma entrada adicional.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Siga as instruções para o portal do Azure ou o PowerShell.

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Criar um grupo de sincronização e um ponto de extremidade de nuvem
Um grupo de sincronização define a topologia de sincronização para um conjunto de arquivos. Os pontos de extremidade em um grupo de sincronização são mantidos em sincronização entre si. Um grupo de sincronização precisa conter um ponto de extremidade de nuvem que represente um compartilhamento de arquivos do Azure e um ou mais pontos de extremidade do servidor. Um ponto de extremidade do servidor representa um caminho em um servidor registrado. Um servidor pode ter pontos de extremidade do servidor em vários grupos de sincronização. É necessário criar tantos grupos de sincronização quantos você precisar para descrever adequadamente sua topologia de sincronização desejada.

Um ponto de extremidade de nuvem é um ponteiro para um compartilhamento de arquivos do Azure. Todos os pontos de extremidade do servidor serão sincronizados com um ponto de extremidade de nuvem, tornando o ponto de extremidade de nuvem o hub. A conta de armazenamento do compartilhamento de arquivos do Azure deve estar localizada na mesma região que o Serviço de Sincronização de Armazenamento. A totalidade do compartilhamento de arquivos do Azure será sincronizada, com uma exceção: uma pasta especial, comparável à pasta "Informações de Volume do Sistema" oculta em um volume NTFS, será provisionada. Este diretório é chamado ".SystemShareInformation". Ele contém importantes metadados de sincronização que não serão sincronizados com outros pontos de extremidade. Não utilize nem exclua-o!

> [!Important]  
> Você pode fazer alterações a qualquer Ponto de extremidade de Nuvem ou de Servidor no Grupo de sincronização e ter seus arquivos sincronizados com os outros pontos de extremidade no Grupo de sincronização. Se você fizer uma alteração diretamente no Ponto de extremidade de nuvem (compartilhamento de Arquivos do Azure), observe que as alterações devem primeiro ser descobertas por um trabalho de detecção de alteração de sincronização de arquivos do Azure. Um trabalho de detecção de alteração é iniciado para um ponto de extremidade da nuvem apenas uma vez a cada 24 horas. Para obter mais informações, consulte [Perguntas frequentes sobre os Arquivos do Azure](storage-files-faq.md#afs-change-detection).

O administrador que cria o ponto de extremidade de nuvem deve ser membro do **proprietário** da função de gerenciamento da conta de armazenamento que contém o compartilhamento de arquivos do Azure ao qual o ponto de extremidade de nuvem está apontando. Isso pode ser configurado no **controle de acesso (iam)** no portal do Azure para a conta de armazenamento.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar um grupo de sincronização, na [portal do Azure](https://portal.azure.com/), vá para o serviço de sincronização de armazenamento e, em seguida, selecione **+ grupo de sincronização**:

![Criar um novo Grupo de Sincronização no Portal do Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

No painel que abrir, insira as informações a seguir para criar um Grupo de Sincronização com um ponto de extremidade de nuvem:

- **Nome do grupo de sincronização**: o nome do grupo de sincronização a ser criado. Esse nome deve ser exclusivo dentro do Serviço de Sincronização de Armazenamento, mas pode ser qualquer nome lógico para você.
- **Assinatura**: a assinatura na qual você implantou o Serviço de Sincronização de Armazenamento em [Implantar o Serviço de Sincronização de Armazenamento](#deploy-the-storage-sync-service).
- **Conta de armazenamento**: se você selecionar **Selecionar conta de armazenamento**, outro painel aparece no qual você pode selecionar a conta de armazenamento que tenha o compartilhamento de arquivos do Azure que você deseja fazer a sincronização.
- **Compartilhamento de Arquivo do Azure**: o nome do Compartilhamento de Arquivo do Azure com o qual você deseja sincronizar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando [AZ storagesync Sync-Group](/cli/azure/ext/storagesync/storagesync/sync-group#ext-storagesync-az-storagesync-sync-group-create) para criar um novo grupo de sincronização.  Para padronizar um grupo de recursos para todos os comandos da CLI, use [AZ configure](/cli/azure/reference-index#az-configure).

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

Use o comando [AZ storagesync Sync-Group Cloud-Endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/cloud-endpoint#ext-storagesync-az-storagesync-sync-group-cloud-endpoint-create) para criar um novo ponto de extremidade de nuvem.

```azurecli
az storagesync sync-group cloud-endpoint create --resource-group myResourceGroup \
                                                --storage-sync-service myStorageSyncServiceName \
                                                --sync-group-name mySyncGroupName \
                                                --name myNewCloudEndpointName \
                                                --storage-account mystorageaccountname \
                                                --azure-file-share-name azure-file-share-name
```

---

## <a name="create-a-server-endpoint"></a>Criar um ponto de extremidade do servidor
Um ponto de extremidade do servidor representa um local específico em um servidor registrado, como uma pasta em um volume do servidor. Um ponto de extremidade do servidor deve ser um caminho em um servidor registrado (em vez de um compartilhamento montado) e, para usar a classificação em camadas de nuvem, o caminho deve estar em um volume que não seja do sistema. Não há suporte para Armazenamento NAS (Network Attached Storage).

> [!NOTE]
> Não há suporte para alterar o caminho ou a letra da unidade depois de estabelecer um ponto de extremidade do servidor em um volume. Verifique se você está usando um caminho final no servidor registrado.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para adicionar um ponto de extremidade do servidor, vá até o grupo de sincronização recém-criado e selecione **Adicionar ponto de extremidade do servidor**.

![Adicionar um novo ponto de extremidade do servidor no painel Grupo de Sincronização](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

No painel **Adicionar ponto de extremidade do servidor** , insira as informações a seguir para criar um ponto de extremidade do servidor:

- **Servidor registrado**: o nome do servidor ou cluster em que você deseja criar o ponto de extremidade do servidor.
- **Caminho**: o caminho do Windows Server a ser sincronizado como parte do grupo de sincronização.
- **Nuvem em camadas**: uma opção para habilitar ou desabilitar a nuvem camadas. Com nuvem em camadas, arquivos raramente usados ou acessados de nuvem podem ter várias camada para arquivos do Azure.
- **Espaço livre no volume**: a quantidade de espaço livre a ser reservada no volume no qual o ponto de extremidade do servidor está localizado. Por exemplo, se o espaço livre do volume estiver definido como 50% em um volume com um único ponto de extremidade do servidor, aproximadamente metade da quantidade de dados será disposta em camadas para os Arquivos do Azure. Independentemente de as camadas na nuvem estarem habilitadas, o Compartilhamento de Arquivos do Azure sempre terá uma cópia completa dos dados no Grupo de Sincronização.
- **Modo de download inicial**: essa é uma seleção opcional, começando com o Agent versão 11, que pode ser útil quando há arquivos no compartilhamento de arquivos do Azure, mas não no servidor. Essa situação pode existir, por exemplo, se você criar um ponto de extremidade do servidor para adicionar outro servidor de filial a um grupo de sincronização ou quando você recuperar um servidor com falha de desastre. Se a disposição em camadas da nuvem estiver habilitada, o padrão será apenas recuperar o namespace, nenhum conteúdo de arquivo inicialmente. Isso é útil se você acredita que, em vez disso, as solicitações de acesso do usuário devem decidir qual conteúdo de arquivo é rechamado para o servidor. Se a disposição em camadas da nuvem estiver desabilitada, o padrão é que o namespace será baixado primeiro e, em seguida, os arquivos serão rechamados com base no carimbo de data/hora da última modificação até que a capacidade local tenha sido atingida. No entanto, você pode alterar o modo de download inicial somente para namespace. Um terceiro modo só poderá ser usado se a disposição em camadas da nuvem estiver desabilitada para este ponto de extremidade do servidor. Esse modo evita a rechamada do namespace primeiro. Os arquivos só serão exibidos no servidor local se tiverem a oportunidade de baixar totalmente. Esse modo será útil se, por exemplo, um aplicativo exigir que os arquivos completos estejam presentes e não puder tolerar arquivos em camadas no namespace.

Para adicionar o ponto de extremidade do servidor, selecione **criar**. Os arquivos agora serão mantidos em sincronia entre o Compartilhamento de Arquivos do Azure e o Windows Server. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Execute os comandos do PowerShell a seguir para criar o ponto de extremidade do servidor e certifique-se de substituir `<your-server-endpoint-path>` `<your-volume-free-space>` e pelos valores desejados e verifique a configuração opcional para a política de download inicial opcional.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = NamespaceOnly

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
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando [AZ storagesync Sync-Group Server-Endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/server-endpoint#ext-storagesync-az-storagesync-sync-group-server-endpoint-create) para criar um novo ponto de extremidade do servidor.

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --offline-data-transfer on \
                                                 --offline-data-transfer-share-name myfilesharename \

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
As etapas recomendadas para a integração em Sincronização de Arquivos do Azure pela primeira vez sem tempo de inatividade, enquanto preserva a fidelidade de arquivo completo e a ACL (lista de controle de acesso) são as seguintes:
 
1. Implantar um Serviço de sincronização de armazenamento.
1. Criar um grupo de sincronização.
1. Instalar o agente de Sincronização de arquivos do Azure no servidor com o conjunto de dados completo.
1. Registrar esse servidor e criar um ponto de extremidade do servidor no compartilhamento. 
1. Permitir que a sincronização faça o upload completo ao compartilhamento de arquivos do Azure (ponto de extremidade de nuvem).  
1. Após a conclusão do upload inicial, instalar o agente de Sincronização de arquivos do Azure em cada um dos servidores restantes.
1. Criar novos compartilhamentos de arquivo em cada um dos servidores restantes.
1. Criar pontos de extremidade do servidor em novos compartilhamentos de arquivo com a política de camadas de nuvem, se desejado. (Essa etapa requer que haja armazenamento adicional disponível para a configuração inicial.)
1. Permitir que o Sincronização de Arquivos do Azure Agent faça uma restauração rápida do namespace completo sem a transferência de dados real. Após a sincronização completa de namespace, o mecanismo de sincronização preencherá o espaço de disco local com base na política de camadas de nuvem do ponto de extremidade do servidor. 
1. Certifique-se de que a sincronização seja concluída e teste a sua topologia conforme desejado. 
1. Redirecionar usuários e aplicativos para esse novo compartilhamento.
1. Outra opção é excluir compartilhamentos duplicados nos servidores.
 
Caso não tenha armazenamento extra para a integração inicial e gostaria de anexar aos compartilhamentos existentes, você pode pré-propagar os dados nos compartilhamentos de arquivos do Azure. Essa abordagem é sugerida se e somente se você puder aceitar o tempo de inatividade e não garante absolutamente nenhuma alteração de dados em compartilhamentos do servidor durante o processo de migração inicial. 
 
1. Verifique se os dados em qualquer um dos servidores não podem ser alterados durante o processo de integração.
1. Pré-propagar compartilhamentos de arquivos do Azure com os dados do servidor usando qualquer ferramenta de transferência de dados no SMB. Robocopy, por exemplo. Você também pode usar AzCopy sobre REST. Certifique-se de usar AzCopy com as opções apropriadas para preservar os carimbos de data/hora das ACLs.
1. Crie a topologia da Sincronização de arquivos do Azure com os pontos de extremidade desejados do servidor apontando para os compartilhamentos existentes.
1. Deixe a sincronização concluir o processo de reconciliação em todos os pontos de extremidade. 
1. Assim que a reconciliação for concluída, você pode abrir compartilhamentos para alterações.
 
Atualmente, a abordagem de pré-propagação tem algumas limitações – 
- Alterações de dados no servidor antes de a topologia de sincronização estar totalmente ativa e em execução podem causar conflitos nos pontos de extremidade do servidor.  
- Depois que o ponto de extremidade de nuvem é criado, o Sincronização de Arquivos do Azure executa um processo para detectar os arquivos na nuvem antes de iniciar a sincronização inicial. O tempo necessário para concluir esse processo varia de acordo com os vários fatores, como velocidade de rede, largura de banda disponível e número de arquivos e pastas. Para obter uma estimativa aproximada na versão prévia, o processo de detecção é executado a aproximadamente 10 arquivos por segundo. Portanto, mesmo se a pré-propagação for executada rapidamente, o tempo geral para obter um sistema totalmente em execução pode ser significativamente maior quando os dados forem pré-propagados na nuvem.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Restauração de autoatendimento por meio de versões anteriores e VSS (Serviço de Cópias de Sombra de Volume)

> [!IMPORTANT]
> As informações a seguir só podem ser usadas com a versão 9 (ou superior) do agente de sincronização de armazenamento. As versões inferiores a 9 não terão os cmdlets StorageSyncSelfService.

As versões anteriores são um recurso do Windows que permite que você utilize instantâneos VSS do lado do servidor de um volume para apresentar versões restauráveis de um arquivo a um cliente SMB.
Isso permite um cenário poderoso, comumente conhecido como restauração de autoatendimento, diretamente para operadores de informações, em vez de depender da restauração de um administrador de ti.

Os instantâneos do VSS e as versões anteriores funcionam independentemente do Sincronização de Arquivos do Azure. No entanto, a camada da nuvem deve ser definida como um modo compatível. Muitos pontos de extremidade do Sincronização de Arquivos do Azure Server podem existir no mesmo volume. Você precisa fazer a seguinte chamada do PowerShell por volume que tenha até mesmo um ponto de extremidade do servidor no qual você planeja ou está usando camadas de nuvem.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

Os instantâneos do VSS são tirados de um volume inteiro. Por padrão, até 64 instantâneos podem existir para um determinado volume, contanto que haja espaço suficiente para armazenar os instantâneos. O VSS trata isso automaticamente. A agenda de instantâneo padrão usa dois instantâneos por dia, de segunda-feira a sexta-feira. Essa agenda pode ser configurada por meio de uma tarefa agendada do Windows. O cmdlet do PowerShell acima faz duas coisas:
1. Ele configura as sincronizações de arquivos do Azure em camadas de nuvem no volume especificado para serem compatíveis com as versões anteriores e garante que um arquivo possa ser restaurado de uma versão anterior, mesmo que ele tenha sido em camadas para a nuvem no servidor. 
1. Ele habilita a agenda padrão do VSS. Em seguida, você pode decidir modificá-lo mais tarde. 

> [!Note]  
> Há dois aspectos importantes a serem observados:
>- Se você usar o parâmetro-Force e o VSS estiver habilitado no momento, ele substituirá o agendamento atual do instantâneo do VSS e o substituirá pelo agendamento padrão. Certifique-se de salvar sua configuração personalizada antes de executar o cmdlet.
> - Se você estiver usando esse cmdlet em um nó de cluster, também deverá executá-lo em todos os outros nós no cluster! 

Para ver se a compatibilidade de restauração de autoatendimento está habilitada, você pode executar o cmdlet a seguir.

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Ele listará todos os volumes no servidor, bem como o número de dias compatíveis em camadas de nuvem para cada um. Esse número é calculado automaticamente com base no máximo de instantâneos possíveis por volume e no agendamento de instantâneo padrão. Portanto, por padrão, todas as versões anteriores apresentadas a um operador de informações podem ser usadas para restaurar do. O mesmo será verdadeiro se você alterar o agendamento padrão para obter mais instantâneos.
No entanto, se você alterar o agendamento de uma maneira que resultará em um instantâneo disponível no volume que seja mais antigo que o valor de dias compatíveis, os usuários não poderão usar esse instantâneo mais antigo (versão anterior) para restaurar.

> [!Note]
> Habilitar a restauração de autoatendimento pode afetar o consumo e a fatura do armazenamento do Azure. Esse impacto é limitado aos arquivos atualmente em camadas no servidor. Habilitar esse recurso garante que haja uma versão de arquivo disponível na nuvem que pode ser referenciada por meio de uma entrada de versões anteriores (instantâneo do VSS).
>
> Se você desabilitar o recurso, o consumo de armazenamento do Azure diminuirá lentamente até que a janela de dias compatíveis tenha passado. Não há como acelerar isso. 

O número máximo padrão de instantâneos VSS por volume (64), bem como o agendamento padrão para levá-los, resulta em um máximo de 45 dias de versões anteriores que um operador de informações pode restaurar, dependendo de quantos instantâneos VSS você pode armazenar em seu volume.

Se o máximo de 64 instantâneos do VSS por volume não for a configuração correta para você, você poderá [alterar esse valor por meio de uma chave do registro](/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
Para que o novo limite entre em vigor, você precisa executar novamente o cmdlet para habilitar a compatibilidade de versão anterior em cada volume que foi habilitado anteriormente, com o sinalizador-Force para obter o novo número máximo de instantâneos do VSS por volume em conta. Isso resultará em um número calculado recentemente de dias compatíveis. Observe que essa alteração só entrará em vigor em arquivos em camadas recentemente e substituirá as personalizações na agenda do VSS que você tenha feito.

<a id="proactive-recall"></a>
## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>Recuperar proativamente arquivos novos e alterados de um compartilhamento de arquivos do Azure

Com o Agent versão 11, um novo modo fica disponível em um ponto de extremidade do servidor. Esse modo permite que empresas distribuídas globalmente tenham o cache de servidor em uma região remota preenchida previamente antes de os usuários locais acessarem qualquer arquivo. Quando habilitado em um ponto de extremidade do servidor, esse modo fará com que esse servidor Relembre os arquivos que foram criados ou alterados no compartilhamento de arquivos do Azure.

### <a name="scenario"></a>Cenário

Uma empresa distribuída globalmente tem filiais nos EUA e na Índia. Nos operadores de informações de manhã (horário dos EUA), eles criam uma nova pasta e novos arquivos para um projeto totalmente novo e trabalham o dia todo. Sincronização de Arquivos do Azure sincronizará a pasta e os arquivos com o compartilhamento de arquivos do Azure (ponto de extremidade de nuvem). Os operadores de informações na Índia continuarão a trabalhar no projeto em seu fuso horário. Quando chegam na manhã, o servidor local Sincronização de Arquivos do Azure habilitado na Índia precisa ter esses novos arquivos disponíveis localmente, de modo que a equipe da Índia possa trabalhar com eficiência em um cache local. Habilitar esse modo impede que o acesso inicial do arquivo seja mais lento devido a uma recall sob demanda e permite que o servidor retorne proativamente os arquivos assim que eles foram alterados ou criados no compartilhamento de arquivos do Azure.

> [!IMPORTANT]
> É importante perceber que o controle de alterações no compartilhamento de arquivos do Azure que em mais detalhes no servidor pode aumentar o tráfego de saída e a cobrança do Azure. Se os arquivos recuperados para o servidor não forem realmente necessários localmente, a recuperação desnecessária no servidor poderá ter consequências negativas. Use esse modo quando você souber preencher previamente o cache em um servidor com alterações recentes na nuvem terá um efeito positivo sobre os usuários ou aplicativos que usam os arquivos nesse servidor.

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>Habilitar um ponto de extremidade do servidor para se lembrar de forma proativa o que mudou em um compartilhamento de arquivos do Azure

# <a name="portal"></a>[Portal](#tab/proactive-portal)

1. No [portal do Azure](https://portal.azure.com/), acesse o serviço de sincronização de armazenamento, selecione o grupo de sincronização correto e, em seguida, identifique o ponto de extremidade do servidor para o qual você deseja controlar as alterações no compartilhamento de arquivos do Azure (ponto de extremidade de nuvem).
1. Na seção camadas de nuvem, localize o tópico "download do compartilhamento de arquivos do Azure". Você verá o modo selecionado no momento e poderá alterá-lo para controlar as alterações do compartilhamento de arquivos do Azure mais de maneira mais minuciosa e refazê-las proativamente no servidor.

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Uma imagem que mostra o comportamento de download do compartilhamento de arquivos do Azure para um ponto de extremidade de servidor atualmente em vigor e um botão para abrir um menu que permite alterá-lo.":::

# <a name="powershell"></a>[PowerShell](#tab/proactive-powershell)

Você pode modificar as propriedades de ponto de extremidade do servidor no PowerShell por meio do cmdlet [set-AzStorageSyncServerEndpoint](/powershell/module/az.storagesync/set-azstoragesyncserverendpoint) .

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

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
