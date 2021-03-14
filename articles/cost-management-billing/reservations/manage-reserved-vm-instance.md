---
title: Gerenciar reservas do Azure
description: Saiba como gerenciar as Reservas do Azure. Confira as etapas para alterar o escopo de reserva, dividir uma reserva e otimizar o uso da reserva.
ms.service: cost-management-billing
ms.subservice: reservations
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 02/09/2021
ms.author: banders
ms.openlocfilehash: 717cf5acb63ee04852ccbb9aae2f7aed2b3c179a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392265"
---
# <a name="manage-reservations-for-azure-resources"></a>Gerenciar Reservas para recursos do Azure

Após comprar uma reserva do Azure, talvez seja necessário aplicar a reserva a uma assinatura diferente, alterar quem pode gerenciar a reserva ou alterar o escopo da reserva. Também é possível dividir uma reserva em duas reservas para aplicar algumas das instâncias que você comprou para outra assinatura.

Se você comprou Instâncias de Máquinas Virtuais Reservadas do Azure, poderá alterar a configuração de otimização da reserva. O desconto de reserva pode ser aplicado a VMs na mesma série ou você pode reservar a capacidade do data center para um tamanho de VM específico. Você deve tentar otimizar as reservas para que elas sejam totalmente usadas.

*A permissão necessária para gerenciar uma reserva é separada da permissão de assinatura.*

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Pedido de Reserva e Reserva

Quando você compra uma reserva, dois objetos são criados: **Pedido de Reserva** e **Reserva**.

No momento da compra, um Pedido de Reserva tinha uma Reserva nele. Ações como dividir, mesclar, reembolsar parcialmente ou trocar criam novas reservas no **Pedido de Reserva**.

Para ver um Pedido de Reserva, acesse **Reservas** > selecione a reserva e escolha a **ID do pedido de reserva**.

![Exemplo dos detalhes do pedido de reserva mostrando a ID do pedido de reserva ](./media/manage-reserved-vm-instance/reservation-order-details.png)

Uma reserva herda permissões de seu pedido de reserva. Para trocar ou reembolsar uma reserva, o usuário deve ser adicionado à ordem de reserva.

## <a name="change-the-reservation-scope"></a>Alterar o escopo da reserva

 Seu desconto de reserva se aplica a máquinas virtuais, bancos de dados SQL, Azure Cosmos DB ou outros recursos que correspondem à sua reserva e são executados no escopo da reserva. O contexto de cobrança depende da assinatura usada para comprar a reserva.

Para atualizar o escopo de uma reserva:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione a reserva.
4. Selecione **Configurações** > **Configuração**.
5. Altere o escopo.

Se você alterar de escopo compartilhado para único, poderá selecionar apenas as assinaturas em que é o proprietário. Somente as assinaturas no mesmo contexto de cobrança que a reserva podem ser selecionadas.

O escopo somente se aplica a assinaturas individuais com taxas pagas conforme o uso (ofertas MS-AZR-0003P ou MS-AZR-0023P), à oferta Enterprise MS-AZR-0017P ou MS-AZR-0148P ou a tipos de assinatura CSP.

## <a name="who-can-manage-a-reservation-by-default"></a>Quem pode gerenciar uma reserva por padrão

Por padrão, os seguintes usuários podem ver e gerenciar reservas:

- A pessoa que compra uma reserva e o administrador da conta da assinatura para cobrança usada para comprar a reserva são adicionadas ao pedido de reserva.
- Administradores de cobrança do Contrato Enterprise e do Contrato de Cliente da Microsoft.

Para permitir que outras pessoas gerenciem reservas, você tem duas opções:

