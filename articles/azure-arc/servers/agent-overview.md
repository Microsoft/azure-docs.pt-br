---
title: Visão geral do agente do computador conectado
description: Este artigo fornece uma visão geral detalhada do agente de servidores habilitados para Arc do Azure disponível, que dá suporte ao monitoramento de máquinas virtuais hospedadas em ambientes híbridos.
ms.date: 03/25/2021
ms.topic: conceptual
ms.openlocfilehash: 2db1758240dca448409af9f4ec00c01d684c920a
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609217"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Visão geral do agente de servidores habilitados para Arc do Azure

O agente de máquina conectado de servidores habilitados para Arc do Azure permite que você gerencie seus computadores Windows e Linux hospedados fora do Azure em sua rede corporativa ou em outro provedor de nuvem. Este artigo fornece uma visão geral detalhada do agente, dos requisitos do sistema e da rede e dos diferentes métodos de implantação.

>[!NOTE]
>A partir da versão geral dos servidores habilitados para Arc do Azure em setembro de 2020, todas as versões de pré-lançamento do agente do computador conectado do Azure (agentes com versões inferiores a 1,0) estão sendo **preteridas** até **2 de fevereiro de 2021**.  Este período de tempo permite que você atualize para a versão 1,0 ou superior antes que os agentes de pré-lançamento não possam mais se comunicar com o serviço de servidores habilitados para Arc do Azure.

## <a name="agent-component-details"></a>Detalhes do componente do agente

:::image type="content" source="media/agent-overview/connected-machine-agent.png" alt-text="Visão geral do agente de servidores habilitados para Arc." border="false":::

O pacote do agente do computador conectado do Azure contém vários componentes lógicos, que são agrupados.

* O serviço de metadados de instância híbrida (HIMDS) gerencia a conexão com o Azure e a identidade do Azure do computador conectado.

* O agente de configuração de convidado fornece In-Guest funcionalidade de configuração de convidado e política, como avaliar se o computador está em conformidade com as políticas necessárias.

    Observe o seguinte comportamento com Azure Policy [configuração de convidado](../../governance/policy/concepts/guest-configuration.md) para um computador desconectado:

    * Uma atribuição de política de configuração de convidado que se destina a computadores desconectados não é afetada.
    * A atribuição de convidado é armazenada localmente por 14 dias. Dentro do período de 14 dias, se o agente da máquina conectada se reconectar ao serviço, as atribuições de política serão reaplicadas.
    * As atribuições são excluídas após 14 dias e não são reatribuídas à máquina após o período de 14 dias.

* O agente de extensão gerencia extensões de VM, incluindo instalar, desinstalar e atualizar. As extensões são baixadas do Azure e copiadas para a `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` pasta no Windows e para o Linux para o `/opt/GC_Ext/downloads` . No Windows, a extensão é instalada no caminho a seguir `%SystemDrive%\Packages\Plugins\<extension>` e, no Linux, a extensão é instalada no `/var/lib/waagent/<extension>` .

## <a name="instance-metadata"></a>Metadados da instância

As informações de metadados sobre o computador conectado são coletadas após o agente do computador conectado ser registrado em servidores habilitados para Arc. Especificamente:

* Nome, tipo e versão do sistema operacional
* Nome do computador
* FQDN (nome de domínio totalmente qualificado) do computador
* Versão do agente do Connected Machine
* Active Directory e FQDN (nome de domínio totalmente qualificado) do DNS
* UUID (ID DO BIOS)
* Pulsação do agente da máquina conectada
* Versão do agente do Connected Machine
* Chave pública para identidade gerenciada
* Detalhes e status de conformidade da política (se estiver usando Azure Policy políticas de configuração de convidado)

As informações de metadados a seguir são solicitadas pelo agente do Azure:

* Local do recurso (região)
* ID da máquina virtual
* Marcações
* Azure Active Directory certificado de identidade gerenciado
* Atribuições de política de configuração de convidado
* Solicitações de extensão – instalar, atualizar e excluir.

## <a name="download-agents"></a>Baixar agentes

Você pode baixar o pacote do agente do Azure Connected Machine para Windows e Linux dos locais listados abaixo.

