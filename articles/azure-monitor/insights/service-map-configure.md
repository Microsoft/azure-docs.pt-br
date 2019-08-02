---
title: Configurar Mapa do Serviço no Azure | Microsoft Docs
description: O Mapa do Serviço é uma solução no Azure que descobre automaticamente os componentes do aplicativo nos sistemas Windows e Linux, e mapeia a comunicação entre os serviços. Este artigo fornece detalhes sobre a implantação do Mapa do Serviço em seu ambiente e sobre como usá-lo em diversos cenários.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: magoedte
ms.openlocfilehash: 8f2b74c4c091aed0a1b5889b0a07d44d450d1922
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477078"
---
# <a name="configure-service-map-in-azure"></a>Configurar Mapa do Serviço no Azure

O Mapa do Serviço detecta automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Você pode usá-lo para exibir os seus servidores da maneira como pensa neles – como sistemas interconectados que fornecem serviços essenciais. O Mapa do Serviço mostra conexões entre servidores, processos e portas em qualquer arquitetura conectada a TCP sem nenhuma configuração necessária além da instalação de um agente.

Este artigo descreve os detalhes sobre a configuração do Mapa do Serviço e dos agentes de integração. Para saber mais sobre como usar o Mapa do Serviço, confira [Como usar a solução Mapa do Serviço no Azure]( service-map.md).

## <a name="supported-azure-regions"></a>Regiões do Azure com suporte

O Mapa do Serviço está disponível atualmente nas seguintes regiões do Azure:
- Leste dos EUA
- Centro-Oeste dos EUA
- Canadá Central
- Sul do Reino Unido
- Europa Ocidental
- Sudeste Asiático

## <a name="supported-windows-operating-systems"></a>Sistemas operacionais Windows compatíveis

As seções a seguir listam os sistemas operacionais com suporte para o Dependency Agent no Windows. 

>[!NOTE]
>O Mapa do Serviço só dá suporte a plataformas de 64 bits.
>

### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Área de trabalho do Windows

- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

## <a name="supported-linux-operating-systems"></a>Sistemas operacionais Linux com suporte

A seção a seguir listam os sistemas operacionais com suporte para o agente de dependência no Linux.  

- Somente as versões de kernel padrão e Linux SMP têm suporte.
- Nenhuma distribuição do Linux dá suporte às versões de kernel não padrão, como PAE e Xen. Por exemplo, não há suporte para um sistema com a cadeia de caracteres de versão "2.6.16.21-0.8-xen".
- Não há suporte para kernels personalizados, incluindo recompilações de kernels padrão.

### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versão do SO | Versão do kernel |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versão do SO | Versão do kernel |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus

| Versão do SO | Versão do kernel |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

### <a name="ubuntu-server"></a>Ubuntu Server

| Versão do SO | Versão do kernel |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versão do SO | Versão do kernel
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |
|12 SP4 | 4.4.* |
|12 SP4 | Kernel ajustado pelo Azure |

## <a name="dependency-agent-downloads"></a>Downloads do Agente de Dependência

| Arquivo | SO | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="connected-sources"></a>Fontes conectadas

O Mapa do Serviço obtém seus dados do Microsoft Dependency Agent. O agente de Dependência depende do Agente do OMS para suas conexões ao Log Analytics. Isso significa que um servidor deve ter o Agente do Log Analytics instalado e configurado primeiro e, em seguida, você instala o Agente de Dependência.  A tabela a seguir descreve as fontes conectadas às quais a solução Mapa do Serviço dá suporte.

