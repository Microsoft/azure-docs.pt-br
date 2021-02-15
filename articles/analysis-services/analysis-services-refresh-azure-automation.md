---
title: Atualizar modelos do Azure Analysis Services com a Automação do Azure | Microsoft Docs
description: Este artigo descreve como codificar atualizações de modelo para o Azure Analysis Services usando a Automação do Azure.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: chlound
ms.openlocfilehash: 7c801511b6f24cf5ef04d55bb195e3a4c62d7b6d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491240"
---
# <a name="refresh-with-azure-automation"></a>Atualizar com Automação do Azure

Usando a Automação do Azure e os runbooks do PowerShell, você pode executar operações automatizadas de atualização de dados em seus modelos de tabela do Azure Analysis.  

O exemplo neste artigo usa o [módulo do SqlServer PowerShell](/powershell/module/sqlserver/?view=sqlserver-ps&preserve-view=true). Um exemplo de runbook do PowerShell, que demonstra a atualização de um modelo é fornecido posteriormente neste artigo.  

## <a name="authentication"></a>Autenticação

Todas as chamadas devem ser autenticadas com um token válido do Azure Active Directory (OAuth 2).  O exemplo neste artigo usa uma entidade de serviço (SPN) para autenticar para Azure Analysis Services. Para saber mais, confira [criar uma entidade de serviço usando portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> O exemplo a seguir pressupõe que o firewall do Azure Analysis Services está desabilitado. Se um firewall estiver habilitado, o endereço IP público do iniciador de solicitação deverá ser incluído em uma regra de firewall.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Instalar os módulos do SqlServer a partir da galeria do PowerShell.

1. Na conta de Automação do Azure, clique em **Módulos** e em **Procurar galeria**.

2. Na barra de pesquisa, procure **SqlServer**.

    ![Módulos de pesquisa](./media/analysis-services-refresh-azure-automation/1.png)

3. Selecione SqlServer e clique em **Importar**.
 
    ![Importar módulo](./media/analysis-services-refresh-azure-automation/2.png)

4. Clique em **OK**.
 
### <a name="create-a-service-principal-spn"></a>Criar uma entidade de serviço (SPN)

Para saber como criar uma entidade de serviço, consulte [Criar uma entidade de serviço usando o portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Configurar permissões no Azure Analysis Services
 
A entidade de serviço que você criar deve ter permissões de administrador do servidor no servidor. Para saber mais, consulte [Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Criar o runbook de Automação do Azure

1. Na conta de Automação, crie um recurso de **Credenciais** que será usado para armazenar com segurança a entidade de serviço.

    ![Captura de tela que mostra a página "credenciais" com a ação "adicionar uma credencial" selecionada.](./media/analysis-services-refresh-azure-automation/6.png)

2. Insira os detalhes da credencial. Em **nome de usuário**, insira a ID do aplicativo da entidade de serviço (AppID) e, em seguida, em **senha**, insira o segredo da entidade de serviço.

    ![Criar credencial](./media/analysis-services-refresh-azure-automation/7.png)

3. Importe o runbook de automação.

    ![Captura de tela que mostra a página "Runbooks" com a ação "importar um runbook" selecionada.](./media/analysis-services-refresh-azure-automation/8.png)

4. Procure o arquivo [Refresh-Model.ps1](#sample-powershell-runbook), forneça um **Nome** e **Descrição** e, em seguida, clique em **Criar**.

    > [!NOTE]
    > Use o script da seção [exemplo de runbook do PowerShell](#sample-powershell-runbook) na parte inferior deste documento para criar um arquivo chamado Refresh-Model.ps1 e salvar no computador local para importar para o runbook.

    ![Importar Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Quando o runbook tiver sido criado, ele entrará automaticamente no modo de edição.  Selecione **Publicar**.

    ![Publicar runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > O recurso de credencial que foi criado anteriormente é recuperado pelo runbook usando o comando **Get-AutomationPSCredential**.  Esse comando é passado para o comando **Invoke-ProcessASADatabase** do PowerShell para executar a autenticação para o Azure Analysis Services.

6. Teste o runbook clicando em **Iniciar**.

    ![Captura de tela que mostra a página "visão geral" com a ação "Iniciar" selecionada.](./media/analysis-services-refresh-azure-automation/11.png)

7. Preencha os parâmetros **DATABASENAME**, **ANALYSISSERVER** e **REFRESHTYPE** e, em seguida, clique em **OK**. O parâmetro **WEBHOOKDATA** não é necessário quando o runbook é executado manualmente.

    ![Iniciar o runbook](./media/analysis-services-refresh-azure-automation/12.png)

Se o runbook for executado com êxito, você receberá uma saída semelhante à seguinte:

![Execução bem-sucedida](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Usar um runbook autossuficiente da Automação do Azure

O runbook pode ser configurado para disparar a atualização do modelo do Azure Analysis Services de acordo com um agendamento.

Isso pode ser configurado da seguinte maneira:

1. No runbook de Automação, clique em **Agendamentos** e, em seguida, **Adicionar um agendamento**.
 
    ![Criar agendamento](./media/analysis-services-refresh-azure-automation/14.png)

2. Clique em **Agendamento** > **Criar um novo agendamento** e preencha os detalhes.

    ![Configurar agendamento](./media/analysis-services-refresh-azure-automation/15.png)

3. Clique em **Criar**.

4. Preencha os parâmetros para o agendamento. Eles serão usados cada vez que o runbook for disparado. O parâmetro **WEBHOOKDATA** deve ser deixado em branco quando a execução for realizada por meio de um agendamento.

    ![Configurar parâmetros](./media/analysis-services-refresh-azure-automation/16.png)

5. Clique em **OK**.

## <a name="consume-with-data-factory"></a>Consumir com Data Factory

Para consumir o runbook usando o Azure Data Factory, primeiro crie um **Webhook** para o runbook. O **Webhook** fornecerá uma URL que pode ser chamada por meio de uma atividade Web do Azure Data Factory.

> [!IMPORTANT]
> Para criar um **Webhook**, o status do runbook deve ser **Publicado**.

1. Em seu runbook de Automação, clique em **Webhooks** e, em seguida, clique em **Adicionar Webhook**.

   ![Adicionar Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Dê um nome e uma expiração ao Webhook.  O nome identifica apenas o Webhook dentro do runbook de Automação; ele não faz parte da URL.

   >[!CAUTION]
   >Certifique-se de copiar a URL antes de fechar o assistente, pois você não pode obtê-la novamente depois de fechá-lo.
    
   ![Configurar o Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Os parâmetros para o Webhook podem permanecer em branco.  Quando configurar a atividade Web do Azure Data Factory, os parâmetros podem ser passados para o corpo da chamada Web.

3. No Data Factory, configure uma **atividade Web**

### <a name="example"></a>Exemplo

   ![Exemplo de atividade Web](./media/analysis-services-refresh-azure-automation/19.png)

A **URL** é a URL criada no Webhook.

O **corpo** é um documento JSON que deve conter as seguintes propriedades:


|Propriedade  |Valor  |
|---------|---------|
|**AnalysisServicesDatabase**     |O nome do banco de dados do Azure Analysis Services <br/> Exemplo: AdventureWorksDB         |
|**AnalysisServicesServer**     |O nome do servidor do Azure Analysis Services. <br/> Exemplo: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |O tipo de atualização a ser executada. <br/> Exemplo: Completo         |

Exemplo de corpo JSON:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Esses parâmetros são definidos no script do PowerShell do runbook.  Quando a atividade Web é executada, a carga JSON passada é WEBHOOKDATA.

Ela é desserializada e armazenada como parâmetros do PowerShell, que são usados pelo comando Invoke-ProcesASDatabase do PowerShell.

![Webhook desserializado](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Usar um Hybrid Worker com o Azure Analysis Services

Uma máquina virtual do Azure com um endereço IP público estático pode ser usada como um Hybrid Worker de Automação do Azure.  Esse endereço IP público pode ser adicionado ao firewall do Azure Analysis Services.

> [!IMPORTANT]
> Verifique se o endereço IP público da máquina virtual está configurado como estático.
>
>Para saber mais sobre como configurar o Hybrid Worker de Automação do Azure, confira [Instalação do Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md#hybrid-runbook-worker-installation).

Quando um Hybrid Worker estiver configurado, crie um Webhook, conforme descrito na seção [Consumir com Data Factory](#consume-with-data-factory).  A única diferença aqui é selecionar a opção **Executar no** > **Hybrid Worker** ao configurar o Webhook.

Exemplo de Webhook usando Hybrid Worker:

![Exemplo de Webhook do Hybrid Worker](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Exemplo de runbook do PowerShell

O trecho de código a seguir é um exemplo de como executar a atualização de modelo do Azure Analysis Services usando um runbook do PowerShell.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Próximas etapas

[Amostras](analysis-services-samples.md)  
[REST API](/rest/api/analysisservices/servers)