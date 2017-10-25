---
title: "Escolher um tipo de preço ou SKU para o Azure Search | Microsoft Docs"
description: "A Pesquisa do Azure pode ser provisionada nestes SKUs: Gratuito, Básico e Standard, sendo que o Standard está disponível em várias configurações de recursos e níveis de capacidade."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 8d4b7bca-02a5-43ee-b3f8-03551dfb32fd
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/24/2016
ms.author: heidist
ms.openlocfilehash: f9f3a7b2369818791ffac1c8eeccef45216c2ff0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Escolher um tipo de preço ou SKU para a Pesquisa do Azure
No Azure Search, um [serviço é provisionado](search-create-service-portal.md) em um tipo de preço específico ou SKU. As opções incluem: **Gratuito**, **Básico** ou **Standard**, sendo que **Standard** está disponível em várias configurações e capacidades.

A finalidade deste artigo é ajudá-lo a escolher uma camada. Se a capacidade de uma camada for muito baixa, você precisará provisionar um novo serviço na camada superior e recarregar os índices. Não há nenhuma atualização in-loco do mesmo serviço de uma SKU para outra.

> [!NOTE]
> Depois de escolher uma camada e [provisionar um serviço de pesquisa](search-create-service-portal.md), você pode aumentar a réplica e as contagens de partições dentro do serviço. Para obter orientação, veja [Dimensionar os níveis de recursos para cargas de trabalho de consulta e indexação](search-capacity-planning.md).
>
>

## <a name="how-to-approach-a-pricing-tier-decision"></a>Como abordar uma decisão de tipo de preço
No Azure Search, a camada determina a capacidade, não a disponibilidade do recurso. Em geral, os recursos estão disponíveis em todas as camadas, incluindo recursos de versão prévia. A única exceção é que não há suporte para indexadores no S3 HD.

> [!TIP]
> Recomendamos que você sempre forneça um serviço **Gratuito** (um por assinatura, sem prazo de expiração) a fim de tê-lo prontamente disponível para projetos leves. Use o serviço **Gratuito** para teste e avaliação; crie um segundo serviço faturável para cargas maiores de trabalho de teste ou produção no tipo **Básico** ou **Standard**.
>
>

A capacidade e os custos de execução do serviço andam de lado a lado. As informações neste artigo podem ajudá-lo a decidir qual SKU fornece o equilíbrio correto, mas para que possa ser útil, você precisa ter estimativas aproximadas de pelo menos os seguintes itens:

* Número e tamanho dos índices que planeja criar
* Número e tamanho dos documentos a carregar
* Alguma ideia de volume de consulta, em termos de QPS (consultas por segundo)

Número e tamanho são importantes porque os limites máximos são atingidos com um limite rígido na contagem de índices ou documentos em um serviço ou nos recursos (armazenamento ou réplicas) usado pelo serviço. O limite real de seu serviço é aquele consumido primeiro: recursos ou objetos.

Com as estimativas em mãos, as etapas a seguir devem simplificar o processo:

* **Etapa 1** Analisar as descrições de SKU abaixo para saber mais sobre as opções disponíveis.
* **Etapa 2** Responder às perguntas abaixo para chegar a uma decisão preliminar.
* **Etapa 3** Finalizar sua decisão revisando os limites físicos de armazenamento e preços.

## <a name="sku-descriptions"></a>Descrições do SKU
A tabela a seguir fornece descrições de cada camada.

