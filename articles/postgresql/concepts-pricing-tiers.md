---
title: Tipos de preço-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve as opções de computação e armazenamento no banco de dados do Azure para PostgreSQL-servidor único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 119a68c4081cfc4fff6dc35b21badfac0397970b
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605103"
---
# <a name="pricing-tiers-in-azure-database-for-postgresql---single-server"></a>Tipos de preço no Banco de Dados do Azure para PostgreSQL - Servidor único

É possível criar um servidor do Banco de Dados do Azure para PostgreSQL em um dos três tipos de preço diferentes: Básico, Uso Geral e Otimizado para Memória. Os tipos de preço são diferenciados pela quantidade de computação nos vCores que pode ser provisionada, pela memória por vCore e pela tecnologia de armazenamento usada para armazenar os dados. Todos os recursos são provisionados no nível do servidor PostgreSQL. Um servidor pode ter um ou vários bancos de dados.

| Recurso/camada | **Basic** | **Uso Geral** | **Otimizado para memória** |
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

O armazenamento provisionado é a quantidade de capacidade de armazenamento disponível para o Banco de Dados do Azure para servidor PostgreSQL. O armazenamento é usado para os arquivos de banco de dados, os logs de transações e os logs do servidor PostgreSQL. A quantidade total de armazenamento que você provisiona também define a capacidade disponível para o servidor.

| Atributos de armazenamento | **Basic** | **Uso Geral** | **Otimizado para memória** |
|:---|:----------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento básico | Armazenamento Uso Geral | Armazenamento Uso Geral |
| Tamanho de armazenamento | 5 GB a 1 TB | 5 GB a 16 TB | 5 GB a 16 TB |
| Tamanho do incremento de armazenamento | 1 GB | 1 GB | 1 GB |
| IOPS | Variável |3 IOPS/GB<br/>Mín 100 IOPS<br/>IOPS máx. 20.000 | 3 IOPS/GB<br/>Mín 100 IOPS<br/>IOPS máx. 20.000 |

> [!NOTE]
> Há suporte para o armazenamento de até 16TB e 20.000 IOPS nas seguintes regiões: leste da Austrália, leste da Austrália, sul do Brasil, centro do Canadá, leste do Canadá, EUA Central, Leste da China 2, Norte da China 2, Ásia Oriental, leste dos EUA, leste dos EUA 1, leste dos EUA 2, leste do Japão, oeste do Japão, centro da Coreia, sul da Coreia, EUA Central do Sul , Oeste da Suíça, US Gov leste, US Gov centro, US Gov sudoeste, Sul do Reino Unido, Oeste do Reino Unido, Europa Ocidental, Oeste EUA Central, oeste dos EUA e oeste dos EUA 2.
>
> Todas as outras regiões dão suporte a até 4 TB de armazenamento e 6000 IOPS.
>

Você pode adicionar capacidade de armazenamento adicional durante e após a criação do servidor e permitir que o sistema aumente o armazenamento automaticamente com base no consumo de armazenamento de sua carga de trabalho.

>[!NOTE]
> O armazenamento só pode ser escalado verticalmente, não horizontalmente.

A camada Básico não oferece garantia de IOPS. Nos tipos de preço Uso Geral e Otimizado para Memória, o IOPS é dimensionado com o tamanho de armazenamento provisionado a uma taxa de 3:1.

