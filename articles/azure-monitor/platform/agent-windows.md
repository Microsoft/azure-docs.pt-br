---
title: Conectar computadores Windows ao Azure Log Analytics | Microsoft Docs
description: Este artigo descreve como conectar computadores Windows hospedados em outras nuvens ou locais no Log Analytics com o MMA (Microsoft Monitoring Agent).
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: magoedte
ms.openlocfilehash: c7031e54c354392379fee83dbf2a777ba726c5e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60777317"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Conectar computadores Windows para o Azure Monitor

Para monitorar e gerenciar máquinas virtuais ou computadores físicos em seu datacenter local ou em outro ambiente de nuvem com o Azure Monitor, você precisará implantar o agente do Log Analytics (também conhecido como o agente MMA (Microsoft Monitoring)) e configurá-lo para relatar a um ou mais espaços de trabalho do Log Analytics. Adicionalmente, o agente fornece suporte à função do Hybrid Runbook Worker para a Automação do Azure.  

Em um computador Windows monitorado, o agente está listado como o serviço do Microsoft Monitoring Agent. O serviço do Microsoft Monitoring Agent coleta eventos de arquivos de log e log de eventos do Windows, dados de desempenho e outra telemetria. Mesmo quando o agente é capaz de se comunicar com o Azure Monitor, ele relata a, o agente continuará a ser executado e coloca os dados coletados no disco do computador monitorado. Quando a conexão é restaurada, o serviço do Microsoft Monitoring Agent envia os dados coletados para o serviço.

O agente pode ser instalado usando um dos seguintes métodos. A maioria das instalações usa uma combinação desses métodos para instalar diferentes conjuntos de computadores, conforme apropriado.  Detalhes sobre como usar cada método são fornecidos posteriormente neste artigo.

* Instalação manual. O programa de configuração é executado manualmente no computador usando o assistente de configuração, a partir da linha de comando ou implantado, usando uma ferramenta de distribuição de software existente.
* DSC (Desired State Configuration) na Automação do Azure. Usar o DSC na Automação do Azure com um script para computadores Windows já implantado em seu ambiente.  
* Script do PowerShell.
* Modelo do Resource Manager para máquinas virtuais executando o Windows local no Azure Stack.  

