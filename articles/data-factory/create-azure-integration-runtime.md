---
title: Criar o Integration Runtime do Azure no Azure Data Factory | Microsoft Docs
description: "Saiba como criar o Integration Runtime do Azure no Azure Data Factory, que é usado para copiar dados e expedir atividades de transformação."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: spelluru
ms.openlocfilehash: e4e9416d75d5887de059a6fcfc66683940e3e6fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Como criar e configurar o Integration Runtime do Azure
O IR (Integration Runtime) é a infraestrutura de computação usada pelo Azure Data Factory para fornecer funcionalidades de integração de dados entre diferentes ambientes de rede. Para obter mais informações sobre o IR, consulte [Integration Runtime](concepts-integration-runtime.md).

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Documentação do Data Factory V1](v1/data-factory-introduction.md).

O IR do Azure fornece uma computação totalmente gerenciada para executar a movimentação de dados nativamente e expedir atividades de transformação de dados para serviços de computação como o HDInsight. Ele é hospedado no ambiente do Azure e dá suporte à conexão para recursos no ambiente de rede pública com pontos de extremidade públicos acessíveis.

Este documento apresenta como você pode criar e configurar o Integration Runtime do Azure. 

## <a name="default-azure-ir"></a>IR do Azure padrão
Por padrão, cada data factory tem um IR do Azure no back-end que dá suporte a operações em armazenamentos de dados na nuvem e serviços de computação na rede pública. O local desse IR do Azure é resolvido automaticamente. Se a propriedade **connectVia** não for especificada na definição do serviço vinculado, o IR do Azure especificado será usado. Você precisa criar explicitamente um IR do Azure apenas quando deseja definir explicitamente o local do IR ou se desejar agrupar virtualmente as execuções de atividade em diferentes IRs para fins de gerenciamento. 

## <a name="create-azure-ir"></a>Criar IR do Azure
O Integration Runtime pode ser criado usando o cmdlet do PowerShell **Set-AzureRmDataFactoryV2IntegrationRuntime**. Para criar um IR do Azure, você especifica o nome, o local e o tipo para o comando. Aqui está um exemplo de comando para criar um IR do Azure com o local definido como "Europa Ocidental":

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Para o IR do Azure, o tipo deve ser definido como **Managed**. Você não precisa especificar detalhes de computação porque ele é totalmente gerenciado elasticamente na nuvem. Especifique os detalhes de computação como o tamanho do nó e a contagem do nó quando desejar criar o IR Azure-SSIS. Para obter mais informações, consulte [Criar e configurar o IR do Azure-SSIS](create-azure-ssis-integration-runtime.md).

Você pode configurar um IR do Azure existente para alterar seu local usando o cmdlet do PowerShell Set-AzureRmDataFactoryV2IntegrationRuntime. Para obter mais informações sobre o local de um IR do Azure, consulte [Introdução ao Integration Runtime](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Usar o IR do Azure

Depois de criar um IR do Azure, você pode referenciá-lo em sua definição de Serviço Vinculado. Abaixo está um exemplo de como você pode referenciar o Integration Runtime do Azure criado acima de um Serviço Vinculado do Armazenamento do Azure:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=...",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Próximas etapas
Consulte os artigos a seguir para criar outros tipos de tempos de execução de integração:

- [Criar um Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md)
- [Criar o Integration Runtime do Azure-SSIS](create-azure-ssis-integration-runtime.md)
 