Você pode monitorar o consumo de E/S no Portal do Azure ou usando os comandos da CLI do Azure. As métricas relevantes para monitorar são o [limite de armazenamento, a porcentagem de armazenamento, o armazenamento usado e a porcentagem de e/s](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Alcançando o limite de armazenamento

Os servidores com menos de igual a 100 GB de armazenamento provisionado serão marcados como somente leitura se o armazenamento livre for inferior a 512MB ou 5% do tamanho de armazenamento provisionado. Os servidores com mais de 100 GB de armazenamento provisionado serão marcados como somente leitura quando o armazenamento livre for inferior a 5 GB.

Por exemplo, se você tiver provisionado 110 GB de armazenamento e a utilização real passar de 105 GB, o servidor será marcado como somente leitura. Como alternativa, se você tiver provisionado 5 GB de armazenamento, o servidor será marcado como somente leitura quando o armazenamento livre atingir menos de 512 MB.

Quando o servidor é definido como somente leitura, todas as sessões existentes são desconectadas e as transações não confirmadas são revertidas. Todas as operações de gravação subsequente e a transação falham. Todas as consultas de leitura continuam a funcionar sem interrupções.  

Você pode aumentar a quantidade de armazenamento provisionado para o servidor ou iniciar uma nova sessão no modo de gravação de leitura e soltar os dados para recuperar o armazenamento livre. Executar `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` define a sessão atual para o modo de gravação de leitura. Para evitar a corrupção de dados, não execute nenhuma operação de gravação quando o servidor ainda estiver em status somente leitura.

Recomendamos que você ative o aumento automático do armazenamento ou configure um alerta para notificá-lo quando o armazenamento do servidor estiver se aproximando do limite para que você possa evitar entrar no estado somente leitura. Para mais informações, consulte a documentação em [como configurar um alerta](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Crescimento automático do armazenamento

O crescimento automático de armazenamento impede que o servidor fique sem armazenamento e se torne somente leitura. Se o crescimento automático do armazenamento estiver habilitado, o armazenamento aumentará automaticamente sem afetar a carga de trabalho. Para servidores com menos de igual a 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado é aumentado em 5 GB assim que o armazenamento livre está abaixo do maior que 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado aumenta em 5% quando o espaço livre de armazenamento está abaixo do maior que 10 GB ou 5% do tamanho de armazenamento provisionado. Os limites de armazenamento máximos especificados acima se aplicam.

Por exemplo, se você tiver provisionado 1000 GB de armazenamento e a utilização real passar de 950 GB, o tamanho do armazenamento do servidor será aumentado para 1050 GB. Como alternativa, se você tiver provisionado 10 GB de armazenamento, o tamanho do armazenamento será aumentado para 15 GB quando menos de 1 GB de armazenamento for gratuito.

Lembre-se de que o armazenamento pode ser escalado verticalmente, não inativo.

## <a name="backup-storage"></a>Armazenamento de backup

O Banco de Dados do Azure para PostgreSQL fornece até 100% de seu armazenamento de servidor configurado como armazenamento de backup sem custo adicional. Qualquer armazenamento de backup que você usa em excesso desse valor é cobrado em GB por mês. Por exemplo, se você provisionar um servidor com 250 GB de armazenamento, terá 250 GB de armazenamento adicional disponível para backups de servidor sem encargos. O armazenamento para backups que ultrapassem os 250 GB é cobrado de acordo com o [modelo de preços](https://azure.microsoft.com/pricing/details/postgresql/). Para entender os fatores que influenciam o uso do armazenamento de backup, o monitoramento e o controle do custo de armazenamento de backup, você pode consultar a [documentação de backup](concepts-backup.md).

## <a name="scale-resources"></a>Escalar recursos

Após criar o servidor, você poderá, independentemente, alterar vCores, a geração de hardware, o tipo de preço (exceto em Básico), a quantidade de armazenamento e o período de retenção de backup. Não será possível alterar o tipo de armazenamento de backup depois que um servidor é criado. O número de vCores pode ser dimensionado para cima ou para baixo. Os vCores e o período de retenção de backup podem ser aumentados ou reduzidos de 7 a 35 dias. O tamanho de armazenamento só pode ser aumentado. O dimensionamento dos recursos pode ser feito por meio do portal ou da CLI do Azure. Para obter um exemplo de dimensionamento usando a CLI do Azure, consulte [Monitorar e dimensionar um servidor do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure](scripts/sample-scale-server-up-or-down.md).

> [!NOTE]
> O tamanho de armazenamento só pode ser aumentado. Não é possível voltar para um tamanho de armazenamento menor após o aumento.

Ao alterar o número de vCores, a geração de hardware ou o tipo de preço, uma cópia do servidor original é criada com a nova alocação de computação. Depois que o novo servidor entra em execução, as conexões são alternadas para o novo servidor. Durante um momento enquanto o sistema muda para o novo servidor, nenhuma nova conexão pode ser estabelecida e todas as transações não confirmadas são revertidas. Esse período varia, mas na maioria dos casos fica abaixo um minuto.

O dimensionamento do armazenamento e a alteração do período de retenção de backup são operações realmente online. Não há nenhum tempo de inatividade e o aplicativo não é afetado. Conforme o IOPS é dimensionado com o tamanho do armazenamento provisionado, você pode aumentar o IOPS disponível para seu servidor aumentando o armazenamento.

## <a name="pricing"></a>Preços

Para as informações mais recentes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/PostgreSQL/) do serviço. Para ver os custos da configuração desejada, o [Portal do Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal na guia **Tipo de preço** com base nas opções que você seleciona. Se você não tiver uma assinatura do Azure, poderá usar a calculadora de preços do Azure para obter um preço estimado. No site da [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/), selecione **Adicionar itens**, expanda a categoria **Bancos de dados** e escolha **Banco de Dados do Azure para PostgreSQL** para personalizar as opções.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um servidor PostgreSQL no portal](tutorial-design-database-using-azure-portal.md).
- Conheça os [limites de serviço](concepts-limits.md).
- Saiba como [fazer a expansão com réplicas de leitura](howto-read-replicas-portal.md).
