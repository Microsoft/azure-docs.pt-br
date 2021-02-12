---
title: Solucionar problemas de tipo de reserva do Azure não disponível
description: Este artigo ajuda você a entender e solucionar problemas de instâncias reservadas que aparecem como não disponíveis no portal do Azure.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 8575d9d86d8e720122a295cf92fa571ef33d5b4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92798173"
---
# <a name="troubleshoot-reservation-type-not-available"></a>Solucionar problemas de tipo de reserva não disponível

Este artigo ajuda você a entender e solucionar problemas de instâncias reservadas que aparecem como não disponíveis no portal do Azure.

## <a name="symptoms"></a>Sintomas

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até **Reservas**.
2. Selecione **+ Adicionar** e escolha um produto.
3. Selecione a guia **Todos os Produtos**.
4. Na lista de produtos, selecione um. Você poderá ver uma das seguintes mensagens:
    - `Product unavailable for the selected subscription or region. Contact support.`  
        :::image type="content" source="./media/troubleshoot-product-not-available/product-unavailable-message.png" alt-text="Exemplo mostrando a mensagem O produto não está disponível para a assinatura ou a região selecionada" lightbox="./media/troubleshoot-product-not-available/product-unavailable-message.png" :::
    - `The selected subscription does not have enough core quota remaining to purchase this product. Request quota increase`  
        :::image type="content" source="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" alt-text="Exemplo mostrando a mensagem de cota de núcleo insuficiente" lightbox="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" :::

## <a name="cause"></a>Causa

Algumas reservas não estão disponíveis para compra porque:

- Nem todos os produtos de instância reservada estão disponíveis para compra em todas as regiões
- Sua assinatura tem uma restrição de cota

### <a name="cause-1"></a>Causa 1

Nem todos os produtos de instância reservada estão disponíveis para compra. O Azure decide permitir ou não alguns produtos com base nos custos associados ao fornecimento do desconto monetário aos clientes. Em outros casos, o Azure não oferece alguns produtos devido a condições de capacidade em datacenters específicos. Além disso, alguns grupos de desenvolvimento de produtos do Azure agora podem permitir certos produtos porque desejam desativar um produto mais antigo.

Em outros casos, o Azure coloca restrições de capacidade em vários produtos com base na demanda desses recursos em algumas regiões. Por exemplo, essa restrição pode ser feita quando a demanda de um determinado tamanho de VM acaba em um data center. Neste exemplo, o Azure não pode garantir a capacidade para clientes que compraram uma reserva para esse tamanho nessa região. Por fim, há alguns produtos que são exclusivos por vários motivos. Esses produtos são disponibilizados apenas para um conjunto pequeno e selecionado de clientes.

Como algumas reservas não estão disponíveis para compra, por que elas são mostradas no portal do Azure? A resposta é porque os usuários criam solicitações de suporte informando que eles não conseguem localizar os produtos que desejam. A equipe de desenvolvimento da Reserva do Azure determinou que mostrar todos os produtos com a mensagem `Product unavailable` gera menos solicitações de suporte do que não os mostrar.

### <a name="cause-2"></a>Causa 2

Sua assinatura tem uma restrição de cota. As assinaturas têm limites de quantos núcleos de CPU elas podem consumir. O limite se aplica a alguns produtos de instância reservada, principalmente às máquinas virtuais. O Azure quer garantir que as pessoas que comprarem uma instância reservada possam usá-la. O Azure faz uma verificação superficial simples no portal do Azure para verificar se você tem núcleos livres suficientes em sua assinatura para implantar a VM e obter um benefício da compra de reserva.

A verificação para permitir que você adicione um produto específico ao seu carrinho e adquira uma reserva é simples. O Azure avalia o número total de núcleos de CPU disponíveis para sua assinatura e verifica se ele é maior do que o número de núcleos para o item selecionado.

O Azure não verifica se a cota tem instâncias reservadas de escopo **Compartilhado**. O benefício da instância reservada para o escopo compartilhado se aplica a todas as assinaturas no registro. O Azure não pode determinar se você tem núcleos livres suficientes em todas as suas assinaturas para implantar o recurso. Seja qual for a cota, o Azure sempre permite que você selecione um tamanho de VM quando o escopo selecionado é compartilhado.

Além disso, o Azure não faz uma verificação de cota para compras **Recomendadas**. As recomendações são baseadas no uso ativo. O Azure pressupõe que você tenha núcleos suficientes para executar um tamanho de VM específico porque você já gerou o uso necessário para criar a recomendação.

## <a name="solution"></a>Solução

Dependendo da mensagem de erro que você recebeu, use uma das soluções a seguir para o seu problema.

### <a name="solution-1"></a>Solução 1

Se você vir uma mensagem _Produto não disponível_ , selecione o link **Entrar em contato com o suporte** na mensagem de erro para solicitar a adição de uma exceção à sua assinatura. As exceções nem sempre são concedidas.

### <a name="solution-2"></a>Solução 2

Se você vir uma mensagem _Cota de núcleo insuficiente_ , poderá alterar o escopo para **Compartilhado**. Após comprar a reserva, você poderá alterar o escopo da reserva de **Compartilhado** para **Único**.

Ou selecione o link **Solicitar aumento de cota** na mensagem de erro para solicitar cota de núcleo de CPU adicional para sua assinatura.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre as opções de escopo de reserva, confira [Reservas de escopo](prepare-buy-reservation.md#scope-reservations).