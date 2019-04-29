---
title: Continuidade dos negócios – Microsoft Genomics | Microsoft Docs
titleSuffix: Azure
description: Esta visão geral descreve os recursos que o Microsoft Genomics fornece para a continuidade dos negócios e a recuperação de desastre. Saiba mais sobre as opções para recuperação de eventos de interrupção, como uma interrupção de região do Azure, o que poderia causar perda de dados.
keywords: continuidade de negócios, recuperação de desastre
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 04/06/2018
ms.openlocfilehash: 7a51477dbbf6f4e50959a6d979342961c7e49ad9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60641102"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Visão geral da continuidade dos negócios com o Microsoft Genomics
Esta visão geral descreve os recursos que o Microsoft Genomics fornece para a continuidade dos negócios e a recuperação de desastre. Saiba mais sobre as opções para recuperação de eventos de interrupção, como uma interrupção de região do Azure, o que poderia causar perda de dados. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Recursos do Microsoft Genomics com suporte à continuidade de negócios 
Embora seja raro, um data center do Azure pode ter uma interrupção, o que poderia causar uma interrupção de negócios que pode durar de alguns minutos a algumas horas. Quando ocorrer uma interrupção, todos os trabalhos em execução no momento no data center falharão e o serviço Microsoft Genomics não reenviará automaticamente trabalhos para uma região secundária. 

* Uma opção é aguardar até que o data center retorne online quando a interrupção do data center terminar. Se a interrupção for pequena, o serviço Microsoft Genomics detectará automaticamente os trabalhos com falha e o fluxo de trabalho será reiniciado automaticamente.

* Outra opção é enviar proativamente o fluxo de trabalho em outra região de dados. O Microsoft Genomics implanta instâncias em várias [regiões do Azure](https://azure.microsoft.com/regions/services/), e cada instância específica da região é independente. Se uma das instâncias do Microsoft Genomics enfrentar uma falha local de região, outras regiões executando instâncias do Microsoft Genomics continuarão a processar os trabalhos. Essa transferência para uma região alternativa está sob o controle do usuário e está disponível a qualquer momento.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Faça failover manualmente de fluxos de trabalho do Microsoft Genomics para outra região
No caso de uma interrupção do data center regional, você pode optar por enviar fluxos de trabalho do Microsoft Genomics em uma região secundária, com base em soberania individual de dados e necessidades de continuidade de negócios. Para fazer failover manualmente de fluxos de trabalho do Microsoft Genomics, você usaria uma especificação diferente de região. Criar conta do Genomics e enviar o trabalho com credenciais apropriadas da conta de armazenamento e do Genomics específicas da região.

Especificamente, você precisará:
* Criar uma conta do Genomics na região secundária, usando o Portal do Azure. 
* Migrar os dados de entrada para uma conta de armazenamento na região secundária e configurar uma pasta de saída na região secundária.
* Enviar o fluxo de trabalho na região secundária.

Quando a região original for restaurada, o serviço Microsoft Genomics não migrará os dados da região secundária para a região original. Você pode optar por mover os arquivos de entrada e saída da região secundária de volta à região original.  Se você optar por mover seus dados, isso está fora do serviço do Genomics e todos os encargos relacionados à movimentação de dados é de sua responsabilidade. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Preparando para uma possível interrupção específica da região
Se você estiver preocupado com a recuperação mais rápida no caso de uma paralisação do data center, há algumas etapas que podem ser realizadas para reduzir o tempo necessário para você reenviar manualmente seus fluxos de trabalho do Microsoft Genomics para uma região secundária:

* Identifique uma região secundária apropriada e crie ativamente uma conta do Genomics nessa região
* Duplique os dados na região primária e secundária para que seus dados fiquem imediatamente disponíveis na região secundária. Isso pode ser feito manualmente ou usando o recurso [armazenamento com redundância geográfica](https://docs.microsoft.com/azure/storage/common/storage-redundancy) disponível no armazenamento do Azure. 

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu sobre as opções de continuidade de negócios e recuperação de desastres ao usar o serviço Microsoft Genomics. Para obter mais informações sobre continuidade de negócios e recuperação de desastres no Azure em geral, consulte [Orientação técnica de resiliência do Azure.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 