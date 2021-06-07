---
title: Instalar o Agente do Log Analytics em computadores Windows
description: Este artigo descreve como conectar computadores Windows hospedados em outras nuvens ou locais no Azure Monitor com o agente do Log Analytics para Windows.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/03/2020
ms.openlocfilehash: aec39b86f9651539028efce93ba6a88c3be75b0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038303"
---
# <a name="install-log-analytics-agent-on-windows-computers"></a>Instalar o Agente do Log Analytics em computadores Windows
Este artigo fornece detalhes sobre como instalar o agente de Log Analytics em computadores Windows usando os seguintes métodos:

* Instalação manual usando o [Assistente de instalação](#install-agent-using-setup-wizard) ou a [linha de comando](#install-agent-using-command-line).
* [DSC (configuração de estado desejado) da automação do Azure](#install-agent-using-dsc-in-azure-automation). 

>[!IMPORTANT]
> Os métodos de instalação descritos neste artigo normalmente são usados para máquinas virtuais locais ou em outras nuvens. Consulte [Opções de instalação](./log-analytics-agent.md#installation-options) para obter opções mais eficientes que você pode usar para máquinas virtuais do Azure.

> [!NOTE]
> Se você precisar configurar o agente para relatar mais de um workspace, isso não poderá ser executado durante a configuração inicial, somente depois, atualizando as configurações do painel de controle ou do PowerShell conforme descrito em [Adicionar ou remover um workspace](agent-manage.md#adding-or-removing-a-workspace).  

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

Consulte [visão geral de agentes de Azure monitor](agents-overview.md#supported-operating-systems) para obter uma lista de versões do Windows com suporte pelo agente de log Analytics.

### <a name="sha-2-code-signing-support-requirement"></a>Requisito de suporte à assinatura de código SHA-2 
O agente do Windows começará a usar exclusivamente a assinatura SHA-2 em 17 de agosto de 2020. Essa alteração afetará os clientes que usam o Agente do Log Analytics em um sistema operacional herdado como parte de um serviço do Azure (Azure Monitor, Automação do Azure, Gerenciamento de Atualizações do Azure, Controle de Alterações do Azure, Central de Segurança do Azure, Azure Sentinel, Windows Defender ATP). A alteração não requer nenhuma ação do cliente, a menos que você esteja executando o agente em uma versão do sistema operacional herdada (Windows 7, Windows Server 2008 R2 e Windows Server 2008). Os clientes em execução em uma versão de sistema operacional herdada precisarão executar as seguintes ações em suas máquinas antes de 17 de agosto de 2020, ou seus agentes deixarão de enviar dados para seus workspaces do Log Analytics:

1. Instale o Service Pack mais recente para seu sistema operacional. As versões do service pack necessárias são:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Instale as atualizações do Windows de assinatura do SHA-2 para seu sistema operacional conforme descrito no requisito de suporte à assinatura de código [SHA-2 2019 para Windows e WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Atualize para a versão mais recente do agente do Windows (versão 10.20.18029).
4. É recomendável configurar o agente para [usar o TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12). 

## <a name="network-requirements"></a>Requisitos de rede
Consulte [visão geral do agente de log Analytics](./log-analytics-agent.md#network-requirements) para os requisitos de rede para o agente do Windows.


   
## <a name="configure-agent-to-use-tls-12"></a>Configurar o agente para usar o TLS 1.2
O protocolo [TLS 1,2](/windows-server/security/tls/tls-registry-settings#tls-12) garante a segurança dos dados em trânsito para comunicação entre o agente do Windows e o serviço de log Analytics. Se você estiver instalando o em um [sistema operacional sem o tls 1,2 habilitado por padrão](../logs/data-security.md#sending-data-securely-using-tls-12), configure o TLS 1,2 usando as etapas abaixo.

1. Localize a seguinte subchave do Registro: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Crie uma subchave em **Protocolos** para o TLS 1.2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. Criar uma subchave **Cliente** na subchave do protocolo TLS 1.2 que você criou anteriormente. Por exemplo, **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Crie os seguintes valores DWORD em **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**:

    * **Habilitado** [Valor = 1]
    * **DisabledByDefault** [Valor = 0]  

Configurar o .NET Framework 4.6 ou posterior para dar suporte à criptografia segura, uma vez que, por padrão, é desabilitado. A [criptografia forte](/dotnet/framework/network-programming/tls#schusestrongcrypto) usa mais protocolos de rede seguros como TLS 1.2 e bloqueia os protocolos que não são seguros. 

1. Localize a seguinte subchave do Registro: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**.  
2. Crie o valor DWORD **SchUseStrongCrypto** nessa subchave com um valor de **1**.  
3. Localize a seguinte subchave do Registro: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**.  
4. Crie o valor DWORD **SchUseStrongCrypto** nessa subchave com um valor de **1**. 
5. Reinicie o sistema para que as configurações entrem em vigor. 

## <a name="install-agent-using-setup-wizard"></a>Instalar o agente usando o assistente para instalação
As etapas a seguir instalam e configuram o agente do Log Analytics do Azure e a nuvem do Azure Government usando o assistente de instalação em seu computador. Se você quiser saber como configurar o agente para também relatar grupo de gerenciamento System Center Operations Manager, consulte [ implantar o agente do Operations Manager com o Assiste de Configuração do Agente](/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

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

Após concluir, o **Microsoft Monitoring Agent** aparecerá no **Painel de Controle**. Para confirmar que está relatando ao Log Analytics, revise [Verificar a conectividade do agente com Log Analytics](#verify-agent-connectivity-to-azure-monitor). 

## <a name="install-agent-using-command-line"></a>Instalar o agente usando a linha de comando
O arquivo baixado para o agente é um pacote de instalação autossuficiente.  O programa de instalação para o agente e os arquivos de suporte estão contidos no pacote e precisam ser extraídos para instalar corretamente usando a linha de comando mostrada nos exemplos a seguir.    

>[!NOTE]
>Se você quiser atualizar um agente, precisará usar a API de script do Log Analytics. Consulte o tópico [Gerenciar e manter o agente de Log Analytics para o Windows e Linux](agent-manage.md), para obter mais informações.

A tabela a seguir destaca os parâmetros específicos com suporte pela configuração do agente, inclusive quando implantados usando o DSC de Automação.

|Opções específicas do MMA                   |Observações         |
|---------------------------------------|--------------|
| NOAPM=1                               | Parâmetro opcional. Instala o agente sem Monitoramento de Desempenho de Aplicativos .NET.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = configurar o agente para reportar a um workspace                |
|OPINSIGHTS_WORKSPACE_ID                | ID do workspace (guid) para o workspace para adicionar                    |
|OPINSIGHTS_WORKSPACE_KEY               | Chave do workspace usada para autenticar inicialmente com o workspace |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Especifique o ambiente de nuvem no qual o workspace está <br> 0 = nuvem comercial do Azure (padrão) <br> 1 = Azure Governamental |
|OPINSIGHTS_PROXY_URL               | URI do proxy a ser usado |
|OPINSIGHTS_PROXY_USERNAME               | Nome de usuário para acessar um proxy autenticado |
|OPINSIGHTS_PROXY_PASSWORD               | Senha para acessar um proxy autenticado |

1. Para extrair os arquivos de instalação do agente de um prompt de comando com privilégios elevados, execute `MMASetup-<platform>.exe /c` e será solicitado que você especifique o caminho para extrair os arquivos.  Como alternativa, você poderá especificar o caminho ao passar os argumentos `MMASetup-<platform>.exe /c /t:<Full Path>`.  
2. Para instalar silenciosamente o agente e configurá-lo para relatar a um workspace na nuvem comercial do Azure, da pasta que extraiu os arquivos de configuração para inserir: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   ou para configurar o agente para relatar a nuvem do Governo dos EUA do Azure, inserir: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >Os valores de cadeia de caracteres para os parâmetros *OPINSIGHTS_WORKSPACE_ID* e *OPINSIGHTS_WORKSPACE_KEY* precisam ser encapsulados em aspas duplas para instruir Windows Installer a interpretá-los como opções válidas para o pacote. 

## <a name="install-agent-using-dsc-in-azure-automation"></a>Instalar o agente usando a DSC na automação do Azure

Você pode usar o seguinte exemplo de script para instalar o agente usando o DSC de Automação do Azure.   Se você não possuir uma conta de Automação, consulte [Comece a usar a automação do Azure](../../automation/index.yml) para entender os requisitos e as etapas para criar uma conta de Automação necessária antes de usar o DSC de Automação.  Se você não estiver familiarizado com o DSC de Automação, revise [Introdução ao DSC de Automação](../../automation/automation-dsc-getting-started.md).

O exemplo a seguir instala o agente de 64 bits, identificado pelo valor `URI`. Também é possível usar a versão de 32 bits, substituindo o valor do URI. Os URIs para ambas as versões são:

- Agente de Windows 64 bits - https://go.microsoft.com/fwlink/?LinkId=828603
- Agente de Windows 32 bits - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Esse procedimento e exemplo de script não dá suporte para a atualização do agente já implantado em um computador Windows.

As versões de 32 bits e 64 bits do pacote do agente têm códigos de produtos diferentes e as novas versões liberadas também possuem um valor exclusivo.  O código do produto é um GUID que é a principal identificação de um aplicativo ou produto e é representado pela propriedade **ProductCode** do Windows Installer.  O valor `ProductId` no script **MMAgent.ps1** deve corresponder ao código do produto do pacote do instalador do agente de 32 bits ou 64 bits.

Para recuperar o código do produto do pacote do instalador do agente diretamente, você pode usar Orca.exe dos [Componentes SDK do Windows para desenvolvedores do Windows Installer](/windows/win32/msi/platform-sdk-components-for-windows-installer-developers) que é um Software Development Kit do Windows ou usando o PowerShell após um [script de exemplo](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) gravado por um MVP (Microsoft Valuable Professional).  Para qualquer abordagem, você primeiro precisa extrair o arquivo **MOMagent.msi** do pacote de instalação MMASetup.  Isso é mostrado anteriormente na primeira etapa na seção [Instalar o agente usando a linha de comando](#install-agent-using-command-line).  

1. Importar o Módulo DSC xPSDesiredStateConfiguration de [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) na Automação do Azure.  
2.    Crie ativos de variável da Automação do Azure para *OPSINSIGHTS_WS_ID* e *OPSINSIGHTS_WS_KEY*. Defina *OPSINSIGHTS_WS_ID* para sua ID do espaço de trabalho do Log Analytics e defina *OPSINSIGHTS_WS_KEY* para a chave primária do seu espaço de trabalho.
3.    Copie o script e salve-o como MMAgent.ps1.

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
5. [Importe o script de configuração MMAgent.ps1](../../automation/automation-dsc-getting-started.md#import-a-configuration-into-azure-automation) na sua conta de Automação. 
6. [Atribua um computador Windows ou um nó](../../automation/automation-dsc-getting-started.md#enable-an-azure-resource-manager-vm-for-management-with-state-configuration) à configuração. Dentro de 15 minutos, o nó verificará a configuração e o agente será enviado por push para o nó.

## <a name="verify-agent-connectivity-to-azure-monitor"></a>Verificar a conectividade do agente para Azure Monitor

Quando a instalação do agente for concluída, verifique se está conectado com êxito e se o relatório pode ser realizado de duas maneiras.  

No computador, no **Painel de Controle**, localize o item **Microsoft Monitoring Agent**.  Selecione-o e, na guia **Azure Log Analytics**, o agente deverá exibir uma mensagem informando: **O Microsoft Monitoring Agent conectou-se com êxito ao serviço Microsoft Operations Management Suite.**<br><br> ![Status de conexão do MMA ao Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Você também pode realizar uma consulta simples ao log no portal do Azure.  

1. No portal do Azure, pesquise e selecione **Monitor**.
1. Selecione **Logs** no menu.
1. No painel **Logs**, no tipo de campo de consulta:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Nos resultados de pesquisa retornados, você deverá ver os registros de pulsação para o computador, indicando que está conectado e relatando para o serviço.

## <a name="cache-information"></a>Informações de cache

Os dados do agente de Log Analytics são armazenados em cache no computador local em *C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State* antes de serem enviados para Azure monitor. O agente tenta carregar a cada 20 segundos. Se falhar, ele aguardará um período de tempo cada vez maior até que tenha êxito. Ele aguardará 30 segundos antes da segunda tentativa, 60 segundos antes da próxima, 120 segundos e assim por diante até um máximo de 8,5 horas entre as repetições até que ela se conecte com êxito novamente. Esse tempo de espera é ligeiramente aleatório para evitar que todos os agentes tentem a conexão simultaneamente. Os dados mais antigos são descartados quando o buffer máximo é atingido.

O tamanho de cache padrão é 50 MB, mas pode ser configurado entre um mínimo de 5 MB e o máximo de 1,5 GB. Ele é armazenado na chave do registro *HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Persistence Cache Maximum*. O valor representa o número de páginas, com 8 KB por página.


## <a name="next-steps"></a>Próximas etapas

- Examinar [Gerenciar e manter o agente de Log Analytics para o Windows e Linux](agent-manage.md) para aprender sobre como reconfigurar, atualizar ou remover o agente da máquina virtual.

- Consulte [Solução de problemas do agente do Windows](agent-windows-troubleshoot.md) se você encontrar problemas ao instalar ou gerenciar o agente.