| Fonte conectada | Suportado | Descrição |
|:--|:--|:--|
| Agentes do Windows | Sim | O Mapa do Serviço analisa e coleta dados de computadores de agente do Windows. <br><br>Além do [Agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md), os agentes do Windows exigem o Microsoft Dependency Agent. Veja os sistemas operacionais com suporte para obter uma lista completa das versões de sistema operacional. |
| Agentes do Linux | Sim | O Mapa do Serviço analisa e coleta dados de computadores de agente do Linux. <br><br>Além do [Agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md), os agentes do Linux exigem o Microsoft Dependency Agent. Veja os sistemas operacionais com suporte para obter uma lista completa das versões de sistema operacional. |
| Grupo de gerenciamento do System Center Operations Manager | Sim | O Mapa do Serviço analisa e coleta dados de agentes do Windows e do Linux em um [grupo de gerenciamento do System Center Operations Manager](../../azure-monitor/platform/om-agents.md) conectado. <br><br>Uma conexão direta do computador do agente do System Center Operations Manager para Log Analytics é necessária. |
| Conta de Armazenamento do Azure | Não | O Mapa do Serviço coleta dados de computadores do agente e, portanto, não há nenhum dado dele a ser coletado do Armazenamento do Azure. |

No Windows, o MMA (Microsoft Monitoring Agent) é usado pelo System Center Operations Manager e pelo Log Analytics para coletar e enviar dados de monitoramento. (esse agente é chamado de agente do System Center Operations Manager, agente do Log Analytics, MMA ou agente direto, dependendo do contexto.) O System Center Operations Manager e o Log Analytics fornecem versões do MMA diferentes prontas para uso. Essas versões podem relatar para o System Center Operations Manager, para o Log Analytics ou para ambos.  

No Linux, o Agente do Log Analytics para Linux coleta e envia os dados de monitoramento para o Log Analytics. Você pode usar o Mapa do Serviço em servidores com agentes do Log Analytics conectados diretamente ao serviço, ou que estão se comunicando a um grupo de gerenciamento do Operations Manager integrado ao Log Analytics.  

Neste artigo, vamos nos referir a todos os agentes - seja Linux ou Windows, conectados a um grupo de gerenciamento ou diretamente ao Log Analytics - como o *Agente do Log Analytics* . 

O agente do Mapa do Serviço não transmite todos os dados em si e não requer alterações em portas ou em firewalls. Os dados no Mapa do Serviço sempre são transmitidos pelo agente do Log Analytics para o serviço Log Analytics, diretamente ou por meio do gateway do Log Analytics.

![Agentes do Mapa do Serviço](media/service-map-configure/agents.png)

Se você for um cliente do System Center Operations Manager com um grupo de gerenciamento conectado ao Log Analytics:

- Se os agentes do System Center Operations Manager podem acessar a Internet para conectarem-se ao Log Analytics, nenhuma configuração adicional é necessária.  
- Se os agentes do System Center Operations Manager não podem acessar o Log Analytics pela Internet, você precisará configurar o gateway do Log Analytics para trabalhar com o System Center Operations Manager.
  
Se seus computadores Windows ou Linux não podem se conectar diretamente ao serviço, você precisará configurar o agente do Log Analytics para se conectar ao espaço de trabalho do Log Analytics usando o gateway. Para obter mais informações sobre como implantar e configurar o gateway do Log Analytics, consulte [Conectar computadores sem acesso à Internet usando o gateway do Log Analytics](../../azure-monitor/platform/gateway.md).  

### <a name="management-packs"></a>Pacotes de gerenciamento

Quando o Mapa do Serviço é ativado em um espaço de trabalho do Log Analytics, um pacote de gerenciamento de 300 KB é enviado a todos os servidores do Windows nesse espaço de trabalho. Se você estiver usando agentes do System Center Operations Manager em um [grupo de gerenciamento conectado](../../azure-monitor/platform/om-agents.md), o pacote de gerenciamento do Mapa do Serviço será implantado do System Center Operations Manager. 

O pacote de gerenciamento chama-se Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Ele é gravado em %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\. A fonte de dados usada pelo pacote de gerenciamento é %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="data-collection"></a>Coleta de dados

Você pode esperar que cada agente transmita aproximadamente 25 MB por dia, dependendo da complexidade das dependências do seu sistema. Os dados de dependência do Mapa do Serviço são enviados por cada agente a cada 15 segundos.  

O Agente de Dependência geralmente consome 0,1% da memória do sistema e 0,1% da CPU do sistema.

## <a name="diagnostic-and-usage-data"></a>Dados de uso e de diagnóstico

