---
title: Cobrança limitada para aplicativos gerenciados usando o serviço de medição do Marketplace | Azure Marketplace
description: Esta documentação é um guia para ISVs que publicam aplicativos do Azure com modelos de cobrança flexíveis.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: d015cec30e516541b50c2acfac38fad898965e1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96436344"
---
# <a name="managed-application-metered-billing"></a>Cobrança limitada do aplicativo gerenciado 

Com o serviço de medição do Marketplace, você pode criar planos de aplicativos gerenciados para Aplicativo Azure ofertas que são cobradas de acordo com unidades não padrão. Antes de publicar essa oferta, você define as dimensões de cobrança, como largura de banda, tíquetes ou emails processados. Em seguida, os clientes pagam de acordo com o consumo dessas dimensões.  Seu sistema informará à Microsoft por meio da API de serviço de medição do Marketplace de eventos faturáveis conforme eles ocorrerem.

## <a name="prerequisites-for-metered-billing"></a>Pré-requisitos para cobrança limitada

Para que um plano de aplicativo gerenciado use a cobrança limitada, ele deve:

* Atenda a todos os requisitos de oferta, conforme descrito em [criar uma oferta de aplicativo do Azure](../create-new-azure-apps-offer.md).
* Configure os **preços** para cobrar os clientes pelo custo por mês para seu serviço. O preço pode ser zero se você não quiser cobrar uma taxa fixa e, em vez disso, confiar inteiramente na cobrança limitada.
* Defina as **dimensões de cobrança** para os eventos de medição que o cliente pagará por cima da taxa fixa.
* Integre-se com as [APIs do serviço de medição do Marketplace](./marketplace-metering-service-apis.md) para informar à Microsoft sobre eventos faturáveis.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Como a cobrança limitada se encaixa com os preços

Quando se trata de definir a oferta junto com seus modelos de preços, é importante entender a hierarquia da oferta.

