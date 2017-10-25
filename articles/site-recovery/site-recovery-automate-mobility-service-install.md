---
title: "Implantar o serviço de Mobilidade do Site Recovery com o DSC de Automação do Azure | Microsoft Docs"
description: "Descreve como usar o DSC de Automação do Azure para implantar automaticamente o Serviço de Mobilidade do Azure Site Recovery e o agente do Azure para VM do VMware e replicação do servidor físico no Azure"
services: site-recovery
documentationcenter: 
author: krnese
manager: lorenr
editor: 
ms.assetid: 1f8cd3ac-0522-48eb-a5f0-679ee9192ddb
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: krnese
ms.openlocfilehash: bcc5f11afbecac8fe63935f3401dd3e2d767e8aa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-the-mobility-service-with-azure-automation-dsc-for-replication-of-vm"></a>Implantar o serviço de Mobilidade com o DSC de Automação do Azure para replicação da VM
No Operations Management Suite, oferecemos uma solução abrangente de backup e recuperação de desastre que pode ser usada como parte de seu plano de continuidade de negócios.

Começamos esta jornada com o Hyper-V, usando a Réplica do Hyper-V. Mas expandimos para dar suporte a uma configuração heterogênea porque os clientes têm vários hipervisores e plataformas em suas nuvens.

Se você estiver executando servidores físicos e/ou cargas de trabalho da VMware, um servidor de gerenciamento executará todos os componentes do Azure Site Recovery no seu ambiente para lidar com a replicação dos dados e a comunicação com o Azure quando ele for seu destino.

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>Implantar o Serviço de Mobilidade do Site Recovery usando o DSC de Automação
Vamos começar fazendo uma análise rápida do que este servidor de gerenciamento faz.

o servidor de gerenciamento executa várias funções de servidor. Uma dessas funções é a de *configuração*, que coordena a comunicação e gerencia os processos de replicação e recuperação de dados.

Além disso, a função de *processo* atua como um gateway de replicação. Essa função recebe dados de replicação de computadores de origem protegida, otimiza-os com caching, compactação e criptografia e os envia para uma conta de armazenamento do Azure. Uma das funções da função do processo também é a instalação do Serviço de Mobilidade por push nas máquinas protegidas e a execução da descoberta automática das VMs da VMware.

No caso de um failback do Azure, a função de *destino mestre* lidará com os dados de replicação como parte dessa operação.

Para os computadores protegidos, podemos contar com a *Serviço de Mobilidade*. Esse componente é implantado em cada computador (servidor físico ou VM da VMware) que você quiser replicar para o Azure. Ele captura dados gravados no computador e os encaminha para o servidor de gerenciamento (função de processo).

Quando se trata de continuidade de negócios, é importante compreender suas cargas de trabalho, sua infraestrutura e os componentes envolvidos. Assim, é possível atender os requisitos de RTO (objetivo do tempo de recuperação) e RPO (objetivo de ponto de recuperação). Nesse contexto, o Serviço de Mobilidade é fundamental para garantir que suas cargas de trabalho sejam protegidas da forma esperada.

Sendo assim, como você pode assegurar, de forma otimizada, que tem uma configuração protegida confiável com a ajuda de alguns componentes do Operations Management Suite?

Este artigo fornece um exemplo de como você pode usar a DSC (Configuração de Estado Desejado) da Automação do Azure em conjunto com o Site Recovery para garantir que:

* o Serviço de Mobilidade e o agente de VM do Azure sejam implantados nos computadores com Windows que você deseja proteger.
* o Serviço de Mobilidade e o agente de VM do Azure sempre estejam em execução quando o Azure for o destino de replicação.

## <a name="prerequisites"></a>Pré-requisitos
* Um repositório para armazenar a configuração necessária
* Um repositório para armazenar a senha necessária para se registrar no servidor de gerenciamento

  > [!NOTE]
  > Uma senha exclusiva é gerada para cada servidor de gerenciamento. Se for implantar vários servidores de gerenciamento, você precisará garantir que a senha correta seja armazenada no arquivo passphrase.txt.
  >
  >