>[!NOTE]
>Central de segurança do Azure (ASC) depende do Microsoft Monitoring Agent (também conhecido como o agente do Windows do Log Analytics) e instalará e configurá-lo para relatar para um espaço de trabalho do Log Analytics como parte da sua implantação. ASC inclui uma opção de provisionamento automático que permite a instalação automática do agente do Windows do Log Analytics em todas as VMs em sua assinatura e o configura para relatar para um espaço de trabalho específico. Para obter mais informações sobre essa opção, consulte [habilitar o provisionamento automático de agente do Log Analytics](../../security-center/security-center-enable-data-collection.md#enable-automatic-provisioning-of-microsoft-monitoring-agent-).
>

Para entender a configuração com suporte, revise [suporte para sistemas operacionais Windows](log-analytics-agent.md#supported-windows-operating-systems) e [configuração de firewall de rede](log-analytics-agent.md#network-firewall-requirements).

## <a name="obtain-workspace-id-and-key"></a>Obter a ID do workspace e a chave
Antes de instalar o agente do Log Analytics para Windows, você precisa da ID do espaço de trabalho e da chave para o espaço de trabalho do Log Analytics.  Essas informações são necessárias durante a instalação de cada método de instalação para configurar corretamente o agente e garantir que ele possa se comunicar com êxito com o Azure Monitor no Azure comercial e de nuvem do governo dos EUA.  

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
2. Na lista de workspaces do Log Analytics, selecione o workspace que pretende configurar o agente a qual relatar.
3. Selecione **Configurações avançadas**.<br><br> ![Configurações avançadas do Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Selecione **Fontes Conectadas** e depois **Servidores Windows**.   
5. Copie e cole em seu editor favorito, a **ID do workspace** e a **chave primária**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Configurar o agente para usar o TLS 1.2
Para configurar o uso do protocolo9 [TLS 1.2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) para comunicação entre o agente do Windows e o serviço Log Analytics, você pode seguir as etapas abaixo para habilitar antes que o agente seja instalado na máquina virtual ou posteriormente.   

1. Localize a seguinte subchave do Registro: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Crie uma subchave em **Protocolos** para o TLS 1.2 **HKLM\System\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. Criar uma subchave **Cliente** na subchave do protocolo TLS 1.2 que você criou anteriormente. Por exemplo, **HKLM\System\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Crie os seguintes valores DWORD em **HKLM\System\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**:

    * **Habilitado** [Valor = 1]
    * **DisabledByDefault** [Valor = 0]  

Configurar o .NET Framework 4.6 ou posterior para dar suporte à criptografia segura, uma vez que, por padrão, é desabilitado. A [criptografia forte](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) usa mais protocolos de rede seguros como TLS 1.2 e bloqueia os protocolos que não são seguros. 

1. Localize a seguinte subchave do Registro: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**.  
2. Crie o valor DWORD **SchUseStrongCrypto** nessa subchave com um valor de **1**.  
3. Localize a seguinte subchave do Registro: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**.  
4. Crie o valor DWORD **SchUseStrongCrypto** nessa subchave com um valor de **1**. 
5. Reinicie o sistema para que as configurações entrem em vigor. 

## <a name="install-the-agent-using-setup-wizard"></a>Instalar o agente usando o assistente de instalação
As etapas a seguir instalar e configurar o agente do Log Analytics na nuvem do Azure e o Azure governamental usando o Assistente de instalação para o agente no seu computador. Se você quiser saber como configurar o agente para também relatar grupo de gerenciamento System Center Operations Manager, consulte [ implantar o agente do Operations Manager com o Assiste de Configuração do Agente](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. No seu espaço de trabalho do Log Analytics, na página **Servidores Windows** que você navegou anteriormente, selecione a versão apropriada de **Fazer o download do Agente para Windows** para baixar dependendo da arquitetura do processador do sistema operacional Windows.   
2. Execute a Instalação para instalar o agente no seu computador.
2. Na página de **Boas-vindas**, clique em **Avançar**.
3. Na página **Termos de Licença**, leia a licença e clique em **Aceito**.
4. Na página **Pasta de Destino**, altere ou mantenha a pasta de instalação padrão e clique em **Avançar**.
5. Na página **Opções de Instalação do Agente**, escolha a opção de conectar o agente ao Azure Log Analytics e clique em **Avançar**.   
6. Na página **Log Analytics do Azure**, faça o seguinte:
   1. Cole a **ID do Workspace** e a **Chave do Workspace (Chave Primária)** que você copiou anteriormente.  Caso o computador deva se reportar a um espaço de trabalho do Log Analytics na nuvem do Azure Governamental, selecione **Governo dos EUA do Azure** na lista suspensa do **Azure Cloud**.  
   2. Caso o computador precise se comunicar por meio de um servidor proxy ao serviço Log Analytics, clique em **Avançado** e forneça a URL e o número da porta do servidor proxy.  Caso seu servidor proxy exija autenticação, digite o nome de usuário e a senha para se autenticar com o servidor proxy e clique em **Avançar**.  
7. Clique em **Avançar** depois de ter terminado de fornecer as configurações necessárias.<br><br> ![colar ID de Workspace e a Chave Primária](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Na página **Pronto para Instalar**, revise suas escolhas e clique em **Instalar**.
9. Na página **Configuração concluída com êxito**, clique em **Concluir**.

Após concluir, o **Microsoft Monitoring Agent** aparecerá no **Painel de Controle**. Para confirmar que está relatando ao Log Analytics, revise [Verificar a conectividade do agente com Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Instalar o agente usando a linha de comando
O arquivo baixado para o agente é um pacote de instalação autossuficiente.  O programa de instalação para o agente e os arquivos de suporte estão contidos no pacote e precisam ser extraídos para instalar corretamente usando a linha de comando mostrada nos exemplos a seguir.    

>[!NOTE]
>Se você quiser atualizar um agente, precisará usar a API de script do Log Analytics. Consulte o tópico [Gerenciar e manter o agente de Log Analytics para o Windows e Linux](agent-manage.md), para obter mais informações.

A tabela a seguir destaca os parâmetros específicos com suporte pela configuração do agente, inclusive quando implantados usando o DSC de Automação.

|Opções específicas do MMA                   |Observações         |
|---------------------------------------|--------------|
| NOAPM=1                               | Parâmetro opcional. Instala o agente sem Monitoramento de Desempenho de Aplicativos .NET.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = configurar o agente para reportar a um workspace                |
|OPINSIGHTS_WORKSPACE_ID                | ID do espaço de trabalho (guid) para o espaço de trabalho para adicionar                    |
|OPINSIGHTS_WORKSPACE_KEY               | Chave do workspace usada para autenticar inicialmente com o workspace |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Especifique o ambiente de nuvem no qual o workspace está <br> 0 = nuvem comercial do Azure (padrão) <br> 1 = Azure Governamental |
|OPINSIGHTS_PROXY_URL               | URI do proxy a ser usado |
|OPINSIGHTS_PROXY_USERNAME               | Nome de usuário para acessar um proxy autenticado |
|OPINSIGHTS_PROXY_PASSWORD               | Senha para acessar um proxy autenticado |

1. Para extrair os arquivos de instalação do agente de um prompt de comando com privilégios elevados, execute `MMASetup-<platform>.exe /c` e será solicitado que você especifique o caminho para extrair os arquivos.  Como alternativa, você poderá especificar o caminho ao passar os argumentos `MMASetup-<platform>.exe /c /t:<Full Path>`.  
2. Para instalar silenciosamente o agente e configurá-lo para relatar a um workspace na nuvem comercial do Azure, da pasta que extraiu os arquivos de configuração para inserir: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID=<your workspace ID> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

   ou para configurar o agente para relatar a nuvem do Governo dos EUA do Azure, inserir: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace ID> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instalar o agente usando o DSC na Automação do Azure

Você pode usar o seguinte exemplo de script para instalar o agente usando o DSC de Automação do Azure.   Se você não possuir uma conta de Automação, consulte [Comece a usar a automação do Azure](/azure/automation/) para entender os requisitos e as etapas para criar uma conta de Automação necessária antes de usar o DSC de Automação.  Se você não estiver familiarizado com o DSC de Automação, revise [Introdução ao DSC de Automação](../../automation/automation-dsc-getting-started.md).

O exemplo a seguir instala o agente de 64 bits, identificado pelo valor `URI`. Também é possível usar a versão de 32 bits, substituindo o valor do URI. Os URIs para ambas as versões são:

- Agente de Windows 64 bits - https://go.microsoft.com/fwlink/?LinkId=828603
- Agente de Windows 32 bits - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Esse procedimento e exemplo de script não dá suporte para a atualização do agente já implantado em um computador Windows.

As versões de 32 bits e 64 bits do pacote do agente têm códigos de produtos diferentes e as novas versões liberadas também possuem um valor exclusivo.  O código do produto é um GUID que é a principal identificação de um aplicativo ou produto e é representado pela propriedade **ProductCode** do Windows Installer.  O valor `ProductId` no script **MMAgent.ps1** deve corresponder ao código do produto do pacote do instalador do agente de 32 bits ou 64 bits.

Para recuperar o código do produto do pacote do instalador do agente diretamente, você pode usar Orca.exe dos [Componentes SDK do Windows para desenvolvedores do Windows Installer](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) que é um Software Development Kit do Windows ou usando o PowerShell após um [script de exemplo](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) gravado por um MVP (Microsoft Valuable Professional).  Para qualquer abordagem, você primeiro precisa extrair o arquivo **MOMagent.msi** do pacote de instalação MMASetup.  Isso é mostrado anteriormente na primeira etapa na seção [Instalar o agente usando a linha de comando](#install-the-agent-using-the-command-line).  

1. Importar o Módulo DSC xPSDesiredStateConfiguration de [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) na Automação do Azure.  
2.  Crie ativos de variável da Automação do Azure para *OPSINSIGHTS_WS_ID* e *OPSINSIGHTS_WS_KEY*. Defina *OPSINSIGHTS_WS_ID* para sua ID do espaço de trabalho do Log Analytics e defina *OPSINSIGHTS_WS_KEY* para a chave primária do seu espaço de trabalho.
3.  Copie o script e salve-o como MMAgent.ps1.

    ```powershell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
        $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
        $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

        Import-DscResource -ModuleName xPSDesiredStateConfiguration
        Import-DscResource -ModuleName PSDesiredStateConfiguration

        Node OMSnode {
            Service OIService
            {
                Name = "HealthService"
                State = "Running"
                DependsOn = "[Package]OI"
            }

            xRemoteFile OIPackage {
                Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
                DestinationPath = $OIPackageLocalPath
            }

            Package OI {
                Ensure = "Present"
                Path  = $OIPackageLocalPath
                Name = "Microsoft Monitoring Agent"
                ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
                Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
                DependsOn = "[xRemoteFile]OIPackage"
            }
        }
    }

    ```

4. Atualize o valor `ProductId` no script com o código de produto extraído da versão mais recente do agente de instalar o pacote usando os métodos recomendados anteriormente. 
5. [Importe o script de configuração MMAgent.ps1](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) na sua conta de Automação. 
5. [Atribua um computador Windows ou um nó](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) à configuração. Dentro de 15 minutos, o nó verificará a configuração e o agente será enviado por push para o nó.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Verificar a conectividade do agente com Log Analytics

Quando a instalação do agente for concluída, verifique se está conectado com êxito e se o relatório pode ser realizado de duas maneiras.  

No computador, no **Painel de Controle**, localize o item **Microsoft Monitoring Agent**.  Selecione-o e, na guia **Azure Log Analytics**, o agente deverá exibir uma mensagem informando: **O Microsoft Monitoring Agent conectou-se com êxito ao serviço Microsoft Operations Management Suite.**<br><br> ![Status de conexão do MMA ao Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Você também pode executar uma consulta de log simples no portal do Azure.  

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **do Azure Monitor**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **o Azure Monitor**.  
2. Selecione **Logs** no menu. 
2. No painel de Logs, no campo de consulta, digite:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Nos resultados de pesquisa retornados, você deverá ver os registros de pulsação para o computador, indicando que está conectado e relatando para o serviço.   

## <a name="next-steps"></a>Próximas etapas

Revisar[Gerenciar e manter o agente de Log Analytics para o Windows e Linux](agent-manage.md) para aprender sobre como gerenciar o agente durante o ciclo de vida da implantação em suas máquinas.  
