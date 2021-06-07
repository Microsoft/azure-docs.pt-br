---
title: Opções de computação e armazenamento-banco de dados do Azure para MySQL-servidor flexível
description: Este artigo descreve as opções de computação e armazenamento no banco de dados do Azure para MySQL-servidor flexível.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 7165cdc072ffaa5b0d862e1fe17f94e35c35aeec
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034530"
---
# <a name="compute-and-storage-options-in-azure-database-for-mysql---flexible-server-preview"></a>Opções de computação e armazenamento no banco de dados do Azure para MySQL – servidor flexível (visualização)

> [!IMPORTANT] 
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Você pode criar um servidor flexível do banco de dados do Azure para MySQL em uma das três diferentes camadas de computação: expansível, Uso Geral e otimizado para memória. As camadas de computação são diferenciadas pelo SKU de VM subjacente usado pela série B, série D e série E. A escolha da camada e do tamanho de computação determina a memória e o vCores disponíveis no servidor. A mesma tecnologia de armazenamento é usada em todas as camadas de computação. Todos os recursos são provisionados no nível do servidor MySQL. Um servidor pode ter um ou vários bancos de dados.

| Recurso/camada | **Expansíveis** | **Uso Geral** | **Otimizado para memória** |
|:---|:----------|:--------------------|:---------------------|
| Série da VM| Série B | Série Ddsv4 | Série Edsv4|
| vCores | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Memória por vCore | Variável | 4 GiB | 8 GiB * |
| Tamanho de armazenamento | 5 GiB a 16 TiB | 5 GiB a 16 TiB | 5 GiB a 16 TiB |
| Período de retenção do backup de banco de dados | 1 a 35 dias | 1 a 35 dias | 1 a 35 dias |

\* Exceção do SKU de E64ds_v4 (com otimização de memória), que tem 504 GB de memória

Para escolher uma camada de computação, use a tabela a seguir como um ponto de partida.

| Camada de computação | Cargas de trabalho de destino |
|:-------------|:-----------------|
| Expansíveis | Melhor para cargas de trabalho que não precisam da CPU completa continuamente. |
| Uso Geral | A maioria das cargas de trabalho que exigem a computação e a memória balanceadas com a taxa de transferência de E/S escalonável. Os exemplos incluem servidores para hospedar aplicativos Web e móveis e outros aplicativos empresariais.|
| Otimizado para memória | Cargas de trabalho de banco de dados de alto desempenho que exigem desempenho na memória para o processamento de transações mais rápido e com simultaneidade mais alta. Os exemplos incluem servidores para o processamento de dados em tempo real e aplicativos analíticos ou transacionais de alto desempenho.|

