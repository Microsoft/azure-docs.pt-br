---
title: Como um desconto de reserva do Azure é aplicado
description: Este artigo ajuda você a entender como os descontos de instância reservada são geralmente aplicados.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: 1dec8253d785973aa900434c10f85ec0a63e0599
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574865"
---
# <a name="how-a-reservation-discount-is-applied"></a>Como um desconto de reserva é aplicado

Este artigo ajuda você a entender como os descontos de instância reservada são geralmente aplicados. O desconto de reserva se aplica para o uso de recursos que correspondem aos atributos que você seleciona ao comprar a reserva. Os atributos incluem o escopo em que as VMs, os banco de dados SQL, o Azure Cosmos DB ou outros recursos correspondentes são executados. Por exemplo, caso deseje obter um desconto de reserva para quatro máquinas virtuais Standard D2 na região Oeste dos EUA, selecione a assinatura na qual as VMs estão em execução.

Um desconto de reserva é do tipo "*usar ou perder*". Se você não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível postergar horas reservadas não utilizadas.

Quando você desliga um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso correspondente for encontrado no escopo especificado, as horas reservadas serão *perdidas*.

Por exemplo, você pode criar um recurso posteriormente e ter uma reserva correspondente que fica subutilizada. O desconto da reserva se aplica automaticamente ao novo recurso de correspondente.

Se as máquinas virtuais são executadas em diferentes assinaturas em sua conta ou registro, então, selecione o escopo como compartilhado. O escopo compartilhado permite que o desconto de reserva seja aplicado entre assinaturas. Você pode alterar o escopo depois de comprar uma reserva. Para obter mais informações, consulte [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md).

Um desconto de reserva aplica-se apenas aos recursos associados aos tipos de assinatura Enterprise, Contrato de Cliente da Microsoft, CSP ou assinaturas com tarifas pagas conforme o uso. Os recursos executados em uma assinatura com outros tipos de oferta não recebem o desconto de reserva.

## <a name="when-the-reservation-term-expires"></a>Quando o prazo da reserva expira

No final do período de reserva, o desconto de cobrança expira e os recursos são cobrados pelo preço de pré-pagamento. Por padrão, as reservas não são definidas para renovação automática. Você pode optar por habilitar a renovação automática de uma reserva selecionando a opção nas configurações de renovação. Com a renovação automática, uma reserva substituta será adquirida após a expiração da reserva existente. Por padrão, a reserva substituta tem os mesmos atributos que a reserva que está vencendo. Opcionalmente, você pode alterar a frequência de cobrança, o prazo ou a quantidade nas configurações de renovação. Qualquer usuário com acesso de proprietário na reserva e na assinatura usada para cobrança pode configurar a renovação.  

## <a name="discount-applies-to-different-sizes"></a>O desconto se aplica a diferentes tamanhos

Quando você compra uma reserva, o desconto pode ser aplicado a outras instâncias com atributos que estão dentro do mesmo grupo de tamanho. Esse recurso é conhecido como flexibilidade de tamanho da instância. A flexibilidade da cobertura de desconto depende do tipo de reserva e dos atributos que você escolhe quando compra a reserva.

Planos de serviço:

- Instâncias de VM reservadas: Ao comprar a reserva, se você selecionar **Otimizado para flexibilidade de tamanho da instância**, a cobertura de desconto dependerá do tamanho da VM que você selecionar. A reserva pode ser aplicada aos tamanhos de VMs (máquinas virtuais) no mesmo grupo de série de tamanho. Para obter mais informações, confira [Flexibilidade de tamanho de máquina virtual com Instâncias de VM Reservadas](../../virtual-machines/reserved-vm-instance-size-flexibility.md).
- Capacidade reservada do Armazenamento do Azure: você pode comprar capacidade reservada para contas de armazenamento padrão do Azure em unidades de 100 TiB ou 1 PiB por mês. Para obter informações sobre quais regiões dão suporte à capacidade reservada do Armazenamento do Azure, confira [Preço do blob de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/). A capacidade reservada do Armazenamento do Azure está disponível para todas as camadas de acesso (quente, fria ou de arquivos) e para qualquer configuração de replicação (LRS, GRS ou ZRS).
- Capacidade reservada do Banco de Dados SQL: A cobertura de desconto depende do nível de desempenho escolhido. Para obter mais informações, confira [Entender como um desconto de reserva do Azure é aplicado](understand-reservation-charges.md).
- Capacidade reservada do Azure Cosmos DB: A cobertura de desconto depende da taxa de transferência provisionada. Para obter mais informações, confira [Entender como um desconto de reserva do Azure Cosmos DB é aplicado](understand-cosmosdb-reservation-charges.md).

## <a name="how-discounts-apply-to-specific-azure-services"></a>Como os descontos se aplicam a serviços específicos do Azure

Leia os artigos a seguir que se aplicam a você para saber como os descontos se aplicam a um serviço específico do Azure:

- [Serviço de Aplicativo](reservation-discount-app-service.md)
- [Cache Redis do Azure](understand-azure-cache-for-redis-reservation-charges.md)
- [Cosmos DB](understand-cosmosdb-reservation-charges.md)
- [Banco de Dados para MariaDB](understand-reservation-charges-mariadb.md)
- [Banco de Dados para MySQL](understand-reservation-charges-mysql.md)
- [Banco de Dados para PostgreSQL](understand-reservation-charges-postgresql.md)
- [Databricks](reservation-discount-databricks.md)
- [Data Explorer](understand-azure-data-explorer-reservation-charges.md)
- [Hosts Dedicados](billing-understand-dedicated-hosts-reservation-charges.md)
- [Armazenamento em Disco](understand-disk-reservations.md)
- [Red Hat Enterprise Linux](understand-rhel-reservation-charges.md)
- [Planos de software](understand-suse-reservation-charges.md)
- [Storage](understand-storage-charges.md)
- [Banco de Dados SQL](understand-reservation-charges.md)
- [Azure Synapse Analytics](reservation-discount-azure-sql-dw.md)
- [Máquinas virtuais](../manage/understand-vm-reservation-charges.md)


## <a name="next-steps"></a>Próximas etapas

- [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md)
- [Noções básicas sobre o uso de reserva para sua assinatura com taxas pagas conforme o uso](understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não estão incluídos nas reservas](reserved-instance-windows-software-costs.md)