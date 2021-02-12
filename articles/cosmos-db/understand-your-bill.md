---
title: Entendendo sua cobrança do Azure Cosmos DB
description: Este artigo ajuda você a entender sua fatura do Azure Cosmos DB com alguns exemplos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2020
ms.reviewer: sngun
ms.openlocfilehash: b0d27cb2f941db374a144cbd5c026444730e24e8
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247481"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Entendendo sua fatura do Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Como um serviço de banco de dados nativo de nuvem totalmente gerenciado, Azure Cosmos DB simplifica a cobrança carregando apenas as operações de banco de dados e o armazenamento consumido. Não existem valores de licença adicionais, hardware, custos de utilitários ou custos de instalação, comparados com alternativas locais ou hospedadas em IaaS. Quando você considera os recursos multirregiões do Azure Cosmos DB, o serviço de banco de dados reduz os custos substancialmente em comparação com soluções de IaaS ou locais existentes.

- **Operações de banco de dados**: a maneira como você é cobrado pelas operações de banco de dados depende do tipo de conta do Azure cosmos que você está usando.

  - **Taxa de transferência provisionada**: você será cobrado por hora para obter a taxa de transferência máxima provisionada para uma determinada hora, em incrementos de 100 ru/s.
  - Sem **servidor**: você é cobrado por hora para a quantidade total de unidades de solicitação consumidas pelas operações de banco de dados.

- **Armazenamento**: você é cobrado como uma taxa fixa para a quantidade total de armazenamento consumido por seus dados e índices para uma determinada hora.

Consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter as informações de preços mais recentes.

Este artigo usa alguns exemplos para ajudá-lo a entender os detalhes encontrados na fatura mensal. Os números mostrados nos exemplos podem ser diferentes se seus contêineres do Azure Cosmos têm uma taxa de transferência provisionada diferente, se eles abrangem várias regiões ou são executados em regiões diferentes por algum tempo durante o mês. Todos os exemplos neste artigo calculam a fatura com base nas informações de preços mostradas na [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).

