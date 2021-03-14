---
title: Configure o BYOS (Traga seu próprio armazenamento) para o profiler & Depurador de Instantâneos
description: Configure o BYOS (Traga seu próprio armazenamento) para o profiler & Depurador de Instantâneos
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 01/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 9c3ff91cbfb6423099040a6ea46eeb66f5461f48
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589671"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>Configure o BYOS (Traga seu próprio armazenamento) para Application Insights Profiler e Depurador de Instantâneos

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>O que é o BYOS (Traga seu próprio armazenamento) e por que posso precisar dele? 
Quando você usa Application Insights Profiler ou Depurador de Instantâneos, os artefatos gerados pelo seu aplicativo são carregados em contas de armazenamento do Azure pela Internet pública. Essas contas são pagas e controladas pela Microsoft para processamento e análise. A Microsoft controla as políticas de criptografia em repouso e gerenciamento de tempo de vida para esses artefatos.

Com o traga seu próprio armazenamento, esses artefatos são carregados em uma conta de armazenamento que você controla. Isso significa que você controla a política de criptografia em repouso, a política de gerenciamento de tempo de vida e o acesso à rede. No entanto, você será responsável pelos custos associados a essa conta de armazenamento.

> [!NOTE]
> Se você estiver habilitando o link privado, traga seu próprio armazenamento é um requisito. Para obter mais informações sobre o link privado para Application Insights, [consulte a documentação.](../logs/private-link-security.md)
>
> Se você estiver habilitando Customer-Managed chaves, traga seu próprio armazenamento é um requisito. Para obter mais informações sobre Customer-Managed chaves para Application Insights, [consulte a documentação.](../logs/customer-managed-keys.md)

## <a name="how-will-my-storage-account-be-accessed"></a>Como minha conta de armazenamento será acessada?
1. Os agentes em execução em suas máquinas virtuais ou no serviço de aplicativo carregarão artefatos (perfis, instantâneos e símbolos) para contêineres de BLOB em sua conta. Esse processo envolve entrar em contato com o serviço de Application Insights Profiler ou Depurador de Instantâneos para obter um token SAS (assinatura de acesso compartilhado) para um novo BLOB em sua conta de armazenamento.
1. O serviço de Application Insights Profiler ou Depurador de Instantâneos analisará o blob de entrada e gravará os resultados da análise e os arquivos de log no armazenamento de BLOBs. Dependendo da capacidade de computação disponível, esse processo pode ocorrer a qualquer momento após o carregamento.
1. Quando você exibir os rastreamentos do criador de perfil ou a análise do depurador de instantâneo, o serviço buscará os resultados da análise do armazenamento de BLOBs.

## <a name="prerequisites"></a>Pré-requisitos
* Certifique-se de criar sua conta de armazenamento no mesmo local que o recurso de Application Insights. Exemplo: Se o recurso de Application Insights estiver no oeste dos EUA 2, sua conta de armazenamento também deverá estar no oeste dos EUA 2. 
* Conceda a função "colaborador de dados de blob de armazenamento" ao aplicativo do AAD "acesso de armazenamento confiável dos serviços de diagnóstico" em sua conta de armazenamento por meio da interface de usuário do controle de acesso (IAM).
* Se o link privado estiver habilitado, defina a configuração adicional para permitir a conexão com o nosso serviço confiável da Microsoft de sua rede virtual. 

## <a name="how-to-enable-byos"></a>Como habilitar BYOS

### <a name="create-storage-account"></a>Criar uma conta de armazenamento
Crie uma nova conta de armazenamento (se você não a tiver) no mesmo local que o recurso de Application Insights.
Se o recurso de Application Insights estiver ativado `West US 2` , sua conta de armazenamento deverá estar no `West US 2` .

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>Conceder acesso aos serviços de diagnóstico para sua conta de armazenamento
Uma conta de armazenamento BYOS será vinculada a um recurso de Application Insights. Pode haver apenas uma conta de armazenamento por Application Insights recurso e ambas devem estar no mesmo local. Você pode usar a mesma conta de armazenamento com mais de um recurso de Application Insights.

Primeiro, o Application Insights Profiler e o serviço de Depurador de Instantâneos precisam receber acesso à conta de armazenamento. Para conceder acesso, adicione a função `Storage Blob Data Contributor` ao aplicativo AAD chamado `Diagnostic Services Trusted Storage Access` por meio da página controle de acesso (iam) em sua conta de armazenamento, conforme mostrado na Figura 1,0. 

Etapas: 
1. Clique no botão "Adicionar" na seção "adicionar uma atribuição de função" 
1. Selecione a função "Colaborador de dados do Blob de Armazenamento" 
1. Selecione "usuário, grupo ou entidade de serviço do Azure AD" na seção "atribuir acesso a" 
1. Pesquise & selecione o aplicativo "acesso ao armazenamento confiável dos serviços de diagnóstico" 
1. Salvar alterações

