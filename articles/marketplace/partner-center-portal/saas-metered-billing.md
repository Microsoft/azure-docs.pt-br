---
title: Cobrança limitada para ofertas de SaaS usando o serviço de medição do Microsoft Commercial Marketplace
description: Saiba mais sobre modelos de cobrança flexível para ofertas de SaaS usando o serviço de medição do Marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 9f72d54fda8f66c2fce35f0520b51406aa276bb0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892746"
---
# <a name="metered-billing-for-saas-using-the-commercial-marketplace-metering-service"></a>Cobrança limitada para SaaS usando o serviço de medição do Marketplace comercial

Com o serviço de medição do Marketplace comercial, você pode criar ofertas de SaaS (software como serviço) que são cobradas de acordo com as unidades não padrão. Antes de publicar uma oferta de SaaS no Marketplace comercial, você define as dimensões de cobrança, como largura de banda, tíquetes ou emails processados.  Em seguida, os clientes pagam de acordo com o consumo dessas dimensões, com seu sistema informando a Microsoft por meio da API de serviço de medição do Marketplace comercial de eventos faturáveis à medida que elas ocorrem.  

## <a name="prerequisites-for-metered-billing"></a>Pré-requisitos para cobrança limitada

Para uma oferta de SaaS para usar a cobrança limitada, ela deve primeiro:

