---
title: Cobrança limitada usando o serviço de medição do Marketplace | Azure Marketplace
description: Esta documentação é um guia para ISVs que publicam ofertas de SaaS com modelos de cobrança flexível.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 4b24805cd59d1eb9d28591749d5169486e54d506
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250114"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Cobrança limitada usando o serviço de medição do Marketplace

Com o serviço de medição do Marketplace, você pode criar ofertas de software como serviço (SaaS) no programa do Marketplace comercial que são cobrados de acordo com unidades não padrão.  Antes de publicar essa oferta, você define as dimensões de cobrança, como largura de banda, tíquetes ou emails processados.  Em seguida, os clientes pagam de acordo com o consumo dessas dimensões, com seu sistema informando a Microsoft por meio da API de serviço de medição do Marketplace de eventos faturáveis conforme eles ocorrem.  

## <a name="prerequisites-for-metered-billing"></a>Pré-requisitos para cobrança limitada

Para que uma oferta de SaaS use a cobrança limitada, ela deve:

* Atenda a todos os requisitos de oferta para uma oferta de [Venda por meio da Microsoft](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) , conforme descrito em [criar uma oferta de SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer).
* Integre-se com as [APIs de preenchimento de SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) para que os clientes provisionem e se conectem à sua oferta.  
* Ser configurado para o modelo de preços de **taxa fixa** para cobrar os clientes pelo seu serviço.  Dimensões são uma extensão opcional para o modelo de preços de taxa simples. 
* Integre-se com as [APIs do serviço de medição do Marketplace](./marketplace-metering-service-apis.md) para informar à Microsoft sobre eventos faturáveis.

>[!Note]
>O serviço de medição do Marketplace está disponível somente para o modelo de cobrança de taxa simples e não se aplica ao modelo de cobrança por usuário.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Como a cobrança limitada se encaixa com os preços

Quando se trata de definir a oferta junto com seus modelos de preços, é importante entender a hierarquia da oferta.

* Cada oferta de SaaS é configurada para vender pela Microsoft ou não.  Essa configuração não pode ser alterada depois que uma oferta é publicada.
* Cada oferta de SaaS, configurada para vender pela Microsoft, pode ter um ou mais planos. Um usuário assina a oferta de SaaS, mas é adquirida pela Microsoft dentro do contexto de um plano.
* Cada plano tem um modelo de preços associado a ele: **taxa fixa** ou **por usuário**. Todos os planos em uma oferta devem ser associados ao mesmo modelo de preços. Por exemplo, não pode haver uma oferta em que um de seus planos é um modelo de preços de taxa simples e outro é por modelo de preços de usuário.
* Dentro de cada plano configurado para um modelo de cobrança de taxa simples, pelo menos uma taxa recorrente (que pode ser $0) é incluída:
    * Taxa **mensal** recorrente: taxa mensal simples que é pré-paga em uma recorrência mensal quando o usuário adquire o plano.
    * Taxa **anual** recorrente: taxa anual simples que é pré-paga em uma recorrência anual quando o usuário adquire o plano.