_![ Figura 1,0](media/profiler-bring-your-own-storage/figure-10.png)_ 
 _Figura 1,0_ 

Depois de adicionar a função, ela será exibida na seção "atribuições de função", como a figura abaixo 1,1. 
_![ Figura 1,1](media/profiler-bring-your-own-storage/figure-11.png)_ 
 _Figura 1,1_ 

Se você também estiver usando o link privado, será necessária uma configuração adicional para permitir a conexão com o nosso serviço confiável da Microsoft de sua rede virtual. Consulte a [documentação de segurança de rede de armazenamento](../../storage/common/storage-network-security.md#trusted-microsoft-services).

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>Vincular sua conta de armazenamento com o recurso Application Insights
Para configurar o BYOS para diagnósticos de nível de código (criador de perfil/depurador), há três opções:

* Usando cmdlets Azure PowerShell
* Usando a CLI (interface de linha de comando) do Azure
* Usar o modelo do Azure Resource Manager

#### <a name="configure-using-azure-powershell-cmdlets"></a>Configurar usando cmdlets Azure PowerShell

1. Verifique se você instalou o AZ PowerShell 4.2.0 ou superior.

    Para instalar o Azure PowerShell, consulte a [documentação oficial do Azure PowerShell](/powershell/azure/install-az-ps).

1. Instale a extensão Application Insights PowerShell.
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. Entre com sua conta do Azure
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    Para obter mais informações sobre como entrar, consulte a documentação do [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

1. Remova a conta de armazenamento anterior vinculada ao recurso Application Insights.

    Padrão:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

    Exemplo:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

1. Conecte sua conta de armazenamento com o recurso Application Insights.
    
    Padrão:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

    Exemplo:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "byos-test" -Name "byosteststoragewestus2"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

#### <a name="configure-using-azure-cli"></a>Configuração usando a CLI do Azure

1. Verifique se você instalou o CLI do Azure.

    Para instalar o CLI do Azure, consulte a [documentação oficial do CLI do Azure](/cli/azure/install-azure-cli).

1. Instale a extensão da CLI do Application Insights.
    ```powershell
    az extension add -n application-insights
    ```

1. Conecte sua conta de armazenamento com o recurso Application Insights.

    Padrão:
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "{resource_group_name}" --app "{application_insights_name}" --storage-account "{storage_account_name}"
    ```
    
    Exemplo:
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "byos-test" --app "byos-test-westus2-ai" --storage-account "byosteststoragewestus2"
    ```
    
    Saída esperada:
    ```powershell
    {
      "id": "/subscriptions/{subscription}/resourcegroups/byos-test/providers/microsoft.insights/components/byos-test-westus2-ai/linkedstorageaccounts/serviceprofiler",
      "linkedStorageAccount": "/subscriptions/{subscription}/resourceGroups/byos-test/providers/Microsoft.Storage/storageAccounts/byosteststoragewestus2",
      "name": "serviceprofiler",
      "resourceGroup": "byos-test",
      "type": "microsoft.insights/components/linkedstorageaccounts"
    }
    ```

    > [!NOTE]
    > Para executar atualizações nas contas de armazenamento vinculadas para o recurso Application Insights, consulte a [documentação da CLI do Application insights](/cli/azure/ext/application-insights/monitor/app-insights/component/linked-storage).

#### <a name="configure-using-azure-resource-manager-template"></a>Configurar usando o modelo de Azure Resource Manager

1. Crie um arquivo de modelo de Azure Resource Manager com o conteúdo a seguir (byos.template.jsem).
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "applicationinsights_name": {
          "type": "String"
        },
        "storageaccount_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "name": "[concat(parameters('applicationinsights_name'), '/serviceprofiler')]",
          "type": "Microsoft.Insights/components/linkedStorageAccounts",
          "apiVersion": "2020-03-01-preview",
          "properties": {
            "linkedStorageAccount": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageaccount_name'))]"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Execute o seguinte comando do PowerShell para implantar o modelo anterior (criar conta de armazenamento vinculada).

    Padrão:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    Exemplo:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. Forneça os seguintes parâmetros quando solicitado no console do PowerShell:
    
    |           Parâmetro           |                                Descrição                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | O nome do recurso de Application Insights para habilitar BYOS.            |
    | storage_account_name          | O nome do recurso de conta de armazenamento que você usará como seu BYOS. |
    
    Saída esperada:
    ```powershell
    Supply values for the following parameters:
    (Type !? for Help.)
    application_insights_name: byos-test-westus2-ai
    storage_account_name: byosteststoragewestus2
    
    DeploymentName          : byos.template
    ResourceGroupName       : byos-test
    ProvisioningState       : Succeeded
    Timestamp               : 4/16/2020 1:24:57 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name                            Type                       Value
                              ==============================  =========================  ==========
                              application_insights_name        String                     byos-test-westus2-ai
                              storage_account_name             String                     byosteststoragewestus2
                              
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

1. Habilite o diagnóstico de nível de código (criador de perfil/depurador) na carga de trabalho de interesse por meio do portal do Azure. (Application Insights de > do serviço de aplicativo) _![ Figura 2,0](media/profiler-bring-your-own-storage/figure-20.png)_ 
 _Figura 2,0_

## <a name="troubleshooting"></a>Solução de problemas
### <a name="template-schema-schema_uri-isnt-supported"></a>Não há suporte para o esquema de modelo ' {schema_uri} '.
* Verifique se a `$schema` Propriedade do modelo é válida. Ele deve seguir o seguinte padrão: `https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#`
* Verifique se o `schema_version` do modelo está dentro dos valores válidos: `2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01` .
    Mensagem de erro:
    ```powershell
    New-AzResourceGroupDeployment : 11:53:49 AM - Error: Code=InvalidTemplate; Message=Deployment template validation failed: 'Template schema
    'https://schema.management.azure.com/schemas/2020-01-01/deploymentTemplate.json#' is not supported. Supported versions are
    '2014-04-01-preview,2015-01-01,2018-05-01,2019-04-01,2019-08-01'. Please see https://aka.ms/arm-template for usage details.'.
    ```

### <a name="no-registered-resource-provider-found-for-location-location"></a>Nenhum provedor de recursos registrado foi encontrado para o local ' {location} '.
* Verifique se o `apiVersion` do recurso `microsoft.insights/components` é `2015-05-01` .
* Verifique se o `apiVersion` do recurso `linkedStorageAccount` é `2020-03-01-preview` .
    Mensagem de erro:
    ```powershell
    New-AzResourceGroupDeployment : 6:18:03 PM - Resource microsoft.insights/components 'byos-test-westus2-ai' failed with message '{
      "error": {
        "code": "NoRegisteredProviderFound",
        "message": "No registered resource provider found for location 'westus2' and API version '2020-03-01-preview' for type 'components'. The supported api-versions are '2014-04-01,
    2014-08-01, 2014-12-01-preview, 2015-05-01, 2018-05-01-preview'. The supported locations are ', eastus, southcentralus, northeurope, westeurope, southeastasia, westus2, uksouth,
    canadacentral, centralindia, japaneast, australiaeast, koreacentral, francecentral, centralus, eastus2, eastasia, westus, southafricanorth, northcentralus, brazilsouth, switzerlandnorth,
    australiasoutheast'."
      }
    }'
    ```
### <a name="storage-account-location-should-match-ai-component-location"></a>O local da conta de armazenamento deve corresponder ao local do componente do ia.
* Certifique-se de que o local do recurso Application Insights seja o mesmo que a conta de armazenamento.
    Mensagem de erro:
    ```powershell
    New-AzResourceGroupDeployment : 1:01:12 PM - Resource microsoft.insights/components/linkedStorageAccounts 'byos-test-centralus-ai/serviceprofiler' failed with message '{
      "error": {
        "code": "BadRequest",
        "message": "Storage account location should match AI component location",
        "innererror": {
          "trace": [
            "System.ArgumentException"
          ]
        }
      }
    }'
    ```

Para solucionar problemas gerais do criador de perfil, consulte a [documentação solução de problemas do criador de perfil](profiler-troubleshooting.md).

Para obter uma solução de problemas de Depurador de Instantâneos geral, consulte a [documentação de solução de problemas do depurador de instantâneos](snapshot-debugger-troubleshoot.md). 

## <a name="faqs"></a>Perguntas frequentes
* Se eu tiver o criador de perfil ou o instantâneo habilitado e, em seguida, eu habilitar o BYOS, meus dados serão migrados para minha conta de armazenamento?
    _Não, não vai._

* O BYOS funcionará com a criptografia em repouso e a chave Customer-Managed?
    _Sim, para ser preciso, BYOS é um requisito para ter o criador de perfil/depurador habilitado com chaves de Customer-Manager._

* O BYOS funcionará em um ambiente isolado da Internet?
    _Ok. Na verdade, o BYOS é um requisito para cenários de rede isolada._

* O BYOS funcionará quando, Customer-Managed chaves e o link privado estiverem habilitados? 
    _Sim, pode ser possível._

* Se eu tiver habilitado BYOS, posso voltar a usar as contas de armazenamento de serviços de diagnóstico para armazenar meus dados coletados? 
    _Sim, você pode, mas agora não oferecemos suporte à migração de dados do seu BYOS._

* Depois de habilitar a BYOS, assumirei todos os custos relacionados, que são o armazenamento e a rede? 
    _Sim_