> [!NOTE]
> A cobrança é para qualquer parte de uma hora do relógio, não para uma duração de 60 minutos. Todos os exemplos mostrados neste documento baseiam-se no preço de uma conta do Azure Cosmos implantada em uma região não governamental nos EUA. O preço e o cálculo variam de acordo com a região que você está usando, consulte a [página de preços Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter as informações mais recentes sobre preços.

## <a name="billing-examples"></a>Exemplos de cobrança

### <a name="billing-example---provisioned-throughput-on-a-container-full-month"></a>Exemplo de cobrança-taxa de transferência provisionada em um contêiner (mês inteiro)

* Vamos supor que você configurou uma taxa de transferência de 1.000 RU/s em um contêiner e ele existe por 24 horas * 30 dias do mês = 720 horas no total.  

* 1\.000 RU/s representa 10 unidades de 100 RU/s por hora para cada hora de existência do contêiner (ou seja, 1.000/100 = 10). 

* Multiplicando 10 unidades por hora ao custo de US$ 0,008 (por 100 RU/s por hora) = US$ 0,08 por hora. 

* Multiplicando US$ 0,08 por hora pelo número de horas do mês, dá US$ 0,08 * 24 horas * 30 dias = US$ 57,60 por mês.  

* O total de sua fatura mensal mostrará 7.200 unidades (de 100 RUs), o que custará US$ 57,60.

### <a name="billing-example---provisioned-throughput-on-a-container-partial-month"></a>Exemplo de cobrança-taxa de transferência provisionada em um contêiner (mês parcial)

* Vamos supor que criamos um contêiner com taxa de transferência provisionada de 2.500 RU/s. O contêiner existe por 24 horas ao longo do mês (por exemplo, podemos excluí-lo 24 horas depois de o criarmos).  

* Em seguida, vamos ver 600 unidades na fatura (2.500 RU/s / 100 RU/s/unidade * 24 horas). O custo será de US$ 4,80 (600 unidades * US$ 0,008/unidade).

* A fatura total para o mês será de US$ 4,80.

### <a name="billing-example---serverless-container"></a>Exemplo de cobrança-contêiner sem servidor

* Vamos supor que criamos um contêiner sem servidor. 

* Mais de um mês, emitimos solicitações de banco de dados consumindo um total de 500.000 unidades de solicitação. O custo será de $0.125 (500.000 * $0,25/milhão).

* A fatura total do mês será $0.125.

### <a name="billing-rate-if-storage-size-changes"></a>Taxa de cobrança em caso de mudança do tamanho de armazenamento

A capacidade de armazenamento é cobrada em unidades da quantidade máxima por hora de dados armazenados (em GB) durante um período de um mês. Por exemplo, se você usar 100 GB de armazenamento na primeira metade do mês e 50 GB na segunda metade, será cobrado pelo equivalente a 75 GB de armazenamento durante o mês em questão.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Taxa de cobrança quando o contêiner ou um conjunto de contêineres fica ativo por menos de uma hora

Será cobrada a taxa fixa por hora de existência do contêiner ou do banco de dados, independentemente do uso ou de o contêiner ou banco de dados ficar ativo por menos de uma hora. Por exemplo, se você criar um contêiner ou banco de dados e o excluir depois de 5 minutos, sua fatura incluirá uma hora.

### <a name="billing-rate-when-provisioned-throughput-on-a-container-or-database-scales-updown"></a>Taxa de cobrança quando a taxa de transferência provisionada em um contêiner ou banco de dados é dimensionada para cima/para baixo

Se às 9h30 você aumentar a taxa de transferência provisionada de 400 RU/s para 1.000 RU/s e voltar a diminuí-la às 10h45 para 400 RU/s, será cobrado por duas horas de 1.000 RU/s. 

Se você aumentar a taxa de transferência provisionada para um contêiner ou um conjunto de contêineres às 9h30 de 100 mil RU/s para 200 mil RU/s e, em seguida, reduzi-la às 10h45 para 100 mil RU/s, será cobrado por duas horas de 200 mil RU/s.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Exemplo de cobrança: vários contêineres, cada um com modo de taxa de transferência provisionada dedicada

* Se você criar no Leste dos EUA 2 uma conta do Azure Cosmos com dois contêineres com a taxa de transferência provisionada de 500 RU/s e 700 RU/s, respectivamente, terá uma taxa de transferência total de 1.200 RU/s.  

* Você será cobrado 1.200/100 * US$ 0,008 = US$ 0,096/hora. 

* Se suas necessidades de taxa de transferência forem alteradas e você aumentar a capacidade de cada contêiner em 500 RU/s e ainda criar um novo contêiner ilimitado com 20.000 RU/s, sua capacidade provisionada geral será de 22.200 RU/s (1.000 RU/s + 1.200 RU/s + 20.000 RU/s).  

* Nesse caso, sua fatura será alterada para: US$ 0,008  x 222 =  US$ 1.776/hora. 

* Em um mês de 720 horas (24 horas * 30 dias), se por 500 horas a taxa de transferência provisionada foi de 1.200 RU/s e, nas 220 horas restantes a taxa de transferência provisionada foi de 22.200 RU/s, sua fatura mensal mostrará: 500 x US$ 0,096/hora + 220 x US$ 1.776/hora = US$ 438,72/mês.

:::image type="content" source="./media/understand-your-bill/bill-example1.png" alt-text="Exemplo de cobrança da taxa de transferência dedicada":::

### <a name="billing-example-containers-with-shared-provisioned-throughput-mode"></a>Exemplo de cobrança: contêineres com modo de taxa de transferência compartilhado (provisionado)

* Se você criar uma conta do Azure Cosmos no Leste dos EUA 2 com dois bancos de dados Azure Cosmos (com um conjunto de contêineres que compartilham a taxa de transferência no nível do banco de dados) e taxas de transferência provisionadas de 50 mil RU/s e 70 mil RU/s, respectivamente, terá um total de taxa de transferência provisionada de 120 mil RU/s.  

* Você será cobrado 1.200 x US$ 0,008 = US$ 9,60 por hora. 

* Se suas necessidades de taxa de transferência forem alteradas e você aumentar a taxa de transferência provisionada de cada banco de dados em 10 mil RU/s para cada um, e adicionar um novo contêiner ao primeiro banco de dados com o modo de taxa de transferência dedicada de 15 mil RU/s para o banco de dados com taxa de transferência compartilhada, a capacidade provisionada geral será de 155 mil RU/s (60 mil RU/s + 80 mil RU/s + 15 mil RU/s).  

* Nesse caso, sua fatura será alterada para: 1.550 * US$ 0,008 = US$ 12,40 por hora.  

* Em um mês de 720 horas, se por 300 horas a taxa de transferência provisionada foi de 120 mil RU/s e, nas 420 horas restantes a taxa de transferência provisionada foi de 155 mil RU/s, sua fatura mensal mostrará: 300 x US$ 9,60/hora + 420 x US$12,40/hora = US$ 2.880 + US$ 5.208 = US$ 8.088/mês. 

:::image type="content" source="./media/understand-your-bill/bill-example2.png" alt-text="Exemplo de cobrança da taxa de transferência compartilhada":::

## <a name="billing-examples-with-geo-replication"></a>Exemplos de cobrança com replicação geográfica  

É possível adicionar/remover regiões do Azure em qualquer lugar do mundo da sua conta de banco de dados do Azure Cosmos a qualquer momento. A taxa de transferência que você configurou para vários bancos de dados e contêineres do Azure Cosmos estará reservada em cada região do Azure associada à sua conta de banco de dados do Azure Cosmos DB. Se a soma da taxa de transferência provisionada (RU/s) configurada em todos os bancos de dados e contêineres na sua conta do Azure Cosmos Database (provisionado por hora) for T e o número de regiões do Azure associado à sua conta de banco de dados for N, em seguida, a taxa de transferência total provisionada para uma determinada hora, para sua conta do banco de dados Cosmos do Azure é igual a T x N RU/seg. A taxa de transferência provisionada (região de gravação única) custa US $0.008/hora por 100 RU/s e taxa de transferência provisionada com várias regiões graváveis (configuração de gravações de várias regiões) custos de $0.016/por hora por 100 RU/s (consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)). Seja com região de gravação única ou várias regiões de gravação, o Azure Cosmos DB permite a leitura de dados de qualquer região.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Exemplo de cobrança: conta do Azure Cosmos multirregiões, com gravação em uma única região

