---
title: O que são Reservas do Azure?
description: Saiba mais sobre as Reservas do Azure e sobre os preços para economizar com instâncias reservadas de máquinas virtuais, de bancos de dados SQL, do Azure Cosmos DB e de outros recursos.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: overview
ms.date: 12/15/2020
ms.author: banders
ms.openlocfilehash: 0e45e9741e92bb9e1fe23af79695cae06e64e871
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602049"
---
# <a name="what-are-azure-reservations"></a>O que são Reservas do Azure?

As Reservas do Azure ajudam você a economizar dinheiro se comprometendo com planos de um ou de três anos para vários produtos. O compromisso permite que você obtenha um desconto nos recursos que usar. As reservas podem reduzir significativamente seus custos com recursos, podendo chegar a 72% com relação aos preços pagos conforme o uso. As reservas fornecem um desconto de cobrança e não afetam o estado de runtime dos recursos. Após você comprar uma reserva, o desconto se aplica automaticamente aos recursos correspondentes.

Você pode pagar por uma reserva de forma antecipada ou mensal. O custo total das reservas antecipadas e mensais é o mesmo e você não paga nenhuma taxa adicional quando opta por pagar mensalmente. O pagamento mensal está disponível para reservas do Azure, e não para produtos de terceiros.

Você pode comprar uma instância reservada no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Por que comprar uma reserva?

Se você tiver um uso de recursos consistente que dá suporte a reservas, a compra de uma reserva lhe oferecerá a opção de reduzir os custos. Por exemplo, quando executa continuamente instâncias de um serviço sem a reserva, você é cobrado com base em taxas pagas conforme o uso. Quando compra uma reserva, você obtém imediatamente o desconto de reserva. Os recursos não serão mais cobrados com base nas taxas pagas conforme o uso.

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Após a compra, o desconto de reserva se aplica automaticamente para o uso de recursos que correspondem aos atributos que você seleciona ao comprar a reserva. Os atributos incluem o SKU, as regiões (quando aplicável) e o escopo. O escopo de uma reserva seleciona o local a que a economia da reserva se aplica.

Para obter mais informações sobre como o desconto é aplicado, confira [Aplicação do desconto de instância reservada](reservation-discount-application.md).

