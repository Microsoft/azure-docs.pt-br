---
title: Configurar o desempenho para o tempo de execução de integração do Azure-SSIS | Microsoft Docs
description: Saiba como configurar as propriedades do Microsoft Integration Runtime do Azure-SSIS para alto desempenho
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 271da0a6ff443fcee28bc870821f4222b3018c91
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61262172"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Configurar o Microsoft Integration Runtime do Azure-SSIS para alto desempenho

Este artigo descreve como configurar um IR (Microsoft Integration Runtime) do Azure-SSIS para alto desempenho. O IR do Azure-SSIS permite que você implante e execute pacotes SSIS (SQL Server Integration Services) no Azure. Para obter mais informações sobre o IR do Azure-SSIS, consulte o artigo [Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Para obter informações sobre como implantar e executar pacotes SSIS no Azure, consulte [Método lift-and-shift de cargas de trabalho do SQL Server Integration Services para a nuvem](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Este artigo contém resultados de desempenho e observações de testes internos realizados por membros da equipe de desenvolvimento do SSIS. Os resultados podem variar. Faça seu próprio teste antes de finalizar suas configurações, o que afeta custo e desempenho.

## <a name="properties-to-configure"></a>Propriedades para configurar

A seguinte parte de um script de configuração mostra as propriedades que você pode configurar ao criar um Integration Runtime do Azure-SSIS. Para obter o script e a descrição completos do PowerShell, consulte [Implantar pacotes do SQL Server Integration Services no Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# Only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Database Managed Instance
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** é o local para o nó de trabalho do tempo de execução de integração. O nó de trabalho mantém uma conexão constante ao SSISDB (banco de dados do Catálogo SSIS) em um Banco de dados SQL do Azure. Defina **AzureSSISLocation** para o mesmo local que o servidor do Banco de Dados SQL do Microsoft Azure que hospeda o SSISDB, permitindo que o tempo de execução de integração funcione tão eficientemente quanto possível.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory, incluindo o IR do Azure-SSIS, dá suporte às opções a seguir:
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2.

No teste interno não oficial da equipe de engenharia do SSIS, a série D mostra-se ser mais adequada para a execução do pacote SSIS do que a série A.

-   A relação desempenho/preço da série D é maior do que a série A.
-   A taxa de transferências da série D é maior do que a série A pelo mesmo preço.

### <a name="configure-for-execution-speed"></a>Configurar para velocidade de execução
Caso não tenha muitos pacotes para executar e deseja que os pacotes sejam executados rapidamente, utilize as informações no gráfico a seguir para escolher um tipo de máquina virtual adequado ao seu cenário.

Esses dados representam uma única execução de pacote em um nó de trabalho único. O pacote carrega 10 milhões de registros com as colunas de nome e sobrenome do Armazenamento de Blobs do Azure, gera uma coluna de nome completo e grava os registros que possuem o nome completo com mais de 20 caracteres para o Armazenamento de Blobs do Azure.

![Velocidade de execução do pacote Integration Runtime do SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>Configurar a taxa de transferência geral

Caso você tenha muitos lotes de pacotes para executar, e se preocupa mais com a taxa de transferência geral, utilize as informações no gráfico a seguir para escolher um tipo de máquina virtual adequado ao seu cenário.

![Taxa de transferência geral máxima do Integration Runtime do SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** ajusta a escalabilidade do tempo de execução de integração. A taxa de transferência do tempo de execução de integração é proporcional ao **AzureSSISNodeNumber**. Primeiramente, defina **AzureSSISNodeNumber** para um valor pequeno, monitore a taxa de transferência do tempo de execução de integração e, em seguida, ajuste o valor para o seu cenário. Para reconfigurar a contagem do nó de trabalho, consulte [Gerenciar um tempo de execução de integração do Azure-SSIS](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Quando você já estiver utilizando um nó de trabalho avançado para executar pacotes, aumentar o **AzureSSISMaxParallelExecutionsPerNode** poderá aumentar a taxa de transferência geral do tempo de execução de integração. Para nós Standard_D1_v2, há suporte para execuções paralelas 1-4 por nó. Para todos os outros tipos de nós, há suporte para execuções paralelas 1-8 por nó.
É possível estimar o valor apropriado com base no custo do pacote e nas seguintes configurações para os nós de trabalho. Para obter mais informações, consulte [Tamanhos das máquinas virtuais para uso geral](../virtual-machines/windows/sizes-general.md).

| Tamanho             | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | Máximo de NICs/Desempenho de rede esperado (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2 x 500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4 x 500                         | 2 / 1500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8 x 500                         | 4 / 3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16 x 500                       | 8 / 6000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8 x 500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16 x 500                       | 8 / 2000                                       |

Aqui estão as diretrizes para definir o valor direito para a propriedade **AzureSSISMaxParallelExecutionsPerNode**: 

1. Primeiramente, defina-o para um valor pequeno.
2. Aumente-o por em pequena quantidade para verificar se a taxa de transferência geral é aperfeiçoada.
3. Pare de aumentar o valor quando a taxa de transferência geral atingir o valor máximo.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** é o tipo de preço para o SSISDB (Banco de dados do catálogo do SSIS) em um banco de dados SQL do Azure. Essa configuração afeta o número máximo de funções de trabalho na instância do IR, a velocidade para colocar na fila uma execução de pacote e a velocidade para carregar o log de execução.

-   Se você não se preocupar com a velocidade para colocar na fila a execução do pacote e para carregar o log de execução, você poderá escolher o menor tipo de preço do banco de dados. O banco de dados SQL do Azure com preço Básico fornece suporte a 8 funções de trabalho em uma instância de tempo de execução de integração.

-   Escolha um banco de dados mais avançado do que Básico, se o número de funções de trabalho for superior a 8 ou a contagem do núcleo for superior a 50. Caso contrário, o banco de dados se torna o gargalo da instância de tempo de execução de integração e o desempenho geral é impactado negativamente.

Também é possível ajustar o tipo de preço do banco de dados com base nas informações de uso de DTU [(unidade de transações do banco de dados)](../sql-database/sql-database-what-is-a-dtu.md) disponível no Portal do Azure.

## <a name="design-for-high-performance"></a>Design para alto desempenho
Projetar um pacote SSIS para executar no Azure é diferente de projetar um pacote para execução local. Em vez de combinar várias tarefas independentes no mesmo pacote, separe-as em vários pacotes para uma execução mais eficiente no IR do Azure-SSIS. Crie uma execução de pacote para cada pacote, de modo que não precisem aguardar uns aos outros para concluir. Essa abordagem beneficia da escalabilidade do tempo de execução de integração do Azure-SSIS e aprimora a taxa de transferência geral.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o Microsoft Integration Runtime do Azure-SSIS. Consulte [Microsoft Integration Runtime do Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).
