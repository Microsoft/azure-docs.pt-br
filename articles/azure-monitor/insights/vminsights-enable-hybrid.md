---
title: Habilitar o Azure Monitor para um ambiente híbrido
description: Este artigo descreve como habilitar Azure Monitor para VMs para um ambiente de nuvem híbrida que contém uma ou mais máquinas virtuais.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 5d866729d428e7667cd2225a5d37836b3fd75fa7
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680329"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-virtual-machine"></a>Habilitar Azure Monitor para VMs para uma máquina virtual híbrida
Este artigo descreve como habilitar Azure Monitor para VMs para uma máquina virtual fora do Azure, incluindo ambientes de nuvem locais e outros.

> [!IMPORTANT]
> O método recomendado para habilitar VMs híbridas é primeiro habilitar o [arco do Azure para servidores](../../azure-arc/servers/overview.md) para que as VMs possam ser habilitadas para Azure monitor para VMs usando processos semelhantes às VMs do Azure. Este artigo descreve como carregar VMs híbridas se você optar por não usar o Azure Arc.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar e configurar um espaço de trabalho log Analytics](vminsights-configure-workspace.md).
- Consulte [sistemas operacionais com suporte](vminsights-enable-overview.md#supported-operating-systems) para garantir que o sistema operacional da máquina virtual ou do conjunto de dimensionamento de máquinas virtuais que você está habilitando tem suporte. 


## <a name="overview"></a>Visão geral
As máquinas virtuais fora do Azure exigem o mesmo agente de Log Analytics e agente de dependência usados para VMs do Azure. Como você não pode usar extensões de VM para instalar os agentes, no entanto, você deve instalá-los manualmente no sistema operacional convidado ou tê-los instalados por meio de algum outro método. 

Consulte [conectar computadores Windows a Azure monitor](../platform/agent-windows.md) ou [conectar computadores Linux ao Azure monitor](../platform/agent-linux.md) para obter detalhes sobre como implantar o agente de log Analytics. Os detalhes do agente de dependência são fornecidos neste artigo. 

## <a name="firewall-requirements"></a>Requisitos de firewall
Os requisitos de firewall para o agente de Log Analytics são fornecidos na [visão geral do agente de log Analytics](../platform/log-analytics-agent.md#network-requirements). O Dependency Agent de Mapa do Azure Monitor para VMs não transmite dados por conta própria e não exige alterações em firewalls ou portas. Os dados do mapa são sempre transmitidos pelo agente de Log Analytics para o serviço Azure Monitor, diretamente ou por meio do [gateway do Operations Management Suite](../../azure-monitor/platform/gateway.md) se as suas políticas de segurança de ti não permitirem que computadores na rede se conectem à Internet.


## <a name="dependency-agent"></a>Agente de dependência

>[!NOTE]
>As informações a seguir descritas nesta seção também são aplicáveis à [solução de mapa do serviço](service-map.md).  

Você pode baixar o Dependency Agent nestes locais:

| Arquivo | Sistema operacional | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.7.12710 | CA29CC328F991D7301FD0360F4F56DF78275545BB8CDA853679899CA885E96F0  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.7.12710 | 98380DBEB2E2A5848F2202BC22422C68B20B62090C1BFC1DECAB37ED5451ED8C |


## <a name="install-the-dependency-agent-on-windows"></a>Instalar o Dependency Agent no Windows

Instale o Dependency Agent manualmente em computadores Windows executando `InstallDependencyAgent-Windows.exe`. Se você executar esse arquivo executável sem opções, ele iniciará um assistente de instalação que você poderá seguir para instalar o agente de forma interativa. Você precisa de privilégios de *administrador* no sistema operacional convidado para instalar ou desinstalar o agente.

A tabela a seguir destaca os parâmetros compatíveis com a instalação do agente por meio da linha de comando.

| Parâmetro | DESCRIÇÃO |
|:--|:--|
| /? | Retorna uma lista das opções de linha de comando. |
| /S | Realiza uma instalação silenciosa sem a interação do usuário. |

Por exemplo, para executar o programa de instalação com o `/?` parâmetro, digite **InstallDependencyAgent-Windows.exe/?**.

Os arquivos do Dependency Agent do Windows são instalados em *C:\Arquivos de Programas\Microsoft Dependency Agent* por padrão. Se o agente de dependência não for iniciado após a conclusão da instalação, verifique os logs para obter informações detalhadas sobre o erro. O diretório de log é *%Programfiles%\Microsoft Dependency Agent\logs*.

### <a name="powershell-script"></a>Script do PowerShell
Use o seguinte script do PowerShell de exemplo para baixar e instalar o agente:

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```


## <a name="install-the-dependency-agent-on-linux"></a>Instale o Agente de Dependência no Linux

O Dependency Agent é instalado em servidores Linux por meio de *InstallDependencyAgent-Linux64.bin*, um script de shell com um binário de extração automática. Você pode executar o arquivo usando `sh` ou adicionar permissões de execução ao próprio arquivo.

>[!NOTE]
> O acesso root é necessário para instalar ou configurar o agente.
>

| Parâmetro | Descrição |
|:--|:--|
| -help | Obtenha uma lista das opções de linha de comando. |
| -S | Realize uma instalação silenciosa sem solicitações ao usuário. |
| --check | Verifica as permissões e o sistema operacional, mas não instala o agente. |

Por exemplo, para executar o programa de instalação com o `-help` parâmetro, insira **installdependencyagent-Linux64. bin-Help**. Instale o agente de dependência do Linux como raiz executando o comando `sh InstallDependencyAgent-Linux64.bin` .

Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Em agentes do Linux, o diretório de log é */var/opt/Microsoft/Dependency-Agent/log*.

Os arquivos do Agente de Dependência são colocados nos diretórios a seguir:

| Arquivos | Localização |
|:--|:--|
| Arquivos de núcleo | /opt/microsoft/dependency-agent |
| Arquivos de log | /var/opt/microsoft/dependency-agent/log |
| Arquivos de configuração | /etc/opt/microsoft/dependency-agent/config |
| Arquivos executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Arquivos de armazenamento binário | /var/opt/microsoft/dependency-agent/storage |

### <a name="shell-script"></a>Script de Shell 
Use o seguinte script de Shell de exemplo para baixar e instalar o agente:

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

Para implantar o agente de dependência por meio da Desired State Configuration (DSC), você pode usar o módulo xPSDesiredStateConfiguration e um pouco de código semelhante ao seguinte:

```powershell
configuration VMInsights {

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



## <a name="troubleshooting"></a>Solução de problemas

### <a name="vm-doesnt-appear-on-the-map"></a>A VM não aparece no mapa

Se a instalação do agente de dependência for bem-sucedida, mas você não vir seu computador no mapa, diagnostique o problema seguindo estas etapas.

1. O Agente de Dependência foi instalado com êxito? Confirme isso verificando se o serviço está instalado e em execução.

    **Windows**: Procure o serviço chamado "Microsoft Dependency Agent".

    **Linux**: Procure o processo em execução "Microsoft-Dependency-Agent".

2. Você está no [tipo de preço gratuito do log Analytics](./solutions.md)? O plano gratuito permite até cinco computadores exclusivos. Todos os computadores subsequentes não aparecerão no mapa, mesmo que os cinco anteriores não estejam mais enviando dados.

3. O computador está enviando dados de log e desempenho para Azure Monitor logs? Execute a seguinte consulta para seu computador:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Retornou um ou mais resultados? Os dados são recentes? Nesse caso, seu agente de Log Analytics está operando corretamente e se comunicando com o serviço. Caso contrário, verifique o agente no servidor: [Solução de problemas do agente do Log Analytics para Windows](../platform/agent-windows-troubleshoot.md) ou [Solução de problemas do agente do Log Analytics para Linux](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>O computador aparece no mapa, mas não tem nenhum processo

Se você vir o servidor no mapa, mas ele não tiver dados de processo ou de conexão, isso indicará que o agente de dependência está instalado e em execução, mas o driver do kernel não foi carregado.

Verifique o arquivo C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) ou o arquivo /var/opt/microsoft/dependency-agent/log/service.log (Linux). As últimas linhas do arquivo devem indicar por que o kernel não foi carregado. Por exemplo, se o kernel tiver sido atualizado por você, talvez o Linux não dê suporte a ele.


## <a name="next-steps"></a>Próximas etapas

Agora que o monitoramento está habilitado para suas máquinas virtuais, essas informações estão disponíveis para análise com Azure Monitor para VMs.

- Para exibir as dependências de aplicativos descobertas, confira [Exibir o Mapa do Azure Monitor para VMs](vminsights-maps.md).

- Para identificar afunilamentos e a utilização geral com o desempenho da VM, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md).