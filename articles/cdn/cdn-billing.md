---
title: Noções básicas sobre a cobrança da Rede de Distribuição de Conteúdo do Azure
description: Essas Perguntas Frequentes descrevem como a cobrança do Rede de Distribuição de Conteúdo do Azure funciona.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: magattus
ms.openlocfilehash: 2fd3d2f8fbc98d8c7b19cbcc365748cc088d76fd
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594089"
---
# <a name="understanding-azure-cdn-billing"></a>Noções básicas sobre a cobrança da rede de distribuição de conteúdo do Azure

Essas Perguntas Frequentes descrevem a estrutura de cobrança para o conteúdo hospedado pela Rede de Distribuição de Conteúdo (CDN) do Azure.

## <a name="what-is-a-billing-region"></a>O que é uma região de cobrança?
Uma região de cobrança é uma área geográfica usada para determinar qual taxa é cobrada para a entrega de objetos da CDN do Azure. As zonas de cobrança atuais e suas regiões são as seguintes:

- Zona 1: América do Norte, Europa, Oriente Médio e África

- Zona 2: Pacífico Asiático (incluindo Japão)

- Zona 3: América do Sul

- Zona 4: Austrália e Nova Zelândia

- Zona 5: Índia

Para obter informações sobre regiões de ponto de presença (POP), consulte [Locais de POP da CDN do Azure por região](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Por exemplo, um POP localizado no México está na região da América do Norte e, portanto, é incluído na zona 1. 

Para obter informações sobre preços da CDN do Azure, consulte [Preços de Rede de Distribuição de Conteúdo](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Como os encargos de entrega são calculados por região?
A região de cobrança da CDN do Azure é baseada no local do servidor de origem que fornece o conteúdo para o usuário final. O destino (local físico) do cliente não é considerado a região de cobrança.

Por exemplo, se um usuário localizado no México emite uma solicitação e ela é atendida por um servidor localizado em um POP dos Estados Unidos devido a condições de emparelhamento ou tráfego, a região de cobrança será dos Estados Unidos.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>O que é uma transação faturável da CDN do Azure?
Qualquer solicitação de HTTP(S) que termina na CDN é um evento faturável, o que inclui todos os tipos de resposta: êxito, falha ou outro. No entanto, diferentes respostas podem gerar quantidades diferentes de tráfego. Por exemplo, *304 Não modificado* e outras respostas somente cabeçalho geram pouco tráfego porque são uma pequena resposta no cabeçalho; de forma semelhante, as respostas de erro (por exemplo, *404 Não Encontrado*) são faturáveis, mas incorrem em um pequeno custo devido ao pequeno conteúdo da resposta.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Quais outros custos do Azure estão associados ao uso da CDN do Azure?
Usar a CDN do Azure também incorre em alguns encargos de uso sobre os serviços usados como a origem para os objetos. Normalmente, esses custos são uma pequena fração do custo geral de uso da CDN.

Se você estiver usando o armazenamento de BLOBs do Azure como a origem para o seu conteúdo, você também incorre nos seguintes encargos de armazenamento para preenchimentos de cache:

- Uso real em GB: o armazenamento real dos seus objetos de origem.

- Transferências em GB: a quantidade de dados transferidos para preencher os caches da CDN.

- Transações: conforme o necessário para preencher o cache.

Para saber mais sobre a cobrança do Armazenamento do Microsoft Azure, confira [Noções básicas sobre cobrança no armazenamento do Azure – largura de banda, transações e capacidade](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Se estiver usando *entrega de serviço hospedado*, incorrerá em cobranças da seguinte maneira:

- Tempo de computação do Azure: as instâncias de computação que agem como a origem.

- Transferência de computação do Azure: as transferências de dados de instâncias de computação para preencher os caches da CDN do Azure.

Se o cliente usa solicitações de intervalo de bytes (independentemente do serviço de origem), as seguintes considerações se aplicam:

- Uma *solicitação de intervalo de bytes* é uma transação faturável na CDN. Quando um cliente emite uma solicitação de intervalo de bytes, ela é para um subconjunto (intervalo) do objeto. A CDN responde com apenas uma parte parcial do conteúdo que é solicitado. Essa resposta parcial é uma transação faturável e a quantidade de transferência está limitada ao tamanho da resposta de intervalo (mais cabeçalhos).

- Quando chega uma solicitação para apenas uma parte de um objeto (especificando um cabeçalho de intervalo de bytes), a CDN pode buscar todo o objeto em seu cache. Como resultado, mesmo que a transação faturável da CDN seja uma resposta parcial, a transação faturável da origem poderá envolver o tamanho total do objeto.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Quantas atividades de transferência ocorrem para dar suporte ao cache?
Cada vez que um POP da CDN precisa preencher seu cache, ele faz uma solicitação para a origem para o objeto que está sendo armazenado em cache. Como resultado, a origem incorre em uma transação faturável em todos os erros de cache. O número de erros de cache depende de vários fatores:

- Quão armazenável em cache o conteúdo é: se o conteúdo tem alta TTL (vida útil)/valor de expiração e é acessado com frequência para que fique popular em cache, a vasta maioria da carga é manipulada pela CDN. Uma taxa de acertos de cache boa típica é bem acima de 90%, o que significa que menos de 10% das solicitações de clientes precisam retornar para a origem, para um objeto ou um erro de cache de atualização.

- Quantos nós precisam carregar o objeto: cada vez que um nó carrega um objeto de origem, ele incorre em uma transação faturável. Como resultado, mais conteúdo global (acessado a partir de outros nós) resulta em transações mais faturáveis.

- Influência da TTL: uma TTL superior para um objeto significa que ele precisa ser obtido da origem com menos frequência. Isso também significa que os clientes, como navegadores, podem armazenar em cache o objeto mais longo, o que pode reduzir as transações para a CDN.

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Como gerencio meus custos com mais eficiência?
Defina a vida útil mais longa possível em seu conteúdo. 
