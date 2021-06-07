---
title: 'Tipos de preço: Banco de Dados do Azure para MySQL'
description: Saiba mais sobre os vários tipos de preço do banco de dados do Azure para MySQL, incluindo gerações de computação, tipo de armazenamento, tamanho de armazenamento, vCores, memória e períodos de retenção de backup.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: be7f15b5221be8b3acb7f64c4435e40f40f21f8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720912"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Tipos de preço no Banco de Dados do Azure para MySQL

Você pode criar um servidor do Banco de Dados do Azure para MySQL em um dos três tipos de preço diferentes: Básico, Uso Geral e Otimizado para Memória. Os tipos de preço são diferenciados pela quantidade de computação nos vCores que pode ser provisionada, pela memória por vCore e pela tecnologia de armazenamento usada para armazenar os dados. Todos os recursos são provisionados no nível do servidor MySQL. Um servidor pode ter um ou vários bancos de dados.

| Atributo   | **Basic** | **Uso Geral** | **Otimizado para memória** |
|:---|:----------|:--------------------|:---------------------|
| Geração de computação | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória por vCore | 2 GB | 5 GB | 10 GB |
| Tamanho de armazenamento | 5 GB a 1 TB | 5 GB a 16 TB | 5 GB a 16 TB |
| Período de retenção do backup de banco de dados | 7 a 35 dias | 7 a 35 dias | 7 a 35 dias |

Para escolher um tipo de preço, use a tabela a seguir como ponto de partida.

| Tipo de preço | Cargas de trabalho de destino |
|:-------------|:-----------------|
| Basic | Cargas de trabalho que exigem desempenho de E/S e computação leve. Os exemplos incluem servidores usados para desenvolvimento ou teste ou aplicativos de pequena escala usados com pouca frequência. |
| Uso Geral | A maioria das cargas de trabalho que exigem a computação e a memória balanceadas com a taxa de transferência de E/S escalonável. Os exemplos incluem servidores para hospedar aplicativos Web e móveis e outros aplicativos empresariais.|
| Otimizado para memória | Cargas de trabalho de banco de dados de alto desempenho que exigem desempenho na memória para o processamento de transações mais rápido e com simultaneidade mais alta. Os exemplos incluem servidores para o processamento de dados em tempo real e aplicativos analíticos ou transacionais de alto desempenho.|

