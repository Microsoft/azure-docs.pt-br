---
title: Guia de desempenho e escalabilidade da atividade de cópia
description: Saiba mais sobre os principais fatores que afetam o desempenho da movimentação de dados em Azure Data Factory quando você usa a atividade de cópia.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/15/2020
ms.openlocfilehash: 1c166b99243e5a6ee576100b8470aa38b9535c7a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387658"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Guia de desempenho e escalabilidade da atividade de cópia

> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-activity-performance.md)
> * [Versão atual](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Às vezes, você deseja executar uma migração de dados em larga escala do data Lake ou Enterprise data warehouse (EDW) para o Azure. Outras vezes você deseja ingerir grandes quantidades de dados, de diferentes fontes para o Azure, para Big Data Analytics. Em cada caso, é essencial alcançar o desempenho e a escalabilidade ideais.

Azure Data Factory (ADF) fornece um mecanismo para ingerir dados. O ADF tem as seguintes vantagens:

* Lida com grandes quantidades de dados
* Tem alto desempenho
* É econômico

Essas vantagens tornam o ADF uma ótima opção para engenheiros de dados que desejam criar pipelines de ingestão de dados escalonáveis que são altamente com alto desempenho.

Depois de ler este artigo, você poderá responder as seguintes perguntas:

* Que nível de desempenho e escalabilidade posso conseguir usando a atividade de cópia do ADF para cenários de migração de dados e de ingestão de dados?
* Quais etapas devo tomar para ajustar o desempenho da atividade de cópia do ADF?
* Quais botões de otimização de desempenho do ADF posso utilizar para otimizar o desempenho de uma única execução da atividade de cópia?
* Quais outros fatores fora do ADF a considerar ao otimizar o desempenho da cópia?

> [!NOTE]
> Se você não estiver familiarizado com a atividade de cópia em geral, consulte a [visão geral da atividade de cópia](copy-activity-overview.md) antes de ler este artigo.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Desempenho de cópia e escalabilidade atingíveis usando o ADF

O ADF oferece uma arquitetura sem servidor que permite paralelismo em diferentes níveis.

Essa arquitetura permite que você desenvolva pipelines que maximizam a taxa de transferência de movimentação de dados para seu ambiente. Esses pipelines utilizam totalmente os seguintes recursos:

* Largura de banda da rede
* Operações de entrada/saída de armazenamento por segundo (IOPS) e largura de banda

Essa utilização completa significa que você pode estimar a taxa de transferência geral medindo a taxa de transferência mínima disponível com os seguintes recursos:

* Armazenamento de dados de origem
* Armazenamento de dados de destino
* Largura de banda de rede entre os armazenamentos de dados de origem e de destino

A tabela a seguir calcula a duração da cópia. A duração é baseada no tamanho dos dados e no limite de largura de banda do seu ambiente.

&nbsp;

| Tamanho dos dados/ <br/> largura de banda | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | mínimo de 2,7    | mínimo de 1,4   | mínimo de 0,3   | mínimo de 0,1  | mínimo de 0, 3 | mínimo de 0, 1 | mínimo de 0,0   |
| **10 GB**                   | mínimo de 27,3   | mínimo de 13,7  | mínimo de 2,7   | mínimo de 1,3  | mínimo de 0,3  | mínimo de 0,1  | mínimo de 0, 3  |
| **100 GB**                  | 4,6 horas    | 2,3 horas   | 0,5 horas   | 0,2 horas  | 0, 5 horas | 0, 2 horas | 0,0 horas   |
| **1 TB**                    | 46,6 horas   | 23,3 horas  | 4,7 horas   | 2,3 horas  | 0,5 horas  | 0,2 horas  | 0, 5 horas  |
| **10 TB**                   | 19,4 dias  | 9,7 dias  | 1,9 dias  | 0,9 dias | 0,2 dias | 0,1 dias | 0, 2 dias |
| **100 TB**                  | 194,2 dias | 97,1 dias | 19,4 dias | 9,7 dias | 1,9 dias | 1 dia    | 0,2 dias  |
| **1 PB**                    | 64,7 mo    | 32,4 mo   | 6,5 mo    | 3,2 mo   | 0,6 mo   | 0,3 mo   | 0, 6 mo   |
| **10 PB**                   | 647,3 mo   | 323,6 mo  | 64,7 mo   | 31,6 mo  | 6,5 mo   | 3,2 mo   | 0,6 mo    |
| | |  | | |  | | |

A cópia do ADF é escalonável em diferentes níveis:

![como a cópia do ADF é dimensionada](media/copy-activity-performance/adf-copy-scalability.png)

* O fluxo de controle do ADF pode iniciar várias atividades de cópia em paralelo, por exemplo, usando o [loop For Each](control-flow-for-each-activity.md).

* Uma única atividade de cópia pode aproveitar os recursos de computação escalonáveis.
  * Ao usar o IR (Integration Runtime) do Azure, você pode especificar [até 256 DIUs (unidades de integração de dados)](#data-integration-units) para cada atividade de cópia, de maneira sem servidor.
  * Ao usar o IR auto-hospedado, você pode adotar uma das seguintes abordagens:
    * Escalar verticalmente o computador manualmente.
    * Escalar horizontalmente para vários computadores ([até 4 nós](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) e uma única atividade de cópia particionará seu conjunto de arquivos em todos os nós.

* Uma única atividade de cópia lê e grava no armazenamento de dados usando vários threads [em paralelo](#parallel-copy).

## <a name="performance-tuning-steps"></a>Etapas de ajuste do desempenho

Execute as seguintes etapas para ajustar o desempenho do seu serviço de Azure Data Factory com a atividade de cópia:

1. **Pegue um conjunto de testes e estabeleça uma linha de base.**

    Durante o desenvolvimento, teste seu pipeline usando a atividade de cópia em um exemplo de dados representativos. O conjunto de dados que você escolher deve representar seus padrões de dado típicos ao longo dos seguintes atributos:

    * Estrutura de pastas
    * Padrão de arquivo
    * Esquema de dados

    E seu conjunto de espaço de os deve ser grande o suficiente para avaliar o desempenho da cópia. Um bom tamanho leva pelo menos 10 minutos para que a atividade de cópia seja concluída. Coletar detalhes de execução e características de desempenho após o [monitoramento da atividade de cópia](copy-activity-monitoring.md).

2. **Como maximizar o desempenho de uma única atividade de cópia**:

    Recomendamos que você primeiro maximize o desempenho usando uma única atividade de cópia.

    * **Se a atividade de cópia estiver sendo executada em um tempo de execução de integração _do Azure_ :**

        Comece com valores padrão para [DIU (unidades de integração de dados)](#data-integration-units) e configurações de [cópia paralelas](#parallel-copy) .

    * **Se a atividade de cópia estiver sendo executada em um tempo de execução de integração _auto-hospedado_ :**

        Recomendamos que você use um computador dedicado para hospedar o IR. O computador deve ser separado do servidor que hospeda o armazenamento de dados. Comece com valores padrão para a configuração de [cópia paralela](#parallel-copy) e use um único nó para o ir auto-hospedado.

    Realize uma execução de teste de desempenho. Anote o desempenho obtido. Inclua os valores reais usados, como DIUs e cópias paralelas. Consulte [monitoramento de atividade de cópia](copy-activity-monitoring.md) sobre como coletar resultados de execução e configurações de desempenho usadas. Saiba como [solucionar problemas de desempenho da atividade de cópia](copy-activity-performance-troubleshooting.md) para identificar e resolver o afunilamento.

    Iterar para realizar execuções de teste de desempenho adicionais seguindo as diretrizes de solução de problemas e ajuste. Após a execução de uma única atividade de cópia não conseguir uma melhor taxa de transferência, considere a possibilidade de maximizar a taxa de transferência agregada executando várias cópias simultaneamente. Essa opção é discutida no próximo marcador numerado.

3. **Como maximizar a taxa de transferência agregada executando várias cópias simultaneamente:**

    Agora, você maximizou o desempenho de uma única atividade de cópia. Se ainda não tiver atingido os limites superiores da taxa de transferência do seu ambiente, você poderá executar várias atividades de cópia em paralelo. Você pode executar em paralelo usando construções de fluxo de controle do ADF. Um desses constructos é o [loop for each](control-flow-for-each-activity.md). Para obter mais informações, consulte os seguintes artigos sobre modelos de solução:

    * [Copiar arquivos de vários contêineres](solution-template-copy-files-multiple-containers.md)
    * [Migrar dados do Amazon S3 para o ADLS Gen2](solution-template-migration-s3-azure.md)
    * [Cópia em massa com uma tabela de controle](solution-template-bulk-copy-with-control-table.md)

4. **Expanda a configuração para todo o conjunto de seus conjuntos de seus.**

    Quando estiver satisfeito com os resultados e o desempenho da execução, você poderá expandir a definição e o pipeline para abranger todo o seu conjunto de todos.

## <a name="troubleshoot-copy-activity-performance"></a>Solucionar problemas de desempenho da atividade de cópia

Siga as [etapas de ajuste de desempenho](#performance-tuning-steps) para planejar e conduzir o teste de desempenho para seu cenário. E saiba como solucionar o problema de desempenho de cada execução da atividade de cópia no Azure Data Factory de [solucionar problemas de desempenho da atividade de cópia](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Copiar recursos de otimização de desempenho

O Azure Data Factory fornece os seguintes recursos de otimização de desempenho:

* [Unidades de integração de dados](#data-integration-units)
* [Escalabilidade do tempo de execução de integração auto-hospedado](#self-hosted-integration-runtime-scalability)
* [Cópia paralela](#parallel-copy)
* [Cópia em etapas](#staged-copy)

### <a name="data-integration-units"></a>Unidades de integração de dados

Uma DIU (unidade de integração de dados) é uma medida que representa a potência de uma única unidade em Azure Data Factory. A potência é uma combinação de CPU, memória e alocação de recursos de rede. DIU se aplica somente ao [Integration Runtime do Azure](concepts-integration-runtime.md#azure-integration-runtime). DIU não se aplica ao [tempo de execução de integração auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime). [Saiba mais aqui](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Escalabilidade do tempo de execução de integração auto-hospedado

Talvez você queira hospedar uma carga de trabalho simultânea cada vez maior. Ou talvez você queira obter um desempenho maior em seu nível de carga de trabalho atual. Você pode aprimorar a escala de processamento pelas seguintes abordagens:

* Você _pode escalar_ verticalmente o ir via hospedagem interna, aumentando o número de [trabalhos simultâneos](create-self-hosted-integration-runtime.md#scale-up) que podem ser executados em um nó.  
Escalar verticalmente funcionará somente se o processador e a memória do nó estiverem sendo menores que totalmente utilizados.
* Você _pode escalar_ horizontalmente o ir de hospedagem interna, adicionando mais nós (máquinas).

Para obter mais informações, consulte:

* [Recursos de otimização de desempenho da atividade de cópia: escalabilidade do tempo de execução de integração autohospedado](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)
* [Criar e configurar um tempo de execução de integração auto-hospedado: considerações de escala](create-self-hosted-integration-runtime.md#scale-considerations)

### <a name="parallel-copy"></a>Cópia paralela

Você pode definir a `parallelCopies` propriedade para indicar o paralelismo que deseja que a atividade de cópia use. Considere essa propriedade como o número máximo de threads na atividade de cópia. Os threads operam em paralelo. Os threads lêem da origem ou gravam em seus armazenamentos de dados do coletor. [Saiba mais](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Cópia em etapas

Uma operação de cópia de dados pode enviar os dados _diretamente_ para o armazenamento de dados do coletor. Como alternativa, você pode optar por usar o armazenamento de BLOBs como um armazenamento de _preparo provisório_ . [Saiba mais](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Próximas etapas

Confira os outros artigos sobre atividade de cópia:

* [Visão geral da atividade de cópia](copy-activity-overview.md)
* [Solucionar problemas de desempenho da atividade de cópia](copy-activity-performance-troubleshooting.md)
* [Recursos de otimização de desempenho da atividade de cópia](copy-activity-performance-features.md)
* [Use Azure Data Factory para migrar dados de seu data Lake ou data warehouse para o Azure](data-migration-guidance-overview.md)
* [Migrar dados do Amazon S3 para o Armazenamento do Azure](data-migration-guidance-s3-azure-storage.md)