Vamos supor que você tenha um contêiner do Azure Cosmos no Oeste dos EUA. O contêiner é criado com uma taxa de transferência de 10K RU/s e você armazenou 1 TB de dados este mês. Vamos supor que você adicionou três regiões (Leste dos EUA, Norte da Europa e Leste da Ásia) à conta do Azure Cosmos, cada uma com o mesmo armazenamento e a mesma taxa de transferência. O valor total da sua fatura mensal será (considerando 30 dias em um mês). Sua fatura ficaria da seguinte maneira: 

|**Item** |**Uso (por mês)** |**Tarifa** |**Custo mensal** |
|---------|---------|---------|-------|
|Cobrança da taxa de transferência para o contêiner no Oeste dos EUA      | 10 mil RU/s * 24 * 30    |US$ 0,008 por 100 RU/s por hora   |US$ 576|
|Cobrança da taxa de transferência para três regiões adicionais: Leste dos EUA, Norte da Europa e Leste da Ásia       | 3 * 10 mil RU/s * 24 * 30    |US$ 0,008 por 100 RU/s por hora  |US$ 1.728|
|Cobrança de armazenamento para o contêiner no Oeste dos EUA      | 250 GB    |US$ 0,25/GB  |US$ 62,50|
|Cobrança de armazenamento para três regiões adicionais – Leste dos EUA, Norte da Europa e Leste da Ásia      | 3 * 250 GB    |US$ 0,25/GB  |US$ 187,50|
|**Total**     |     |  |**US$ 2.554**|

*Suponha também que você use 100 GB de dados todos os meses do contêiner no Oeste dos EUA para replicar os dados no Leste dos EUA, no Norte da Europa e no Leste da Ásia. Você será cobrado pela saída conforme as taxas de transferência de dados.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Exemplo de cobrança: conta do Azure Cosmos multirregiões, com gravação em várias regiões

Vamos supor que você criou um contêiner do Azure Cosmos no Oeste dos EUA. O contêiner é criado com uma taxa de transferência de 10K RU/s e você armazenou 1 TB de dados este mês. Vamos supor que você adicionou três regiões (Leste dos EUA, Norte da Europa e Leste da Ásia), cada uma com o mesmo armazenamento e com a mesma taxa de transferência, e deseja ter capacidade de gravação para os contêineres em todas as regiões associadas à conta do Azure Cosmos. O valor total da sua fatura mensal será (considerando 30 dias em um mês) o seguinte:

|**Item** |**Uso (por mês)**|**Tarifa** |**Custo mensal** |
|---------|---------|---------|-------|
|Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)       | 10 mil RU/s * 24 * 30    |US$ 0,016 por 100 RU/s por hora    |US$ 1.152 |
|Cobrança da taxa de transferência para três regiões adicionais: Leste dos EUA, Norte da Europa e Leste da Ásia (todas as regiões são graváveis)        | 3 * 10 mil RU/s * 24 * 30    |US$ 0,016 por 100 RU/s por hora   |$3456 |
|Cobrança de armazenamento para o contêiner no Oeste dos EUA      | 250 GB    |US$ 0,25/GB  |US$ 62,50|
|Cobrança de armazenamento para três regiões adicionais – Leste dos EUA, Norte da Europa e Leste da Ásia      | 3 * 250 GB    |US$ 0,25/GB  |US$ 187,50|
|**Total**     |     |  |**US$ 6.010**|

*Suponha também que você use 100 GB de dados todos os meses do contêiner no Oeste dos EUA para replicar os dados no Leste dos EUA, no Norte da Europa e no Leste da Ásia. Você será cobrado pela saída conforme as taxas de transferência de dados.*

### <a name="billing-example-azure-cosmos-account-with-multi-region-writes-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Exemplo de cobrança: conta do Azure cosmos com gravações de várias regiões, taxa de transferência no nível do banco de dados, incluindo modo de taxa de transferência dedicada para alguns contêiner

Vamos considerar o exemplo a seguir, em que temos uma conta do Azure Cosmos de várias regiões, em que toda região é gravável (várias regiões de gravação são configuradas). Vamos supor que o tamanho de armazenamento permanece constante e não é alterado, e vamos omiti-lo aqui para simplificar a explicação. A taxa de transferência durante o mês variou da seguinte maneira (supondo-se 30 dias ou 720 horas): 

[0 a 100 horas]:  

* Criamos uma conta do Azure Cosmos com três regiões (Oeste dos EUA, Leste dos EUA, Norte da Europa), em que todas as regiões são graváveis 

* Criamos um banco de dados (D1) com taxa de transferência compartilhada de 10 mil RU/s 

* Criamos um banco de dados (D2) com taxa de transferência compartilhada de 30 mil RU/s e  

* Criamos um contêiner (C1) com taxa de transferência dedicada de 20 mil RU/s 

[101 a 200 horas]:  

* Expandimos o banco de dados (D1) para 50 mil RU/s 

* Expandimos o banco de dados (D2) para 70 mil RU/s  

* Excluímos o contêiner (C1)  

[201 a 300 horas]:  

* Criamos o contêiner (C1) novamente com taxa de transferência dedicada de 20 mil RU/s 

[301 a 400 horas]:  

* Removemos uma das regiões da conta do Azure Cosmos (o número de regiões graváveis agora é 2) 

* Reduzimos verticalmente o banco de dados (D1) para 10 mil RU/s 

* Expandimos o banco de dados (D2) para 80 mil RU/s  

* Excluímos o contêiner (C1) novamente 

[401 a 500 horas]:  

* Reduzimos verticalmente o banco de dados (D2) para 10 mil RU/s  

* Criamos o contêiner (C1) novamente com taxa de transferência dedicada de 20 mil RU/s 

[501 a 700 horas]:  

* Expandimos o banco de dados (D1) para 20 mil RU/s  

* Expandimos o banco de dados (D2) para 100 mil RU/s  

* Excluímos o contêiner (C1) novamente  

[701 a 720 horas]:  

* Reduzimos verticalmente o banco de dados (D2) para 50 mil RU/s  

Visualmente, as alterações na taxa de transferência provisionada total durante 720 horas no mês são mostradas na figura abaixo: 

:::image type="content" source="./media/understand-your-bill/bill-example3.png" alt-text="Exemplo da vida real":::

A fatura mensal total (supondo 30 dias/720 horas em um mês) será calculada da seguinte maneira:

|**Horas**  |**RU/s** |**Item** |**Uso (por hora)** |**Custo** |
|---------|---------|---------|-------|-------|
|[0 a 100] |D1:10 mil <br/>D2:30 mil <br/>C1:20 mil |Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |US$ 960  |
| | |Cobrança de taxa de transferência para duas regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são graváveis)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |US$ 2.880  |
|[101 a 200] |D1:50 mil <br/>D2:70 mil <br/>C1: -- |Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |US$ 1.920  |
| | |Cobrança de taxa de transferência para duas regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são graváveis)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |US$ 5.760  |
|[201 a 300]  |D1:50 mil <br/>D2:70 mil <br/>C1:20 mil |Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |US$ 2.240  |
| | |Cobrança de taxa de transferência para duas regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são graváveis)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |US$ 6.720 |
|[301 a 400] |D1:10 mil <br/>D2:80 mil <br/>C1: -- |Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |US$ 1.440   |
| | |Cobrança de taxa de transferência para duas regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são graváveis)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |US$ 2.880  |
|[401 a 500] |D1:10 mil <br>D2:10 mil <br>C1:20 mil |Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |US$ 640  |
| | |Cobrança de taxa de transferência para duas regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são graváveis)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |US$ 1.280  |
|[501 a 700] |D1:20 mil <br>D2:100 mil <br>C1: -- |Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |US$ 3.840  |
| | |Cobrança de taxa de transferência para duas regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são graváveis)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |US$ 7.680  |
|[701 a 720] |D1:20 mil <br/>D2:50 mil <br/>C1: -- |Cobrança de taxa de transferência para o contêiner no Oeste dos EUA (todas as regiões são graváveis)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |U$ 224  |
| | |Cobrança de taxa de transferência para duas regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são graváveis)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |U$ 224  |
|| |**Custo mensal total**  | |**US$ 38.688**   |

## <a name="billing-examples-with-azure-cosmos-db-free-tier-accounts"></a><a id="azure-free-tier"></a>Exemplos de cobrança com Azure Cosmos DB contas de camada gratuita

Com a camada gratuita do Azure Cosmos DB, você obtém gratuitamente os primeiros 400 RU/s e 5 GB de armazenamento em sua conta, aplicados no nível da conta. Qualquer RU/s e armazenamento além de 400 RU/s e 5 GB será cobrado de acordo com o preço regular da página de preços. Na lista, você não verá uma cobrança ou item de linha para os 5 GB e 400 RU/s gratuitos, somente os RUs e o armazenamento além do que é coberto pela camada gratuita. O 400 RU/s se aplica a qualquer tipo de taxa de transferência, autoescala e gravações de várias regiões provisionadas por RU/s.  

> [!NOTE]
> Azure Cosmos DB camada gratuita é diferente da conta gratuita do Azure. A conta gratuita do Azure oferece créditos e recursos do Azure gratuitamente por um período limitado. Ao usar Azure Cosmos DB como parte desta conta gratuita, você obtém armazenamento de 25 GB e 400 RU/s de taxa de transferência provisionada por 12 meses.

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Exemplo de cobrança - contêiner ou banco de dados com taxa de transferência provisionada
- Vamos supor que criamos um banco de dados ou contêiner em uma conta de camada gratuita com 400 RU/s e 5 GB de armazenamento.
- A fatura não mostrará nenhuma cobrança para esse recurso. O custo por hora e por mês será de US$ 0.
- Agora vamos supor que, na mesma conta, adicionamos outro banco de dados ou contêiner com 1000 RU/s e 10 GB de armazenamento.
- Agora a fatura mostrará uma cobrança pelos 1000 RU/s e 10 GB de armazenamento. 