Depois de criar um servidor, a camada de computação, o tamanho da computação e o tamanho do armazenamento foram alterados. O dimensionamento de computação requer uma reinicialização e demora entre 60-120 segundos, enquanto o dimensionamento de armazenamento não requer reinicialização. Você também pode ajustar de forma independente o período de retenção de backup para cima ou para baixo. Para obter mais informações, consulte a seção [Scale Resources](#scale-resources) .

## <a name="compute-tiers-size-and-server-types"></a>Camadas de computação, tamanho e tipos de servidor

Os recursos de computação podem ser selecionados com base na camada e no tamanho. Isso determina o vCores e o tamanho da memória. vCores representam a CPU lógica do hardware subjacente.

As especificações detalhadas dos tipos de servidor disponíveis são as seguintes:

| Tamanho da computação         | vCores | Tamanho da memória (GiB) | IOPS máximo com suporte | Largura de banda de e/s com suporte máx (MBps)|
|----------------------|--------|-------------------| ------------------ |-----------------------------------|
| **Expansíveis**        |        |                   | 
| Standard_B1s         | 1      | 1                 | 320                | 10                                | 
| Standard_B1ms        | 1      | 2                 | 640                | 10                                |
| Standard_B2s         | 2      | 4                 | 1280               | 15                                |
| **Uso Geral**  |        |                   |                    |                                   |
| Standard_D2ds_v4     | 2      | 8                 | 3200               | 48                                |
| Standard_D4ds_v4     | 4      | 16                | 6400               | 96                                |
| Standard_D8ds_v4     | 8      | 32                | 12800              | 192                               |
| Standard_D16ds_v4    | 16     | 64                | 20000              | 384                               |
| Standard_D32ds_v4    | 32     | 128               | 20000              | 768                               |
| Standard_D48ds_v4    | 48     | 192               | 20000              | 1152                              |
| Standard_D64ds_v4    | 64     | 256               | 20000              | 1200                              |
| **Otimizado para memória** |        |                   |                    |                                   |
| Standard_E2ds_v4     | 2      | 16                | 3200               | 48                                |
| Standard_E4ds_v4     | 4      | 32                | 6400               | 96                                |
| Standard_E8ds_v4     | 8      | 64                | 12800              | 192                               |
| Standard_E16ds_v4    | 16     | 128               | 20000              | 384                               |
| Standard_E32ds_v4    | 32     | 256               | 20000              | 768                               |
| Standard_E48ds_v4    | 48     | 384               | 20000              | 1152                              |
| Standard_E64ds_v4    | 64     | 504               | 20000              | 1200                              |

Para obter mais detalhes sobre a série de computação disponível, consulte a documentação da VM do Azure para [intermitência (série B)](../../virtual-machines/sizes-b-series-burstable.md), [uso geral (série Ddsv4)](../../virtual-machines/ddv4-ddsv4-series.md)e [otimizado para memória (série Edsv4)](../../virtual-machines/edv4-edsv4-series.md).

>[!NOTE]
>Para a [camada de computação expansível (série B)](../../virtual-machines/sizes-b-series-burstable.md) se a VM for iniciada/interrompida ou reiniciada, os créditos poderão ser perdidos. Para obter mais informações, consulte [FAQ (série B) com intermitência](../../virtual-machines/sizes-b-series-burstable.md#q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart).

## <a name="storage"></a>Armazenamento

O armazenamento que você provisiona é a quantidade de capacidade de armazenamento disponível para seu servidor flexível. O armazenamento é usado para arquivos de banco de dados, arquivos temporários, logs de transações e logs do servidor MySQL. Em todas as camadas de computação, o armazenamento mínimo com suporte é 5 GiB e o máximo é 16 TiB. O armazenamento é dimensionado em incrementos de 1 GiB e pode ser escalado verticalmente após a criação do servidor.

>[!NOTE]
> O armazenamento só pode ser escalado verticalmente, não horizontalmente.

Você pode monitorar seu consumo de armazenamento no portal do Azure (com Azure Monitor) usando o limite de armazenamento, a porcentagem de armazenamento e as métricas de armazenamento usadas. Consulte o [artigo monitoramento](./concepts-monitoring.md) para saber mais sobre as métricas. 

### <a name="reaching-the-storage-limit"></a>Alcançando o limite de armazenamento

Quando o armazenamento consumido no servidor está próximo de atingir o limite provisionado, o servidor é colocado no modo somente leitura para proteger as gravações perdidas no servidor. Servidores com menos de igual a 100 GiB de armazenamento provisionado são marcados como somente leitura se o armazenamento livre tiver menos de 5% do tamanho de armazenamento provisionado. Servidores com mais de 100 GiB de armazenamento provisionado são marcados como somente leitura quando o armazenamento livre é inferior a 5 GiB.

Por exemplo, se você tiver provisionado 110 GiB de armazenamento e a utilização real passar de 105 GiB, o servidor será marcado como somente leitura. Como alternativa, se você tiver provisionado 5 GiB de armazenamento, o servidor será marcado como somente leitura quando o armazenamento livre atingir menos de 256 MB.

Enquanto o serviço tenta tornar o servidor somente leitura, todas as novas solicitações de transação de gravação são bloqueadas e as transações ativas existentes continuarão a ser executadas. Quando o servidor é definido como somente leitura, todas as operações de gravação subsequente e a transação comentem falha. Consultas de leitura continuam a funcionar sem interrupções. 

Para colocar o servidor fora do modo somente leitura, você deve aumentar o armazenamento provisionado no servidor. Isso pode ser feito usando o portal do Azure ou CLI do Azure. Uma vez aumentado, o servidor estará pronto para aceitar as transações de gravação novamente.

Recomendamos que você configure um alerta para notificá-lo quando o armazenamento do servidor estiver se aproximando do limite, para evitar entrar no estado somente leitura. Consulte o [artigo monitoramento](./concepts-monitoring.md) para saber mais sobre as métricas disponíveis. 

É recomendável que você <!--turn on storage auto-grow or to--> Configure um alerta para notificá-lo quando o armazenamento do servidor estiver se aproximando do limite para que você possa evitar entrar no estado somente leitura. Para obter mais informações, consulte a documentação sobre a documentação do alerta [como configurar um alerta](how-to-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Crescimento automático do armazenamento

O crescimento automático de armazenamento ainda não está disponível para o servidor flexível do banco de dados do Azure para MySQL.

## <a name="iops"></a>IOPS

Banco de dados do Azure para MySQL – o servidor flexível dá suporte ao provisionamento de IOPS adicional. Esse recurso permite que você provisione IOPS adicionais acima do limite de IOPS gratuito. Usando esse recurso, você pode aumentar ou diminuir o número de IOPS provisionados com base em seus requisitos de carga de trabalho a qualquer momento. 

O IOPS mínimo é de 100 em todos os tamanhos de computação e o máximo de IOPS é determinado pelo tamanho de computação selecionado. Em versão prévia, o máximo de IOPS com suporte é de 20.000 IOPS.

Para saber mais sobre o máximo de IOPS por tamanho de computação, veja abaixo: 

| Tamanho da computação         | IOPS máximo        | 
|----------------------|---------------------|
| **Expansíveis**        |                     |
| Standard_B1s         | 320                 |
| Standard_B1ms        | 640                 |
| Standard_B2s         | 1280                | 
| **Uso Geral**  |                     |
| Standard_D2ds_v4     | 3200                |
| Standard_D4ds_v4     | 6400                |
| Standard_D8ds_v4     | 12800               |
| Standard_D16ds_v4    | 20000               |
| Standard_D32ds_v4    | 20000               |
| Standard_D48ds_v4    | 20000               | 
| Standard_D64ds_v4    | 20000               | 
| **Otimizado para memória** |                     | 
| Standard_E2ds_v4     | 3200                | 
| Standard_E4ds_v4     | 6400                | 
| Standard_ E8ds_v4    | 12800               | 
| Standard_ E16ds_v4   | 20000               | 
| Standard_E32ds_v4    | 20000               | 
| Standard_E48ds_v4    | 20000               | 
| Standard_E64ds_v4    | 20000               |  

O IOPS máximo depende do máximo de IOPS disponível por tamanho de computação. Consulte a coluna *máximo de taxa de transferência de disco não armazenado em cache: IOPS/Mbps* na documentação da série [B](../../virtual-machines/sizes-b-series-burstable.md), [série Ddsv4](../../virtual-machines/ddv4-ddsv4-series.md)e [série Edsv4](../../virtual-machines/edv4-edsv4-series.md) .

> [!Important]
> **IOPS complementares** são iguais ao mínimo ("taxa de transferência máxima do disco não armazenado em cache: IOPS/Mbps" do tamanho da computação, armazenamento provisionado em GIB * 3)<br>
> O **IOPS mínimo** é 100 em todos os tamanhos de computação<br>
> O **IOPS máximo** é determinado pelo tamanho de computação selecionado. Em versão prévia, o máximo de IOPS com suporte é de 20.000 IOPS.

Você pode monitorar o consumo de e/s no portal do Azure (com Azure Monitor) usando a métrica de [percentual de e](./concepts-monitoring.md) /s. Se precisar de mais IOPS, então o máximo de IOPS com base na computação, você precisará dimensionar a computação do servidor.

## <a name="backup"></a>Backup

O serviço faz backups do servidor automaticamente. Você pode selecionar um período de retenção de um intervalo de 1 a 35 dias. Saiba mais sobre backups no [artigo conceitos de backup e restauração](concepts-backup-restore.md).

## <a name="scale-resources"></a>Escalar recursos

Depois de criar o servidor, você pode alterar independentemente a camada de computação, o tamanho da computação (vCores e a memória) e a quantidade de armazenamento e o período de retenção do backup. O tamanho da computação pode ser aumentado ou reduzido verticalmente. O período de retenção de backup pode ser aumentado ou reduzido de 1 a 35 dias. O tamanho de armazenamento só pode ser aumentado. O dimensionamento dos recursos pode ser feito por meio do portal ou CLI do Azure.

> [!NOTE]
> O tamanho de armazenamento só pode ser aumentado. Não é possível voltar para um tamanho de armazenamento menor após o aumento.

Quando você altera a camada de computação ou o tamanho da computação, o servidor é reiniciado para que o novo tipo de servidor entre em vigor. Durante um momento enquanto o sistema muda para o novo servidor, nenhuma nova conexão pode ser estabelecida e todas as transações não confirmadas são revertidas. Essa janela varia, mas na maioria dos casos, está entre 60-120 segundos. 

O dimensionamento do armazenamento e a alteração do período de retenção do backup são operações online e não exigem uma reinicialização do servidor.

## <a name="pricing"></a>Preços

Para as informações mais recentes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/MySQL/) do serviço. Para ver o custo da configuração desejada, o [portal do Azure](https://portal.azure.com/#create/Microsoft.MySQLServer/flexibleServers) mostra o custo mensal na guia **computação + armazenamento** com base nas opções selecionadas. Se você não tiver uma assinatura do Azure, poderá usar a calculadora de preços do Azure para obter um preço estimado. No site da [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) , selecione **Adicionar itens**, expanda a categoria **bancos** de dados, escolha **banco de dados do Azure para MySQL** e **servidor flexível** como o tipo de implantação para personalizar as opções.

Se desejar otimizar o custo do servidor, você pode considerar as seguintes dicas:

- Reduza verticalmente sua camada de computação ou tamanho de computação (vCores) se a computação estiver subutilizada.
- Considere mudar para a camada de computação expansível se sua carga de trabalho não precisar da capacidade de computação completa continuamente das camadas de Uso Geral e com otimização de memória.
- Parar o servidor quando não estiver em uso.
- Reduza o período de retenção de backup se não for necessária uma retenção mais longa do backup.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um servidor MySQL no portal](quickstart-create-server-portal.md).
- Saiba mais sobre as [limitações de serviço](concepts-limitations.md).
