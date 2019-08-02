---
title: Tipos de preço para banco de dados do Azure para PostgreSQL – servidor único
description: Este artigo descreve os tipos de preço para banco de dados do Azure para PostgreSQL – servidor único.
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 5f60a2786a87f4bd9be1f4a9e2a7a222e097b2e1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448073"
---
# <a name="pricing-tiers-in-azure-database-for-postgresql---single-server"></a>Tipos de preço no banco de dados do Azure para PostgreSQL – servidor único

Você pode criar um servidor do Banco de Dados do Azure para PostgreSQL em um dos três tipos de preço diferentes: Básico, Uso Geral e Otimizado para Memória. Os tipos de preço são diferenciados pela quantidade de computação nos vCores que pode ser provisionada, pela memória por vCore e pela tecnologia de armazenamento usada para armazenar os dados. Todos os recursos são provisionados no nível do servidor PostgreSQL. Um servidor pode ter um ou vários bancos de dados.

|    | **Básico** | **Uso geral** | **Otimizado para memória** |
|:---|:----------|:--------------------|:---------------------|
| Geração de computação | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória por vCore | 2 GB | 5 GB | 10 GB |
| Tamanho do armazenamento | 5 GB a 1 TB | 5 GB a 4 TB | 5 GB a 4 TB |
| Tipo de armazenamento | Armazenamento Standard do Azure | Armazenamento Premium do Azure | Armazenamento Premium do Azure |
| Período de retenção do backup de banco de dados | 7 a 35 dias | 7 a 35 dias | 7 a 35 dias |

Para escolher um tipo de preço, use a tabela a seguir como ponto de partida.

| Tipo de preço | Cargas de trabalho de destino |
|:-------------|:-----------------|
| Basic | Cargas de trabalho que exigem desempenho de E/S e computação leve. Os exemplos incluem servidores usados para desenvolvimento ou teste ou aplicativos de pequena escala usados com pouca frequência. |
| Uso geral | A maioria das cargas de trabalho que exigem a computação e a memória balanceadas com a taxa de transferência de E/S escalonável. Os exemplos incluem servidores para hospedar aplicativos Web e móveis e outros aplicativos empresariais.|
| Otimizado para memória | Cargas de trabalho de banco de dados de alto desempenho que exigem desempenho na memória para o processamento de transações mais rápido e com simultaneidade mais alta. Os exemplos incluem servidores para o processamento de dados em tempo real e aplicativos analíticos ou transacionais de alto desempenho.|