* Cada oferta de Aplicativo Azure pode ter um modelo de solução ou planos de aplicativo gerenciados.
* A cobrança limitada é implementada somente com planos de aplicativos gerenciados.
* Cada plano de aplicativo gerenciado tem um modelo de preços associado a ele. 
* O modelo de preços tem uma taxa recorrente mensal, que pode ser definida como $0.
* Além da taxa recorrente, o plano também pode incluir dimensões opcionais usadas para cobrar os clientes pelo uso não incluído na taxa fixa. Cada dimensão representa uma unidade Faturável que seu serviço irá comunicar com a Microsoft usando a [API do serviço de medição do Marketplace](marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Oferta de exemplo

Por exemplo, a contoso é um Publicador com um serviço de aplicativo gerenciado chamado contoso Analytics (CoA). O CoA permite que os clientes analisem grandes quantidades de dados para relatórios e data warehousing. A Contoso está registrada como um editor no Partner Center para o programa comercial do Marketplace publicar ofertas para clientes do Azure. Há dois planos associados ao CoA, descritos abaixo:

* Plano base
    * Analisar 100 GB e gerar relatórios de 100 para $0/mês
    * Além dos 100 GB, pague $10 para cada 1 GB
    * Além dos relatórios 100, pague $1 para cada relatório
* Plano Premium
    * Analise 1000 GB e gere relatórios de 1000 para US $350/mês
    * Além dos 1000 GB, pague $100 para cada 1 TB
    * Além dos relatórios 1000, pague $0.05 para cada relatório

Um cliente do Azure que está assinando o serviço CoA pode analisar e gerar relatórios por mês com base no plano selecionado. A contoso mede o uso até a quantidade incluída sem enviar nenhum evento de uso à Microsoft. Quando os clientes consomem mais do que a quantidade incluída, eles não precisam alterar os planos ou fazer algo diferente. A contoso medirá o excedente além da quantidade incluída e começará a emitir eventos de uso para a Microsoft para uso adicional usando a [API do serviço de medição do Marketplace](./marketplace-metering-service-apis.md). A Microsoft, por sua vez, cobrará o cliente pelo uso adicional, conforme especificado pelo Publicador.

## <a name="billing-dimensions"></a>Dimensões de cobrança

As dimensões de cobrança são usadas para se comunicar com o cliente sobre como eles serão cobrados pelo uso do software.  Essas dimensões também são usadas para comunicar eventos de uso à Microsoft. Eles são definidos da seguinte maneira:

* **Identificador de dimensão**: o identificador imutável referenciado ao emitir eventos de uso.
* **Nome da dimensão**: o nome de exibição associado à dimensão, por exemplo, "mensagens de texto enviadas".
* **Unidade de medida**: a descrição da unidade de cobrança, por exemplo "mensagem por texto" ou "por email 100".
* **Preço por unidade**: o preço de uma unidade da dimensão.
* **Quantidade incluída para o termo mensal**: a quantidade de dimensões incluída por mês para clientes que pagam a taxa mensal recorrente, deve ser um número inteiro.

As dimensões de cobrança são compartilhadas entre todos os planos para uma oferta. Alguns atributos se aplicam à dimensão em todos os planos e outros atributos são específicos do plano.

Os atributos, que definem a própria dimensão, são compartilhados entre todos os planos para uma oferta. Antes de publicar a oferta, uma alteração feita nesses atributos do contexto de qualquer plano afetará a definição da dimensão em todos os planos. Depois de publicar a oferta, esses atributos não serão mais editáveis. Os atributos são:

* Identificador
* Nome
* Unidade de medida

Os outros atributos de uma dimensão são específicos para cada plano e podem ter valores diferentes do plano para o plano.  Antes de publicar o plano, você pode editar esses valores e somente esse plano será afetado. Depois de publicar o plano, esses atributos não serão mais editáveis. Os atributos são:

* Preço por unidade
* Quantidade incluída para clientes mensais 
* Quantidade incluída para clientes anuais 

As dimensões também têm dois conceitos especiais, "Enabled" e "Infinite":

* **Habilitado** indica que este plano participa desta dimensão.  Talvez você queira deixar essa opção desmarcada se estiver criando um novo plano que não envia eventos de uso com base nessa dimensão. Além disso, todas as novas dimensões adicionadas depois que um plano foi publicado pela primeira vez serão exibidas como "não habilitado" no plano já publicado.  Uma dimensão desabilitada não aparecerá em nenhuma lista de dimensões para um plano visto pelos clientes.
* **Infinito**, representado pelo símbolo de infinito "∞", indica que este plano participa dessa dimensão, sem uso medido em relação a essa dimensão. Se você quiser indicar aos clientes que a funcionalidade representada por essa dimensão está incluída no plano, mas sem limite de uso.  Uma dimensão com uso infinito será exibida em listas de dimensões para um plano visto pelos clientes.  Esse plano nunca incorrerá em um encargo.

>[!Note] 
>Os cenários a seguir têm suporte explícito:  <br> -Você pode adicionar uma nova dimensão a um novo plano.  A nova dimensão não será habilitada para nenhum plano já publicado. <br> -Você pode publicar um plano com uma taxa mensal fixa e sem nenhuma dimensão e, em seguida, adicionar um novo plano e configurar uma nova dimensão para esse plano. A nova dimensão não será habilitada para planos já publicados.

## <a name="constraints"></a>Restrições

### <a name="locking-behavior"></a>Comportamento de bloqueio

Uma dimensão usada com o serviço de medição do Marketplace representa uma compreensão de como um cliente pagará pelo serviço.  Todos os detalhes de uma dimensão não são mais editáveis quando uma oferta é publicada.  Antes de publicar sua oferta, é importante que as dimensões tenham sido totalmente definidas.

Depois que uma oferta é publicada com uma dimensão, os detalhes de nível de oferta para essa dimensão não podem mais ser alterados:

* Identificador
* Nome
* Unidade de medida

Depois que um plano é publicado, os detalhes de nível de plano não podem mais ser alterados:

* Preço por unidade
* Quantidade incluída para o termo mensal
* Se a dimensão está habilitada para o plano

>[!Note]
>A cobrança limitada usando o serviço de medição do Marketplace ainda não tem suporte na nuvem do Azure governamental.

### <a name="upper-limits"></a>Limites superiores

O número máximo de dimensões que podem ser configuradas para uma única oferta é de 30 dimensões exclusivas.

## <a name="get-support"></a>Obtenha suporte

Se você tiver um dos seguintes problemas, poderá abrir um tíquete de suporte.

* Problemas técnicos com a API do serviço de medição do Marketplace.
* Um problema que precisa ser escalonado devido a um erro ou bug no seu lado (por exemplo, evento de uso incorreto).
* Quaisquer outros problemas relacionados à cobrança limitada.

Siga a instrução em [suporte para o programa do Marketplace comercial no Partner Center](../support.md) para entender as opções de suporte do Publicador e abrir o tíquete de suporte com a Microsoft.

## <a name="next-steps"></a>Próximas etapas

- Consulte [APIs do serviço de medição do Marketplace](./marketplace-metering-service-apis.md) para obter mais informações.