### <a name="billing-example---container-with-autoscale-throughput"></a>Exemplo de cobrança - Contêiner com taxa de transferência de dimensionamento automático
- Vamos supor que, em uma conta de camada gratuita, criamos um contêiner com dimensionamento automático habilitado com 4000 RU/s, no máximo. Este recurso será dimensionado automaticamente entre 400 e 4000 RU/s. 
- Suponha que entre a hora 1 e a hora 10, o recurso esteja a 400 RU/s, no mínimo. Durante a hora 11, o recurso escala verticalmente até 1000 RU/s e, em seguida, volta para 400 RU/s dentro da hora.
- Entre as horas 1 a 10, você será cobrado US$ 0 pela taxa de transferência, pois as 400 RU/s foram cobertas pela camada gratuita. 
- Você será cobrado por um efetivo de 1000 RU/s a 400 RU/s = 600 RU/s na hora 11, pois esse é o RU/s mais alto na hora. Ou seja, 6 unidades de 100 RU/s para a hora. Portanto, o custo total de taxa de transferência para a hora será de 6 unidades x US$ 0,012 = US$ 0,072. 
- Após os primeiros 5 GB, qualquer armazenamento será cobrado em taxas de armazenamento normais. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Exemplo de cobrança – Várias regiões, única conta de região de gravação
- Vamos supor que em uma conta de camada gratuita, criamos um banco de dados ou contêiner com 1200 RU/s e 10 GB de armazenamento. Nós replicamos a conta para três regiões e temos uma única conta da região de gravação.
- Sem a camada gratuita, você seria cobrado por 3 x 1.200 RU/s = 3.600 RU/s e 3 x 10 GB = 30 GB de armazenamento no total.
- Após remover 400 RU/s e 5 GB de armazenamento, será cobrado um valor equivalente a 3.200 RU/s (32 unidades) de taxa de transferência provisionada na taxa de região de gravação única e 25 GB de armazenamento com o desconto por nível gratuito.
- O custo mensal para RU/s seria: 32 unidades x US$ 0,008 x 24 horas x 31 dias = US$ 190,46. O custo mensal para armazenamento seria: 25 GB x 0,25/GB = US$ 6,25. O custo total seria de US$ 190,46 + US$ 6,25 = US$ 196,71.
- Observação: se o preço unitário por RU/s ou por armazenamento for diferente nas regiões, a camada gratuita de 400 RU/s e 5 GB será refletida nas taxas da região onde a conta foi criada.

### <a name="billing-example---multi-region-account-with-multiple-write-regions"></a>Exemplo de cobrança – multiregião, conta com várias regiões de gravação

