---
title: Solucionar problemas do Data Factory do Azure
description: Saiba como solucionar problemas com o uso do Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
author: gauravmalhot
ms.author: gamal
ms.reviewer: maghan
manager: craigg
robots: noindex
ms.openlocfilehash: cc880885777cbca67d6fb39b90feadc889339f76
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836171"
---
# <a name="troubleshoot-data-factory-issues"></a>Solucionar problemas do Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. 

Esse artigo fornece dicas de solução de problemas ao usar o Azure Data Factory. Este artigo não lista todos os possíveis problemas ao usar o serviço, mas abrange alguns problemas e dicas de solução geral de problemas.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Erro: a assinatura não está registrada para usar o namespace 'Microsoft.DataFactory'
Caso você receba esse erro, o provedor de recursos do Azure Data Factory não foi registrado no seu computador. Faça o seguinte:

1. Inicie o Azure PowerShell.
2. Faça logon na conta do Azure usando o comando a seguir.

    ```powershell
    Connect-AzAccount
    ```
3. Execute o seguinte comando para registrar o provedor do Azure Data Factory.

    ```powershell        
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problema: erro não autorizado ao executar um cmdlet da Data Factory
Você provavelmente não está usando a assinatura ou conta do Azure correta com o Azure PowerShell. Use os cmdlets a seguir para selecionar a assinatura e conta do Azure corretas para usar com o Azure PowerShell.

1. Conectar-se-AzAccount - Use a ID de usuário correto e a senha
2. Get-AzSubscription - exibir todas as assinaturas para a conta.
3. Selecione AzSubscription &lt;o nome da assinatura&gt; -selecione a assinatura correta. Use a mesma assinatura usada para criar um data factory no portal do Azure.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problema: falha ao inicializar a Configuração Expressa do Gateway de Gerenciamento de Dados no Portal do Azure
A instalação Expressa do Gateway de Gerenciamento de Dados requer o Internet Explorer ou um navegador da Web compatível com Microsoft ClickOnce. Se a Instalação Expressa não for iniciada, siga um destes procedimentos:

* Use o Internet Explorer ou um navegador da Web compatível com o Microsoft ClickOnce.

    Se você estiver usando o Chrome, vá para a [loja na Web do Chrome](https://chrome.google.com/webstore/), pesquise a palavra-chave "ClickOnce", escolha uma das extensões do ClickOnce e instale-a.

    Faça o mesmo para o Firefox (instalar o suplemento). Clique no botão Abrir menu na barra de ferramentas (três linhas horizontais no canto superior direito), clique em Complementos, pesquise a palavra-chave "ClickOnce", escolha uma das extensões do ClickOnce e instale-a.
* Use o link **Configuração Manual** mostrado na mesma folha no portal. Use essa abordagem para baixar o arquivo de instalação e executá-lo manualmente. Depois que a instalação for bem-sucedida, você verá a caixa de diálogo Configuração do Gateway de Gerenciamento de Dados. Copie a **chave** na tela do portal e use-a no gerenciador de configuração para registrar manualmente o gateway com o serviço.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problema: falha ao se conectar ao SQL Server local
Inicie o **Gerenciador de Configuração de Gateway de Gerenciamento de Dados** no computador do gateway e use a guia **Solução de Problemas** para testar a conexão ao SQL Server do computador do gateway. Consulte [Solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para ver dicas sobre como solucionar os problemas relacionados à conexão/gateway.   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problema: As fatias de entrada ficam sempre no estado Aguardando
As fatias podem estar no estado **Aguardando** devido a vários motivos. Um dos motivos comuns é que a propriedade **external** não está definida como **true**. Qualquer conjunto de dados que seja produzido fora do escopo do Azure Data Factory deve ser marcado com a propriedade **external** . Essa propriedade indica que os dados são externos e não são compatíveis com pipelines no data factory. As fatias de dados são marcadas como **Pronto** depois que os dados estão disponíveis no respectivo armazenamento.

Consulte o exemplo a seguir para o uso da propriedade **external** . Como opção, você pode especificar **externalData*** quando definir external como true.

Consulte o artigo [Conjuntos de dados](data-factory-create-datasets.md) para obter mais detalhes sobre essa propriedade.

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

Para resolver o erro, adicione a propriedade **external** e a seção opcional **externalData** à definição de JSON da tabela de entrada e recrie a tabela.

### <a name="problem-hybrid-copy-operation-fails"></a>Problema: falha na operação de cópia híbrida
Consulte [Solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para ver as etapas para solucionar os problemas de cópia para/a partir de um armazenamento de dados local usando o Gateway de Gerenciamento de Dados.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problema: falha no provisionamento sob demanda do HDInsight
Ao usar um serviço vinculado do tipo HDInsightOnDemand, você deve especificar um linkedServiceName que aponta para o Armazenamento de Blobs do Azure. O serviço do Data Factory usa esse armazenamento para armazenar logs e arquivos de suporte para seu cluster do HDInsight sob demanda.  Às vezes, o provisionamento de um cluster de HDInsight sob demanda falha com o seguinte erro:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Esse erro normalmente indica que o local da conta de armazenamento especificado no linkedServiceName não está no mesmo local de datacenter, no qual o provisionamento do HDInsight está ocorrendo. Exemplo: se seu data factory estiver no Oeste dos EUA e o armazenamento do Azure estiver no Leste dos EUA, o provisionamento sob demanda falhará no Oeste dos EUA.

Além disso, há uma segunda propriedade JSON additionalLinkedServiceNames, em que as contas de armazenamento adicionais podem ser especificadas no HDInsight sob demanda. Essas contas de armazenamento adicionais vinculadas devem estar no mesmo local que o cluster HDInsight, ou falharão com o mesmo erro.

### <a name="problem-custom-net-activity-fails"></a>Problema: Falha de atividade .NET personalizada
Consulte [Depurar um pipeline com atividade personalizada](data-factory-use-custom-activities.md#troubleshoot-failures) para obter etapas detalhadas.

## <a name="use-azure-portal-to-troubleshoot"></a>Usar o portal do Azure para solucionar o problema
### <a name="using-portal-blades"></a>Usando as folhas do portal
Consulte [Monitorar pipeline](data-factory-monitor-manage-pipelines.md) para obter as etapas.

### <a name="using-monitor-and-manage-app"></a>Usando o Aplicativo Monitorar e Gerenciar
Consulte [Monitorar e gerenciar os pipelines do Data Factory usando o Aplicativo de Monitoramento e Gerenciamento](data-factory-monitor-manage-app.md) para obter detalhes.

## <a name="use-azure-powershell-to-troubleshoot"></a>Usar o Azure PowerShell para solucionar o problema
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Usar o Azure PowerShell para solucionar o erro
Consulte [Monitorar pipelines do Data Factory usando o Azure PowerShell](data-factory-monitor-manage-pipelines.md) para obter detalhes.

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: https://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