* Além das tarifas recorrentes, o plano também pode incluir dimensões opcionais usadas para cobrar os clientes pelo uso não incluído na taxa fixa.   Cada dimensão representa uma unidade Faturável que seu serviço irá comunicar com a Microsoft usando a [API do serviço de medição do Marketplace](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Oferta de exemplo

Por exemplo, a contoso é um editor com um serviço SaaS chamado contoso Notification Services (CNS). O CNS permite que os clientes enviem notificações por email ou texto. A Contoso está registrada como um editor no Partner Center para o programa comercial do Marketplace publicar ofertas para clientes do Azure.  Há dois planos associados ao CNS, descritos abaixo:

* Plano base
    * Envie emails 10000 e 1000 textos para US $0/mês
    * Além dos 10000 emails, pague $1 para cada 100 emails
    * Além dos 1000 textos, pague $0.02 para cada texto
* Plano Premium
    * Envie emails 50000 e 10000 textos para US $350/mês
    * Além dos 50000 emails, pague $0.05 para cada 100 emails
    * Além dos 10000 textos, pague $0.01 para cada texto

Um cliente do Azure que está assinando o serviço CNS poderá enviar a quantidade incluída de texto e emails por mês com base no plano selecionado.  Quando os clientes consomem mais do que a quantidade incluída, eles não precisam alterar os planos ou fazer algo diferente.  A contoso medirá o excedente além da quantidade incluída e começará a emitir eventos de uso para a Microsoft para uso adicional usando a [API do serviço de medição do Marketplace](./marketplace-metering-service-apis.md).  A Microsoft, por sua vez, cobrará o cliente pelo uso adicional, conforme especificado pelo Publicador.

## <a name="billing-dimensions"></a>Dimensões de cobrança

As dimensões de cobrança são usadas para se comunicar com o cliente sobre como eles serão cobrados pelo uso do software e também para comunicar eventos de uso à Microsoft. Eles são definidos da seguinte maneira:

* **Identificador de dimensão**: o identificador imutável referenciado ao emitir eventos de uso.
* **Nome da dimensão**: o nome de exibição associado à dimensão, por exemplo, "mensagens de texto enviadas".
* **Unidade de medida**: a descrição da unidade de cobrança, por exemplo, "mensagem por texto" ou "por email 100".
* **Preço por unidade**: o preço de uma unidade da dimensão.  
* **Quantidade incluída para o termo mensal**: a quantidade de dimensões incluída por mês para clientes que pagam a taxa mensal recorrente, deve ser um número inteiro.
* **Quantidade incluída para o termo anual**: a quantidade de dimensões incluída por mês para clientes que pagam a taxa anual recorrente, deve ser um número inteiro.

As dimensões de cobrança são compartilhadas entre todos os planos para uma oferta.  Alguns atributos se aplicam à dimensão em todos os planos e outros atributos são específicos do plano.

Os atributos que definem a própria dimensão são compartilhados entre todos os planos para uma oferta.  Antes de publicar a oferta, uma alteração feita nesses atributos do contexto de qualquer plano afetará a definição da dimensão em todos os planos.  Depois de publicar a oferta, esses atributos não serão mais editáveis.  Esses atributos são:

* Identificador
* Nome
* Unidade de medida

Os outros atributos de uma dimensão são específicos para cada plano e podem ter valores diferentes do plano para o plano.  Antes de publicar o plano, você pode editar esses valores e somente esse plano será afetado.  Depois de publicar o plano, esses atributos não serão mais editáveis.  Esses atributos são:

* Preço por unidade
* Quantidade incluída para clientes mensais 
* Quantidade incluída para clientes anuais 

As dimensões também têm dois conceitos especiais, "Enabled" e "Infinite":

* **Habilitado** indica que este plano participa desta dimensão.  Talvez você queira deixar essa opção desmarcada se estiver criando um novo plano que não envia eventos de uso com base nessa dimensão.  Além disso, todas as novas dimensões adicionadas depois que um plano foi publicado pela primeira vez serão exibidas como "não habilitado" no plano já publicado.  Uma dimensão desabilitada agora aparecerá em qualquer lista de dimensões para um plano visto pelos clientes.
* **Infinito**, representado pelo símbolo de infinito "∞", indica que este plano participa dessa dimensão, mas não monitora o uso em relação a essa dimensão.  Se você quiser indicar aos clientes que a funcionalidade representada por essa dimensão está incluída no plano, mas sem limite de uso.  Uma dimensão com uso infinito será exibida em listas de dimensões para um plano visto pelos clientes, com uma indicação de que ele nunca incorrerá em um encargo para esse plano.

>[!Note] 
>Os cenários a seguir têm suporte explícito: <br> -Você pode adicionar uma nova dimensão a um novo plano.  A nova dimensão não será habilitada para nenhum plano já publicado. <br> -Você pode publicar um plano de **taxa fixa** sem nenhuma dimensão e, em seguida, adicionar um novo plano e configurar uma nova dimensão para esse plano. A nova dimensão não será habilitada para planos já publicados.

## <a name="constraints"></a>Restrições

### <a name="trial-behavior"></a>Comportamento da avaliação

A cobrança limitada usando o serviço de medição do Marketplace não é compatível com a oferta de uma avaliação gratuita.  Não é possível configurar um plano para usar a cobrança limitada e uma avaliação gratuita.

### <a name="locking-behavior"></a>Comportamento de bloqueio

Como uma dimensão usada com o serviço de medição do Marketplace representa uma compreensão de como um cliente pagará pelo serviço, todos os detalhes de uma dimensão não serão mais editáveis depois que você publicá-lo.  É importante que você tenha suas dimensões totalmente definidas para um plano antes de publicar.
  
Depois que uma oferta é publicada com uma dimensão, os detalhes de nível de oferta para essa dimensão não podem mais ser alterados:

* Identificador
* Nome
* Unidade de medida

Depois que um plano é publicado, os detalhes de nível de plano não podem mais ser alterados:

* Preço por unidade
* Quantidade incluída para o termo mensal
* Quantidade incluída para o termo anual
* Se a dimensão está habilitada para o plano

### <a name="upper-limits"></a>Limites superiores

O número máximo de dimensões que podem ser configuradas para uma única oferta é 18 dimensões exclusivas.

## <a name="get-support"></a>Obtenha suporte

Se você tiver um dos seguintes, poderá abrir um tíquete de suporte.

* Problemas técnicos com a API do serviço de medição do Marketplace.
* Um problema que precisa ser escalonado devido a um erro ou bug no seu lado (por exemplo, evento de uso incorreto).
* Quaisquer outros problemas relacionados à cobrança limitada. 

Siga as etapas abaixo para enviar seu tíquete de suporte:

1. Vá para a [página de suporte](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). Os primeiros menus suspensos são preenchidos automaticamente para você. Para obter suporte ao Marketplace, identifique a família de produtos como **serviços de nuvem e online**, o produto como Publicador do **Marketplace**.  Não altere as seleções de menu suspenso preenchidas previamente.
2. Em "selecionar a versão do produto", selecione **Gerenciamento de ofertas dinâmicas**.
3. Em "Selecione uma categoria que melhor descreva o problema", escolha **aplicativos SaaS**.
4. Em "Selecione um problema que melhor descreva o problema", selecione **cobrança limitada**.
5. Ao selecionar o botão **Avançar** , você será direcionado para a página **detalhes do problema** , onde poderá inserir mais detalhes sobre o problema.

Consulte [suporte para o programa do Marketplace comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support) para obter mais opções de suporte do Publicador.

## <a name="next-steps"></a>Próximas etapas

- Consulte [APIs do serviço de medição do Marketplace](./marketplace-metering-service-apis.md) para obter mais informações.
