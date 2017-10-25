---
title: "Solução de Dados de Transmissão no Log Analytics | Microsoft Docs"
description: "Dados de transmissão são dados consolidados de rede e de desempenho recebidos de computadores com agentes do OMS, incluindo agentes do Operations Manager e agentes conectados com o Windows. Os dados de rede são combinados com os dados de log para ajudá-lo a correlacionar dados."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: banders
ms.openlocfilehash: ce8065d777bb315d4f9589d1b24a5152296facfe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="wire-data-20-preview-solution-in-log-analytics"></a>Solução Wire Data 2.0 (Versão Prévia) no Log Analytics

![Símbolo do Wire Data](./media/log-analytics-wire-data/wire-data2-symbol.png)

O Wire Data consiste em dados de desempenho e rede consolidados de computadores com agentes do OMS, incluindo agentes Operations Manager, conectados ao Windows e aos agentes do Linux. Os dados de rede são combinados a seus outros dados de log para ajudá-lo a correlacionar dados.

Além dos agentes do OMS, a solução Wire Data usa os agentes de Dependência da Microsoft que você instala em computadores na sua infraestrutura de TI. Os Agentes de Dependência monitoram dados de rede enviados de e para seus computadores para os níveis de rede 2 e 3 no [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), incluindo os diversos protocolos e portas usados. Os dados então são enviados para o Log Analytics usando agentes.

> [!NOTE]
> Você não pode adicionar a versão anterior da solução Wire Data a novos espaços de trabalho. Se você tiver a solução Wire Data original habilitada, poderá continuar a usá-la. No entanto, para usar Wire Data 2.0, você deve primeiro remover a versão original.

Por padrão, o Log Analytics coleta dados registrados para dados de CPU, memória, disco e desempenho de rede dos contadores internos do Windows. A coleta de dados de rede e de outros dados é feita em tempo real para cada agente, incluindo sub-redes e protocolos no nível de aplicativo usados pelo computador. É possível adicionar outros contadores de desempenho na página Configurações, na guia Logs.

