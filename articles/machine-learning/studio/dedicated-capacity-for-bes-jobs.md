---
title: Trabalhos do serviço de lote do Azure
titleSuffix: Azure Machine Learning Studio
description: Visão geral dos serviços do lote do Azure para trabalhos do Machine Learning Studio. O processamento de Pool do Lote permite criar pools nos quais é possível enviar trabalhos em lotes.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio | Microsoft Docs'
ms.date: 04/19/2017
ms.openlocfilehash: 24efa3caba3918a38c09b1c921c600b117dedbc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751138"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-jobs"></a>Serviço de Lote do Azure para trabalhos do Azure Machine Learning Studio

O processamento em Pool do Lote do Machine Learning fornece escala gerenciada pelo cliente para o Serviço de Execução em Lotes do Azure Machine Learning. O processamento em lote clássico para o machine learning ocorre em um ambiente multilocatário, que limita o número de trabalhos simultâneos que você pode enviar e no qual os trabalhos são enfileirados na ordem primeiro a entrar, primeiro a sair. Essa incerteza significa que você não pode prever com exatidão quando seu trabalho será executado.

O processamento de Pool do Lote permite criar pools nos quais é possível enviar trabalhos em lotes. Você controla o tamanho do pool e para qual pool o trabalho é enviado. O trabalho BES é executado em seu próprio espaço de processamento, proporcionando desempenho de processamento previsível e a capacidade de criar pools de recursos que correspondam à carga de processamento que você envia.

> [!NOTE]
> Você deve ter um novo Gerenciador de Recursos baseado em serviço Web do Machine Learning para criar um pool. Depois de criado, é possível executar qualquer serviço Web do BES, clássico e baseado no Novo Resource Manager, no pool.

## <a name="how-to-use-batch-pool-processing"></a>Como usar o processamento Pool do Lote

A configuração de processamento de Pool do Lote não está disponível atualmente pelo Portal do Azure. Para usar o processamento de Pool do Lote, você deve:

-   Chamar o CSS para criar uma Conta do Pool do Lote e obter uma URL do Serviço do Pool e uma chave de autorização
-   Criar um serviço Web baseado no novo Resource Manager e um plano de cobrança

Para criar sua conta, ligue para o CSS (Serviço de Suporte e Atendimento ao Cliente) Microsoft e forneça a ID da sua Assinatura. Com você, o CSS determina a capacidade apropriada para seu cenário. Assim, o CSS configura sua conta com o número máximo de pools que você pode criar e o número máximo de VMs (máquinas virtuais) que podem ser colocadas em cada pool. Assim que sua conta estiver configurada, você receberá a URL de Serviço do Pool e uma chave de autorização.

Depois que sua conta for criada, use a URL de Serviço do Pool e a chave de autorização para executar operações de gerenciamento de pool no Pool do Lote.