Depois de criar um servidor, o número de vCores a geração de hardware e o tipo de preço (exceto em Básico) pode ser alterado para cima ou para baixo em segundos. Você pode também, independentemente, ajustar a quantidade de armazenamento de backup e o período de retenção de backup para cima ou para baixo sem tempo de inatividade do aplicativo. Não será possível alterar o tipo de armazenamento de backup depois que um servidor é criado. Para obter mais informações, consulte a seção [Scale Resources](#scale-resources) .

## <a name="compute-generations-and-vcores"></a>Gerações de computação e vCores

Os recursos de computação são fornecidos como vCores, que representam a CPU lógica do hardware subjacente. Leste da China 1, Norte da China 1, US DoD Central e US DoD Leste utilizam CPUs lógicas Gen 4 baseadas em processadores Intel E5-2673 v3 (Haswell) de 2,4 GHz. Todas as outras regiões utilizam CPUs lógicas de Gen 5 baseadas em processadores Intel E5-2673 V4 (Broadwell) de 2,3 GHz.

## <a name="storage"></a>Armazenamento

O armazenamento provisionado é a quantidade de capacidade de armazenamento disponível para o Banco de Dados do Azure para servidor MySQL. O armazenamento é usado para os arquivos de banco de dados, os logs de transações e os logs do servidor MySQL. A quantidade total de armazenamento que você provisiona também define a capacidade disponível para o servidor.

| Atributo de armazenamento   | Basic | Propósito geral | Otimizado para memória |
|:---|:----------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento básico | Armazenamento Uso Geral | Armazenamento Uso Geral |
| Tamanho de armazenamento | 5 GB a 1 TB | 5 GB a 16 TB | 5 GB a 16 TB |
| Tamanho do incremento de armazenamento | 1 GB | 1 GB | 1 GB |
| IOPS | Variável |3 IOPS/GB<br/>Mín 100 IOPS<br/>IOPS máx. 20.000 | 3 IOPS/GB<br/>Mín 100 IOPS<br/>IOPS máx. 20.000 |

> [!NOTE]
> O armazenamento de até 16TB e 20.000 IOPS tem suporte nas seguintes regiões: leste dos EUA, leste dos EUA 2, EUA Central, sul do Brasil, oeste dos EUA, norte EUA Central, Sul EUA Central, Europa Setentrional, Europa Ocidental, Sul do Reino Unido, Oeste do Reino Unido, Sudeste Asiático, Ásia Oriental, leste do Japão, oeste do Japão, Coreia central, Coreia do Sul, leste da Austrália, sudeste da Austrália, oeste dos EUA , Leste do Canadá e central do Canadá.
>
> Todas as outras regiões dão suporte a até 4 TB de armazenamento e até 6000 IOPS.
>

Você pode adicionar capacidade de armazenamento adicional durante e após a criação do servidor e permitir que o sistema aumente o armazenamento automaticamente com base no consumo de armazenamento de sua carga de trabalho. 

>[!NOTE]
> O armazenamento só pode ser escalado verticalmente, não horizontalmente.

A camada Básico não oferece garantia de IOPS. Nos tipos de preço Uso Geral e Otimizado para Memória, o IOPS é dimensionado com o tamanho de armazenamento provisionado a uma taxa de 3:1.

Você pode monitorar o consumo de E/S no Portal do Azure ou usando os comandos da CLI do Azure. As métricas relevantes para monitorar são o [limite de armazenamento, a porcentagem de armazenamento, o armazenamento usado e a porcentagem de e/s](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Alcançando o limite de armazenamento

Os servidores com armazenamento provisionado menor ou igual a 100 GB serão marcados como somente leitura caso o armazenamento livre seja inferior a 5% do tamanho de armazenamento provisionado. Os servidores com mais de 100 GB de armazenamento provisionado serão marcados como somente leitura quando o armazenamento livre for inferior a 5 GB.

Por exemplo, se você tiver provisionado 110 GB de armazenamento e a utilização real passar de 105 GB, o servidor será marcado como somente leitura. Como alternativa, se você tiver provisionado 5 GB de armazenamento, o servidor será marcado como somente leitura quando o armazenamento livre atingir menos de 256 MB.

Enquanto o serviço tenta tornar o servidor somente leitura, todas as novas solicitações de transação de gravação são bloqueadas e as transações ativas existentes continuarão a ser executadas. Quando o servidor é definido como somente leitura, todas as operações de gravação subsequente e a transação comentem falha. Consultas de leitura continuam a funcionar sem interrupções. Após aumentar o armazenamento provisionado, o servidor estará pronto para aceitar transações de gravação novamente.

Recomendamos que você ative o aumento automático do armazenamento ou configure um alerta para notificá-lo quando o armazenamento do servidor estiver se aproximando do limite para que você possa evitar entrar no estado somente leitura. Para mais informações, consulte a documentação em [como configurar um alerta](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Crescimento automático do armazenamento

O crescimento automático de armazenamento impede que o servidor fique sem armazenamento e se torne somente leitura. Se o crescimento automático do armazenamento estiver habilitado, o armazenamento aumentará automaticamente sem afetar a carga de trabalho. Para servidores com menos de igual a 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado é aumentado em 5 GB quando o armazenamento livre está abaixo de 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado aumenta em 5% quando o espaço livre de armazenamento está abaixo de 10 GB do tamanho de armazenamento provisionado. Os limites de armazenamento máximos especificados acima se aplicam.

Por exemplo, se você tiver provisionado 1000 GB de armazenamento e a utilização real passar de 990 GB, o tamanho do armazenamento do servidor será aumentado para 1050 GB. Como alternativa, se você tiver provisionado 10 GB de armazenamento, o tamanho do armazenamento será aumentado para 15 GB quando menos de 1 GB de armazenamento for gratuito.

Lembre-se de que o armazenamento pode ser escalado verticalmente, não inativo.

## <a name="backup-storage"></a>Armazenamento de backup 

O Banco de Dados do Azure para MySQL fornece até 100% de seu armazenamento de servidor configurado como armazenamento de backup sem custo adicional. Qualquer armazenamento de backup que você usa em excesso desse valor é cobrado em GB por mês. Por exemplo, se você provisionar um servidor com 250 GB de armazenamento, terá 250 GB de armazenamento adicional disponível para backups de servidor sem encargos. O armazenamento para backups que ultrapassem os 250 GB é cobrado de acordo com o [modelo de preços](https://azure.microsoft.com/pricing/details/mysql/). Para entender os fatores que influenciam o uso do armazenamento de backup, o monitoramento e o controle do custo de armazenamento de backup, você pode consultar a [documentação de backup](concepts-backup.md).

## <a name="scale-resources"></a>Escalar recursos

Após criar o servidor, você poderá, independentemente, alterar vCores, a geração de hardware, o tipo de preço (exceto em Básico), a quantidade de armazenamento e o período de retenção de backup. Não será possível alterar o tipo de armazenamento de backup depois que um servidor é criado. O número de vCores pode ser dimensionado para cima ou para baixo. Os vCores e o período de retenção de backup podem ser aumentados ou reduzidos de 7 a 35 dias. O tamanho de armazenamento só pode ser aumentado. O dimensionamento dos recursos pode ser feito por meio do portal ou da CLI do Azure. Para obter um exemplo de dimensionamento usando a CLI do Azure, consulte [Monitorar e dimensionar um servidor do Banco de Dados do Azure para MySQL usando a CLI do Azure](scripts/sample-scale-server.md).

Ao alterar o número de vCores, a geração de hardware ou o tipo de preço, uma cópia do servidor original é criada com a nova alocação de computação. Depois que o novo servidor entra em execução, as conexões são alternadas para o novo servidor. Durante um momento enquanto o sistema muda para o novo servidor, nenhuma nova conexão pode ser estabelecida e todas as transações não confirmadas são revertidas. Esse tempo de inatividade durante o dimensionamento pode ser de cerca de 60-120 segundos. O tempo de inatividade durante o dimensionamento depende do momento da recuperação do banco de dados, o que pode fazer com que o banco de dados fique online mais tempo se você tiver atividade transacional pesada no servidor no momento da operação de dimensionamento. Para evitar tempo de reinício mais longo, é recomendável executar operações de dimensionamento durante períodos de atividade transacional baixa no servidor.

O dimensionamento do armazenamento e a alteração do período de retenção de backup são operações realmente online. Não há nenhum tempo de inatividade e o aplicativo não é afetado. Conforme o IOPS é dimensionado com o tamanho do armazenamento provisionado, você pode aumentar o IOPS disponível para seu servidor aumentando o armazenamento.

## <a name="pricing"></a>Preços

Para as informações mais recentes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/mysql/) do serviço. Para ver os custos da configuração desejada, o [Portal do Azure](https://portal.azure.com/#create/Microsoft.MySQLServer) mostra o custo mensal na guia **Tipo de preço** com base nas opções que você seleciona. Se você não tiver uma assinatura do Azure, poderá usar a calculadora de preços do Azure para obter um preço estimado. No site da [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/), selecione **Adicionar itens**, expanda a categoria **Bancos de dados** e escolha **Banco de Dados do Azure para MySQL** para personalizar as opções.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um servidor MySQL no portal](howto-create-manage-server-portal.md).
- Conheça os [limites de serviço](concepts-limits.md).
- Saiba como [fazer a expansão com réplicas de leitura](howto-read-replicas-portal.md).
