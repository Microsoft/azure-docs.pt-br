---
title: Criar tempo de execução de integração do Azure no Azure Data Factory
description: Saiba como criar o Integration Runtime do Azure no Azure Data Factory, que é usado para copiar dados e expedir atividades de transformação.
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/09/2020
author: nabhishek
ms.author: abnarain
ms.openlocfilehash: ecf2a74a16234084fbac4d1c26157d1703b56a13
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373055"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Como criar e configurar o Integration Runtime do Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O IR (Integration Runtime) é a infraestrutura de computação usada pelo Azure Data Factory para fornecer funcionalidades de integração de dados entre diferentes ambientes de rede. Para obter mais informações sobre o IR, consulte [Integration Runtime](concepts-integration-runtime.md).

O IR do Azure fornece uma computação totalmente gerenciada para executar a movimentação de dados nativamente e expedir atividades de transformação de dados para serviços de computação como o HDInsight. Ele é hospedado no ambiente do Azure e dá suporte à conexão para recursos no ambiente de rede pública com pontos de extremidade públicos acessíveis.

Este documento apresenta como você pode criar e configurar o Integration Runtime do Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>IR do Azure padrão
Por padrão, cada data factory tem um IR do Azure no back-end que dá suporte a operações em armazenamentos de dados na nuvem e serviços de computação na rede pública. O local do Azure IR é autoresolver. Se a propriedade **connectVia** não for especificada na definição do serviço vinculado, o IR do Azure padrão será usado. Você precisa criar explicitamente um IR do Azure apenas quando deseja definir explicitamente o local do IR ou se desejar agrupar virtualmente as execuções de atividade em diferentes IRs para fins de gerenciamento. 

## <a name="create-azure-ir"></a>Criar IR do Azure

Para criar e configurar um Azure IR, você pode usar os procedimentos a seguir.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Criar um Azure IR via Azure PowerShell
Integration Runtime pode ser criado usando o cmdlet do PowerShell **set-AzDataFactoryV2IntegrationRuntime** . Para criar um Azure IR, especifique o nome, o local e o tipo para o comando. Aqui está um exemplo de comando para criar um IR do Azure com o local definido como "Europa Ocidental":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Para o IR do Azure, o tipo deve ser definido como **Managed**. Você não precisa especificar detalhes de computação porque ele é totalmente gerenciado elasticamente na nuvem. Especifique os detalhes de computação como o tamanho do nó e a contagem do nó quando desejar criar o IR Azure-SSIS. Para obter mais informações, consulte [Criar e configurar o IR do Azure-SSIS](create-azure-ssis-integration-runtime.md).

Você pode configurar um Azure IR existente para alterar seu local usando o cmdlet do PowerShell Set-AzDataFactoryV2IntegrationRuntime. Para obter mais informações sobre o local de um IR do Azure, consulte [Introdução ao Integration Runtime](concepts-integration-runtime.md).

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Criar um Azure IR por meio da interface do usuário do Azure Data Factory
Use as etapas a seguir para criar um Azure IR usando a interface do usuário do Azure Data Factory.

1. Na página **Vamos começar** da interface do usuário do Azure Data Factory, selecione a [guia Gerenciar](./author-management-hub.md) no painel mais à esquerda.

   ![O botão Gerenciar da home page](media/doc-common-process/get-started-page-manage-button.png)

1. Selecione **Runtimes de integração** no painel esquerdo e, em seguida, selecione **+ Novo**.

   ![Captura de tela que realça os tempos de execução de integração no painel esquerdo e no botão + novo.](media/doc-common-process/manage-new-integration-runtime.png)

1. Na página **instalação do Integration Runtime** , selecione **Azure, auto-hospedado** e, em seguida, selecione **continuar**. 

1. Na página a seguir, selecione **Azure** para criar um Azure ir e, em seguida, selecione **continuar**.
   ![Criar um Integration Runtime](media/create-azure-integration-runtime/new-azure-integration-runtime.png)

1. Insira um nome para sua Azure IR e selecione **criar**.
   ![Criar um Azure IR](media/create-azure-integration-runtime/create-azure-integration-runtime.png)

1. Você verá uma notificação pop-up quando a criação for concluída. Na página **tempos de execução de integração** , certifique-se de que você veja o ir recém-criado na lista.

## <a name="use-azure-ir"></a>Usar o IR do Azure

Depois de criar um IR do Azure, você pode referenciá-lo em sua definição de Serviço Vinculado. Abaixo está um exemplo de como você pode referenciar o Integration Runtime do Azure criado acima de um Serviço Vinculado do Armazenamento do Azure:

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Próximas etapas
Consulte os artigos a seguir para criar outros tipos de runtimes de integração:

- [Criar um Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md)
- [Criar o Integration Runtime do Azure-SSIS](create-azure-ssis-integration-runtime.md)