Se você já tiver usado [sFlow](http://www.sflow.org/) ou outro software com o [protocolo NetFlow da Cisco](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), as estatísticas e os dados que você verá do Wire Data já serão conhecidos.

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

No entanto, já que você está exibindo metadados, eles não são necessariamente úteis para solução de problemas detalhada. Wire Data no Log Analytics não é uma captura completa dos dados da rede. Portanto, não se destina a uma solução de problemas em profundidade no nível de pacote. A vantagem de usar o agente, em comparação a outros métodos de coleta, é que você não precisa instalar dispositivos, reconfigurar os comutadores de rede nem realizar configurações complicadas. O Wire Data é simplesmente baseado em agente – você instala o agente em um computador e ele monitorará seu próprio tráfego de rede. Outra vantagem é quando você deseja monitorar cargas de trabalho em execução em provedores de nuvem, provedor de serviços de hospedagem ou no Microsoft Azure, em que o usuário não tem a camada de malha.

## <a name="connected-sources"></a>Fontes conectadas

O Wire Data obtém seus dados do Agente de Dependência da Microsoft. O Agente de Dependência depende do Agente do OMS para suas conexões ao Log Analytics. Isso significa que um servidor deve ter o Agente do OMS instalado e configurado primeiro e, em seguida, você instala o Agente de Dependência. A tabela a seguir descreve as fontes conectadas às quais a solução Wire Data dá suporte.

| **Fonte conectada** | **Com suporte** | **Descrição** |
| --- | --- | --- |
| Agentes do Windows | Sim | O Wire Data analisa e coleta dados de computadores de agente do Windows. <br><br> Além do [Agente do OMS](log-analytics-windows-agents.md), os agentes do Windows exigem o Microsoft Dependency Agent. Veja os [sistemas operacionais com suporte](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) para obter uma lista completa das versões de sistema operacional. |
| Agentes do Linux | Sim | O Wire Data analisa e coleta dados de computadores de agente do Linux.<br><br> Além do [Agente do OMS](log-analytics-linux-agents.md), os agentes do Linux exigem o Microsoft Dependency Agent. Veja os [sistemas operacionais com suporte](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) para obter uma lista completa das versões de sistema operacional. |
| Grupo de gerenciamento do System Center Operations Manager | Sim | O Wire Data analisa e coleta dados de agentes do Windows e do Linux em um [grupo de gerenciamento do System Center Operations Manager](log-analytics-om-agents.md) conectado. <br><br> Uma conexão direta do computador do agente do System Center Operations Manager para Log Analytics é necessária. Os dados são encaminhados do grupo de gerenciamento ao Log Analytics. |
| Conta de Armazenamento do Azure | Não | O Wire Data coleta dados de computadores do agente e, portanto, não há nenhum dado dele a ser coletado do Armazenamento do Azure. |

No Windows, o MMA (Microsoft Monitoring Agent) é usado pelo System Center Operations Manager e pelo Log Analytics para coletar e enviar dados. Dependendo do contexto, esse agente é chamado de Agente do System Center Operations Manager, Agente do OMS, Agente do Log Analytics, MMA ou Agente Direto. O System Center Operations Manager e o Log Analytics fornecem versões do MMA ligeiramente diferentes. Essas versões podem relatar para o System Center Operations Manager, para o Log Analytics ou para ambos.

No Linux, o Agente OMS para Linux coleta e envia os dados ao Log Analytics. Você pode usar o Wire Data em servidores com Agentes Diretos do OMS ou em servidores anexados ao Log Analytics por meio de grupos de gerenciamento do System Center Operations Manager.

Neste artigo, as referências a todos os agentes, sejam Linux ou Windows, sejam conectados a um grupo de gerenciamento do System Center Operations Manager ou diretamente ao Log Analytics são chamados de _agente do OMS_. Usaremos o nome de implantação específico do agente somente se ele for necessário para o contexto.

O Agente de Dependência não transmite todos os dados em si e não requer alterações a portas ou firewalls. Os dados no Wire Data sempre são transmitidos pelo agente do OMS ao Log Analytics, seja diretamente ou usando o Gateway do OMS.

![diagrama do agente](./media/log-analytics-wire-data/agents.png)

Se você for um usuário do System Center Operations Manager com um grupo de gerenciamento conectado ao Log Analytics:

- Se nenhuma configuração adicional é necessária quando os agentes do System Center Operations Manager podem acessar a Internet para conectarem-se ao Log Analytics.
- Você precisa configurar o Gateway do OMS para operar com o System Center Operations Manager quando os agentes do System Center Operations Manager não puderem acessar o Log Analytics pela Internet.

Se você estiver usando o Direct Agent, precisará configurar o próprio agente do OMS para conexão ao Log Analytics ou ao Gateway do OMS. Você pode baixar o Gateway do OMS no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Pré-requisitos

- Requer a oferta da solução [Insight and Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing).
- Se você estiver usando a versão anterior da solução Wire Data, deverá primeiro removê-la. No entanto, todos os dados capturados por meio de solução Wire Data original ainda estão disponível no Wire Data 2.0 e na pesquisa de log.
- São necessários privilégios de administrador para instalar ou desinstalar o Agente de Dependência.
- O Agente de Dependência deve ser instalado em um computador com um sistema operacional de 64 bits.

### <a name="operating-systems"></a>Sistemas operacionais

As seções a seguir listam os sistemas operacionais com suporte para o Dependency Agent. O Wire Data não dá suporte a arquiteturas de 32 bits de nenhum sistema operacional.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Área de trabalho do Windows

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux e Oracle Linux (com Kernel RHEL)

- Somente as versões de kernel padrão e Linux SMP têm suporte.
- Nenhuma distribuição do Linux dá suporte às versões de kernel não padrão, como PAE e Xen. Por exemplo, não há suporte para um sistema com a cadeia de caracteres de versão _2.6.16.21-0.8-xen_.
- Não há suporte para kernels personalizados, incluindo recompilações de kernels padrão.
- Não há suporte para o kernel CentOSPlus.
- O UEK (Unbreakable Enterprise Kernel) da Oracle é abordado em uma seção posterior deste artigo.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| **Versão do SO** | **Versão do kernel** |
| --- | --- |
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| **Versão do SO** | **Versão do kernel** |
| --- | --- |
| 6,0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6,8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5

| **Versão do SO** | **Versão do kernel** |
| --- | --- |
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398 <br> 2.6.18-400 <br>2.6.18-402 <br>2.6.18-404 <br>2.6.18-406 <br> 2.6.18-407 <br> 2.6.18-408 <br> 2.6.18-409 <br> 2.6.18-410 <br> 2.6.18-411 <br> 2.6.18-412 <br> 2.6.18-416 <br> 2.6.18-417 <br> 2.6.18-419 |

#### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux com Unbreakable Enterprise Kernel

#### <a name="oracle-linux-6"></a>Oracle Linux 6

| **Versão do SO** | **Versão do kernel** |
| --- | --- |
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| **Versão do SO** | **Versão do kernel** |
| --- | --- |
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11

| **Versão do SO** | **Versão do kernel** |
| --- | --- |
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10

| **Versão do SO** | **Versão do kernel** |
| --- | --- |
| 10 SP4 | 2.6.16.60 |

#### <a name="dependency-agent-downloads"></a>Downloads do Agente de Dependência

| **Arquivo** | **SO** | **Versão** | **SHA-256** |
| --- | --- | --- | --- |
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |



## <a name="configuration"></a>Configuração

Execute as seguintes etapas para configurar a solução Wire Data para seus espaços de trabalho.

1. Habilite a solução Log Analytics da atividade no [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) ou usando o processo descrito em [Adicionar soluções Log Analytics por meio da Galeria de soluções](log-analytics-add-solutions.md).
2. Instale o Agente de Dependência em cada computador em que você deseje obter dados. O Agente de Dependência pode monitorar conexões a vizinhos imediatos, portanto, talvez não seja necessário ter um agente em cada computador.

### <a name="install-the-dependency-agent-on-windows"></a>Instalar o Agente de Dependência no Windows

São necessários privilégios de administrador para instalar ou desinstalar o agente.

O Agente de Dependência é instalado em computadores que executam o Windows por meio do InstallDependencyAgent-Windows.exe. Se você executar o arquivo executável sem opções, ele iniciará um assistente que você poderá seguir para executar a instalação interativamente.

Use as etapas a seguir para instalar o Agente de Dependência em cada computador com o Windows:

1. Instalar o Agente OMS usando as instruções em [Conectar computadores Windows ao serviço do Log Analytics no Azure](log-analytics-windows-agents.md).
2. Baixe o agente do Windows usando o link na seção anterior e, em seguida, executando-o usando o seguinte comando: InstallDependencyAgent-Windows.exe
3. Acompanhe o assistente para instalar o agente.
4. Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Em Agentes do Windows, o diretório de log será %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Linha de comando do Windows

Use as opções da tabela a seguir para instalar a partir de uma linha de comando. Para ver uma lista dos sinalizadores de instalação, execute o instalador usando o sinalizador /? da seguinte maneira.

InstallDependencyAgent-Windows.exe /?

| **Sinalizador** | **Descrição** |
| --- | --- |
| <code>/?</code> | Obtenha uma lista das opções de linha de comando. |
| <code>/S</code> | Realize uma instalação silenciosa sem solicitações ao usuário. |

Os arquivos do Agente de Dependência do Windows são colocados em C:\Program Files\Microsoft Dependency Agent por padrão.

### <a name="install-the-dependency-agent-on-linux"></a>Instalar o Agente de Dependência no Linux

O acesso root é necessário para instalar ou configurar o agente.

O Agente de Dependência é instalado em computadores com o Linux por meio do InstallDependencyAgent-Linux64.bin, um script de shell com um binário de extração automática. Você pode executar o arquivo usando _sh_ ou adicionar permissões de execução ao próprio arquivo.

Use as seguintes etapas para instalar o Dependency Agent em cada computador com o Linux:

1. Instale o Agente OMS usando as instruções em [Coletar e gerenciar dados de computadores com o Linux](log-analytics-agent-linux.md).
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

Os arquivos do Dependency Agent são colocados nos diretórios a seguir:

| **Arquivos** | **Localidade** |
| --- | --- |
| Arquivos de núcleo | /opt/microsoft/dependency-agent |
| Arquivos de log | /var/opt/microsoft/dependency-agent/log |
| Arquivos de configuração | /etc/opt/microsoft/dependency-agent/config |
| Arquivos executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Arquivos de armazenamento binário | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Exemplos de script de instalação

Para implantar facilmente o agente de dependência em vários servidores simultaneamente, convém usar um script. Você pode usar os exemplos de script a seguir para baixar e instalar o agente de dependência no Windows ou Linux.

#### <a name="powershell-script-for-windows"></a>Script do PowerShell para Windows

```PowerShell

Invoke-WebRequest &quot;https://aka.ms/dependencyagentwindows&quot; -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Script de Shell para Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Configuração de estado desejado

Para implantar o agente de dependência por meio da Desired State Configuration, você pode usar o módulo xPSDesiredStateConfiguration e um pouco de código semelhante ao seguinte:

```
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = &quot;C:\InstallDependencyAgent-Windows.exe&quot;



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = &quot;https://aka.ms/dependencyagentwindows&quot;

        DestinationPath = $DAPackageLocalPath

        DependsOn = &quot;[Package]OI&quot;

    }

    xPackage DA

    {

        Ensure=&quot;Present&quot;

        Name = &quot;Dependency Agent&quot;

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = &quot;&quot;

        InstalledCheckRegKey = &quot;HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent&quot;

        InstalledCheckRegValueName = &quot;DisplayName&quot;

        InstalledCheckRegValueData = &quot;Dependency Agent&quot;

    }

}

