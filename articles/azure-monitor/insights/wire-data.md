---
title: Solução de dados de transmissão em Azure Monitor | Microsoft Docs
description: Os dados de transmissão são dados consolidados de rede e de desempenho de computadores com agentes do Log Analytics. Os dados de rede são combinados com os dados de log para ajudá-lo a correlacionar dados.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/29/2020
ms.openlocfilehash: 06698ad3ab2ceb76278e23bc1ac0002b9c2284f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445770"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Solução Wire Data 2.0 (versão prévia) no Azure Monitor

![Símbolo do Wire Data](media/wire-data/wire-data2-symbol.png)

Os dados de transferência são dados consolidados de rede e de desempenho coletados de computadores conectados por Windows e conectados por Linux com o agente do Log Analytics, incluindo aqueles monitorados pelo Operations Manager em seu ambiente. Os dados de rede são combinados a seus outros dados de log para ajudá-lo a correlacionar dados.

Além do agente do Log Analytics, a solução Wire Data usa os Agentes de Dependência da Microsoft que você instala em computadores na infraestrutura de TI. Os Agentes de Dependência monitoram dados de rede enviados de e para seus computadores para os níveis de rede 2 e 3 no [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), incluindo os diversos protocolos e portas usados. Em seguida, os dados são enviados para Azure Monitor usando agentes.  

>[!NOTE]
>A solução de dados de transmissão foi substituída pela [solução de mapa do serviço](service-map.md).  Ambos usam o agente de Log Analytics e o agente de dependência para coletar dados de conexão de rede em Azure Monitor. 
> 
>Os clientes existentes que usam a solução de dados de transmissão podem continuar a usá-la. Publicaremos as diretrizes para uma linha do tempo de migração para mudar para Mapa do Serviço.
>
>Os novos clientes devem instalar a [solução mapa do serviço](service-map.md) ou [Azure monitor para VMs](vminsights-overview.md).  O conjunto de dados Mapa do Serviço é comparável a dados de transmissão.  Azure Monitor para VMs inclui o conjunto de dados Mapa do Serviço com recursos e dados de desempenho adicionais para análise. 


Por padrão, o Azure Monitor registra dados de CPU, memória, disco e dados de desempenho de rede de contadores criados no Windows e Linux, bem como outros contadores de desempenho que você pode especificar. A coleta de dados de rede e de outros dados é feita em tempo real para cada agente, incluindo sub-redes e protocolos no nível de aplicativo usados pelo computador.  O Wire Data analisa dados de rede no nível do aplicativo, não embaixo na camada de transporte TCP. A solução não examina ACKs e SYNs individuais. Após a conclusão do handshake, ele é considerado uma conexão dinâmica e marcado como Conectado. Essa conexão permanece ativa desde que ambos os lados aceitem que o soquete está aberto e que os dados podem passar em ambas as direções. Quando um dos lados fecha a conexão, ele é marcado como desconectado.  Portanto, ele conta apenas a largura de banda de pacotes concluídos com êxito, não relatando nem reenviando pacotes com falha.

