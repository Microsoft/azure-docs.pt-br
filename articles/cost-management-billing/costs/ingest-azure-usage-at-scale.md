---
title: Recuperar grandes conjuntos de dados de custo de modo recorrente com exportações
description: Este artigo ajuda você a exportar regularmente grandes quantidades de dados com exportações do Gerenciamento de Custos do Azure.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 465225341bdffc984ac6cbc82ba94eb656ad60df
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509610"
---
# <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Recuperar grandes conjuntos de dados de custo de modo recorrente com exportações

Este artigo ajuda você a exportar regularmente grandes quantidades de dados com exportações do Gerenciamento de Custos do Azure. A exportação é a maneira recomendada para recuperar dados de custos não agregados. Especialmente quando os arquivos de uso são muito grandes para serem chamados e baixados de maneira confiável usando a API de Detalhes de Uso. Os dados exportados serão colocados na conta do Armazenamento do Azure que você escolher. Nela, você poderá carregá-los nos próprios sistemas e analisá-los conforme necessário. Para configurar as exportações no portal do Azure, confira [Exportar dados](tutorial-export-acm-data.md).

Caso você deseje automatizar as exportações em vários escopos, a solicitação de API de exemplo da próxima seção será um bom ponto de partida. Use a API de Exportações para criar exportações automáticas como parte da configuração de ambiente geral. As exportações automáticas ajudam a garantir que você tenha os dados de que precisa. Use-os nos próprios sistemas da organização conforme expandir o seu uso do Azure.

## <a name="common-export-configurations"></a>Configurações comuns de exportação

Antes de criar sua primeira exportação, considere seu cenário e as opções de configuração necessárias para habilitá-lo. Considere as seguintes opções de exportação:

- **Recorrência**: determina com que frequência o trabalho de exportação é executado e quando um arquivo é colocado na sua conta do Armazenamento do Azure. Escolha entre as opções Diária, Semanal e Mensal. Tente configurar sua recorrência para que corresponda aos trabalhos de importação de dados usados pelo sistema interno da sua organização.
- **Período de Recorrência**: determina por quanto tempo a exportação permanecerá válida. Os arquivos são exportados somente durante o período de recorrência.
- **Período**: determina o volume de dados gerado pela exportação em determinada execução. As opções comuns são MonthToDate e WeekToDate.
- **StartDate**: configura quando você deseja que a agenda de exportação seja iniciada. Uma exportação é criada na StartDate e, posteriormente, com base na Recorrência.
- **Tipo**: há três tipos de exportação:
  - ActualCost: mostra o uso total e os custos para o período especificado, conforme eles são acumulados e mostrados na sua fatura.
  - AmortizedCost: mostra o uso total e os custos para o período especificado, com amortização aplicada aos custos de compra de reserva aplicáveis.
  - Usage: todas as exportações criadas antes de 20 de julho de 2020 são do tipo Usage. Atualize todas as exportações agendadas como ActualCost ou AmortizedCost.
- **Colunas**: define os campos de dados que você deseja incluir no arquivo de exportação. Elas correspondem aos campos disponíveis na API de Detalhes de Uso. Para obter mais informações, confira [API de Detalhes de Uso](/rest/api/consumption/usagedetails/list).

## <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Criar uma exportação diária do mês atual para uma assinatura

URL da solicitação: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

## <a name="copy-large-azure-storage-blobs"></a>Copiar grandes blobs de armazenamento do Azure

Você pode usar o Gerenciamento de Custos para agendar exportações dos detalhes de uso do Azure para suas contas de armazenamento do Azure como blobs. Os tamanhos de blob resultantes podem ter mais de gigabytes de tamanho. A equipe de Gerenciamento de Custos do Azure trabalhou com a equipe de Armazenamento do Azure para testar a cópia de grandes blobs de armazenamento. Os resultados estão documentados nas seções a seguir. Você pode esperar ter resultados semelhantes ao copiar blobs de armazenamento de uma região do Azure para outra.

Com o intuito de testar seu desempenho, a equipe transferiu os blobs das contas de armazenamento na região Oeste dos EUA para a mesma região e para outras. A equipe mediu as velocidades, que variaram de 2 GB por segundo na mesma região a 150 MB por segundo em contas de armazenamento na região do Sudeste da Ásia.

### <a name="test-configuration"></a>Configuração de teste

Para medir as velocidades de transferência de blobs, a equipe criou um aplicativo de console .NET simples que faz referência à versão mais recente (v 2.0.1) da DLM (Biblioteca de Movimentação de Dados) do Azure via NuGet. A DLM é um SDK fornecido pela equipe de Armazenamento do Azure que facilita o acesso programático aos seus serviços de transferência. Eles criaram contas de armazenamento Standard V2 em várias regiões e usaram Oeste dos EUA como a região de origem. Eles preencheram as contas de armazenamento com contêineres, cada um contendo dez blobs de blocos de 2 GB. Depois, copiaram os contêineres para outras contas de armazenamento usando o método _TransferManager.CopyDirectoryAsync()_ do DLM com a opção _CopyMethod.ServiceSideSyncCopy_. Testes foram conduzidos em um computador que executa o Windows 10 com 12 núcleos e uma rede de 1 GbE.

Configurações de aplicativo usadas:

- _TransferManager.Configurations.ParallelOperations_ = _Environment.ProcessorCount \* 32_. A equipe descobriu que a configuração tem o maior efeito na taxa de transferência geral. Um valor de 32 vezes o número de núcleos forneceu a melhor taxa de transferência para o cliente de teste.
- _ServicePointManager.DefaultConnectionLimit = int.MaxValue_. Configurar como um valor máximo transmite efetivamente o controle total do paralelismo de transferência para a configuração _ParallelOperations_ acima.
- _TransferManager.Configurations.BlockSize = 4,194,304_. Teve algum efeito sobre as taxas de transferência com 4 MB, provando que é melhor para os testes.

Para obter mais informações e código de exemplo, confira os links na seção [Próximas etapas](#next-steps).

### <a name="test-results"></a>Resultados do teste

| **Número do teste** | **Para a região** | **Blobs** | **Tempo (segundos)** | **MB/s** | **Comentários** |
| --- | --- | --- | --- | --- | --- |
| 1 | WestUS | 2 GB x 10 | 10 | 2\.000 |   |
| 2 | WestUS2 | 2 GB x 10 | 33 | 600 |   |
| 3 | EastUS | 2 GB x 10 | 67 | 300 |   |
| 4 | EastUS | 2 GB x 10 x 4 | 99 | 200 | Quatro transferências paralelas usando oito contas de armazenamento: média por transferência de quatro Oeste para quatro Leste |
| 6 | EastUS | 2 GB x 10 x 4 | 92 | 870 | Quatro transferências paralelas de uma conta de armazenamento para outra |
| 5 | EastUS | 2G x 10 x 8 | 148 | 135 | Oito transferências paralelas usando oito contas de armazenamento: média por transferência de quatro Oeste para quatro x dois Leste |
| 7 | Sudeste da Ásia | 2 GB x 10 | 133 | 150 |   |
| 8 | Sudeste da Ásia | 2 GB x 10 x 4 | 444 | 180 | Quatro transferências paralelas de uma conta de armazenamento para outra |

### <a name="sync-transfer-characteristics"></a>Características da transferência síncrona

Confira aqui algumas das características da transferência síncrona do lado do serviço usada com DML que são relevantes para seu uso:

- O DML pode transferir um único blob ou um diretório. Para a transferência de diretório, você pode usar um padrão de pesquisa para corresponder ao prefixo do blob.
- As transferências do blob de blocos acontecem em paralelo. Tudo é concluído próximo ao final do processo de transferência. Blocos de blob individuais são transferidos em paralelo.
- A transferência é executada de forma assíncrona no cliente. O status da transferência está disponível periodicamente por meio de um retorno de chamada para um método que pode ser definido em um objeto _TransferContext_.
- A transferência cria pontos de verificação durante seu progresso e expõe um objeto _TransferCheckpoint_. O objeto representa o ponto de verificação mais recente por meio do objeto _TransferContext_. Se o _TransferCheckpoint_ for salvo antes de uma transferência ser cancelada/anulada, a transferência poderá ser retomada do ponto de verificação por até sete dias. A transferência pode ser retomada de qualquer ponto de verificação, não apenas do mais recente.
- Se o processo de transferência do cliente for eliminado e reiniciado sem implementar o recurso de ponto de verificação:
  - Antes de todas as transferências de blob serem concluídas, a transferência será reiniciada.
  - Depois que alguns dos blobs forem concluídos, a transferência será reiniciada apenas para os blobs incompletos.
- Pausar a execução do cliente pausa as transferências.
- O recurso de transferência do blob abstrai o cliente de falhas transitórias. Por exemplo, a limitação da conta de armazenamento normalmente não causará uma falha na transferência, mas a tornará mais lenta.
- As transferências do lado do serviço têm baixo uso de recursos do cliente para CPU e memória, alguma largura de banda de rede e conexões.

### <a name="async-transfer-characteristics"></a>Características da transferência assíncrona

Você pode invocar o método _TransferManager.CopyDirectoryAsync()_ com a opção _CopyMethod.ServiceSideAsyncCopy_. Ele funciona de forma semelhante ao mecanismo de transferência síncrona da perspectiva do cliente, mas com as seguintes diferenças na operação:

- As taxas de transferência são muito mais lentas do que a transferência de sincronização equivalente (normalmente, 10 MB/s ou menos).
- A transferência continuará mesmo que o processo do cliente seja encerrado.
- Embora haja suporte para pontos de verificação, a retomada de uma transferência usando um _TransferCheckpoint_ não retomará no momento do ponto de verificação, mas no estado atual da transferência.

### <a name="test-summary"></a>Resumo do teste

O Armazenamento de Blobs do Azure dá suporte a altas taxas de transferência globais com seu recurso de transferência síncrona do lado do serviço. O uso do recurso em aplicativos .NET é simples por meio da Biblioteca de Movimentação de Dados. É possível que as exportações de Gerenciamento de Custos copiem centenas de gigabytes de dados de forma confiável para uma conta de armazenamento em qualquer lugar em menos de uma hora.

## <a name="next-steps"></a>Próximas etapas

- Confira a fonte [Biblioteca de Movimentação de Dados do Armazenamento do Microsoft Azure](https://github.com/Azure/azure-storage-net-data-movement).
- [Transferir dados com a Biblioteca de Movimentação de Dados](../../storage/common/storage-use-data-movement-library.md).
- Confira a amostra da fonte do [aplicativo de exemplo AzureDmlBackup](https://github.com/markjbrown/AzureDmlBackup).
- Leia [Alta taxa de transferência com o Armazenamento de Blobs do Azure](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage).