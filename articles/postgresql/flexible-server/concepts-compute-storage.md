---
title: Opções de computação e armazenamento-banco de dados do Azure para PostgreSQL – servidor flexível
description: Este artigo descreve as opções de computação e armazenamento no banco de dados do Azure para PostgreSQL – servidor flexível.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 93a439c814b6bac630c5231f46d7a022a131687c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608163"
---
# <a name="compute-and-storage-options-in-azure-database-for-postgresql---flexible-server"></a>Opções de computação e armazenamento no banco de dados do Azure para PostgreSQL – servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Você pode criar um servidor de banco de dados do Azure para PostgreSQL em um dos três tipos de preço diferentes: expansível, Uso Geral e otimizado para memória. Os tipos de preço são diferenciados pela quantidade de computação nos vCores que pode ser provisionada, pela memória por vCore e pela tecnologia de armazenamento usada para armazenar os dados. Todos os recursos são provisionados no nível do servidor PostgreSQL. Um servidor pode ter um ou vários bancos de dados.

| Recurso/camada | **Expansíveis** | **Uso Geral** | **Otimizado para memória** |
|:---|:----------|:--------------------|:---------------------|
| vCores | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Memória por vCore | Variável | 4 GB | 6,75 a 8 GB |
| Tamanho de armazenamento | 32 GB a 16 TB | 32 GB a 16 TB | 32 GB a 16 TB |
| Período de retenção do backup de banco de dados | 7 a 35 dias | 7 a 35 dias | 7 a 35 dias |

Para escolher um tipo de preço, use a tabela a seguir como ponto de partida.

| Tipo de preço | Cargas de trabalho de destino |
|:-------------|:-----------------|
| Expansíveis | Melhor para cargas de trabalho que não precisam da CPU completa continuamente. |
| Uso Geral | A maioria das cargas de trabalho que exigem a computação e a memória balanceadas com a taxa de transferência de E/S escalonável. Os exemplos incluem servidores para hospedar aplicativos Web e móveis e outros aplicativos empresariais.|
| Otimizado para memória | Cargas de trabalho de banco de dados de alto desempenho que exigem desempenho na memória para o processamento de transações mais rápido e com simultaneidade mais alta. Os exemplos incluem servidores para o processamento de dados em tempo real e aplicativos analíticos ou transacionais de alto desempenho.|

Depois de criar um servidor, a camada de computação, o número de vCores e o tamanho do armazenamento podem ser alterados ou reduzidos em segundos. Você também pode ajustar de forma independente o período de retenção de backup para cima ou para baixo. Para obter mais informações, consulte a seção [Scale Resources](#scale-resources) .

## <a name="compute-tiers-vcores-and-server-types"></a>Camadas de computação, vCores e tipos de servidor

Os recursos de computação podem ser selecionados com base na camada, vCores e tamanho da memória. vCores representam a CPU lógica do hardware subjacente.

As especificações detalhadas dos tipos de servidor disponíveis são as seguintes:

| Nome do SKU             | vCores | Tamanho da memória | IOPS máximo com suporte | Largura de banda de e/s máxima com suporte |
|----------------------|--------|-------------|--------------------|-----------------------------|
| **Expansíveis**        |        |             |                    |                             |
| B1ms                 | 1      | 2 GiB       | 640                | 15 MiB/s                  |
| B2S                  | 2      | 4 GiB       | 1280               | 15 MiB/s                  |
| **Uso Geral**  |        |             |                    |                             |
| D2s_v3               | 2      | 8 GiB       | 3200               | 48 MiB/s                  |
| D4s_v3               | 4      | 16 GiB      | 6400               | 96 MiB/s                  |
| D8s_v3               | 8      | 32 GiB      | 12800              | 192 MiB/s                 |
| D16s_v3              | 16     | 64 GiB      | 18000              | 384 MiB/s                 |
| D32s_v3              | 32     | 128 GiB     | 18000              | 750 MiB/segundo                 |
| D48s_v3              | 48     | 192 GiB     | 18000              | 750 MiB/segundo                 |
| D64s_v3              | 64     | 256 GiB     | 18000              | 750 MiB/segundo                 |
| **Otimizado para memória** |        |             |                    |                             |
| E2s_v3               | 2      | 16 GiB      | 3200               | 48 MiB/s                  |
| E4s_v3               | 4      | 32 GiB      | 6400               | 96 MiB/s                  |
| E8s_v3               | 8      | 64 GiB      | 12800              | 192 MiB/s                 |
| E16s_v3              | 16     | 128 GiB     | 18000              | 384 MiB/s                 |
| E32s_v3              | 32     | 256 GiB     | 18000              | 750 MiB/segundo                 |
| E48s_v3              | 48     | 384 GiB     | 18000              | 750 MiB/segundo                 |
| E64s_v3              | 64     | 432 GiB     | 18000              | 750 MiB/segundo                 |

## <a name="storage"></a>Armazenamento

O armazenamento provisionado é a quantidade de capacidade de armazenamento disponível para o Banco de Dados do Azure para servidor PostgreSQL. O armazenamento é usado para os arquivos de banco de dados, os logs de transações e os logs do servidor PostgreSQL. A quantidade total de armazenamento que você provisiona também define a capacidade disponível para o servidor.

O armazenamento está disponível nos seguintes tamanhos fixos:

| Tamanho do disco | IOPS |
|:---|:---|
| 32 GiB | Provisionado 120, até 3.500 |
| 64 GiB | Provisionado 240, até 3.500 |
| 128 GiB | Provisionado 500, até 3.500 |
| 256 GiB | Provisionado 1100, até 3.500 |
| 512 GiB | Provisionado 2300, até 3.500 |
| 1 TiB | 5\.000 |
| 2 TiB | 7.500 |
| 4 TiB | 7\.500 |
| 8 TiB | 16.000 |
| 16 TiB | 18.000 |

Observe que o IOPS também é restrito por seu tipo de VM. Embora você possa selecionar qualquer tamanho de armazenamento independente do tipo de servidor, talvez não seja possível usar todo o IOPS que o armazenamento fornece, especialmente quando você escolhe um servidor com um pequeno número de vCores.

Você pode adicionar mais capacidade de armazenamento durante e após a criação do servidor.

>[!NOTE]
> O armazenamento só pode ser escalado verticalmente, não horizontalmente.

Você pode monitorar o consumo de E/S no Portal do Azure ou usando os comandos da CLI do Azure. As métricas relevantes para monitorar são o [limite de armazenamento, a porcentagem de armazenamento, o armazenamento usado e a porcentagem de e/s](concepts-monitoring.md).

### <a name="maximum-iops-for-your-configuration"></a>IOPS máximo para sua configuração

|Nome do SKU            |Tamanho do armazenamento em GiB                       |32 |64 |128 |256 |512  |1\.024|2\.048|4\.096|8\.192 |16.384|
|--------------------|------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |IOPS máximo                              |120|240|500 |1100|2300 |5.000 |7500 |7500 |16000 |18000 |
|**Expansíveis**       |                                          |   |   |    |    |     |     |     |     |      |      |
|B1ms                |IOPS DE 640                                  |120|240|500 |640 *|640 * |640 * |640 * |640 * |640 *  |640 *  |
|B2S                 |IOPS DE 1280                                 |120|240|500 |1100|1280 *|1280 *|1280 *|1280 *|1280 * |1280 * |
|**Uso Geral** |                                          |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |IOPS DE 3200                                 |120|240|500 |1100|2300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|D4s_v3              |IOPS DE 6.400                                |120|240|500 |1100|2300 |5.000 |6400 *|6400 *|6400 * |6400 * |
|D8s_v3              |IOPS DE 12.800                               |120|240|500 |1100|2300 |5.000 |7500 |7500 |12800 *|12800 *|
|D16s_v3             |IOPS DE 18.000                               |120|240|500 |1100|2300 |5.000 |7500 |7500 |16000 |18000 |
|D32s_v3             |IOPS DE 18.000                               |120|240|500 |1100|2300 |5.000 |7500 |7500 |16000 |18000 |
|D48s_v3             |IOPS DE 18.000                               |120|240|500 |1100|2300 |5.000 |7500 |7500 |16000 |18000 |
|D64s_v3             |IOPS DE 18.000                               |120|240|500 |1100|2300 |5.000 |7500 |7500 |16000 |18000 |
|**Otimizado para memória**|                                          |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |IOPS DE 3200                                 |120|240|500 |1100|2300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|E4s_v3              |IOPS DE 6.400                                |120|240|500 |1100|2300 |5.000 |6400 *|6400 *|6400 * |6400 * |
|E8s_v3              |IOPS DE 12.800                               |120|240|500 |1100|2300 |5.000 |7500 |7500 |12800 *|12800 *|
|E16s_v3             |IOPS DE 18.000                               |120|240|500 |1100|2300 |5.000 |7500 |7500 |16000 |18000 |
|E32s_v3             |IOPS DE 18.000                               |120|240|500 |1100|2300 |5.000 |7500 |7500 |16000 |18000 |
|E48s_v3             |IOPS DE 18.000                               |120|240|500 |1100|2300 |5.000 |7500 |7500 |16000 |18000 |
|E64s_v3             |IOPS DE 18.000                               |120|240|500 |1100|2300 |5.000 |7500 |7500 |16000 |18000 |

Quando marcado com um \* , o IOPS é limitado pelo tipo de VM selecionado. Caso contrário, o IOPS será limitado pelo tamanho de armazenamento selecionado.

>[!NOTE]
> Você pode ver um IOPS maior nas métricas devido à intermitência no nível do disco. Consulte a [documentação](../../virtual-machines/disk-bursting.md#disk-level-bursting) para obter mais detalhes. 

### <a name="maximum-io-bandwidth-mibsec-for-your-configuration"></a>Largura de banda de e/s máxima (MiB/s) para sua configuração

|Nome do SKU            |Tamanho do armazenamento, GiB                             |32 |64 |128 |256 |512  |1\.024|2\.048|4\.096|8\.192 |16.384|
|--------------------|----------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |**Largura de banda de armazenamento, MiB/s**                |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Expansíveis**       |                                              |   |   |    |    |     |     |     |     |      |      |
|B1ms                |10 MiB/s                                    |254|254|254 |254 |254  |254  |254  |254  |254   |254   |
|B2S                 |15 MiB/s                                    |15|15|15 |15 |15  |15  |15  |15  |15   |15   |
|**Uso Geral** |                                              |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |48 MiB/s                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|D4s_v3              |96 MiB/s                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|D8s_v3              |192 MiB/s                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|D16s_v3             |384 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|D32s_v3             |750 MiB/segundo                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D48s_v3             |750 MiB/segundo                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D64s_v3             |750 MiB/segundo                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Otimizado para memória**|                                              |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |48 MiB/s                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|E4s_v3              |96 MiB/s                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|E8s_v3              |192 MiB/s                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|E16s_v3             |384 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|E32s_v3             |750 MiB/segundo                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E48s_v3             |750 MiB/segundo                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E64s_v3             |750 MiB/segundo                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |

Quando marcado com uma \* , a largura de banda de e/s é limitada pelo tipo de VM selecionado. Caso contrário, a largura de banda de e/s é limitada pelo tamanho de armazenamento selecionado.

### <a name="reaching-the-storage-limit"></a>Alcançando o limite de armazenamento

Quando você atingir o limite de armazenamento, o servidor começará a retornar erros e evitará outras modificações. Isso também pode causar problemas com outras atividades operacionais, como backups e WAL Archiving.

Para evitar essa situação, quando a utilização de armazenamento atingir 95% ou se a capacidade disponível for inferior a 5 GiB, o servidor será alternado automaticamente para o **modo somente leitura**.

É recomendável monitorar ativamente o espaço em disco que está em uso e aumentar o tamanho do disco antes de qualquer situação de armazenamento insuficiente. Você pode configurar um alerta para notificá-lo quando o armazenamento do servidor estiver se aproximando do disco para que você possa evitar problemas com a execução de disco insuficiente. Para mais informações, consulte a documentação em [como configurar um alerta](howto-alert-on-metrics.md).


### <a name="storage-auto-grow"></a>Crescimento automático do armazenamento

O crescimento automático do armazenamento ainda não está disponível para o servidor flexível.

## <a name="backup"></a>Backup

O serviço faz backups do servidor automaticamente. Você pode selecionar um período de retenção de um intervalo de 7 a 35 dias. Saiba mais sobre backups no [artigo conceitos](concepts-backup-restore.md).

## <a name="scale-resources"></a>Escalar recursos

Depois de criar o servidor, você pode alterar independentemente o vCores, a camada de computação, a quantidade de armazenamento e o período de retenção de backup. O número de vCores pode ser dimensionado para cima ou para baixo. Os vCores e o período de retenção de backup podem ser aumentados ou reduzidos de 7 a 35 dias. O tamanho de armazenamento só pode ser aumentado. O dimensionamento dos recursos pode ser feito por meio do portal ou da CLI do Azure.

> [!NOTE]
> O tamanho de armazenamento só pode ser aumentado. Não é possível voltar para um tamanho de armazenamento menor após o aumento.

Quando você altera o número de vCores ou a camada de computação, o servidor é reiniciado para que o novo tipo de servidor entre em vigor. Durante um momento enquanto o sistema muda para o novo servidor, nenhuma nova conexão pode ser estabelecida e todas as transações não confirmadas são revertidas. Esse período varia, mas na maioria dos casos fica abaixo um minuto. O dimensionamento do armazenamento funciona da mesma maneira e também requer uma breve reinicialização.

Alterar o período de retenção de backup é uma operação online.

## <a name="pricing"></a>Preços

Para as informações mais recentes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/PostgreSQL/) do serviço. Para ver os custos da configuração desejada, o [Portal do Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal na guia **Tipo de preço** com base nas opções que você seleciona. Se você não tiver uma assinatura do Azure, poderá usar a calculadora de preços do Azure para obter um preço estimado. No site da [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/), selecione **Adicionar itens**, expanda a categoria **Bancos de dados** e escolha **Banco de Dados do Azure para PostgreSQL** para personalizar as opções.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um servidor PostgreSQL no portal](how-to-manage-server-portal.md).
- Conheça os [limites de serviço](concepts-limits.md).