Se você já tiver usado [sFlow](http://www.sflow.org/) ou outro software com o [protocolo NetFlow da Cisco](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), as estatísticas e os dados que você verá do Wire Data já serão conhecidos.

Entre alguns dos tipos de consultas de Pesquisa de log internas estão:

- Agentes que fornecem dados de transmissão
- Endereço IP de agentes que fornecem dados de transmissão
- Comunicações de saída por endereços IP
- Número de bytes enviados por protocolos de aplicativo
- Número de bytes enviados por um serviço de aplicativo
- Bytes recebidos por diferentes protocolos
- Total de bytes enviados e recebidos por versão de IP
- Latência média de conexões que foram medidas de forma confiável
- Processos de computador que iniciaram ou receberam tráfego de rede
- Quantidade de tráfego de rede de um processo

Ao pesquisar com os dados de transmissão, é possível filtrar e agrupar os dados para exibir informações sobre os principais agentes e protocolos. Ou você pode exibir quando determinados computadores (endereços IP/MAC) comunicaram-se entre si, a duração dessa comunicação e a quantidade de dados enviados – basicamente, são exibidos metadados sobre o tráfego de rede, que são baseados em pesquisa.

No entanto, já que você está exibindo metadados, eles não são necessariamente úteis para solução de problemas detalhada. Os dados de transmissão no Azure Monitor não são uma captura completa dos dados da rede.  Ele não se destina a solucionar problemas aprofundados no nível de pacote. A vantagem de usar o agente, em comparação com outros métodos de coleção, é que você não precisa instalar dispositivos, reconfigurar os comutadores de rede ou executar configurações complicadas. O Wire Data é simplesmente baseado em agente – você instala o agente em um computador e ele monitorará seu próprio tráfego de rede. Outra vantagem é quando você deseja monitorar cargas de trabalho em execução em provedores de nuvem, provedor de serviços de hospedagem ou no Microsoft Azure, em que o usuário não tem a camada de malha.

## <a name="connected-sources"></a>Fontes conectadas

O Wire Data obtém seus dados do Agente de Dependência da Microsoft. A Dependency Agent depende do agente de Log Analytics para suas conexões a Azure Monitor. Isso significa que um servidor deve ter o Agente do Log Analytics instalado e configurado primeiro e, em seguida, você instala o Agente de Dependência. A tabela a seguir descreve as fontes conectadas às quais a solução Wire Data dá suporte.

| **Fonte conectada** | **Com suporte** | **Descrição** |
| --- | --- | --- |
| Agentes do Windows | Sim | O Wire Data analisa e coleta dados de computadores de agente do Windows. <br><br> Além do [agente de log Analytics para Windows](../platform/agent-windows.md), os agentes do Windows exigem o Microsoft Dependency Agent. Consulte os [sistemas operacionais com suporte](vminsights-enable-overview.md#supported-operating-systems) para obter uma lista completa de versões de sistema operacional. |
| Agentes do Linux | Sim | O Wire Data analisa e coleta dados de computadores de agente do Linux.<br><br> Além do [agente de log Analytics para Linux](../learn/quick-collect-linux-computer.md), os agentes do Linux exigem o Microsoft Dependency Agent. Consulte os [sistemas operacionais com suporte](vminsights-enable-overview.md#supported-operating-systems) para obter uma lista completa de versões de sistema operacional. |
| Grupo de gerenciamento do System Center Operations Manager | Sim | O Wire Data analisa e coleta dados de agentes do Windows e do Linux em um [grupo de gerenciamento do System Center Operations Manager](../platform/om-agents.md) conectado. <br><br> É necessária uma conexão direta do computador do agente de System Center Operations Manager para Azure Monitor. |
| Conta de Armazenamento do Azure | Não | O Wire Data coleta dados de computadores do agente e, portanto, não há nenhum dado dele a ser coletado do Armazenamento do Azure. |

No Windows, o Microsoft Monitoring Agent (MMA) é usado pelo System Center Operations Manager e Azure Monitor para coletar e enviar dados. Dependendo do contexto, esse agente é chamado de Agente do System Center Operations Manager, agente do Log Analytics, MMA ou Agente Direto. System Center Operations Manager e Azure Monitor fornecem versões ligeiramente diferentes do MMA. Essas versões podem cada relatório para System Center Operations Manager, para Azure Monitor ou para ambos.

No Linux, o agente Log Analytics para Linux coleta e envia dados para Azure Monitor. Você pode usar dados de transmissão em servidores com agentes conectados diretamente a Azure Monitor ou em servidores que estão se conectando a Azure Monitor por meio de grupos de gerenciamento System Center Operations Manager.

O Dependency Agent não transmite nenhum dado e não requer nenhuma alteração em firewalls ou portas. Os dados em dados de transmissão sempre são transmitidos pelo agente de Log Analytics para Azure Monitor, diretamente ou por meio do gateway de Log Analytics.

![diagrama do agente](./media/wire-data/agents.png)

Se você for um usuário System Center Operations Manager com um grupo de gerenciamento conectado a Azure Monitor:

- Nenhuma configuração adicional é necessária quando seus agentes de System Center Operations Manager podem acessar a Internet para se conectarem ao Azure Monitor.
- Você precisa configurar o gateway de Log Analytics para trabalhar com System Center Operations Manager quando seus agentes de System Center Operations Manager não puderem acessar Azure Monitor pela Internet.

Se os computadores Windows ou Linux não puderem se conectar diretamente ao serviço, você precisará configurar o agente de Log Analytics para se conectar ao Azure Monitor usando o gateway de Log Analytics. Você pode baixar o gateway do Log Analytics no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Pré-requisitos

- Requer a oferta da solução [Insight e Análise](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing).
- Se você estiver usando a versão anterior da solução Wire Data, deverá primeiro removê-la. No entanto, todos os dados capturados por meio de solução Wire Data original ainda estão disponível no Wire Data 2.0 e na pesquisa de log.
- São necessários privilégios de administrador para instalar ou desinstalar o Dependency Agent.
- O agente de dependência deve ser instalado em um computador com um sistema operacional de 64 bits.

### <a name="operating-systems"></a>Sistemas operacionais

As seções a seguir listam os sistemas operacionais com suporte para o Dependency Agent. O Wire Data não dá suporte a arquiteturas de 32 bits de nenhum sistema operacional.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Área de trabalho do Windows

- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>Sistemas operacionais Linux com suporte
As seções a seguir listam os sistemas operacionais com suporte para o Dependency Agent no Linux.  

- Somente as versões de kernel padrão e Linux SMP têm suporte.
- Nenhuma distribuição do Linux dá suporte às versões de kernel não padrão, como PAE e Xen. Por exemplo, não há suporte para um sistema com a cadeia de caracteres de versão "2.6.16.21-0.8-xen".
- Não há suporte para kernels personalizados, incluindo recompilações de kernels padrão.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versão do SO | Versão do kernel |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7,5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versão do SO | Versão do kernel |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

##### <a name="centosplus"></a>CentOSPlus
| Versão do SO | Versão do kernel |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| Versão do SO | Versão do kernel |
|:--|:--|
| Ubuntu 18.04 | kernel 4,15.\*<br>4,18 * |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Versão do SO | Versão do kernel
|:--|:--|
| 11 SP4 | 3,0. * |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versão do SO | Versão do kernel
|:--|:--|
| 12 SP2 | 4.4.* |
| 12 SP3 | 4.4.* |

### <a name="dependency-agent-downloads"></a>Downloads do Agente de Dependência

| Arquivo | Sistema operacional | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>Configuração

Execute as seguintes etapas para configurar a solução Wire Data para seus workspaces.

1. Habilite a solução de Análise do Log de Atividades do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) ou usando o processo descrito em [Adicionar soluções de monitoramento do Galeria de soluções](./solutions.md).
2. Instale o Dependency Agent em cada computador em que você deseja obter dados. O Dependency Agent pode monitorar conexões a vizinhos imediatos, portanto, talvez você não precise de um agente em todos os computadores.

> [!NOTE]
> Você não pode adicionar a versão anterior da solução Wire Data a novos workspaces. Se você tiver a solução Wire Data original habilitada, poderá continuar a usá-la. No entanto, para usar Wire Data 2.0, você deve primeiro remover a versão original.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>Instalar o Dependency Agent no Windows

São necessários privilégios de administrador para instalar ou desinstalar o agente.

O Dependency Agent é instalado em computadores que executam o Windows por meio do InstallDependencyAgent-Windows.exe. Se você executar o arquivo executável sem opções, ele iniciará um assistente que você poderá seguir para executar a instalação interativamente.

Use as etapas a seguir para instalar o agente de dependência em cada computador que executa o Windows:

1. Instale o agente do Log Analytics seguindo as etapas em [Coletar dados de computadores Windows hospedados em seu ambiente](../platform/agent-windows.md).
2. Baixe o agente de dependência do Windows usando o link na seção anterior e execute-o usando o seguinte comando: `InstallDependencyAgent-Windows.exe`
3. Acompanhe o assistente para instalar o agente.
4. Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Para Agentes do Windows, o diretório de log será %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Linha de comando do Windows

Use as opções da tabela a seguir para instalar a partir de uma linha de comando. Para ver uma lista dos sinalizadores de instalação, execute o instalador usando o sinalizador /? da seguinte maneira.

InstallDependencyAgent-Windows.exe /?

| **Sinalizador** | **Descrição** |
| --- | --- |
| <code>/?</code> | Obtenha uma lista das opções de linha de comando. |
| <code>/S</code> | Realize uma instalação silenciosa sem solicitações ao usuário. |

Os arquivos para o agente de dependência do Windows são colocados em C:\Program Files\Microsoft Dependency Agent por padrão.

### <a name="install-the-dependency-agent-on-linux"></a>Instale o Agente de Dependência no Linux

O acesso root é necessário para instalar ou configurar o agente.

O Dependency Agent é instalado em computadores Linux por meio de Installdependencyagent-Linux64. bin, um script de shell com um binário de extração automática. Você pode executar o arquivo usando _sh_ ou adicionar permissões de execução ao próprio arquivo.

Use as seguintes etapas para instalar o Dependency Agent em cada computador com o Linux:

1. Instale o agente do Log Analytics seguindo as etapas em [Coletar dados de computadores Linux hospedados em seu ambiente](../learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Baixe o Agente de Dependência do Linux usando o link na seção anterior e, em seguida, instale-o como raiz usando o seguinte comando: sh InstallDependencyAgent-Linux64.bin
3. Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Em agentes do Linux, o diretório de log é /var/opt/microsoft/dependency-agent/log.

Para ver uma lista dos sinalizadores de instalação, execute o programa de instalação com o sinalizador `-help` conforme demonstrado a seguir.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Sinalizador** | **Descrição** |
| --- | --- |
| <code>-help</code> | Obtenha uma lista das opções de linha de comando. |
| <code>-s</code> | Realize uma instalação silenciosa sem solicitações ao usuário. |
| <code>--check</code> | Verifica as permissões e o sistema operacional, mas não instala o agente. |

Os arquivos do Agente de Dependência são colocados nos diretórios a seguir:

| **Arquivos** | **Localidade** |
| --- | --- |
| Arquivos de núcleo | /opt/microsoft/dependency-agent |
| Arquivos de log | /var/opt/microsoft/dependency-agent/log |
| Arquivos de configuração | /etc/opt/microsoft/dependency-agent/config |
| Arquivos executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Arquivos de armazenamento binário | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Exemplos de script de instalação

Para implantar facilmente o Dependency Agent em vários servidores de uma vez, ele ajuda a usar um script. Você pode usar os exemplos de script a seguir para baixar e instalar o Dependency Agent no Windows ou no Linux.

#### <a name="powershell-script-for-windows"></a>Script do PowerShell para Windows

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Script de Shell para Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Desired State Configuration

Para implantar o Dependency Agent por meio da configuração de estado desejado, você pode usar o módulo xPSDesiredStateConfiguration e um pouco de código como o seguinte:

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```

### <a name="uninstall-the-dependency-agent"></a>Desinstalar o Dependency Agent

Use as seções a seguir para ajudá-lo a remover o Dependency Agent.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Desinstalar o Dependency Agent no Windows

O Agente de Dependência para Windows pode ser desinstalado por um administrador por meio do Painel de Controle.

Um administrador também pode executar %Programfiles%\Microsoft Agent\Uninstall.exe para desinstalar o Microsoft Dependency Agent.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Desinstalar o Dependency Agent no Linux

Para desinstalar completamente o Dependency Agent do Linux, você deve remover o próprio agente e o conector, que é instalado automaticamente com o agente. Você pode desinstalar ambos usando o seguinte comando único:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Pacotes de gerenciamento

Quando o Wire Data é ativado em um espaço de trabalho do Log Analytics, um pacote de gerenciamento de 300 KB é enviado a todos os servidores do Windows nesse espaço de trabalho. Se você estiver usando agentes do System Center Operations Manager em um [grupo de gerenciamento conectado](../platform/om-agents.md), o pacote de gerenciamento do Monitor de Dependência será implantado do System Center Operations Manager. Se os agentes estiverem conectados diretamente, Azure Monitor entregará o pacote de gerenciamento.

O pacote de gerenciamento chama-se Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Ele é gravado em: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. A fonte de dados usada pelo pacote de gerenciamento é: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Usando a solução

Use as informações a seguir para instalar e configurar a solução.

- A solução de Dados de Transmissão obtém dados de computadores que executam o Windows Server 2012 R2, Windows 8.1 e sistemas operacionais posteriores.
- O Microsoft .NET Framework 4.0 ou posterior é necessário nos computadores dos quais você deseja obter dados de transmissão.
- Adicione a solução de dados de transmissão ao seu espaço de trabalho Log Analytics usando o processo descrito em [Adicionar soluções de monitoramento do Galeria de soluções](solutions.md). Não é necessária nenhuma configuração.
- Se você desejar exibir os dados de transmissão de uma solução específica, será necessário ter a solução já adicionada ao seu workspace.

Depois de instalar os agentes e instalar a solução, o bloco Wire Data 2.0 aparece no workspace.

![Bloco do Wire Data](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Usando a solução Wire Data 2.0

Na página **Visão geral** do seu espaço de trabalho do Log Analytics no Portal do Azure, clique no bloco **Wire Data 2.0** para abrir o painel do Wire Data. O painel inclui as folhas na tabela a seguir. Cada folha lista os 10 principais itens que correspondem aos critérios da folha para o escopo e o intervalo de tempo especificados. É possível executar uma pesquisa de logs que retorna todos os registros clicando em **Ver todos** na parte inferior da folha ou clicando no cabeçalho de folha.

| **Folha** | **Descrição** |
| --- | --- |
| Agentes capturando tráfego de rede | Mostra o número de agentes que estão capturando tráfego de rede e lista os 10 principais computadores que estão capturando tráfego. Clique no número para executar uma pesquisa de logs para <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Clique em um computador na lista para executar uma pesquisa de logs retornando o número total de bytes capturados. |
| Sub-redes locais | Mostra o número de sub-redes locais que os agentes descobriram.  Clique no número para executar uma pesquisa de logs para <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code> que liste todas as sub-redes com o número de bytes enviados em cada uma. Clique em uma sub-rede na lista para executar uma pesquisa de logs retornando o número total de bytes enviados pela sub-rede. |
| Protocolos no nível do aplicativo | Mostra o número de protocolos no nível de aplicativo em uso, conforme detectados pelos agentes. Clique no número para executar uma pesquisa de logs para <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Clique em um protocolo para executar uma pesquisa de logs retornando o número total de bytes enviados usando o protocolo. |

![Painel do Wire Data](./media/wire-data/wire-data-dash.png)

Você pode usar a folha **Agentes capturando tráfego de rede** para determinar quanta largura de banda de rede está sendo consumida pelos computadores. Essa folha pode ajudá-lo a localizar facilmente o computador _chattiest_ no seu ambiente. Esses computadores podem estar sobrecarregados, operando de modo anormal ou usando mais recursos de rede que o normal.

![Captura de tela dos agentes capturando a folha de tráfego de rede no painel de Wire Data 2.0 mostrando a largura de banda de rede consumida por cada computador.](./media/wire-data/log-search-example01.png)

De modo parecido, você pode usar a folha **Sub-Redes Locais** para determinar quanto tráfego de rede está passando pelas sub-redes. Os usuários geralmente definem sub-redes em torno de áreas críticas para seus aplicativos. Esta folha oferece uma exibição dessas áreas.

![Captura de tela da folha de sub-redes locais no painel de Wire Data 2.0 mostrando a largura de banda de rede consumida por um LocalSubnet.](./media/wire-data/log-search-example02.png)

A folha **Protocolos em Nível de Aplicativo** é interessante porque é útil saber quais protocolos estão em uso. Por exemplo, você pode esperar que SSH não esteja em uso no seu ambiente de rede. Exibir informações disponíveis na folha pode rapidamente confirmar ou v. refutar suas expectativas.

![Captura de tela da folha protocolos de nível de aplicativo no painel Wire Data 2.0 mostrando a largura de banda de rede consumida por cada protocolo.](./media/wire-data/log-search-example03.png)

Também é útil saber se o tráfego do protocolo está aumentando ou diminuindo ao longo do tempo. Por exemplo, se a quantidade de dados que está sendo transmitida por um aplicativo estiver aumentando, isso pode ser algo a que você deve estar atento ou pode considerar importante.

## <a name="input-data"></a>Dados de entrada

A coleta de dados coleta metadados sobre o tráfego de rede usando os agentes que você habilitou. Cada agente envia dados aproximadamente a cada 15 segundos.

## <a name="output-data"></a>Dados de saída

Um registro com um tipo de _WireData_ é criado para cada tipo de dados de entrada. Os registros do WireData têm as propriedades mostradas na tabela a seguir:

| Propriedade | Descrição |
|---|---|
| Computador | Nome do computador em que os dados foram coletados |
| TimeGenerated | Hora do registro |
| LocalIP | Endereço IP do computador local |
| SessionState | Conectado ou desconectado |
| ReceivedBytes | Quantidade de bytes recebidos |
| ProtocolName | Nome do protocolo de rede usado |
| IPVersion | Versão IP |
| Direction | Entrada ou saída |
| MaliciousIP | Endereço IP de uma fonte mal-intencionada conhecida |
| Severity | Gravidade de suspeita de malware |
| RemoteIPCountry | País/região do endereço IP remoto |
| ManagementGroupName | Nome do grupo de gerenciamento do Operations Manager |
| SourceSystem | Fonte na qual o dados foram coletados |
| SessionStartTime | Hora de início da sessão |
| SessionEndTime | Hora de término da sessão |
| LocalSubnet | Sub-rede na qual o dados foram coletados |
| LocalPortNumber | Número da porta local |
| RemoteIP | Endereço IP remoto usado pelo computador remoto |
| RemotePortNumber | Número da porta usada pelo endereço IP remoto |
| SessionID | Um valor exclusivo que identifica a sessão de comunicação entre os dois endereços IP |
| SentBytes | Número de bytes enviados |
| TotalBytes | Número total de bytes enviados durante a sessão |
| ApplicationProtocol | Tipo de protocolo de rede usado   |
| ProcessID | ID de processo do Windows |
| ProcessName | Nome de arquivo e caminho do processo |
| RemoteIPLongitude | Valor de longitude do IP |
| RemoteIPLatitude | Valor de latitude do IP |

## <a name="next-steps"></a>Próximas etapas

- [Pesquise nos logs](../log-query/log-query-overview.md) para exibir registros detalhados da pesquisa de dados de transmissão.