Depois de criar um servidor, o número de vCores a geração de hardware e o tipo de preço (exceto em Básico) pode ser alterado para cima ou para baixo em segundos. Você pode também, independentemente, ajustar a quantidade de armazenamento de backup e o período de retenção de backup para cima ou para baixo sem tempo de inatividade do aplicativo. Não será possível alterar o tipo de armazenamento de backup depois que um servidor é criado. Para obter mais informações, consulte a seção [Recursos de dimensionamento](#scale-resources).

## <a name="compute-generations-and-vcores"></a>Gerações de computação e vCores

Os recursos de computação são fornecidos como vCores, que representam a CPU lógica do hardware subjacente. China Oriental 1, 1 de Norte da China, US DoD Central e US DoD Leste utilizam Gen 4 CPUs lógicas que são baseados em Intel E5-2673 v3 (Haswell) 2,4 GHz. Todas as outras regiões utilizam CPUs lógicas de 5ª baseados em Intel E5-2673 v4 (Broadwell) 2,3 GHz.

## <a name="storage"></a>Armazenamento

O armazenamento provisionado é a quantidade de capacidade de armazenamento disponível para o Banco de Dados do Azure para servidor PostgreSQL. O armazenamento é usado para os arquivos de banco de dados, os logs de transações e os logs do servidor PostgreSQL. A quantidade total de armazenamento que você provisiona também define a capacidade disponível para o servidor.

|    | **Básico** | **Uso geral** | **Otimizado para memória** |
|:---|:----------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento Standard do Azure | Armazenamento Premium do Azure | Armazenamento Premium do Azure |
| Tamanho do armazenamento | 5 GB a 1 TB | 5 GB a 4 TB | 5 GB a 4 TB |
| Tamanho do incremento de armazenamento | 1 GB | 1 GB | 1 GB |
| IOPS | Variável |3 IOPS/GB<br/>Mín 100 IOPS<br/>Máx 6000 IOPS | 3 IOPS/GB<br/>Mín 100 IOPS<br/>Máx 6000 IOPS |

Você pode adicionar mais capacidade de armazenamento durante e após a criação do servidor e permitir que o sistema crescer automaticamente com base no consumo de armazenamento de sua carga de trabalho de armazenamento. A camada Básico não oferece garantia de IOPS. Nos tipos de preço Uso Geral e Otimizado para Memória, o IOPS é dimensionado com o tamanho de armazenamento provisionado a uma taxa de 3:1.

Você pode monitorar o consumo de E/S no Portal do Azure ou usando os comandos da CLI do Azure. As métricas relevantes para monitorar são o [limite de armazenamento, porcentagem de armazenamento, armazenamento usado e porcentagem de E/S](concepts-monitoring.md).

### <a name="large-storage-preview"></a>Armazenamento de grande (visualização)

Estamos aumentando os limites de armazenamento em camadas nosso uso geral e otimizado para memória. Recém-criados em servidores que participar da visualização pode provisionar até 16 TB de armazenamento. O IOPS é dimensionado em uma taxa de 3:1 até 20.000 IOPS. Assim como acontece com o armazenamento disponível atual, você pode adicionar mais capacidade de armazenamento após a criação do servidor e permitir que o sistema crescer automaticamente com base no consumo de armazenamento de sua carga de trabalho de armazenamento.

|              | **Uso geral** | **Otimizado para memória** |
|:-------------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento Premium do Azure | Armazenamento Premium do Azure |
| Tamanho do armazenamento | 32 GB a 16 TB| 32 a 16 TB |
| Tamanho do incremento de armazenamento | 1 GB | 1 GB |
| IOPS | 3 IOPS/GB<br/>Mín 100 IOPS<br/>20.000 IOPS máxima | 3 IOPS/GB<br/>Mín 100 IOPS<br/>20.000 IOPS máxima |

> [!IMPORTANT]
> Armazenamento de grande está atualmente em visualização pública nas seguintes regiões: Leste dos EUA, Leste dos EUA 2, centro dos EUA, oeste dos EUA, Europa Setentrional, Europa Ocidental, Sul do Reino Unido, oeste do Reino Unido, Sudeste Asiático, Sudeste Asiático, Japão Leste do Japão Ocidental, Coreia Central, Coreia do Sul, Leste da Austrália, Sudeste da Austrália.
>
> A visualização do armazenamento de grandes atualmente não dá suporte:
>
> * Conexões de entrada por meio de pontos de extremidade de serviço de rede virtual
> * Backups com redundância geográfica
> * Réplicas de leitura

### <a name="reaching-the-storage-limit"></a>Alcançando o limite de armazenamento

Servidores com menos de 100 GB provisionado de armazenamento são somente leitura se o armazenamento livre é menor que 512MB ou 5% do tamanho do armazenamento provisionado. Servidores com mais de 100 GB provisionado de armazenamento são marcados leitura somente quando o armazenamento livre é menor que 5 GB.

Por exemplo, se você provisionou 110 GB de armazenamento e a utilização real ultrapassar 105 GB, o servidor é marcado como somente leitura. Como alternativa, se você tiver provisionado 5 GB de armazenamento, o servidor é marcado como somente leitura quando o armazenamento livre atinge menos de 512 MB.

Quando o servidor é definido como somente leitura, todas as sessões existentes são desconectadas e as transações não confirmadas são revertidas. Todas as operações de gravação subsequente e a transação falham. Todas as consultas de leitura continuam a funcionar sem interrupções.  

Você pode aumentar a quantidade de armazenamento provisionado para o servidor ou iniciar uma nova sessão no modo de gravação de leitura e soltar os dados para recuperar o armazenamento livre. Executar `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` define a sessão atual para o modo de gravação de leitura. Para evitar a corrupção de dados, não execute nenhuma operação de gravação quando o servidor ainda estiver em status somente leitura.

É recomendável que você ative a armazenamento aumentá-lo ou para configurar um alerta para notificá-lo ao seu armazenamento de servidor está se aproximando do limite então, você pode evitar introdução para o estado somente leitura. Para mais informações, consulte a documentação em [como configurar um alerta](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Crescimento automático de armazenamento

Se o crescimento automático de armazenamento é habilitada, o armazenamento cresce automaticamente sem afetar a carga de trabalho. Para servidores com menos de 100 GB provisionado de armazenamento, o tamanho de armazenamento provisionado é aumentado em 5 GB, assim o armazenamento livre estiver abaixo de 1 GB ou 10% do armazenamento provisionado maior. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento livre estiver abaixo de 5% do tamanho do armazenamento provisionado. Limites de armazenamento máximo especificados acima se aplicam.

Por exemplo, se você provisionou 1000 GB de armazenamento e a utilização real ultrapassar 950 GB, o tamanho de armazenamento do servidor é aumentado para 1050 GB. Como alternativa, se você tiver provisionado 10 GB de armazenamento, o tamanho de armazenamento é aumento para 15 GB quando menos de 1 GB de armazenamento estiver livre.

## <a name="backup"></a>Backup

O serviço faz backups do servidor automaticamente. O período de retenção mínimo para backups é de sete dias. Você pode definir um período de retenção de até 35 dias. A retenção pode ser ajustada a qualquer momento durante o tempo de vida do servidor. Você pode escolher entre backups com redundância geográfica e com redundância local. Backups com redundância geográfica também são armazenados na [região emparelhada geograficamente](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) da região em que seu servidor será criado. Essa redundância fornece um nível de proteção em caso de desastre. Você também ganha a capacidade de restaurar o servidor para qualquer outra região do Azure na qual o serviço esteja disponível com os backups com redundância geográfica. Não é possível alterar entre as duas opções de armazenamento de backup depois que o servidor é criado.

## <a name="scale-resources"></a>Recursos de escala

Após criar o servidor, você poderá, independentemente, alterar vCores, a geração de hardware, o tipo de preço (exceto em Básico), a quantidade de armazenamento e o período de retenção de backup. Não será possível alterar o tipo de armazenamento de backup depois que um servidor é criado. O número de vCores pode ser dimensionado para cima ou para baixo. Os vCores e o período de retenção de backup podem ser aumentados ou reduzidos de 7 a 35 dias. O tamanho de armazenamento só pode ser aumentado. O dimensionamento dos recursos pode ser feito por meio do portal ou da CLI do Azure. Para obter um exemplo de dimensionamento usando a CLI do Azure, consulte [Monitorar e dimensionar um servidor do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure](scripts/sample-scale-server-up-or-down.md).

Ao alterar o número de vCores, a geração de hardware ou o tipo de preço, uma cópia do servidor original é criada com a nova alocação de computação. Depois que o novo servidor entra em execução, as conexões são alternadas para o novo servidor. Durante um momento enquanto o sistema muda para o novo servidor, nenhuma nova conexão pode ser estabelecida e todas as transações não confirmadas são revertidas. Esse período varia, mas na maioria dos casos fica abaixo um minuto.

O dimensionamento do armazenamento e a alteração do período de retenção de backup são operações realmente online. Não há nenhum tempo de inatividade e o aplicativo não é afetado. Conforme o IOPS é dimensionado com o tamanho do armazenamento provisionado, você pode aumentar o IOPS disponível para seu servidor aumentando o armazenamento.

## <a name="pricing"></a>Preços

Para as informações mais recentes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/PostgreSQL/) do serviço. Para ver os custos da configuração desejada, o [Portal do Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal na guia **Tipo de preço** com base nas opções que você seleciona. Se você não tiver uma assinatura do Azure, poderá usar a calculadora de preços do Azure para obter um preço estimado. No site da [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/), selecione **Adicionar itens**, expanda a categoria **Bancos de dados** e escolha **Banco de Dados do Azure para PostgreSQL** para personalizar as opções.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um servidor PostgreSQL no portal](tutorial-design-database-using-azure-portal.md).
- Conheça os [limites de serviço](concepts-limits.md). 
- Saiba como [fazer a expansão com réplicas de leitura](howto-read-replicas-portal.md).