![Arquitetura do serviço do pool do lote.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Crie pools chamando a operação Criar Pool na URL de serviço do pool que o CSS forneceu a você. Ao criar um pool, especifique o número de VMs e a URL do swagger.json de um serviço Web do Machine Learning baseado no Novo Resource Manager. Esse serviço Web é fornecido para estabelecer a associação de cobrança. O serviço de Pool do Lote usa o swagger.json para associar o pool a um plano de cobrança. É possível executar qualquer serviço Web do BES, clássico e baseado no Novo Resource Manager, no pool.

Você pode usar qualquer serviço Web baseado no Novo Resource Manager, mas esteja ciente de que a cobrança pelos trabalhos é feita no plano de cobrança associado a esse serviço. Talvez você queira criar um serviço Web e o novo plano de cobrança especificamente para execução de trabalhos do Pool do Lote.

Para saber mais sobre como criar serviços Web, confira [Implantar um serviço Web do Machine Learning do Azure](publish-a-machine-learning-web-service.md).

Depois de ter criado um pool, você envia o trabalho do BES usando a URL de Solicitações em Lotes para o serviço Web. Você pode optar por enviá-lo a um pool ou ao processamento em lotes clássico. Para enviar um trabalho ao processamento Pool do Lote, você adiciona o seguinte parâmetro ao corpo da solicitação de envio do trabalho:

"AzureBatchPoolId":"&lt;pool ID&gt;"

Se você não adicionar o parâmetro, o trabalho será executado no ambiente de processo em lote clássico. Se o pool tiver recursos disponíveis, o trabalho começará a ser executado imediatamente. Se o pool não tiver recursos livres, seu trabalho ficará na fila até que um recurso esteja disponível.

Caso atinja regularmente a capacidade de seus pools e precise aumentar a capacidade, você poderá chamar o CSS e trabalhar com um representante para aumentar suas cotas.

Solicitação de exemplo:

https://ussouthcentral.services.azureml.net/subscriptions/80c77c7674ba4c8c82294c3b2957990c/services/9fe659022c9747e3b9b7b923c3830623/jobs?api-version=2.0

```json
{

    "Input":{
    
        "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpoint
        =https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://zhguim
        l.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;;",
        
        "BaseLocation":null,
        
        "RelativeLocation":"testint/AdultCensusIncomeBinaryClassificationDataset.csv",
        
        "SasBlobToken":null
    
    },
    
    "GlobalParameters":{ },
    
    "Outputs":{
    
        "output1":{
        
            "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpo
            int=https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://sampleaccount.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;",
            "BaseLocation":null,
            "RelativeLocation":"testintoutput/testint\_results.csv",
            
            "SasBlobToken":null
        
        }
    
    },
    
    "AzureBatchPoolId":"8dfc151b0d3e446497b845f3b29ef53b"

}
```

## <a name="considerations-when-using-batch-pool-processing"></a>Considerações ao usar o processamento Pool do Lote

O processamento Pool do Lote é um serviço faturável sempre ativo que exige que você o associe a um plano de cobrança baseado no Resource Manager. Você será cobrado pelo número de horas de computação que o pool estiver em execução; independentemente do número de trabalho em execução durante esse pool de tempo. Caso crie um pool, você será cobrado pelas horas de computação de cada máquina virtual no pool até que o pool seja excluído, mesmo que não haja trabalhos em lotes em execução no pool. A cobrança pelas máquinas virtuais começa quando o provisionamento delas for finalizado e para quando elas tiverem sido excluídas. Você pode usar qualquer um dos planos encontrados na [página de preços do Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

Exemplo de cobrança:

Caso você crie um Pool do Lote com 2 máquinas virtuais e o exclua após 24 horas, serão debitadas 48 horas de computação do seu plano de cobrança, independentemente de quantos trabalhos foram executados durante esse período.

Caso você crie um Pool do Lote com 4 máquinas virtuais e o exclua após 12 horas, também serão debitadas 48 horas de computação do seu plano de cobrança.

É recomendável examinar o status do trabalho para determinar quando os trabalhos são concluídos. Ao fim da execução de todos os seus trabalhos, chame a operação Redimensionar Pool para definir o número de máquinas virtuais no pool para zero. Se seus recursos de pool não forem suficientes e for necessário criar um novo pool, por exemplo, para cobrança em outro plano, você poderá excluir o pool quando a execução de todos os trabalhos for finalizada.


| **Use o Processamento Pool do Lote quando**    | **Use o processamento em lotes clássico quando**  |
|---|---|
|Você precisar executar um grande número de trabalhos<br>Ou<br/>Você precisar saber que seus trabalho serão executados imediatamente<br/>Ou<br/>Você precisar de taxa de transferência garantida. Por exemplo, você precisa executar vários trabalhos em um determinado intervalo e deseja expandir os recursos de computação para atender às suas necessidades.    | Você estiver executando apenas alguns trabalhos<br/>e<br/> Você não precisar que os trabalhos sejam executados imediatamente |