Este exemplo reflete os [preços para gravações de várias regiões](https://azure.microsoft.com/pricing/details/cosmos-db/) para contas criadas após 1º de dezembro de 2019. 
- Vamos supor que em uma conta de camada gratuita, criamos um banco de dados ou contêiner com 1200 RU/s e 10 GB de armazenamento. Nós replicamos a conta para três regiões e temos uma conta de várias regiões de gravação. 
- Sem a camada gratuita, você seria cobrado por 3 x 1.200 RU/s = 3.600 RU/s e 3 x 10 GB = 30 GB de armazenamento no total.
- Com o desconto por nível gratuito, após a remoção de 400 RU/s e 5 GB de armazenamento, será cobrado um valor por 3.200 RU/s (32 unidades) de taxa de transferência provisionada na taxa de várias regiões de gravação e 25 GB de armazenamento.
- O custo mensal para RU/s seria: 32 unidades x US$ 0,016 x 24 horas x 31 dias = US$ 380,93. O custo mensal para armazenamento seria: 25 GB x 0,25/GB = US$ 6,25. O custo total seria de US$ 380,93 + US$ 6,25 = US$ 387,18.

## <a name="proactively-estimating-your-monthly-bill"></a>Estimando proativamente sua fatura mensal  

Vamos considerar outro exemplo, em que você deseja proativamente estimar sua fatura antes do final do mês. É possível estimar sua fatura da seguinte maneira:

**Custo de armazenamento**

* Tamanho médio de registro (KB) = 1 
* Número de registros = 100 milhões 
* Armazenamento total (GB) = 100 
* Custo mensal por GB = $0.25 
* Custo mensal esperado para armazenamento = $25 

**Custo de taxa de transferência**

|Tipo de operação| Solicitações/s| Média RU/solicitação| RUs necessárias|
|----|----|----|----|
|Gravar| 100 | 5 | 500|
|Ler| 400| 1| 400|

Total de RU/s: 500 + 400 = 900 Custo por hora: 900/100 * US$ 0,008 = US$ 0,072 Custo mensal esperado pela taxa de transferência (considerando 31 dias): US$ 0,072 * 24 * 31 = US$ 53,57

**Custo mensal total**

Custo mensal total = custo mensal pelo armazenamento + custo mensal pela taxa de transferência Custo mensal total = US$ 25,00 + US$ 53,57 = US$ 78,57

*Os preços podem variar por região. Para obter os preços atualizados, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Cobrança com capacidade reservada do Azure Cosmos DB

A capacidade reservada do Azure Cosmos DB permite que você adquira taxa de transferência provisionada com antecedência (uma capacidade reservada, ou uma reserva) que pode ser aplicada a todos os bancos de dados e contêineres do Azure Cosmos (para qualquer API ou modelo de dados) em todas as regiões do Azure. Visto que o preço da taxa de transferência provisionada varia de acordo com a região, vale pensar na capacidade reservada como um crédito monetário adquirido com desconto, que pode ser utilizado na taxa de transferência provisionada pelo preço respectivo de cada região. Por exemplo, digamos que você tenha uma conta do Azure Cosmos com um único contêiner provisionado com 50 mil RU/s e replicou globalmente duas regiões: Leste dos EUA e Leste do Japão. Se você escolher a opção de pagamento conforme o uso, pagará:  

* No Leste dos EUA: por 50 mil RU/s à taxa de US$ 0,008 por 100 RU/s nessa região 

* No leste do Japão: por 50 mil RU/s à taxa de US$ 0,009 por 100 RU/s nessa região

Sua fatura total (sem capacidade reservada) seria (supondo-se 30 dias ou 720 horas): 

|**Região**| **Preço por hora por 100 RU/s**|**Unidades (RU/s)**|**Valor cobrado (por hora)**| **Valor cobrado (por mês)**|
|----|----|----|----|----|
|Leste dos EUA|US$ 0,008 |50 mil|US$ 4|US$ 2.880 |
|Leste do Japão|US$ 0,009 |50 mil| US$ 4,50 |US$ 3.240 |
|Total|||US$ 8,50|US$ 6.120 |

Vamos supor que você tenha comprado capacidade reservada. Você pode comprar capacidade reservada para 100 mil RU/s ao preço de US$ 56.064 por um ano (com 20% de desconto), ou US$ 6,40 por hora. Consulte os preços de capacidade reservada na [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Custo da taxa de transferência (pagamento conforme o uso): 100.000 RU/s/100 * US$ 0,008/hora * 8.760 horas em um ano = US$ 70.080 

* Custo de taxa de transferência (com capacidade reservada) US$ 70.080 com desconto de 20% = US$ 56.064 

O que você efetivamente comprou é um crédito de US$ 8 por hora, para 100 mil RU/s usando o preço de lista no Leste dos EUA, ao preço de US$ 6,40 por hora. Você pode consumir essa reserva pré-paga de taxa de transferência por hora na capacidade da taxa de transferência provisionada em qualquer região global do Azure de acordo com os respectivos valores da lista regional definidos para sua assinatura. Neste exemplo, em que você provisiona 50.000 RU/s cada no Leste dos EUA e no Leste do Japão, poderá consumir US$ 8,00 de taxa de transferência provisionada por hora e será cobrado pelo excedente de US$ 0,50 por hora (ou US$ 360 por mês). 

|**Região**| **Preço por hora por 100 RU/s**|**Unidades (RU/s)**| **Valor cobrado (por hora)**| **Valor cobrado (por mês)**|
|----|----|----|----|----|
|Leste dos EUA|US$ 0,008 |50 mil|US$ 4|US$ 2.880 |
|Leste do Japão|US$ 0,009 |50 mil| US$ 4,50 |US$ 3.240 |
|||Pago conforme o uso|US$ 8,50|US$ 6.120|
|Capacidade reservada comprada|US$ 0,0064 (20% de desconto) |Capacidade de 100 RU/s ou US$ 8 pré-adquirida |-US$ 8|-US$ 5.760 |
|Valor líquido|||US$ 0,50 |US$ 360 |

## <a name="next-steps"></a>Próximas etapas

A seguir, você poderá saber mais sobre a otimização de custos no Azure Cosmos DB nos seguintes artigos:

* Saiba mais sobre [Como o modelo de preços do Cosmos DB é econômico para os clientes](total-cost-ownership.md)
* Saiba mais sobre [Otimizando para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [Otimizando o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre [Otimizando o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [Otimizando o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [Otimizando o custo de consultas](./optimize-cost-reads-writes.md)
* Saiba mais sobre [Otimizando o custo de contas do Azure Cosmos em várias regiões](optimize-cost-regions.md)
