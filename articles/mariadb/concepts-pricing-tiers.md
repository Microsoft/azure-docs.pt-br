---
title: 'Tipos de preço: Banco de Dados do Azure para MariaDB'
description: Saiba mais sobre os vários tipos de preço do banco de dados do Azure para MariaDB, incluindo gerações de computação, tipo de armazenamento, tamanho de armazenamento, vCores, memória e períodos de retenção de backup.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: b5b5a506b2f932d20a617634ace7ebf02093fbfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664241"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Banco de dados do Azure para níveis de preços do MariaDB

Você pode criar um Banco de Dados do Azure para o servidor MariaDB em um dos três níveis de preços diferentes: Básico, Uso Geral e Memória Otimizada. Os tipos de preço são diferenciados pela quantidade de computação nos vCores que pode ser provisionada, pela memória por vCore e pela tecnologia de armazenamento usada para armazenar os dados. Todos os recursos são provisionados no nível do servidor MariaDB. Um servidor pode ter um ou vários bancos de dados.

| Recurso | **Basic** | **Uso Geral** | **Otimizado para memória** |
|:---|:----------|:--------------------|:---------------------|
| Geração de computação | Gen 5 |Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória por vCore | 2 GB | 5 GB | 10 GB |
| Tamanho de armazenamento | 5 GB a 1 TB | 5 GB a 4 TB | 5 GB a 4 TB |
| Período de retenção do backup de banco de dados | 7 a 35 dias | 7 a 35 dias | 7 a 35 dias |

Para escolher um tipo de preço, use a tabela a seguir como ponto de partida.

| Tipo de preço | Cargas de trabalho de destino |
|:-------------|:-----------------|
| Basic | Cargas de trabalho que exigem desempenho de E/S e computação leve. Os exemplos incluem servidores usados para desenvolvimento ou teste ou aplicativos de pequena escala usados com pouca frequência. |
| Uso Geral | A maioria das cargas de trabalho que exigem a computação e a memória balanceadas com a taxa de transferência de E/S escalonável. Os exemplos incluem servidores para hospedar aplicativos Web e móveis e outros aplicativos empresariais.|
| Otimizado para memória | Cargas de trabalho de banco de dados de alto desempenho que exigem desempenho na memória para o processamento de transações mais rápido e com simultaneidade mais alta. Os exemplos incluem servidores para o processamento de dados em tempo real e aplicativos analíticos ou transacionais de alto desempenho.|

