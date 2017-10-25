---
title: "Níveis de desempenho da API do DocumentDB | Microsoft Docs"
description: "Saiba como os níveis de desempenho da API do DocumentDB permitem reservar a produtividade por contêiner."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 7dc21c71-47e2-4e06-aa21-e84af52866f4
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6692d5b75954b2162862e6be7c2e39c63fa8408b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Desativando os níveis de desempenho S1, S2 e S3

> [!IMPORTANT] 
> Os níveis de desempenho S1, S2 e S3 abordados neste artigo estão sendo desativados e não estão mais disponíveis para novas contas da API do DocumentDB.
>

Este artigo fornece uma visão geral dos níveis de desempenho S1, S2 e S3 e analisa como as coleções que usam esses níveis de desempenho serão migradas para as coleções de partição única no final de 2017. Após ler este artigo, você poderá responder as perguntas a seguir:

- [Por que os níveis de desempenho S1, S2 e S3 estão sendo desativados?](#why-retired)
- [Como as coleções de partição única e as coleções particionadas se comparam com os níveis de desempenho S1, S2 e S3?](#compare)
- [O que é necessário fazer para garantir o acesso ininterrupto aos meus dados?](#uninterrupted-access)
- [Como a minha coleção mudará após a migração?](#collection-change)
- [Como minha cobrança mudará depois que eu migrar para as coleções de partição única?](#billing-change)
- [E se eu precisar de mais de 10 GB de armazenamento?](#more-storage-needed)
- [Posso mudar entre os níveis de desempenho S1, S2 e S3 antes da migração planejada?](#change-before)
- [Como saberei quando minha coleção migrou?](#when-migrated)
- [Como eu migro dos níveis de desempenho S1, S2 e S3 para as coleções de partição única sozinho?](#migrate-diy)
- [Como serei afetado se eu for um cliente EA?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Por que os níveis de desempenho S1, S2 e S3 estão sendo desativados?

Os níveis de desempenho S1, S2 e S3 não oferecem a flexibilidade que as coleções da API do DocumentDB oferecem. Com os níveis de desempenho S1, S2, S3, a produtividade e a capacidade de armazenamento eram predefinidas e não ofereciam elasticidade. Agora, o Azure Cosmos DB oferece a capacidade de personalizar a produtividade e o armazenamento, oferecendo muito mais flexibilidade em sua capacidade de dimensionar, conforme suas necessidades mudam.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Como as coleções de partição única e as coleções particionadas se comparam com os níveis de desempenho S1, S2 e S3?

A tabela a seguir compara as opções da taxa de transferência e armazenamento disponíveis nas coleções de partição única, coleções particionadas e níveis de desempenho S1, S2 e S3. Aqui está um exemplo para a região no Leste dos EUA 2:

|   |Coleção particionada|Coleção de partição única|S1|S2|S3|
|---|---|---|---|---|---|
|Taxa de transferência máxima|Ilimitado|10 K RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Taxa de transferência mínima|2.5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Armazenamento máximo|Ilimitado|10 GB|10 GB|10 GB|10 GB|
|Preço (mensal)|Taxa de transferência: $ 6/100 RU/s<br><br>Armazenamento: $ 0,25/GB|Taxa de transferência: $ 6/100 RU/s<br><br>Armazenamento: $ 0,25/GB|$ 25 dólares americanos|$ 50 dólares americanos|$ 100 dólares americanos|

Você é um cliente EA? Se for, consulte [Como serei afetado se eu for um cliente EA?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>O que é necessário fazer para garantir o acesso ininterrupto aos meus dados?

Nada. O Cosmos DB cuida da migração para você. Se você tiver uma coleção S1, S2 ou S3, a coleção atual será migrada para uma coleção de partição única no final de 2017. 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Como a minha coleção mudará após a migração?

Se você tiver uma coleção S1, será migrado para uma coleção de partição única com uma taxa de transferência de 400 RU/s. 400 RU/s é a taxa de transferência mais baixa disponível com as coleções de partição única. No entanto, o custo de 400 RU/s em uma coleção de partição única é aproximadamente igual ao que você estava pagando com sua coleção S1 e 250 RU/s – assim, você não está pagando por 150 RU/s extras disponíveis.

Se você tiver uma coleção S2, será migrado para uma coleção de partição única com 1 K RU/s. Você não verá nenhuma alteração no nível da taxa de transferência.

Se tiver uma coleção S3, será migrado para uma coleção de partição única com 2.5 K RU/s. Você não verá nenhuma alteração no nível da taxa de transferência.

Em cada um desses casos, após a migração de sua coleção, você poderá personalizar o nível da taxa de transferência, aumentar ou reduzir conforme o necessário para fornecer um acesso de baixa latência para os usuários. Para alterar o nível de produtividade após a migração de sua coleção, basta abrir sua conta do Cosmos DB no portal do Azure, clicar em Escala, escolher sua coleção e, em seguida, ajustar o nível de produtividade, conforme mostrado na seguinte captura de tela:

![Como dimensionar a taxa de transferência no portal do Azure](./media/performance-levels/portal-scale-throughput.png)

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-im-migrated-to-the-single-partition-collections"></a>Como minha cobrança mudará depois que eu migrar para as coleções de partição única?

Supondo que você tem 10 coleções S1, 1 GB de armazenamento para cada uma, na região Leste dos EUA, e migra essas 10 coleções S1 para 10 coleções de partição única em 400 RU/s (o nível mínimo). Se você mantiver as 10 coleções de partição única por um mês inteiro, sua fatura será semelhante ao seguinte:

![Como o preço de S1 para 10 coleções se compara a 10 coleções usando o preço para uma coleção de partição única](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>E se eu precisar de mais de 10 GB de armazenamento?

Se você tiver uma coleção com um nível de desempenho S1, S2 ou S3 ou uma coleção de partição única, todas com 10 GB de armazenamento disponível, poderá usar a ferramenta de Migração de Dados do Cosmos DB para migrar seus dados para uma coleção particionada com um armazenamento praticamente ilimitado. Para obter informações sobre os benefícios de uma coleção particionada, consulte [Particionamento e escala no Azure Cosmos DB](documentdb-partition-data.md). Para obter informações sobre como migrar seu S1, S2, S3 ou coleção de partição única para uma coleção particionada, consulte [Migrando da partição única para coleções particionadas](documentdb-partition-data.md#migrating-from-single-partition). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Posso mudar entre os níveis de desempenho S1, S2 e S3 antes da migração planejada?

Somente as contas existentes com os desempenhos S1, S2 e S3 conseguirão mudar e alterar as camadas do nível de desempenho no portal ou por meio da programação. Se você mudar de S1, S3 ou S3 para uma coleção de partição único, não será possível retornar aos níveis de desempenho S1, S2 ou S3.

<a name="when-migrated"></a>

## <a name="how-will-i-know-when-my-collection-has-migrated"></a>Como saberei quando minha coleção migrou?

A migração ocorrerá no final de 2017. Se você tiver uma coleção que usa os níveis de desempenho S1, S2 ou S3, a equipe do Cosmos DB o contatará por email antes da migração. Depois que a migração for concluída, o portal do Azure mostrará que a sua coleção usa o preço Standard.

![Como confirmar se sua coleção migrou para a tipo de preço Standard](./media/performance-levels/portal-standard-pricing-applied.png)

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Como eu migro dos níveis de desempenho S1, S2 e S3 para as coleções de partição única sozinho?

Você pode migrar dos níveis de desempenho S1, S2 e S3 para coleções de partição única usando o portal do Azure ou programaticamente. Faça isso por conta própria antes da migração planejada para se beneficiar das opções flexíveis de produtividade disponíveis com as coleções de partição única ou migraremos suas coleções para você no final de 2017.

**Para migrar para coleções de partição única usando o portal do Azure**

1. No [**portal do Azure**](https://portal.azure.com), clique em **Azure Cosmos DB** e, em seguida, selecione a conta do Cosmos DB a ser modificada. 
 
    Se o **Azure Cosmos DB** não estiver na barra de atalhos, clique em >, role até **Bancos de dados**, selecione **Azure Cosmos DB** e, em seguida, selecione a conta do DocumentDB.  

2. No menu de recursos, em **Contêineres**, clique em **Escala**, selecione a coleção a ser modificada na lista suspensa e, em seguida, clique em **Tipo de Preço**. As contas que usam a taxa de transferência predefinida têm um tipo de preço de S1, S2 ou S3.  Na folha **Escolha o tipo de preço**, clique em **Standard** para alterar a taxa de transferência definida pelo usuário e, em seguida, clique em **Selecionar** para salvar as alterações.

    ![Captura de tela da folha Configurações, mostrando em que lugar alterar o valor da taxa de transferência](./media/performance-levels/change-performance-set-thoughput.png)

3. De volta na folha **Escala**, o **Tipo de Preço** foi alterado para **Standard** e a caixa **Produtividade (RU/s)** é exibida com um valor padrão de 400. Defina a produtividade entre 400 e 10.000 [Unidades de Solicitação](request-units.md)/segundo (RUS/s). A **Fatura mensal estimada** na parte inferior da página é atualizada automaticamente para fornecer uma estimativa do custo mensal. 

    >[!IMPORTANT] 
    > Depois de salvar as alterações e ir para o tipo de preço Standard, você não poderá voltar para os níveis de desempenho S1, S2 ou S3.

4. Clique em **Salvar** para salvar as alterações.

    Se determinar que precisa de uma taxa de transferência maior (mais de 10.000 RU/s) ou mais armazenamento (mais de 10 GB), você poderá criar uma coleção particionada. Para migrar uma coleção de partição única para uma coleção particionada, consulte [Migrando da partição única para coleções particionadas](documentdb-partition-data.md#migrating-from-single-partition).

    > [!NOTE]
    > Mudar de S1, S2 ou S3 para o Standard pode levar até 2 minutos.
    > 
    > 

**Para migrar para coleções de partição única usando o .NET SDK**

Outra opção para alterar os níveis de desempenho de suas coleções é por meio de nossos SDKs. Esta seção aborda apenas a alteração do nível de desempenho da coleção usando nossa [API .NET do DocumentDB](documentdb-sdk-dotnet.md), mas o processo é semelhante para nossos outros SDKs.

Aqui está um trecho de código para mudar a taxa de transferência da coleção para 5.000 unidades de solicitação por segundo:
    
```C#
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Visite o [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) para exibir exemplos adicionais e saber mais sobre os métodos oferecidos:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Como serei afetado se eu for um cliente EA?

Os clientes EA terão o preço protegido até o final do contrato atual.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os preços e como gerenciar dados com o Azure Cosmos DB, conheça estes recursos:

1.  [Particionando dados no Cosmos DB](documentdb-partition-data.md). Compreenda a diferença entre contêineres de partição única e contêineres particionados, além de obter dicas de como implementar uma estratégia de particionamento para uma escala perfeita.
2.  [Preços do Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Saiba mais sobre o custo de provisionar a taxa de transferência e consumir o armazenamento.
3.  [Unidades de solicitação](request-units.md). Compreenda o consumo da taxa de transferência para os diferentes tipos de operação, por exemplo, Leitura, Gravação e Consulta.