- Atenda a todos os requisitos de oferta para uma oferta de [Venda por meio da Microsoft](../plan-saas-offer.md#listing-options) , conforme descrito em [criar uma oferta de SaaS no Marketplace comercial](../create-new-saas-offer.md).
- Integre-se com as [APIs de preenchimento de SaaS](./pc-saas-fulfillment-api-v2.md) para que os clientes provisionem e se conectem à sua oferta.  
- Ser configurado para o modelo de preços de **taxa fixa** ao cobrar clientes para seu serviço.  Dimensões são uma extensão opcional para o modelo de preços de taxa simples. 

Em seguida, a oferta de SaaS pode ser integrada às [APIs do serviço de medição do Marketplace comercial](./marketplace-metering-service-apis.md) para informar a Microsoft de eventos faturáveis.

>[!Note]
>O serviço de medição do Marketplace está disponível somente para o modelo de cobrança de taxa simples e não se aplica ao modelo de cobrança por usuário.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Como a cobrança limitada se encaixa com os preços

Compreender a hierarquia de oferta é importante, quando se trata de definir a oferta junto com seus modelos de preços.

- Cada oferta de SaaS é configurada para vender pela Microsoft ou não.  Depois que uma oferta é publicada, essa opção não pode ser alterada.
- Cada oferta de SaaS, configurada para vender pela Microsoft, pode ter um ou mais planos.  Um usuário assina a oferta de SaaS, mas é adquirida pela Microsoft dentro do contexto de um plano.
- Cada plano tem um modelo de preços associado a ele: **taxa fixa** ou **por usuário** . Todos os planos em uma oferta devem ser associados ao mesmo modelo de preços. Por exemplo, não pode haver uma oferta que tenha planos para um modelo de preços de taxa fixa e outro sendo o modelo de preço por usuário.
- Dentro de cada plano configurado para um modelo de cobrança de taxa simples, pelo menos uma taxa recorrente (que pode ser $0) é incluída:
    - Taxa **mensal** recorrente: taxa mensal simples que é pré-paga em uma recorrência mensal quando o usuário adquire o plano.
    - Taxa **anual** recorrente: taxa anual simples que é pré-paga em uma recorrência anual quando o usuário adquire o plano.
- Além das tarifas recorrentes, um plano de tarifas simples também pode incluir dimensões personalizadas opcionais usadas para cobrar os clientes pelo uso excedente não incluído na taxa fixa.  Cada dimensão representa uma unidade Faturável que seu serviço irá comunicar com a Microsoft usando a [API do serviço de medição do Marketplace comercial](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Oferta de exemplo

Por exemplo, a contoso é um editor com um serviço SaaS chamado contoso Notification Services (CNS). O CNS permite que seus clientes enviem notificações por email ou texto. A Contoso está registrada como um editor no Partner Center para o programa do Marketplace comercial publicar ofertas de SaaS para clientes do Azure.  Há dois planos associados ao CNS, descritos abaixo:

- Plano Básico
    - Enviar emails 10000 e 1000 textos por US $0/mês (taxa mensal simples)
    - Além dos 10000 emails, pague $1 para cada 100 emails
    - Além dos 1000 textos, pague $0.02 para cada texto

    [![Preço do plano básico](./media/saas-basic-pricing.png "Clique para ver a exibição ampliada")](./media/saas-basic-pricing.png)

- Plano Premium
    - Envie emails 50000 e 10000 para textos de $350/mês ou 5 min emails e 1 milhão de textos para $3500 por ano
    - Além dos 50000 emails, pague $0.05 para cada 100 emails
    - Além dos 10000 textos, pague $0.01 para cada texto

    [![Preço do plano Premium](./media/saas-premium-pricing.png "Clique para ver a exibição ampliada")](./media/saas-premium-pricing.png)

- Plano Enterprise
    - Enviar um número ilimitado de emails e 50000 de texto por US $400/mês
    - Além dos 50000, os textos são pagos $0.05 para cada txt

    [![Preços do plano Enterprise](./media/saas-enterprise-pricing.png "Clique para ver a exibição ampliada")](./media/saas-enterprise-pricing.png)

Com base no plano selecionado, uma assinatura de compra do cliente do Azure para a oferta de SaaS do CNS será capaz de enviar a quantidade incluída de texto e emails por período de assinatura (mês ou ano, conforme aparece nos detalhes da assinatura-startDate e endDate).  A contoso conta o uso até a quantidade incluída na base sem enviar nenhum evento de uso à Microsoft. Quando os clientes consomem mais do que a quantidade incluída, eles não precisam alterar os planos ou fazer algo diferente.  A contoso medirá o excedente além da quantidade incluída e começará a emitir eventos de uso para a Microsoft para cobrar o uso excedente usando a [API do serviço de medição do Marketplace comercial](./marketplace-metering-service-apis.md).  A Microsoft, por sua vez, cobrará o cliente pelo uso excedente, conforme especificado pelo Publicador nas dimensões personalizadas. A cobrança excedente é feita no próximo ciclo de cobrança (mensal, mas pode ser trimestral ou antecipadamente para alguns clientes).  Para um plano de taxa fixa mensal, a cobrança excedente será feita para todos os meses em que o excedente ocorreu.  Para um plano de taxa uniforme anual, uma vez que a quantidade incluída na base por ano seja consumida, todo o uso adicional emitido pelo medidor personalizado será cobrado como excedente durante cada ciclo de cobrança (mensal) até o final do período do ano da assinatura.

## <a name="billing-dimensions"></a>Dimensões de cobrança

Cada dimensão de cobrança define uma unidade personalizada pela qual o ISV pode emitir eventos de uso.  As dimensões de cobrança também são usadas para se comunicar com o cliente sobre como eles serão cobrados pelo uso do software.  Eles são definidos da seguinte maneira:

- **ID** : o identificador de dimensão imutável referenciado durante a emissão de eventos de uso.
- **Nome de exibição** : o nome de exibição associado à dimensão, por exemplo, "mensagens de texto enviadas".
- **Unidade de medida** : a descrição da unidade de cobrança, por exemplo "mensagem por texto" ou "por email 100".
- **Preço por unidade em USD** : o preço de uma unidade da dimensão.  Pode ser 0. 
- **Quantidade mensal incluída na base** : a quantidade de dimensões inclusas por mês para clientes que pagam a taxa mensal recorrente, deve ser um número inteiro. Pode ser 0 ou ilimitado.
- **Quantidade anual incluída na base** : a quantidade de dimensões inclusas por cada ano para clientes pagando a taxa anual recorrente, deve ser um número inteiro. Pode ser 0 ou ilimitado.

As dimensões de cobrança são compartilhadas entre todos os planos para uma oferta.  Alguns atributos se aplicam à dimensão em todos os planos e outros atributos são específicos do plano.

Os atributos, que definem a própria dimensão, são compartilhados entre todos os planos para uma oferta.  Antes de publicar a oferta, uma alteração feita nesses atributos do contexto de qualquer plano afetará a definição da dimensão em todos os planos.  Depois de publicar a oferta, esses atributos não serão mais editáveis.  Esses atributos são:

- ID
- Nome de exibição
- Unidade de medida

Os outros atributos de uma dimensão são específicos para cada plano e podem ter valores diferentes do plano para o plano.  Antes de publicar o plano, você pode editar esses valores e somente esse plano será afetado.  Depois de publicar o plano, esses atributos não serão mais editáveis.  Esses atributos são:

- Preço por unidade em USD
- Quantidade mensal incluída na base  
- Quantidade anual incluída em baseIncluded  

As dimensões também têm dois conceitos especiais, "Enabled" e "Infinite":

- **Habilitado** indica que este plano participa desta dimensão.  Se você estiver criando um novo plano que não envia eventos de uso com base nessa dimensão, talvez você queira deixar essa opção desmarcada.  Além disso, todas as novas dimensões adicionadas depois que um plano foi publicado pela primeira vez serão exibidas como "não habilitado" no plano já publicado.  Uma dimensão desabilitada não aparecerá em nenhuma lista de dimensões para um plano visto pelos clientes.
- **Infinitamente** representado pelo símbolo de infinito "∞", indica que este plano participa dessa dimensão, mas não emite uso nessa dimensão.  Se você quiser indicar aos clientes que a funcionalidade representada por essa dimensão está incluída no plano, mas sem limite de uso.  Uma dimensão com uso infinito será exibida em listas de dimensões para um plano visto pelos clientes, com uma indicação de que ele nunca incorrerá em um encargo para esse plano.

>[!Note] 
>Os cenários a seguir têm suporte explícito: <br> -Você pode adicionar uma nova dimensão a um novo plano.  A nova dimensão não será habilitada para nenhum plano já publicado. <br> -Você pode publicar um plano de **taxa fixa** sem nenhuma dimensão e, em seguida, adicionar um novo plano e configurar uma nova dimensão para esse plano. A nova dimensão não será habilitada para planos já publicados.

### <a name="setting-dimension-price-per-unit-per-supported-market"></a>Definindo o preço da dimensão por unidade por mercado com suporte

Como preço de taxa simples, os preços de dimensão de cobrança podem ser definidos por país ou região com suporte. Você precisa usar o recurso de importação e exportação de dados de preços no Partner Center, da seguinte maneira.

1. Defina as dimensões desejadas e marque quais mercados têm suporte. 
1. Exporte esses dados para um arquivo.
1. Adicione os preços corretos por país/região e importe o arquivo no Partner Center.

A interface do usuário do medidor será alterada para refletir que os preços da dimensão só podem ser vistos no arquivo.

[![dimensões do serviço de medição do Marketplace comercial](media/metering-service-dimensions.png "Clique para ver a exibição ampliada")](media/metering-service-dimensions.png)

### <a name="private-plan"></a>Plano particular

Como planos de tarifas simples, um plano com dimensões pode ser definido como plano privado, acessível somente pelo público definido do plano.

## <a name="constraints"></a>Restrições

### <a name="trial-behavior"></a>Comportamento da avaliação

A cobrança limitada usando o serviço de medição do Marketplace comercial não é compatível com a oferta de uma avaliação gratuita.  Não é possível configurar um plano para usar a cobrança limitada e uma avaliação gratuita.

### <a name="locking-behavior"></a>Comportamento de bloqueio

Como uma dimensão usada com o serviço de medição do Marketplace comercial representa uma compreensão de como um cliente pagará pelo serviço, todos os detalhes de uma dimensão não serão mais editáveis depois que você publicá-lo.  É importante que você tenha suas dimensões totalmente definidas para um plano antes de publicar.

Depois que uma oferta é publicada com uma dimensão, os detalhes de nível de oferta para essa dimensão não podem mais ser alterados:

- ID
- Nome de exibição
- Unidade de medida

Depois que um plano é publicado, os detalhes de nível de plano não podem mais ser alterados:

- Preço por unidade em USD
- Quantidade mensal incluída na base
- Quantidade anual incluída na base
- Se a dimensão está habilitada para o plano ou não

### <a name="upper-limits"></a>Limites superiores

O número máximo de dimensões que podem ser configuradas para uma única oferta é de 30 dimensões exclusivas.

## <a name="get-support"></a>Obter suporte

Se você tiver um dos seguintes problemas, poderá abrir um tíquete de suporte.

- Problemas técnicos com a API do serviço de medição do Marketplace.
- Um problema que precisa ser escalonado devido a um erro ou bug no seu lado (por exemplo, evento de uso incorreto).
- Quaisquer outros problemas relacionados à cobrança limitada.

Para entender as opções de suporte do Publicador e abrir um tíquete de suporte com a Microsoft, siga as instruções em [suporte para o programa do Marketplace comercial no Partner Center](../support.md).

## <a name="next-steps"></a>Próximas etapas

- [APIs de serviço de medição do Marketplace](./marketplace-metering-service-apis.md)