A Microsoft coleta automaticamente dados de uso e de desempenho por meio do uso do serviço Mapa do Serviço. A Microsoft usa esses dados para fornecer e aprimorar a qualidade, a segurança e a integridade do serviço Mapa do Serviço. Os dados incluem informações sobre a configuração do software, como o sistema operacional e a versão. Ele também inclui o endereço IP, o nome DNS e o nome da estação de trabalho para fornecer recursos de solução de problemas precisos e eficientes. Não coletamos nomes, endereços ou outras informações de contato.

Para saber mais sobre o uso e a coleta de dados, veja a [Política de Privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="installation"></a>Instalação

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="azure-vm-extension"></a>Extensão da VM do Azure

Há uma extensão disponível para Windows (DependencyAgentWindows) e Linux (DependencyAgentLinux), e você pode implantar facilmente o agente de dependência para suas VMs do Azure usando um [extensão de VM do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).  Com a Extensão de VM do Azure, você pode implantar o Agente de Dependência para as suas VMs do Windows ou Linux por meio de um script do PowerShell ou diretamente no modelo do Azure Resource Manager da VM.  Se você implantar o agente com a Extensão da máquina virtual do Azure, seus agentes são atualizados automaticamente para a versão mais recente.

Para implantar a Extensão de VM do Azure por meio do PowerShell, você pode usar o exemplo a seguir:

```powershell
#
# Deploy the Dependency agent to every VM in a Resource Group
#

$version = "9.4"
$ExtPublisher = "Microsoft.Azure.Monitoring.DependencyAgent"
$OsExtensionMap = @{ "Windows" = "DependencyAgentWindows"; "Linux" = "DependencyAgentLinux" }
$rmgroup = "<Your Resource Group Here>"

Get-AzVM -ResourceGroupName $rmgroup |
ForEach-Object {
    ""
    $name = $_.Name
    $os = $_.StorageProfile.OsDisk.OsType
    $location = $_.Location
    $vmRmGroup = $_.ResourceGroupName
    "${name}: ${os} (${location})"
    Date -Format o
    $ext = $OsExtensionMap.($os.ToString())
    $result = Set-AzVMExtension -ResourceGroupName $vmRmGroup -VMName $name -Location $location `
    -Publisher $ExtPublisher -ExtensionType $ext -Name "DependencyAgent" -TypeHandlerVersion $version
    $result.IsSuccessStatusCode
}
```

Uma maneira ainda mais fácil de garantir que o agente de Dependência esteja instalado em cada uma das VMs é incluir o agente no modelo do Azure Resource Manager.  O exemplo de código do JSON a seguir pode ser adicionado à seção *recursos* do modelo.

```JSON
"type": "Microsoft.Compute/virtualMachines/extensions",
"name": "[concat(parameters('vmName'), '/DependencyAgent')]",
"apiVersion": "2017-03-30",
"location": "[resourceGroup().location]",
"dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
],
"properties": {
    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
    "type": "DependencyAgentWindows",
    "typeHandlerVersion": "9.4",
    "autoUpgradeMinorVersion": true
}