- Delegar o gerenciamento de acesso para um pedido de reserva individual:
    1. Entre no [portal do Azure](https://portal.azure.com).
    1. Selecione **Todos os serviços** > **Reserva** para listar as reservas às quais você tem acesso.
    1. Selecione a reserva que deseja delegar acesso a outros usuários.
    1. Em Detalhes da reserva, selecione o pedido de reserva.
    1. Selecione **IAM (Controle de acesso)** .
    1. Selecione **Adicionar atribuição de função** > **Função** > **Proprietário**. Se quiser permitir acesso limitado, escolha outra função.
    1. Digite o endereço de email do usuário que você deseja adicionar como proprietário.
    1. Selecione o usuário e, em seguida, selecione **Salvar**.

- Adicione um usuário como Administrador de cobrança a um Contrato Enterprise ou um Contrato de Cliente da Microsoft:
    - Para um Contrato Enterprise, adicione usuários com a função _Administrador Corporativo_ para ver e gerenciar todos os pedidos de reserva que se aplicam ao Contrato Enterprise. Os usuários com a função _Administrador Corporativo (somente leitura)_ só podem ver a reserva. Os administradores de departamento e os proprietários da conta não podem ver as reservas _a menos que_ sejam explicitamente adicionados a elas por meio do Controle de acesso (IAM). Para obter mais informações, confira [Como gerenciar funções corporativas do Azure](../manage/understand-ea-roles.md).

        _Os administradores corporativos podem se apropriar de um pedido de reserva e adicionar outros usuários a uma reserva usando o Controle de acesso (IAM)._
    - Para um Contrato de Cliente da Microsoft, os usuários com a função proprietário ou colaborador do perfil de cobrança podem gerenciar todas as compras de reserva feitas usando o perfil de cobrança. Os leitores do perfil de cobrança e os gerenciadores de faturas podem ver todas as reservas pagas com o perfil de cobrança. No entanto, eles não podem fazer alterações nas reservas.
    Para obter mais informações, confira [Funções e tarefas do perfil de cobrança](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="how-billing-administrators-view-or-manage-reservations"></a>Como os administradores de cobrança veem ou gerenciam as reservas

1. Acesse **Gerenciamento de Custos e Cobrança** e, no lado esquerdo da página, selecione **Transações de Reserva**.
2. Se você tiver as permissões de cobrança necessárias, poderá ver e gerenciar as reservas. Caso não veja nenhuma reserva, verifique se está conectado usando o locatário do Azure AD em que as reservas foram criadas.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dividir uma única reserva em duas reservas

 Depois de comprar mais de uma instância de recurso em uma reserva, é recomendável atribuir instâncias nessa reserva a assinaturas diferentes. Por padrão, todas as instâncias têm um escopo – assinatura única, grupo de recursos ou compartilhada. Digamos que você tenha comprado uma reserva de 10 instâncias de VM e especificado o escopo como a assinatura A. Agora, você quer alterar o escopo para ter sete instâncias de VM na assinatura A e as três restantes na assinatura B. A divisão de uma reserva permite fazer isso. Depois que você divide uma reserva, a ReservationID original é cancelada e duas outras reservas são criadas. A divisão não afeta o pedido de reserva: não ocorre nenhuma nova transação comercial com a divisão e as novas reservas têm a mesma data de término que a reserva que foi dividida.

 Você pode dividir uma reserva em duas reservas por meio do PowerShell, CLI ou da API.

### <a name="split-a-reservation-by-using-powershell"></a>Dividir uma reserva usando o PowerShell

1. Obtenha a ID do pedido de reserva executando o seguinte comando:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Obtenha os detalhes de uma reserva:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Divida a reserva em duas e distribua as instâncias:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Você pode atualizar o escopo executando o seguinte comando:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

É possível cancelar, trocar ou reembolsar reservas com determinadas limitações. Para saber mais, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](exchange-and-refund-azure-reservations.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Alterar a configuração de otimização para Instâncias de VM Reservadas

 Ao comprar uma Instância de VM Reservada, você escolhe a flexibilidade do tamanho da instância ou a prioridade da capacidade. A flexibilidade do tamanho da instância aplica o desconto de reserva a outras VMs no mesmo [grupo de tamanhos de VM](../../virtual-machines/reserved-vm-instance-size-flexibility.md). A prioridade da capacidade designa a capacidade do data center mais importante para suas implantações. Essa opção oferece mais confiança na capacidade de iniciar as instâncias de VM quando forem necessárias.

Por padrão, quando o escopo da reserva é compartilhado, a flexibilidade do tamanho da instância é ativada. A capacidade do data center não é priorizada para implantações de VM.

Para reservas em que o escopo é único, você pode otimizar a reserva para prioridade de capacidade em vez de flexibilidade de tamanho de instância de VM.

Para atualizar a configuração de otimização da reserva:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione a reserva.
4. Selecione **Configurações** > **Configuração**.
  ![Exemplo mostrando o item de Configuração](./media/manage-reserved-vm-instance/add-product03.png)
5. Altere a configuração **Otimizar para**.
  ![Exemplo mostrando a configuração Otimizar para](./media/manage-reserved-vm-instance/instance-size-flexibility-option.png)

## <a name="optimize-reservation-use"></a>Otimizar o uso da reserva

A economia da reserva do Azure são provenientes do uso do recurso sustentado. Ao realizar uma compra de reserva, você paga por um uso de recurso 100% possível durante um prazo de um ou três anos. Tente maximizar sua reserva para ter o máximo de uso e de economia possível. As seguintes seções explicam como monitorar uma reserva e otimizar seu uso.

### <a name="view-reservation-use-in-the-azure-portal"></a>Exibir uso de reserva no portal do Azure

Uma maneira de exibir o uso de reserva é no portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > [**Reservas**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e observe a **Utilização (%)** de uma reserva.  
  ![Imagem mostrando a lista de reservas](./media/manage-reserved-vm-instance/reservation-list.png)
3. Selecione uma reserva.
4. Examine a tendência de uso da reserva com o tempo.
  ![Imagem mostrando o uso da reserva ](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Exibir uso da reserva com API

Se você for um cliente EA (Contrato Enterprise), poderá exibir programaticamente como as reservas em sua organização estão sendo usadas. Você obtém a reserva não utilizada por meio de dados de uso. Quando você examina os encargos da reserva, tenha em mente que os dados são divididos entre custo real e custos amortizados. O custo real fornece dados para reconciliar sua fatura mensal. Ele também tem o custo de compra da reserva e os detalhes do aplicativo de reserva. O custo amortizado é como o custo real, exceto que o preço efetivo do uso da reserva é proporcional. As horas de reserva não utilizadas são mostradas em dados de custo amortizado. Para obter mais informações sobre dados de uso para clientes EA, confira [Obter uso e custos de reserva do Contrato Enterprise](understand-reserved-instance-usage-ea.md).

Para outros tipos de assinatura, use a API [Resumos de Reservas – Listar por Pedido de Reserva e Reserva](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

### <a name="optimize-your-reservation"></a>Otimize sua reserva

Se você achar que as reservas da sua organização estão sendo subutilizadas:

- Verifique se as máquinas virtuais que sua organização cria correspondem ao tamanho da VM que está na reserva.
- Verifique se a flexibilidade de tamanho da instância está ligada. Para obter mais informações, confira [Gerenciar reservas – alterar configuração de otimização para Instâncias de VM Reservadas](#change-optimize-setting-for-reserved-vm-instances).
- Altere o escopo da reserva para _compartilhado_ de modo que ele se aplique mais amplamente. Para obter mais informações, confira [Alterar o escopo de uma reserva](#change-the-reservation-scope).
- Considere trocar a quantidade não utilizada. Para obter mais informações, confira [Cancelamentos e trocas](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são reservas para o Azure?](save-compute-costs-reservations.md)

Comprar um plano de serviço:
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Banco de Dados SQL do Azure](../../azure-sql/database/reserved-capacity-overview.md)
- [Pagar antecipadamente por recursos do Azure Cosmos DB com capacidade reservada do Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)

Comprar um plano de software:
- [Pagar antecipadamente por planos de software Red Hat das reservas do Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Pré-pagamento para planos de software SUSE das reservas do Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)

Noções básicas sobre desconto e uso:
- [Entender como o desconto de reserva de VM é aplicado](../manage/understand-vm-reservation-charges.md)
- [Noções básicas sobre como o desconto do plano de software do Red Hat Enterprise Linux é aplicado](understand-rhel-reservation-charges.md)
- [Entender como o desconto do plano de software do SUSE Linux Enterprise é aplicado](understand-suse-reservation-charges.md)
- [Entender como outros descontos de reserva são aplicados](understand-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não estão incluídos nas reservas](reserved-instance-windows-software-costs.md)