* O WMF (Windows Management Framework) 5.0 instalado nos computadores que você deseja habilitar para proteção (requisito para o DSC de Automação)

  > [!NOTE]
  > Se você quiser usar o DSC para computadores com Windows que tenham o WMF 4.0 instalado, consulte a seção [Usar o DSC em ambientes desconectados](## Use DSC in disconnected environments).
  

O Serviço de Mobilidade pode ser instalado por meio da linha de comando e aceita vários argumentos. É por isso que você precisa ter os binários (após extraí-los de sua configuração) e armazená-los em algum lugar em que possa recuperá-los usando uma configuração de DSC.

## <a name="step-1-extract-binaries"></a>Etapa 1: Extrair os binários
1. Para extrair os arquivos necessários para esta instalação, navegue até o seguinte diretório no servidor de gerenciamento:

    **\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**

    Nessa pasta, você verá um arquivo MSI chamado:

    **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe**

    Use o seguinte comando para extrair o instalador:

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**
2. Selecione todos os arquivos e envie-os para uma pasta compactada (zipada).

Agora, você tem os binários necessários para automatizar a instalação do Serviço de Mobilidade usando o DSC de Automação.

### <a name="passphrase"></a>Senha
Em seguida, você precisa determinar onde deseja colocar essa pasta compactada. Você pode usar uma conta de armazenamento do Azure, como mostrado posteriormente, para armazenar a senha de que precisa para a instalação. O agente será registrado no servidor de gerenciamento como parte do processo.

A senha que você recebeu quando implantou o servidor de gerenciamento pode ser salva em um arquivo de texto, como passphrase.txt.

Coloque a pasta compactada e a senha em um contêiner dedicado na conta de armazenamento do Azure.

![Localização da pasta](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Se preferir manter esses arquivos em um compartilhamento na rede, você poderá fazer isso. Você só precisa garantir que o recurso de DSC que será usado posteriormente realmente tenha acesso e possa obter a instalação e a senha.

## <a name="step-2-create-the-dsc-configuration"></a>Etapa 2: Criar a configuração de DSC
A instalação depende do WMF 5.0. Para o computador aplicar com êxito a configuração por meio do DSC de Automação, o WMF 5.0 deve estar presente.

O ambiente usa o seguinte exemplo de configuração de DSC:

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
A configuração fará o seguinte:

* As variáveis informarão a configuração sobre onde obter os binários para o Serviço de Mobilidade e o agente de VM do Azure, onde obter a senha e onde armazenar a saída.
* A configuração importará o recurso de DSC xPSDesiredStateConfiguration para que você possa usar `xRemoteFile` para baixar os arquivos do repositório.
* A configuração criará um diretório no qual você deseja armazenar os binários.
* O recurso de arquivamento extrairá os arquivos da pasta compactada.
* O pacote de instalação do recurso instalará o Serviço de Mobilidade do instalador UNIFIEDAGENT.EXE com os argumentos específicos. (As variáveis que criam os argumentos precisam ser alteradas para refletir seu ambiente).
* O recurso de pacote AzureAgent instalará o agente de VM do Azure, que é recomendado em cada VM executada no Azure. O agente de VM do Azure também torna possível adicionar extensões à VM após o failover.
* Os recursos de serviço garantirão que os serviços de Mobilidade relacionados e os serviços do Azure estejam sempre em execução.

Salve a configuração como **ASRMobilityService**.

> [!NOTE]
> Lembre-se de substituir o CSIP em sua configuração para refletir o servidor de gerenciamento real, para que o agente seja conectado corretamente e use a senha correta.
>
>

## <a name="step-3-upload-to-automation-dsc"></a>Etapa 3: Carregar o DSC de Automação
Como a configuração DSC que você fez importará um módulo de recursos DSC necessário (xPSDesiredStateConfiguration), você precisa importar esse módulo na Automação antes de carregar a configuração DSC.

Entre na sua conta de Automação, navegue até **Ativos** >  > **Módulos** e clique em **Procurar na Galeria**.

Aqui, você pode procurar o módulo e importá-lo para sua conta.

![Importar módulo](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Feito isso, vá até o computador no qual os módulos do Azure Resource Manager estão instalados e prossiga para importar a configuração de DSC recém-criada.

### <a name="import-cmdlets"></a>Importar cmdlets
No PowerShell, entre em sua assinatura do Azure. Modifique os cmdlets para refletir seu ambiente e capturar suas informações da conta de Automação em uma variável:

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Carregue a configuração no DSC de Automação usando o seguinte cmdlet:

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>Compilar a configuração no DSC de Automação
Em seguida, é necessário compilar a configuração no DSC de Automação para poder começar a registrar os nós. Podemos fazer isso executando o seguinte cmdlet:

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Isso pode levar alguns minutos, pois você está basicamente implantando a configuração no serviço hospedado de pull do DSC.

Após compilar a configuração, você pode recuperar as informações do trabalho usando o PowerShell (Get-AzureRmAutomationDscCompilationJob) ou usando o [Portal do Azure](https://portal.azure.com/).

![Recuperar trabalho](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Agora, você publicou e carregou com êxito sua configuração de DSC no DSC de Automação.

## <a name="step-4-onboard-machines-to-automation-dsc"></a>Etapa 4: Integrar os computadores no DSC de Automação
> [!NOTE]
> Um dos pré-requisitos para concluir esse cenário é que seus computadores com Windows estejam atualizados com a última versão do WMF. Você pode baixar e instalar a versão correta para sua plataforma no [Centro de Download](https://www.microsoft.com/download/details.aspx?id=50395).
>
>

Agora, você criará uma metaconfiguração para o DSC que será aplicado em seus nós. Para ter êxito com isso, você precisa recuperar a URL do ponto de extremidade e a chave primária de sua conta de Automação selecionada no Azure. Esses valores podem ser localizados em **Chaves** na folha **Todas as configurações** da conta de Automação.

![Valores da chave](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

Nesse exemplo, você tem um servidor físico do Windows Server 2012 R2 que quer proteger usando o Site Recovery.

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>Verificar quaisquer operações de renomeação de arquivo pendentes no registro
Antes de começar a associar o servidor ao ponto de extremidade do DSC de Automação, é recomendável verificar quaisquer operações de renomeação pendentes no Registro. Elas podem impedir que a instalação seja concluída devido a uma reinicialização pendente.

Execute o seguinte cmdlet para verificar se não há nenhuma reinicialização pendente no servidor:

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Se isso não mostrar nada, será OK continuar. Caso contrário, deverá lidar com isso reinicializando o servidor durante uma janela de manutenção.

Para aplicar a configuração no servidor, inicie o ISE (Ambiente de Script Integrado) do PowerShell e execute o script a seguir. Isso é basicamente uma configuração local do DSC que instruirá o mecanismo do Gerenciador de Configurações Local a se registrar no serviço de DSC de Automação e recuperar a configuração específica (ASRMobilityService.localhost).

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Essa configuração fará com que o mecanismo do Gerenciador de Configurações Local se registre no DSC de Automação. Ela também determinará como o mecanismo deve operar, o que ele deverá fazer se houver falta de sincronia na configuração (ApplyAndAutoCorrect) e como deverá prosseguir com a configuração se uma reinicialização for necessária.

Após você executar o script, o nó deve começar a se registrar no DSC de Automação.

![Registro de nó em andamento](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Se voltar para o portal do Azure, você poderá ver que o nó recém-registrado agora apareceu no portal.

![Nó registrado no portal](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

No servidor, é possível executar o seguinte cmdlet do PowerShell para verificar se o nó foi registrado corretamente:

```powershell
Get-DscLocalConfigurationManager
```

Depois da configuração ter sido extraída e aplicada no servidor, você pode verificar isso executando o seguinte cmdlet:

```powershell
Get-DscConfigurationStatus
```

A saída mostra que o servidor extraiu com êxito sua configuração:

![Saída](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

Além disso, a instalação do Serviço de Mobilidade tem seu próprio log, que pode ser encontrado em *SystemDrive*\ProgramData\ASRSetupLogs.

É isso. Você implantou e registrou com êxito o Serviço de Mobilidade no computador que deseja proteger usando o Site Recovery. O DSC garantirá que os serviços necessários estejam sempre em execução.

![Implantação bem-sucedida](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Após o servidor de gerenciamento detectar a implantação bem-sucedida, você poderá configurar a proteção e habilitar a replicação no computador usando o Site Recovery.

## <a name="use-dsc-in-disconnected-environments"></a>Usar o DSC em ambientes desconectados
Se seus computadores não estiverem conectados à Internet, você ainda poderá contar com o DSC para implantar e configurar o Serviço de Mobilidade nas cargas de trabalho que gostaria de proteger.

Você pode instanciar seu próprio servidor de recepção do DSC em seu ambiente para, essencialmente, fornecer os mesmos recursos que obtém com o DSC de Automação. Ou seja, os clientes efetuarão pull da configuração (após o registro) para o ponto de extremidade do DSC. No entanto, outra opção é enviar por push manualmente a configuração de DSC por push a seus computadores, local ou remotamente.

Observe que, neste exemplo, há um parâmetro adicional para o nome do computador. Agora, os arquivos remotos estão localizados em um compartilhamento remoto deve ser acessível pelos computadores que você deseja proteger. O final do script coloca a configuração em prática e começa a aplicar a configuração de DSC ao computador de destino.

### <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que o módulo xPSDesiredStateConfiguration do PowerShell esteja instalado. Para computadores com Windows em que o WMF 5.0 está instalado, você pode instalar o módulo xPSDesiredStateConfiguration executando o seguinte cmdlet nos computadores de destino:

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

Você também pode baixar e salvar o módulo caso precise distribuí-lo para máquinas do Windows que têm o WMF 4.0. Execute este cmdlet em um computador em que o PowerShellGet (WMF 5.0) está presente:

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Também para o WMF 4.0, verifique se a [atualização do Windows 8.1 KB2883200](https://www.microsoft.com/download/details.aspx?id=40749) está instalado nos computadores.

A configuração a seguir pode ser enviada por push para computadores com Windows com o WMF 5.0 e 4.0:

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Se você quiser criar uma instância de seu próprio servidor de recepção do DSC na rede corporativa para imitar os recursos que você pode obter do DSC de Automação, consulte [Configurando um servidor de pull da Web de DSC](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>Opcional: implantar uma configuração de DSC usando um modelo do Azure Resource Manager
Neste artigo, o foco é como você pode criar sua própria configuração de DSC para implantar automaticamente o Serviço de Mobilidade e o Agente de VM do Azure – e assegurar que eles estejam em execução nos computadores que você quer proteger. Também temos um modelo do Azure Resource Manager que implantará essa configuração de DSC em uma conta nova ou existente da Automação do Azure. O modelo usará parâmetros de entrada para criar ativos de Automação que conterão as variáveis para seu ambiente.

Após implantar o modelo, você pode simplesmente consultar a etapa 4 deste guia para integrar suas máquinas.

O modelo fará o seguinte:

1. Usar uma conta existente ou criar uma nova conta de Automação
2. Obter parâmetros de entrada para:
   * ASRRemoteFile – a localização na qual você armazenou a instalação do Serviço de Mobilidade
   * ASRPassphrase – a localização na qual você armazenou o arquivo passphrase.txt
   * ASRCSEndpoint – o endereço IP do seu servidor de gerenciamento
3. Importar o módulo do PowerShell xPSDesiredStateConfiguration
4. Criar e compilar a configuração de DSC

Todas as etapas anteriores serão executadas na ordem correta para que você possa começar a integrar suas máquinas para ter proteção.

O modelo com instruções de implantação está localizado no [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

Implantar o modelo usando o PowerShell:

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>Próximas etapas
Depois de implantar os agentes do Serviço de Mobilidade, você pode [habilitar a replicação](site-recovery-vmware-to-azure.md) para as máquinas virtuais.