Para obter mais informações sobre como funciona o escopo de reserva, confira [Reservas de escopo](prepare-buy-reservation.md#scope-reservations).

## <a name="determine-what-to-purchase"></a>Determinar o que comprar 

Todas as reservas, exceto as do Azure Databricks, são aplicadas a cada hora. Considere as compras de reserva com base no seu uso de base consistente. Você pode determinar qual reserva comprar analisando seus dados de uso ou usando recomendações de reserva. As recomendações estão disponíveis em:

- Assistente do Azure (somente VMs)
- Experiência de compra de reserva no portal do Azure
- Aplicativo Power BI do Gerenciamento de Custos
- APIs 

Para obter mais informações, confira [Determinar qual reserva comprar](determine-reservation-purchase.md) 

## <a name="buying-a-reservation"></a>Comprar uma reserva 

Você pode comprar reservas por meio do portal do Azure, das APIs, do PowerShell e da CLI. 

Vá para o [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs) para fazer uma compra.

Para obter mais informações, confira [Comprar uma reserva](prepare-buy-reservation.md).

## <a name="how-is-a-reservation-billed"></a>Como uma reserva é cobrada? 

A reserva é cobrada no método de pagamento associado à assinatura. O custo de reserva é deduzido do saldo do seu Pagamento antecipado do Azure (anteriormente conhecido como compromisso monetário), se disponível. Quando o saldo do Pagamento antecipado do Azure não cobrir o custo da reserva, você será cobrado pelo excedente. Se você tiver uma assinatura de um plano individual com tarifas pagas conforme o uso, o cartão de crédito que você tem em sua conta será cobrado imediatamente para as compras antecipadas. Os pagamentos mensais aparecem em sua fatura e seu cartão de crédito é cobrado mensalmente. Quando for cobrado por fatura, você verá os encargos na sua próxima fatura. 

## <a name="who-can-manage-a-reservation-by-default"></a>Quem pode gerenciar uma reserva por padrão

Por padrão, os seguintes usuários podem ver e gerenciar reservas:

- A pessoa que compra uma reserva e o administrador da conta da assinatura para cobrança usada para comprar a reserva são adicionadas ao pedido de reserva.
- Administradores de cobrança do Contrato Enterprise e do Contrato de Cliente da Microsoft.

Para permitir que outras pessoas gerenciem reservas, confira [Gerenciar reservas para recursos do Azure](manage-reserved-vm-instance.md).

## <a name="get-reservation-details-and-utilization-after-purchase"></a>Obter detalhes e a utilização da reserva após a compra

Se você tiver permissão para exibir a reserva, poderá ver a reserva e o uso dela no portal do Azure. Você pode obter os dados usando APIs também. 

Para obter mais informações sobre como ver reservas no portal do Azure, confira [Exibir reservas no portal do Azure](view-reservations.md) 

## <a name="manage-reservations-after-purchase"></a>Gerenciar reservas após a compra 

Após comprar uma reserva do Azure, você poderá atualizar o escopo para aplicar a reserva a uma assinatura diferente, alterar quem pode gerenciar a reserva, dividir uma reserva em partes menores ou alterar a flexibilidade de tamanho da instância. 

Para obter mais informações, confira [Gerenciar reservas para recursos do Azure](manage-reserved-vm-instance.md) 

## <a name="flexibility-with-azure-reservations"></a>Flexibilidade com reservas do Azure

As Reservas do Azure fornecem flexibilidade para ajudar a atender às suas necessidades em constante evolução. Você pode trocar uma reserva por outra do mesmo tipo. Você também poderá pedir reembolso de uma reserva de até US$ 50.000 em uma janela de 12 meses se não precisar mais dela. O limite máximo do reembolso se aplica a todas as reservas no escopo do seu contrato com a Microsoft.

Para obter mais informações, confira [Trocas e reembolsos via autoatendimento para Reservas do Azure](exchange-and-refund-azure-reservations.md)


## <a name="charges-covered-by-reservation"></a>Preços cobertos pela reserva

- **Instância de Máquina Virtual Reservada** – uma reserva abrange apenas os custos de computação de máquina virtual e de serviços de nuvem. Não cobre encargos adicionais de software, Windows, rede nem armazenamento.
- **Capacidade reservada de Armazenamento do Azure**: uma reserva abrange a capacidade de contas de armazenamento padrão para armazenamento de blobs ou do Azure Data Lake Storage Gen2. A reserva não abrange a largura de banda nem as taxas de transação.
- **Capacidade reservada do Azure Cosmos DB** – cobre a taxa de transferência provisionada para seus recursos. Ela não cobre encargos de armazenamento e rede.
- **vCore reservado do Banco de Dados SQL** – abrange a Instância Gerenciada de SQL e o Pool Elástico de Banco de Dados SQL/banco de dados individual. Apenas os custos de computação são incluídos em uma reserva. A licença do SQL é cobrada separadamente. 
- **Azure Synapse Analytics** – uma reserva abrange o uso da cDWU. Ela não abrange os encargos de armazenamento nem de rede associados ao uso do Azure Synapse Analytics.
- **Azure Databricks** – uma reserva abrange apenas o uso de DBU. Outros encargos, como computação, armazenamento e rede, são aplicados separadamente.
- **Imposto de selo do Serviço de Aplicativo** – uma reserva cobre o uso do imposto de selo. Ela não se aplica a funções de trabalho, de forma que todos os outros recursos associados ao selo são cobrados separadamente.
- **Banco de Dados do Azure para MySQL** – apenas os custos de computação são incluídos com uma reserva. Uma reserva não cobre custos de software, rede ou armazenamento associados à instância do servidor de Banco de Dados MySQL.
- **Banco de Dados do Azure para PostgreSQL** – apenas os custos de computação são incluídos com uma reserva. Uma reserva não cobre custos de software, rede ou armazenamento associados à instância dos servidores de Banco de Dados PostgreSQL.
- **Banco de Dados do Azure para MariaDB** – apenas os custos de computação são incluídos com uma reserva. Uma reserva não cobre custos de software, rede ou armazenamento associados à instância do servidor de banco de dados MariaDB.
- **Azure Data Explorer** – uma reserva abrange os encargos de marcação. Uma reserva não se aplica aos encargos de computação, rede ou armazenamento associados aos clusters.
- **Cache do Azure para Redis**: apenas os custos de computação são incluídos com uma reserva. Uma reserva não abrange os preços de rede ou armazenamento associados às instâncias de Cache Redis.
- **Host Dedicado do Azure** – Somente os custos de computação estão incluídos no Host Dedicado.
- **Reservas do Armazenamento em Disco do Azure** – uma reserva cobre apenas SSDs Premium de tamanho P30 ou superior. Ela não cobre nenhum outro tipo de disco ou tamanhos menores que P30.

Planos de software:

- **SUSE Linux** – uma reserva abrange os custos do plano de software. Os descontos aplicam-se apenas aos medidores do SUSE e não ao uso da máquina virtual.
- **Planos do Red Hat** – uma reserva abrange os custos do plano de software. Os descontos aplicam-se apenas aos medidores Red Hat e não ao uso da máquina virtual.
- **Solução VMware da CloudSimple no Azure** – uma reserva abrange os nós VMWare da CloudSimple. Os custos de software adicionais ainda se aplicam.
- **Red Hat OpenShift no Azure** – uma reserva se aplica aos custos do OpenShift, mas não aos custos de infraestrutura do Azure.

Para máquinas virtuais do Windows e o Banco de Dados SQL, o desconto de reserva não se aplica aos custos de software. Você pode cobrir os custos de licenciamento com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as Reservas do Azure com os seguintes artigos:
    - [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md)
    - [Noções básicas sobre o uso de reserva para sua assinatura com taxas pagas conforme o uso](understand-reserved-instance-usage.md)
    - [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não estão incluídos nas reservas](reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](/partner-center/azure-reservations)

- Saiba mais sobre reservas para planos de serviço:
    - [Máquinas virtuais com Instâncias de VM Reservadas do Azure](../../virtual-machines/prepay-reserved-vm-instances.md)
    - [Recursos do Azure Cosmos DB com capacidade reservada do Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Recursos de computação do Banco de Dados SQL com capacidade reservada do Banco de Dados SQL do Azure](../../azure-sql/database/reserved-capacity-overview.md)
    - [Recursos do Cache do Azure para Redis com capacidade reservada do Cache do Azure para Redis](../../azure-cache-for-redis/cache-reserved-pricing.md) Saiba mais sobre as reservas para planos de software:
    - [Planos de software Red Hat das Reservas do Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
    - [Planos de software SUSE das Reservas do Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