* [Pacote do Windows Installer do agente do Windows](https://aka.ms/AzureConnectedMachineAgent) do Centro de Download da Microsoft.

* O pacote do agente do Linux é distribuído do [repositório de pacotes](https://packages.microsoft.com/) da Microsoft usando o formato de pacote preferencial para a distribuição (.RPM ou .DEB).

O agente do Azure Connected Machine para Windows e Linux pode ser atualizado para a versão mais recente manual ou automaticamente dependendo de suas necessidades. Para saber mais, clique [aqui](manage-agent.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="supported-environments"></a>Ambientes compatíveis

Os servidores habilitados para Arc oferecem suporte à instalação do agente do computador conectado em qualquer servidor físico e máquina virtual hospedada *fora* do Azure. Isso inclui máquinas virtuais em execução em plataformas como VMware, Azure Stack HCI e em outros ambientes de nuvem. Os servidores habilitados para ARC não dão suporte à instalação do agente em máquinas virtuais em execução no Azure, ou máquinas virtuais em execução no Hub de Azure Stack ou Azure Stack Edge, pois já estão modeladas como VMs do Azure.

### <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

Há suporte oficial para as seguintes versões do sistema operacional Windows e Linux para o agente do Azure Connected Machine:

- Windows Server 2008 R2, Windows Server 2012 R2 e superior (incluindo Server Core)
- Ubuntu 16, 4 e 18, 4 LTS (x64)
- CentOS Linux 7 (x64)
- SLES (SUSE Linux Enterprise Server) 15 (x64)
- RHEL (Red Hat Enterprise Linux) 7 (x64)
- Amazon Linux 2 (x64)
- Oracle Linux 7

> [!WARNING]
> O nome do host do Linux ou do computador Windows não pode usar nenhuma das palavras reservadas nem marcas no nome. Caso contrário, a tentativa de registrar o computador conectado com o Azure falhará. Confira [Resolver erros de nome de recurso reservado](../../azure-resource-manager/templates/error-reserved-resource-name.md) para obter uma lista das palavras reservadas.

### <a name="required-permissions"></a>Permissões necessárias

* Para carregar computadores, você é membro da função de [colaborador](../../role-based-access-control/built-in-roles.md#contributor) ou **integração do computador conectado do Azure** no grupo de recursos.

* Para ler, modificar e excluir um computador, você é membro da função de **administrador de recursos do computador conectado do Azure** no grupo de recursos.

* Para selecionar um grupo de recursos na lista suspensa ao usar o método **Generate script** , no mínimo você é um membro da função [leitor](../../role-based-access-control/built-in-roles.md#reader) para esse grupo de recursos.

### <a name="azure-subscription-and-service-limits"></a>Limites de serviço e assinatura do Azure

Antes de configurar seus computadores com os servidores habilitados para Arc do Azure, examine os limites de [assinatura](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) Azure Resource Manager e [os limites de grupo de recursos](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) para planejar o número de computadores a serem conectados.

Os servidores habilitados para Arc do Azure dão suporte a até 5.000 instâncias de computador em um grupo de recursos.

### <a name="transport-layer-security-12-protocol"></a>Protocolo de segurança de camada de transporte 1,2

Para garantir a segurança de dados em trânsito para o Azure, incentivamos você a configurar o computador para usar o protocolo TLS 1.2. Constatou-se que versões mais antigas do protocolo TLS/protocolo SSL eram vulneráveis e embora elas ainda funcionem no momento para permitir a compatibilidade com versões anteriores, elas **não são recomendadas**.

|Plataforma/linguagem | Suporte | Mais informações |
| --- | --- | --- |
|Linux | Distribuições do Linux tendem a depender do [OpenSSL](https://www.openssl.org) para suporte a TLS 1.2. | Verifique as [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) para confirmar a sua versão do OpenSSL é suportada.|
| Windows Server 2012 R2 e versões posteriores | Suporte e habilitado por padrão. | Para confirmar que você ainda está usando o [as configurações padrão](/windows-server/security/tls/tls-registry-settings).|

### <a name="networking-configuration"></a>Configuração de rede

O agente do Connected Machine para Linux e Windows comunica a saída com segurança ao Azure Arc pela porta TCP 443. Se o computador se conectar por meio de um firewall ou servidor proxy para se comunicar pela Internet, examine o seguinte para entender os requisitos de configuração de rede.

> [!NOTE]
> Os servidores habilitados para ARC não dão suporte ao uso de um [Gateway de log Analytics](../../azure-monitor/agents/gateway.md) como proxy para o agente de computador conectado.
>

Se a conectividade de saída estiver restrita por seu firewall ou servidor proxy, verifique se as URLs listadas abaixo não estão bloqueadas. Quando você permite apenas os intervalos IP ou nomes de domínio necessários para que o agente se comunique com o serviço, é necessário permitir o acesso às seguintes marcas de serviço e URLs.

Marcas de serviço:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure

URLs:

| recurso de agente | Descrição |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`login.microsoftonline.com`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`*.guestconfiguration.azure.com` |Configuração de convidado|
|`*.his.arc.azure.com`|Serviço de identidade híbrida|
|`www.office.com`|Office 365|

Os agentes de visualização (versão 0,11 e inferior) também exigem acesso às seguintes URLs:

| recurso de agente | Descrição |
|---------|---------|
|`agentserviceapi.azure-automation.net`|Configuração de convidado|
|`*-agentservice-prod-1.azure-automation.net`|Configuração de convidado|

Para obter uma lista de endereços IP para cada tag de serviço/região, confira o arquivo JSON – [Intervalos de IP do Azure e marcas de serviço – nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519). A Microsoft publica atualizações semanais que contêm cada serviço do Azure e os intervalos de IP que ele usa. Essas informações no arquivo JSON são a lista pontual atual dos intervalos de IP que correspondem a cada marca de serviço. Os endereços IP estão sujeitos a alterações. Se os intervalos de endereço IP forem necessários para sua configuração de firewall, a Marca de Serviço **AzureCloud** deverá ser usada para permitir o acesso a todos os serviços do Azure. Não desabilite o monitoramento de segurança ou a inspeção dessas URLs. Permita-os como você faria com outro tráfego de Internet.

Para obter mais informações, consulte [visão geral das marcas de serviço](../../virtual-network/service-tags-overview.md).

### <a name="register-azure-resource-providers"></a>Registrar provedores de recursos do Azure

Os servidores habilitados do Azure Arc dependem dos seguintes provedores de recursos do Azure na sua assinatura para usar esse serviço:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

Se não estiverem registrados, você poderá registrá-los usando os seguintes comandos:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

CLI do Azure:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Você também pode registrar os provedores de recursos no portal do Azure seguindo as etapas no [portal do Azure](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="installation-and-configuration"></a>Instalação e configuração

Conectar computadores em seu ambiente híbrido diretamente com o Azure pode ser feito usando métodos diferentes, dependendo das suas necessidades. A tabela a seguir realça cada método para determinar qual funciona melhor para sua organização.

> [!IMPORTANT]
> O agente do computador conectado não pode ser instalado em uma máquina virtual do Windows do Azure. Se você tentar, a instalação detectará isso e reverterá.

| Método | Descrição |
|--------|-------------|
| Interativamente | Instalar manualmente o agente em um número único ou pequeno de computadores seguindo as etapas em [Conectar computadores do portal do Azure](onboard-portal.md).<br> No portal do Azure, você pode gerar um script e executá-lo no computador para automatizar as etapas de instalação e configuração do agente.|
| Em escala | Instalar e configurar o agente para vários computadores seguindo [Conectar computadores usando uma Entidade de Serviço](onboard-service-principal.md).<br> Esse método cria uma entidade de serviço para conectar computadores de maneira não interativa.|
| Em escala | Instale e configure o agente para vários computadores seguindo o método [Como usar o DSC do Windows PowerShell](onboard-dsc.md).<br> Esse método usa uma entidade de serviço para conectar computadores de maneira não interativa com o DSC do PowerShell. |

## <a name="connected-machine-agent-technical-overview"></a>Visão geral técnica do agente de computador conectado

### <a name="windows-agent-installation-details"></a>Detalhes de instalação do agente Windows

O agente do Connected Machine para Windows pode ser instalado usando um destes três métodos:

* Clique duas vezes no arquivo `AzureConnectedMachineAgent.msi`.
* Manualmente, ao executar o pacote do Windows Installer `AzureConnectedMachineAgent.msi` do Shell de comando.
* De uma sessão do PowerShell usando um método com script.

Depois de instalar o agente de máquina conectado para Windows, as seguintes alterações de configuração em todo o sistema são aplicadas.

* As pastas de instalação a seguir são criadas durante a configuração.

    |Pasta |Descrição |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |Caminho de instalação padrão contendo os arquivos de suporte do agente.|
    |%ProgramData%\AzureConnectedMachineAgent |Contém os arquivos de configuração do agente.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |Contém os tokens adquiridos.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Contém o arquivo de configuração do agente `agentconfig.json` com as suas informações de registro do serviço.|
    |%ProgramFiles%\ArcConnectedMachineAgent\ExtensionService\GC | Caminho de instalação que contém os arquivos do agente de configuração do convidado. |
    |%ProgramData%\GuestConfig |Contém as políticas (aplicadas) do Azure.|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads | As extensões são baixadas do Azure e copiadas aqui.|

* Os seguintes serviços do Windows são criados no computador de destino durante a instalação do agente.

    |Nome do serviço |Nome de exibição |Nome do processo |Descrição |
    |-------------|-------------|-------------|------------|
    |himds |Serviço de Metadados de Instância do Azure Híbrido |himds |Esse serviço implementa o serviço de metadados de instância do Azure (IMDS) para gerenciar a conexão com o Azure e a identidade do Azure do computador conectado.|
    |GCArcService |Serviço de arco de configuração de convidado |gc_service |Monitora a configuração de estado desejado da máquina.|
    |ExtensionService |Serviço de extensão de configuração de convidado | gc_service |Instala as extensões necessárias para direcionar o computador.|

* As variáveis ambientais a seguir são criadas durante a instalação do agente.

    |Nome |Valor padrão |Descrição |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Há vários arquivos de log disponíveis para solução de problemas. Eles são descritos na tabela a seguir.

    |Log |Descrição |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Registra os detalhes do serviço HIMDS (agentes) e a interação com o Azure.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Contém a saída dos comandos da ferramenta azcmagent, quando o argumento verbose (-v) é usado.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |Registra detalhes da atividade de serviço de DSC,<br> em particular, a conectividade entre o serviço HIMDS e o Azure Policy.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |Registra os detalhes sobre o log detalhado e a telemetria do serviço de DSC.|
    |Ext_mgr_logs%ProgramData%\GuestConfig\|Registra os detalhes sobre o componente do agente de extensão.|
    |Extension_logs%ProgramData%\GuestConfig\\<Extension>|Registra os detalhes da extensão instalada.|

* O grupo de segurança local **Aplicativos de extensão de agente híbridos** é criado.

* Durante a desinstalação do agente, os artefatos a seguir não são removidos.

    * %ProgramData%\AzureConnectedMachineAgent\Log
    * %ProgramData%\AzureConnectedMachineAgent e subdiretórios
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Detalhes de instalação do agente Linux

O agente do Connected Machine para Linux é fornecido no formato de pacote preferencial para a distribuição (.RPM ou .DEB) hospedada no [repositório de pacotes](https://packages.microsoft.com/) da Microsoft. O agente é instalado e configurado com o pacote de script do Shell [Install_linux_azcmagent.sh](https://aka.ms/azcmagent).

Depois de instalar o agente de máquina conectado para Linux, as seguintes alterações de configuração em todo o sistema são aplicadas.

* As pastas de instalação a seguir são criadas durante a configuração.

    |Pasta |Descrição |
    |-------|------------|
    |/var/opt/azcmagent/ |Caminho de instalação padrão contendo os arquivos de suporte do agente.|
    |/opt/azcmagent/ |
    |GC_Ext/opt/ | Caminho de instalação que contém os arquivos do agente de configuração do convidado.|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |Contém os tokens adquiridos.|
    |/var/lib/GuestConfig |Contém as políticas (aplicadas) do Azure.|
    |/opt/GC_Ext/downloads|As extensões são baixadas do Azure e copiadas aqui.|

* Os daemons a seguir são criados no computador de destino durante a instalação do agente.

    |Nome do serviço |Nome de exibição |Nome do processo |Descrição |
    |-------------|-------------|-------------|------------|
    |himdsd. Service |Serviço do agente de computador conectado do Azure |himds |Esse serviço implementa o serviço de metadados de instância do Azure (IMDS) para gerenciar a conexão com o Azure e a identidade do Azure do computador conectado.|
    |gcad.servce |Serviço de arco do GC |gc_linux_service |Monitora a configuração de estado desejado da máquina. |
    |extd. Service |Serviço de extensão |gc_linux_service | Instala as extensões necessárias para direcionar o computador.|

* Há vários arquivos de log disponíveis para solução de problemas. Eles são descritos na tabela a seguir.

    |Log |Descrição |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Registra os detalhes do serviço HIMDS (agentes) e a interação com o Azure.|
    |/var/opt/azcmagent/log/azcmagent.log |Contém a saída dos comandos da ferramenta azcmagent, quando o argumento verbose (-v) é usado.|
    |/opt/logs/dsc.log |Registra detalhes da atividade de serviço de DSC,<br> em particular, a conectividade entre o serviço do himds e o Azure Policy.|
    |/opt/logs/dsc.telemetry.txt |Registra os detalhes sobre o log detalhado e a telemetria do serviço de DSC.|
    |ext_mgr_logs/var/lib/GuestConfig/ |Registra os detalhes sobre o componente do agente de extensão.|
    |extension_logs/var/lib/GuestConfig/|Registra os detalhes da extensão instalada.|

* As variáveis ambientais a seguir são criadas durante a instalação do agente. Estas variáveis são definidas em `/lib/systemd/system.conf.d/azcmagent.conf`.

    |Nome |Valor padrão |Descrição |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Durante a desinstalação do agente, os artefatos a seguir não são removidos.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>Próximas etapas

* Para começar a avaliar os servidores habilitados para Arc do Azure, siga o artigo [conectar computadores híbridos ao Azure por meio do portal do Azure](onboard-portal.md).

* Informações de solução de problemas podem ser encontradas no [guia solucionar problemas do agente do computador conectado](troubleshoot-agent-onboard.md).