```

### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Instalar o Agente de Dependência no Microsoft Windows

O agente de dependência pode ser instalado manualmente em computadores Windows executando `InstallDependencyAgent-Windows.exe`. Se você executar o arquivo executável sem opções, ele iniciará um assistente que você poderá seguir para executar a instalação interativamente.  

>[!NOTE]
>São necessários privilégios de administrador para instalar ou desinstalar o agente.

Use as etapas a seguir para instalar o Dependency Agent em cada computador com o Windows:

1.  Instale o agente do Log Analytics para Windows seguindo um dos métodos descritos na [visão geral do agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).
2.  Baixe o Agente do Windows e execute-o usando o comando a seguir: 
    
    `InstallDependencyAgent-Windows.exe`

3.  Acompanhe a configuração do assistente para instalar o agente.
4.  Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Em Agentes do Windows, o diretório de log será %Programfiles%\Microsoft Dependency Agent\logs. 

#### <a name="windows-command-line"></a>Linha de comando do Windows

Use as opções da tabela a seguir para instalar a partir de uma linha de comando. Para ver uma lista dos sinalizadores de instalação, execute o instalador usando o sinalizador /? da seguinte maneira.

    InstallDependencyAgent-Windows.exe /?

| Sinalizador | DESCRIÇÃO |
|:--|:--|
| /? | Obtenha uma lista das opções de linha de comando. |
| /S | Realize uma instalação silenciosa sem solicitações ao usuário. |

Os arquivos do Agente de Dependência do Windows são colocados em C:\Program Files\Microsoft Dependency Agent por padrão.

### <a name="install-the-dependency-agent-on-linux"></a>Instale o Agente de Dependência no Linux

O Agente de Dependência é instalado em computadores do `InstallDependencyAgent-Linux64.bin`,  um script de shell com um binário de extração automática. Você pode executar o arquivo usando `sh` ou adicionar permissões de execução ao próprio arquivo.

>[!NOTE]
> O acesso root é necessário para instalar ou configurar o agente.

Use as seguintes etapas para instalar o Dependency Agent em cada computador com o Linux:

1.  Instale o agente do Log Analytics seguindo um dos métodos descritos na [visão geral do agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).
2.  Instale o Agente de Dependência para Linux como raiz usando o comando a seguir:
    
    `sh InstallDependencyAgent-Linux64.bin`

3.  Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Em agentes do Linux, o diretório de log é /var/opt/microsoft/dependency-agent/log.

Para ver uma lista dos sinalizadores de instalação, execute o programa de instalação com o sinalizador -help conforme demonstrado a seguir.

    InstallDependencyAgent-Linux64.bin -help

| Sinalizador | DESCRIÇÃO |
|:--|:--|
| -help | Obtenha uma lista das opções de linha de comando. |
| -s | Realize uma instalação silenciosa sem solicitações ao usuário. |
| --check | Verifica as permissões e o sistema operacional, mas não instala o agente. |

Os arquivos do Agente de Dependência são colocados nos diretórios a seguir:

| Arquivos | Local padrão |
|:--|:--|
| Arquivos de núcleo | /opt/microsoft/dependency-agent |
| Arquivos de log | /var/opt/microsoft/dependency-agent/log |
| Arquivos de configuração | /etc/opt/microsoft/dependency-agent/config |
| Arquivos executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Arquivos de armazenamento binário | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Exemplos de script de instalação

Para implantar facilmente o agente de dependência em vários servidores ao mesmo tempo, o exemplo de script a seguir é fornecido para baixar e instalar o agente de dependência no Windows ou Linux.

### <a name="powershell-script-for-windows"></a>Script do PowerShell para Windows

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Script de Shell para Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Configuração de estado desejado

Para implantar o agente de dependência por meio da Desired State Configuration (DSC), você pode usar o módulo xPSDesiredStateConfiguration e um pouco de código semelhante ao seguinte:

```powershell
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage 
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="remove-the-dependency-agent"></a>Remover o Agente de Dependência

### <a name="uninstall-agent-on-windows"></a>Desinstalar o agente no Windows

O Agente de Dependência para Windows pode ser desinstalado por um administrador por meio do Painel de Controle.

Um administrador também pode executar %Programfiles%\Microsoft Agent\Uninstall.exe para desinstalar o Microsoft Dependency Agent.

### <a name="uninstall-agent-on-linux"></a>Desinstalar o agente no Linux

Você pode desinstalar o Agente de Dependência do Linux com o seguinte comando.

RHEL, CentOs ou Oracle:

```
sudo rpm -e dependency-agent
```

Ubuntu:

```
sudo apt -y purge dependency-agent
```

## <a name="troubleshooting"></a>solução de problemas

Se você enfrentar problemas ao instalar ou executar o Mapa do Serviço, esta seção poderá lhe ajudar. Se ainda não for possível resolver o problema, entre em contato com o Suporte da Microsoft.

### <a name="dependency-agent-installation-problems"></a>Problemas de instalação do Agente de Dependência

#### <a name="installer-prompts-for-a-reboot"></a>O instalador solicita uma reinicialização