```
### <a name="uninstall-the-dependency-agent"></a>Desinstalar o Agente de Dependência

Use as seções a seguir para ajudá-lo a remover o Agente de Dependência.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Desinstalar o Agente de Dependência no Windows

O Agente de Dependência para Windows pode ser desinstalado por um administrador por meio do Painel de Controle.

Um administrador também pode executar %Programfiles%\Microsoft Agent\Uninstall.exe para desinstalar o Agente de Dependência.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Desinstalar o Agente de Dependência no Linux

Para desinstalar completamente o Agente de Dependência do Linux, você deve remover o agente propriamente dito e o conector instalado automaticamente com o agente. Você pode desinstalar ambos usando o seguinte comando único:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Pacotes de gerenciamento

Quando o Wire Data é ativado em um espaço de trabalho do Log Analytics, um pacote de gerenciamento de 300 KB é enviado a todos os servidores do Windows nesse espaço de trabalho. Se você estiver usando agentes do System Center Operations Manager em um [grupo de gerenciamento conectado](log-analytics-om-agents.md), o pacote de gerenciamento do Monitor de Dependência será implantado do System Center Operations Manager. Se os agentes forem conectados diretamente, o Log Analytics fornecerá o pacote de gerenciamento.

O pacote de gerenciamento chama-se Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Ele é gravado em: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. A fonte de dados usada pelo pacote de gerenciamento é: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Usando a solução

**Instalando e configurando a solução**

Use as informações a seguir para instalar e configurar a solução.

- A solução de Dados de Transmissão obtém dados de computadores que executam o Windows Server 2012 R2, Windows 8.1 e sistemas operacionais posteriores.
- O Microsoft .NET Framework 4.0 ou posterior é necessário nos computadores dos quais você deseja obter dados de transmissão.
- Adicione a solução de Dados de Transmissão ao seu espaço de trabalho do Log Analytics usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md). Não é necessária nenhuma configuração.
- Se você desejar exibir os dados de transmissão de uma solução específica, será necessário ter a solução já adicionada ao seu espaço de trabalho.

Depois de instalar os agentes e instalar a solução, o bloco Wire Data 2.0 aparece no espaço de trabalho.

> [!NOTE]
> No momento, você deve usar o portal do OMS para exibir dados de transmissão. Você não pode usar o portal do Azure para exibir o Wire Data.

![Bloco do Wire Data](./media/log-analytics-wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Usando a solução Wire Data 2.0

No portal do OMS, clique no bloco **Wire Data 2.0** para abrir o painel Wire Data. O painel inclui as folhas na tabela a seguir. Cada folha lista os 10 principais itens que correspondem aos critérios da folha para o escopo e o intervalo de tempo especificados. É possível executar uma pesquisa de logs que retorna todos os registros clicando em **Ver todos** na parte inferior da folha ou clicando no cabeçalho de folha.

| **Folha** | **Descrição** |
| --- | --- |
| Agentes capturando tráfego de rede | Mostra o número de agentes que estão capturando tráfego de rede e lista os 10 principais computadores que estão capturando tráfego. Clique no número para executar uma pesquisa de logs para <code>Type:WireData &#124; measure Sum(TotalBytes) by Computer &#124; top 500000</code>. Clique em um computador na lista para executar uma pesquisa de logs retornando o número total de bytes capturados. |
| Sub-redes locais | Mostra o número de sub-redes locais que os agentes descobriram.  Clique no número para executar uma pesquisa de logs para <code>Type:WireData &#124; Measure Sum(TotalBytes) by LocalSubnet</code> que liste todas as sub-redes com o número de bytes enviados em cada uma. Clique em uma sub-rede na lista para executar uma pesquisa de logs retornando o número total de bytes enviados pela sub-rede. |
| Protocolos no nível do aplicativo | Mostra o número de protocolos no nível de aplicativo em uso, conforme detectados pelos agentes. Clique no número para executar uma pesquisa de logs para <code>Type:WireData &#124; Measure Sum(TotalBytes) by ApplicationProtocol</code>. Clique em um protocolo para executar uma pesquisa de logs retornando o número total de bytes enviados usando o protocolo. |

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Painel do Wire Data](./media/log-analytics-wire-data/wire-data-dash.png)

Você pode usar a folha **Agentes capturando tráfego de rede** para determinar quanta largura de banda de rede está sendo consumida pelos computadores. Essa folha pode ajudá-lo a localizar facilmente o computador _chattiest_ no seu ambiente. Esses computadores podem estar sobrecarregados, operando de modo anormal ou usando mais recursos de rede que o normal.

![exemplo de pesquisa de logs](./media/log-analytics-wire-data/log-search-example01.png)

De modo parecido, você pode usar a folha **Sub-Redes Locais** para determinar quanto tráfego de rede está passando pelas sub-redes. Os usuários geralmente definem sub-redes em torno de áreas críticas para seus aplicativos. Esta folha oferece uma exibição dessas áreas.

![exemplo de pesquisa de logs](./media/log-analytics-wire-data/log-search-example02.png)

A folha **Protocolos em Nível de Aplicativo** é interessante porque é útil saber quais protocolos estão em uso. Por exemplo, você pode esperar que SSH não esteja em uso no seu ambiente de rede. Exibir informações disponíveis na folha pode rapidamente confirmar ou v. refutar suas expectativas.

![exemplo de pesquisa de logs](./media/log-analytics-wire-data/log-search-example03.png)

Neste exemplo, você pode analisar em detalhes SSH para ver quais computadores estão usando o SSH e muitos outros detalhes de comunicação.

![resultados da pesquisa sh](./media/log-analytics-wire-data/ssh-details.png)

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
| Direção | Entrada ou saída |
| MaliciousIP | Endereço IP de uma fonte mal-intencionada conhecida |
| Severity | Gravidade de suspeita de malware |
| RemoteIPCountry | País do endereço IP remoto |
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

- [Pesquise nos logs](log-analytics-log-searches.md) para exibir registros detalhados da pesquisa de dados de transmissão.