Depois de criar um servidor, o número de vCores e a camada de preços (exceto para e do Basic) podem ser alterados para cima ou para baixo em segundos. Você pode também, independentemente, ajustar a quantidade de armazenamento de backup e o período de retenção de backup para cima ou para baixo sem tempo de inatividade do aplicativo. Não será possível alterar o tipo de armazenamento de backup depois que um servidor é criado. Para obter mais informações, consulte a seção [Scale Resources](#scale-resources) .

## <a name="compute-generations-and-vcores"></a>Gerações de computação e vCores

Os recursos de computação são fornecidos como vCores, que representam a CPU lógica do hardware subjacente. As CPUs lógicas de 5ª geração são baseadas em processadores E5-2673 v4 (Broadwell) 2,3 GHz.

## <a name="storage"></a>Armazenamento

O armazenamento que você provisiona é a quantidade de capacidade de armazenamento disponível para seu banco de dados do Azure para o servidor MariaDB. O armazenamento é usado para os arquivos de banco de dados, arquivos temporários, logs de transações e os logs do servidor MariaDB. A quantidade total de armazenamento que você provisiona também define a capacidade disponível para o servidor.

| Atributos de armazenamento   | Basic | Uso Geral | Otimizado para memória |
|:---|:----------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento básico | Armazenamento Uso Geral | Armazenamento Uso Geral |
| Tamanho de armazenamento | 5 GB a 1 TB | 5 GB a 4 TB | 5 GB a 4 TB |
| Tamanho do incremento de armazenamento | 1 GB | 1 GB | 1 GB |
| IOPS | Variável |3 IOPS/GB<br/>Mín 100 IOPS<br/>Máx 6000 IOPS | 3 IOPS/GB<br/>Mín 100 IOPS<br/>Máx 6000 IOPS |

Você pode adicionar capacidade de armazenamento adicional durante e após a criação do servidor e permitir que o sistema aumente o armazenamento automaticamente com base no consumo de armazenamento de sua carga de trabalho.

>[!NOTE]
> O armazenamento só pode ser escalado verticalmente, não horizontalmente.

A camada Básico não oferece garantia de IOPS. Nos tipos de preço Uso Geral e Otimizado para Memória, o IOPS é dimensionado com o tamanho de armazenamento provisionado a uma taxa de 3:1.

Você pode monitorar o consumo de E/S no Portal do Azure ou usando os comandos da CLI do Azure. As métricas relevantes para monitorar são o [limite de armazenamento, a porcentagem de armazenamento, o armazenamento usado e a porcentagem de e/s](concepts-monitoring.md).

### <a name="large-storage-preview"></a>Armazenamento grande (visualização)

Estamos aumentando os limites de armazenamento em nossas camadas de Uso Geral e com otimização de memória. Servidores recém-criados que aceitam a visualização podem provisionar até 16 TB de armazenamento. A escala de IOPS em uma proporção de 3:1 até 20.000 IOPS. Assim como acontece com o armazenamento atual disponível, você pode adicionar capacidade de armazenamento adicional após a criação do servidor e permitir que o sistema aumente o armazenamento automaticamente com base no consumo de armazenamento de sua carga de trabalho.

| Atributos de armazenamento | Uso Geral | Otimizado para memória |
|:-------------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento Premium do Azure | Armazenamento Premium do Azure |
| Tamanho de armazenamento | 32 GB a 16 TB| 32 a 16 TB |
| Tamanho do incremento de armazenamento | 1 GB | 1 GB |
| IOPS | 3 IOPS/GB<br/>Mín 100 IOPS<br/>IOPS máx. 20.000| 3 IOPS/GB<br/>Mín 100 IOPS<br/>IOPS máx. 20.000 |

> [!IMPORTANT]
> O armazenamento grande está atualmente em visualização pública nas seguintes regiões: leste dos EUA, leste dos EUA 2, sul do Brasil, EUA Central, oeste dos EUA, norte EUA Central, Sul EUA Central, Europa Setentrional, Europa Ocidental, Sul do Reino Unido, Oeste do Reino Unido, Sudeste Asiático, Ásia Oriental, leste do Japão, oeste do Japão, Coreia central, Coréia do Sul, leste da Austrália, sudeste da Austrália, oeste dos EUA, Canadá EUA Central e central do Canadá.
>
> Todas as outras regiões dão suporte a até 4 TB de armazenamento e até 6000 IOPS.
>

### <a name="reaching-the-storage-limit"></a>Alcançando o limite de armazenamento

Os servidores com armazenamento provisionado menor ou igual a 100 GB serão marcados como somente leitura caso o armazenamento livre seja inferior a 5% do tamanho de armazenamento provisionado. Os servidores com mais de 100 GB de armazenamento provisionado serão marcados como somente leitura quando o armazenamento livre for inferior a 5 GB.

Por exemplo, se você tiver provisionado 110 GB de armazenamento e a utilização real passar de 105 GB, o servidor será marcado como somente leitura. Como alternativa, se você tiver provisionado 5 GB de armazenamento, o servidor será marcado como somente leitura quando o armazenamento livre atingir menos de 256 MB.

Enquanto o serviço tenta tornar o servidor somente leitura, todas as novas solicitações de transação de gravação são bloqueadas e as transações ativas existentes continuarão a ser executadas. Quando o servidor é definido como somente leitura, todas as operações de gravação subsequente e a transação comentem falha. Consultas de leitura continuam a funcionar sem interrupções. Após aumentar o armazenamento provisionado, o servidor estará pronto para aceitar transações de gravação novamente.

Recomendamos que você ative o aumento automático do armazenamento ou configure um alerta para notificá-lo quando o armazenamento do servidor estiver se aproximando do limite para que você possa evitar entrar no estado somente leitura. Para mais informações, consulte a documentação em [como configurar um alerta](howto-alert-metric.md).

### <a name="storage-auto-grow"></a>Crescimento automático do armazenamento

O crescimento automático de armazenamento impede que o servidor fique sem armazenamento e se torne somente leitura. Se o crescimento automático do armazenamento estiver habilitado, o armazenamento aumentará automaticamente sem afetar a carga de trabalho. Para servidores com menos de igual a 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado é aumentado em 5 GB quando o armazenamento livre está abaixo de 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado aumenta em 5% quando o espaço livre de armazenamento está abaixo de 10 GB do tamanho de armazenamento provisionado. Os limites de armazenamento máximos especificados acima se aplicam.

Por exemplo, se você tiver provisionado 1000 GB de armazenamento e a utilização real passar de 990 GB, o tamanho do armazenamento do servidor será aumentado para 1050 GB. Como alternativa, se você tiver provisionado 10 GB de armazenamento, o tamanho do armazenamento será aumentado para 15 GB quando menos de 1 GB de armazenamento for gratuito.

Lembre-se de que o armazenamento pode ser escalado verticalmente, não inativo.

## <a name="backup"></a>Backup

O Banco de Dados do Azure para MariaDB fornece até 100% de seu armazenamento de servidor configurado como armazenamento de backup, sem custo adicional. Qualquer armazenamento de backup que você usa em excesso desse valor é cobrado em GB por mês. Por exemplo, se você provisionar um servidor com 250 GB de armazenamento, terá 250 GB de armazenamento adicional disponível para backups de servidor sem encargos. O armazenamento para backups que ultrapassem os 250 GB é cobrado de acordo com o [modelo de preços](https://azure.microsoft.com/pricing/details/mariadb/). Para entender os fatores que influenciam o uso do armazenamento de backup, o monitoramento e o controle do custo de armazenamento de backup, você pode consultar a [documentação de backup](concepts-backup.md).

## <a name="scale-resources"></a>Escalar recursos

Após criar o servidor, você poderá alterar separadamente os vCores, a camada de preços (exceto para e para Básico), a quantidade de armazenamento e o período de retenção de backup. Não será possível alterar o tipo de armazenamento de backup depois que um servidor é criado. O número de vCores pode ser dimensionado para cima ou para baixo. Os vCores e o período de retenção de backup podem ser aumentados ou reduzidos de 7 a 35 dias. O tamanho de armazenamento só pode ser aumentado. O dimensionamento dos recursos pode ser feito por meio do portal ou da CLI do Azure. 

Ao alterar o número de vCores ou a camada de preços, uma cópia do servidor original será criada com a nova alocação de computação. Depois que o novo servidor entra em execução, as conexões são alternadas para o novo servidor. Durante um momento enquanto o sistema muda para o novo servidor, nenhuma nova conexão pode ser estabelecida e todas as transações não confirmadas são revertidas. Esse período varia, mas na maioria dos casos fica abaixo um minuto.

O dimensionamento do armazenamento e a alteração do período de retenção de backup são operações realmente online. Não há nenhum tempo de inatividade e o aplicativo não é afetado. Conforme o IOPS é dimensionado com o tamanho do armazenamento provisionado, você pode aumentar o IOPS disponível para seu servidor aumentando o armazenamento.

## <a name="pricing"></a>Preços

Para as informações mais recentes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/mariadb/) do serviço. Para ver os custos da configuração desejada, o [Portal do Azure](https://portal.azure.com/#create/Microsoft.MariaDBServer) mostra o custo mensal na guia **Tipo de preço** com base nas opções que você seleciona. Se você não tiver uma assinatura do Azure, poderá usar a calculadora de preços do Azure para obter um preço estimado. No site [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/), selecione **Adicionar itens**, expanda a categoria **Bancos de dados** e escolha **Banco de Dados do Azure para MariaDB** para personalizar as opções.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [limitações de serviço](concepts-limits.md).
- Saiba como [criar um servidor do MariaDB no portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).