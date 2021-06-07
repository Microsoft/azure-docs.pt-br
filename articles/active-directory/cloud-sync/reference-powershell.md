---
title: Módulo do PowerShell do AADCloudSyncTools para sincronização Azure AD Connect Cloud
description: Este artigo descreve como instalar o agente de provisionamento em nuvem do Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa358b0c9d7747584deabe761160d3bcbcde8feb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593173"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>Módulo do PowerShell do AADCloudSyncTools para sincronização Azure AD Connect Cloud

O módulo AADCloudSyncTools fornece um conjunto de ferramentas úteis que você pode usar para ajudar a gerenciar seu Azure AD Connect implantações de sincronização de nuvem.

## <a name="pre-requisites"></a>Pré-requisitos
Os pré-requisitos a seguir são necessários:

- Todos os pré-requisitos para este módulo podem ser instalados automaticamente usando o `Install-AADCloudSyncToolsPrerequisites`
- Esse módulo usa a autenticação MSAL, portanto, ele requer o módulo MSAL.PS instalado. Para verificar, em uma janela do PowerShell, execute `Get-module MSAL.PS -ListAvailable` . Se o módulo estiver instalado corretamente, você receberá uma resposta. Você pode usar `Install-AADCloudSyncToolsPrerequisites` o para instalar a versão mais recente do MSAL.PS
- Embora o módulo do PowerShell do AzureAD não seja um pré-requisito para qualquer funcionalidade desse módulo, é útil ter, portanto, ele também é instalado automaticamente com o usando o `Install-AADCloudSyncToolsPrerequisites` .
- A instalação manual de módulos do PowerShell requer imposição de TLS 1,2. Para garantir que você possa instalar os módulos, defina o seguinte na sessão do PowerShell antes de usar
  ```
   Install-Module:
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  ```


## <a name="install-the-aadcloudsynctools-powershell-module"></a>Instalar o módulo do PowerShell do AADCloudSyncTools
Para instalar e usar o módulo AADCloudSyncTools, use as seguintes etapas:

1. Abrir o Windows PowerShell com privilégios administrativos
2. Digite ou copie e cole o seguinte: `Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"`
3. Pressione Enter.
4. Para verificar se o módulo foi importado, insira ou copie e cole o seguinte: `Get-module AADCloudSyncTools`
5. Agora você deve ver informações sobre o módulo.
6. Em seguida, para instalar a execução dos pré-requisitos do módulo AADCloudSyncTools: `Install-AADCloudSyncToolsPrerequisites`
7. Na primeira execução, o módulo PoweShellGet será instalado se não estiver presente. Para carregar o novo módulo PowershellGet, feche a janela do PowerShell e abra uma nova sessão do PowerShell com privilégios administrativos. 
8. Importe o módulo novamente usando a etapa 3.
9. Executar `Install-AADCloudSyncToolsPrerequisites` para instalar os módulos MSAL e AzureAD
11. Todos os requisitos devem ser instalados com êxito no ![ módulo de instalação](media/reference-powershell/install-1.png)


## <a name="aadcloudsynctools--cmdlets"></a>Cmdlets AADCloudSyncTools
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
Usa o módulo MSAL.PS para solicitar um token para o administrador do Azure AD acessar Microsoft Graph 


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
Exporta e empacota todos os dados de solução de problemas em um arquivo compactado, da seguinte maneira:
 1. Inicia um rastreamento detalhado com Start-AADCloudSyncToolsVerboseLogs.  Encontre esses logs de rastreamento na pasta C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.
 2. Coleta um log de rastreamento por 3 minutos.
   Você pode especificar uma hora diferente com-TracingDurationMins ou ignorar o rastreamento detalhado com-SkipVerboseTrace
 3. Interrompe o rastreamento detalhado com Stop-AADCloudSyncToolsVerboseLogs
 4. Coleta logs de Visualizador de Eventos para as últimas 24 horas
 5. Compacta todos os logs de agente, logs detalhados e logs do Visualizador de eventos em um arquivo ZIP compactado na pasta documentos do usuário. 
 </br>Você pode especificar uma pasta de saída diferente com-OutputPath \<folder path\>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Mostra detalhes de locatário do Azure AD e estado de variáveis internas

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
Usa o grafo para obter entidades de serviço AD2AAD e retornar as informações do trabalho de sincronização.
Também pode ser chamado usando a ID de trabalho de sincronização específica como um parâmetro.

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
Usa o grafo para obter entidades de serviço AD2AAD e retorna a agenda do trabalho de sincronização.
Também pode ser chamado usando a ID de trabalho de sincronização específica como um parâmetro.

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
Usa o grafo para obter entidades de serviço AD2AAD e retorna o esquema do trabalho de sincronização.

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
Usa o grafo para obter o esquema do trabalho de sincronização para a ID de trabalho de sincronização fornecida e gera todos os escopos do grupo de filtros.

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
Usa o grafo para obter entidades de serviço AD2AAD e retorna as configurações do trabalho de sincronização.
Também pode ser chamado usando a ID de trabalho de sincronização específica como um parâmetro.

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
Usa o grafo para obter entidades de serviço AD2AAD e retorna o status do trabalho de sincronização.
Também pode ser chamado usando a ID de trabalho de sincronização específica como um parâmetro.

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
Usa o grafo para obter a (s) entidade (ões) de serviço para AD2AAD e/ou SyncFabric.
Sem parâmetros, retornará somente as entidades de serviço AD2AAD.

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
Verifica a presença do PowerShellGet v 2.2.4.1 ou posterior e dos módulos do Azure AD e do MSAL.PS e instala-os se estiverem ausentes.

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
Invoca uma solicitação da Web para o URI, o método e o corpo especificados como parâmetros

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Usa o PowerShell do Azure AD para excluir a conta atual (se presente) e redefine a autenticação da conta de sincronização com uma nova conta de sincronização no Azure AD.

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
Reinicia uma sincronização completa.

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
Continua a sincronização da marca d' água anterior.

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
Modifica o ' AADConnectProvisioningAgent.exe.config ' para habilitar o rastreamento detalhado e reinicia o serviço AADConnectProvisioningAgent que você pode usar-SkipServiceRestart para evitar a reinicialização do serviço, mas quaisquer alterações de configuração não terão efeito.  Encontre esses logs de rastreamento na pasta C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
Modifica o ' AADConnectProvisioningAgent.exe.config ' para desabilitar o rastreamento detalhado e reinicia o serviço AADConnectProvisioningAgent. Você pode usar-SkipServiceRestart para impedir a reinicialização do serviço, mas quaisquer alterações de configuração não terão efeito.

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
Pausa a sincronização.

## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem do Azure AD Connect?](what-is-cloud-sync.md)