O Agente de Dependência *geralmente* não exige uma reinicialização após a instalação ou desinstalação. No entanto, em alguns casos raros, o Windows Server exige uma reinicialização para continuar com uma instalação. Isso ocorre quando uma dependência, normalmente Pacotes Redistribuíveis do Microsoft Visual C++, exige uma reinicialização devido a um arquivo bloqueado.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>A mensagem "Não é possível instalar o Dependency Agent: as bibliotecas de tempo de execução do Visual Studio não foram instaladas (código = [número_de_código])" aparece

O Microsoft Dependency Agent é compilado com base nas bibliotecas de tempo de execução do Microsoft Visual Studio. Você receberá uma mensagem se houver um problema durante a instalação das bibliotecas. 

Os instaladores da biblioteca de tempo de execução criam logs na pasta %LOCALAPPDATA%\temp. O arquivo é dd_vcredist_arch_yyyymmddhhmmss.log, em que *arch* é "x86" ou "amd64" e *aaaammddhhmmss* é a data e hora (relógio de 24 horas) em que o log foi criado. O log fornece detalhes sobre o problema que está bloqueando a instalação.

Pode ser útil instalar primeiro você mesmo as [bibliotecas de tempo de execução mais recentes](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

A tabela a seguir lista números de código e soluções sugeridas.

| Código | DESCRIÇÃO | Resolução |
|:--|:--|:--|
| 0x17 | O instalador da biblioteca exige uma atualização do Windows que não foi instalada. | Procure no log do instalador de biblioteca mais recente.<br><br>Se uma referência a "Windows8.1-KB2999226-x64.msu" for seguida pela linha "Erro 0x80240017: falha ao executar o pacote MSU", você não tem os pré-requisitos para instalar o KB2999226. Siga as instruções na seção de pré-requisitos em [Tempo de Execução C Universal no Windows](https://support.microsoft.com/kb/2999226). Talvez seja necessário executar o Windows Update e reiniciar várias vezes para instalar os pré-requisitos.<br><br>Execute novamente o instalador do Agente de Dependência da Microsoft. |

### <a name="post-installation-issues"></a>Problemas pós-instalação

#### <a name="server-doesnt-appear-in-service-map"></a>O servidor não aparece no Mapa do Serviço

Se a instalação do Agente de Dependência for bem-sucedida, mas você não vir seu servidor na solução Mapa do Serviço:
* O Agente de Dependência foi instalado com êxito? Confirme isso verificando se o serviço está instalado e em execução.<br><br>
**Windows**: procure o serviço denominado "Microsoft Dependency Agent".<br>
**Linux**: procure o processo em execução "microsoft-dependency-agent".

* Você está no [tipo de preço Gratuito do Operations Management Suite/Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? O plano Gratuito permite até cinco servidores de Mapa do Serviço exclusivos. Quaisquer servidores subsequentes serão exibidos no Mapa do Serviço, mesmo que os cinco anteriores não estejam enviando dados.

* O servidor está enviando dados de log e desempenho para o Log Analytics? Acesse a Pesquisa de Logs e execute a consulta a seguir para seu computador: 

    Uso | where Computer == "nome-do-computador" | summarize sum(Quantity), any(QuantityUnit) by DataType

Você obteve uma variedade de eventos nos resultados? Os dados são recentes? Se sim, o Agente do Log Analytics está operando corretamente e se comunicando com o Log Analytics. Caso contrário, verifique o agente em seu servidor: [Solução de problemas do agente do Log Analytics para Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-monitoring-onboarding-issues) ou [Solução de problemas do agente do Log Analytics para Linux](../../azure-monitor/platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>O servidor aparece no Mapa do Serviço, mas sem processos

Se você vir seu servidor no Mapa do Serviço, mas ele não tiver processos ou dados de conexão, isso indica que o Agente de Dependência está instalado e em execução, mas o driver do kernel não foi carregado. 

Verifique o arquivo C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) ou o arquivo /var/opt/microsoft/dependency-agent/log/service.log (Linux). As últimas linhas do arquivo devem indicar por que o kernel não foi carregado. Por exemplo, se o kernel tiver sido atualizado por você, talvez o Linux não dê suporte a ele.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Mapa do Serviço]( service-map.md) após ele ter sido implantado e configurado.