| Camada | Principais cenários |
| --- | --- |
| **Gratuito** |Um serviço compartilhado, sem custo adicional, usado para avaliação, investigação ou cargas de trabalho pequenas. Por ser compartilhado com outros assinantes, a taxa de transferência e a indexação da consulta variam dependendo de quem mais está usando o serviço. Capacidade pequena (50 MB ou três índices com até 10 mil documentos cada). |
| **Básico** |Cargas de trabalho de produção pequenas em hardware dedicado. Altamente disponível. Capacidade de até três réplicas e uma partição (2 GB). |
| **S1** |Standard 1 oferece suporte a combinações flexíveis de partições (12) e réplicas (12), usadas para as cargas de trabalho de produção média no hardware dedicado. Você pode alocar partições e réplicas em combinações com suporte de um número máximo de 36 unidades de pesquisa faturáveis. Nesse nível, as partições têm 25 GB cada e a taxa de QPS é aproximadamente 15 consultas por segundo. |
| **S2** |Standard 2 executa maiores cargas de trabalho de produção, usando as mesmas 36 unidades de pesquisa do S1, mas com réplicas e partições maiores. Nesse nível, as partições têm 100 GB e a taxa de QPS é cerca de 60 consultas por segundo. |
| **S3** |Standard 3 executa proporcionalmente maiores cargas de trabalho de produção em sistemas de alta capacidade, em configurações de até 12 partições ou 12 réplicas em 36 unidades de pesquisa. Nesse nível, as partições têm 200 GB e a taxa de QPS é maior que 60 consultas por segundo. |
| **S3 HD** |A Alta Densidade Standard 3 destina-se a um grande número de índices menores. Você pode ter até 3 partições, de 200 GB cada. QPS de mais de 60 consultas por segundo. |

> [!NOTE]
> Os limites máximos de partição e de réplica são cobrados como unidades de pesquisa (máximo de 36 unidades por serviço), que impõe um limite eficaz inferior ao limite máximo em valor nominal. Por exemplo, para usar o máximo de 12 réplicas, você pode ter no máximo três partições (12 * 3 = 36 unidades). Da mesma forma, para usar o máximo de partições, reduza as réplicas para três. Confira [Dimensionar os níveis de recursos para cargas de trabalho de consulta e indexação na Pesquisa do Azure](search-capacity-planning.md) para obter um gráfico de combinações permitidas.
>
>

## <a name="review-limits-per-tier"></a>Revisar os limites por tipo
O gráfico a seguir é um subconjunto dos limites do [Limites de serviço na Pesquisa do Azure](search-limits-quotas-capacity.md). Ele lista os fatores com maior probabilidade de afetar uma decisão de SKU. Você pode consultar esse gráfico ao revisar as perguntas a seguir.

| Recurso | Grátis | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Contrato de nível de serviço (SLA) |Não <sup>1</sup> |Sim |Sim |Sim |Sim |Sim |
| Limites de índice |3 |5 |50 |200 |200 |1000 <sup>2</sup> |
| Limites do documento |10.000 no total |1 milhão por serviço |15 milhões por partição |60 milhões por partição |120 milhões por partição |1 milhões por índice |
| Máximo de partições |N/D |1 |12 |12 |12 |3 <sup>2</sup> |
| Tamanho da partição |Total de 50 MB |2 GB por serviço |25 GB por partição |100 GB por partição (até um máximo de 1,2 TB por serviço) |200 GB por partição (até um máximo de 2,4 TB por serviço) |200 GB (até um máximo de 600 GB por serviço) |
| Máximo de réplicas |N/D |3 |12 |12 |12 |12 |
| Consultas por segundo |N/D |~3 por réplica |~15 por réplica |~60 por réplica |>60 por réplica |>60 por réplica |

<sup>1</sup> Os recursos de camada gratuita e de versão prévia não vêm com SLAs (contratos de nível de serviço). Para todas as camadas faturáveis, os SLAs entram em vigor quando você provisiona redundância suficiente para o serviço. Duas ou mais réplicas são necessárias para o SLA de consulta (leitura). Três ou mais réplicas são necessárias para consulta e indexação do SLA (leitura-gravação). O número de partições não é uma consideração de SLA. 

<sup>2</sup> S3 e S3 HD contam com infraestrutura de alta capacidade idênticas, mas cada um deles atinge seu limite máximo de maneiras diferentes. O S3 tem como alvo um número menor de índices muito grandes. Como tal, seu limite máximo está vinculado a recursos (2,4 TB para cada serviço). O S3 HD destina-se um grande número de índices muito pequenos. Com 1.000 índices, o S3 HD atinge os limites na forma de restrições de índice. Se você for um cliente do S3 HD que requer mais de 1.000 índices, entre em contato com o Suporte da Microsoft para obter informações sobre como proceder.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Eliminar SKUs que não atendam aos requisitos
As perguntas a seguir podem ajudar você a chegar a uma decisão certa sobre o SKU para sua carga de trabalho.

1. Você tem requisitos de **SLA (Contrato de Nível de Serviço)** ? Você pode usar qualquer camada faturável (plano Básico em diante), mas você deve configurar seu serviço para redundância. Duas ou mais réplicas são necessárias para o SLA de consulta (leitura). Três ou mais réplicas são necessárias para consulta e indexação do SLA (leitura-gravação). O número de partições não é uma consideração de SLA.
2. **De quantos índices** você precisa? Uma das maiores variáveis que influenciam uma decisão de SKU é o número de índices com suporte de cada SKU. O suporte aos índices está em níveis muito diferentes nos tipos de preços inferiores. Os requisitos do número de índices pode ser um determinante principal de uma decisão do SKU.
3. **Quantos documentos** serão carregados em cada índice? O número e o tamanho dos documentos determinará o tamanho final do índice. Supondo que você possa fazer uma estimativa do tamanho projetado do índice, será possível comparar esse número com o tamanho da partição por SKU, além do número de partições necessárias para armazenar um índice desse tamanho.
4. **Qual é a carga de consulta esperada**? Após definir os requisitos de armazenamento, considere as cargas de trabalho de consulta. O S2 e os dois SKUs S3 oferecem um rendimento quase equivalente, mas os requisitos de SLA descartarão quaisquer SKUs em fase preview.
5. Se você estiver considerando a camada S2 ou S3, determine se você precisa de [indexadores](search-indexer-overview.md). Indexadores ainda não estão disponíveis para a camada do S3 HD. A abordagem alternativa é usar um modelo de push para atualizações de índice, em que você escreve o código do aplicativo para enviar por push um conjunto de dados para um índice.

A maioria dos clientes pode incluir ou descartar um SKU específico com base em suas respostas às perguntas acima. Se você ainda não tiver certeza de qual SKU acompanhar, poste perguntas nos fóruns do MSDN ou StackOverflow, ou entre em contato com o suporte do Azure para obter mais orientações.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>Validação de decisão: o SKU oferece armazenamento e QPS suficientes?
Como uma última etapa, reveja a [página de preços](https://azure.microsoft.com/pricing/details/search/) e as [seções por serviço e por índice em Limites de serviço](search-limits-quotas-capacity.md) para verificar suas estimativas em relação aos limites de serviço e assinatura.

Se os requisitos de armazenamento ou o preço estiverem fora dos limites, convém refatorar as cargas de trabalho entre vários serviços menores (por exemplo). Em um nível mais granular, você pode recriar os índices com um tamanho menor ou usar filtros para fazer consultas mais eficientes.

> [!NOTE]
> Os requisitos de armazenamento podem ser excessivos se os documentos contiverem dados estranhos. O ideal é que os documentos contenham somente metadados ou dados pesquisáveis. Dados binários não são pesquisáveis e devem ser armazenados separadamente (talvez em um armazenamento de blobs ou tabela do Azure) com um campo no índice para manter uma URL de referência aos dados externos. O tamanho máximo de um documento individual é de 16 MB (ou menos, se você estiver carregando em massa vários documentos em uma solicitação). Confira [Limites de serviço na Pesquisa do Azure](search-limits-quotas-capacity.md) para saber mais.
>
>

## <a name="next-step"></a>Próxima etapa
Quando você souber qual SKU é adequado, prossiga com estas etapas:

* [Criar um serviço de pesquisa no portal](search-create-service-portal.md)
* [Alterar a alocação de partições e réplicas para dimensionar seu serviço](search-capacity-planning.md)
