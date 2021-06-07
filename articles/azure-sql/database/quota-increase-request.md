---
title: Solicitar um aumento de cota
description: Esta página descreve como criar uma solicitação de suporte para aumentar as cotas para o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 06/04/2020
ms.openlocfilehash: 27719663acfbdbcd7293defc4b746153359adb61
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251838"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>Aumentos de cota de solicitação para o banco de dados SQL do Azure e o SQL Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo explica como solicitar um aumento de cota para o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada. Ele também explica como habilitar o acesso à assinatura para uma região e como solicitar a habilitação de um hardware específico em uma região.

## <a name="create-a-new-support-request"></a><a id="newquota"></a> Criar uma nova solicitação de suporte

Use as etapas a seguir para criar uma nova solicitação de suporte do portal do Azure para o banco de dados SQL.

1. No menu do [Portal do Azure](https://portal.azure.com), selecione **Ajuda + suporte**.

   ![Link de ajuda + suporte](./media/quota-increase-request/help-plus-support.png)

1. Em **Ajuda + suporte**, selecione **Nova solicitação de suporte**.

    ![Criar uma solicitação de suporte](./media/quota-increase-request/new-support-request.png)

1. Para **tipo de problema**, selecione **limites de serviço e de assinatura (cotas)**.

   ![Selecione um tipo de problema](./media/quota-increase-request/select-quota-issue-type.png)

1. Para **assinatura**, selecione a assinatura cuja cota você deseja aumentar.

   ![Selecione uma assinatura para uma cota maior](./media/quota-increase-request/select-subscription-support-request.png)

1. Para **tipo de cota**, selecione um dos seguintes tipos de cota:

   - **Banco de dados SQL** para cotas de banco de dados individual e pool elástico.
   - **Instância gerenciada do banco de dados SQL** para instâncias gerenciadas.

   Em seguida, selecione **Avançar: Soluções >>** .

   ![Selecionar um tipo de cota](./media/quota-increase-request/select-quota-type.png)

1. Na janela **detalhes** , selecione **Inserir detalhes** para inserir informações adicionais.

   ![Inserir link de detalhes](./media/quota-increase-request/provide-details-link.png)

Clicar em **Inserir detalhes** exibe a janela **detalhes da cota** que permite que você adicione informações adicionais. As seções a seguir descrevem as diferentes opções para o **banco de dados SQL** e **instância gerenciada do banco de dados SQL** tipos de cota.

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a> Tipos de cota do banco de dados SQL

As seções a seguir descrevem as opções de aumento de cota para os tipos de cota do **banco de dados SQL** :

- DTUs (unidades de transação de banco de dados) por servidor
- Servidores por assinatura
- Acesso à região para assinaturas ou hardware específico

### <a name="database-transaction-units-dtus-per-server"></a>DTUs (unidades de transação de banco de dados) por servidor

Use as etapas a seguir para solicitar um aumento nas DTUs por servidor.

1. Selecione o tipo de cota **unidades de transação de banco de dados (DTUs) por servidor** .

1. Na lista de **Recursos**, selecione o recurso a de destino.

1. No campo **nova cota** , insira o novo limite de DTU que você está solicitando.

   ![Detalhes da cota de DTU](./media/quota-increase-request/quota-details-dtus.png)

Para obter mais informações, consulte [limites de recursos para bancos de dados individuais usando o modelo de compra de DTU](resource-limits-dtu-single-databases.md) e [os limites de recursos para pools elásticos usando o modelo de compra de DTU](resource-limits-dtu-elastic-pools.md).

### <a name="servers-per-subscription"></a>Servidores por assinatura

Siga as etapas a seguir para solicitar um aumento no número de servidores por assinatura.

1. Selecione o tipo de cota de **Servidores por assinatura**.

1. Na lista de **Locais**, selecione a região do Azure a ser usada. A cota é relativa a cada região e por assinatura.

1. No campo **Nova cota**, insira sua solicitação até o número máximo de servidores nessa região.

   ![Detalhes de cota de servidores](./media/quota-increase-request/quota-details-servers.png)

Para obter mais informações, consulte [limites de recursos do banco de dados SQL e governança de recursos](resource-limits-logical-server.md).

### <a name="enable-subscription-access-to-a-region"></a><a id="region"></a> Habilitar o acesso à assinatura para uma região

Nem todos os tipos de oferta estão disponíveis em todas as regiões. Nesse caso, é possível que você veja o seguinte erro:

`Your subscription does not have access to create a server in the selected region. For the latest information about region availability for your subscription, go to aka.ms/sqlcapacity. Please try another region or create a support ticket to request access.`

Se sua assinatura precisar de acesso em uma região específica, selecione a opção **acesso à região** . Em sua solicitação, especifique a oferta e os detalhes de SKU que você deseja habilitar para a região. Para explorar as opções de oferta e SKU, consulte [preços do banco de dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

1. Selecione o tipo de cota de **acesso à região** .

1. Na lista **selecionar um local** , selecione a região do Azure a ser usada. A cota é relativa a cada região e por assinatura.

1. Insira o **modelo de compra** e os detalhes de **consumo esperados** .

   ![Solicitar acesso à região](./media/quota-increase-request/quota-request.png)

### <a name="request-enabling-specific-hardware-in-a-region"></a>Solicitar a habilitação de um hardware específico em uma região

Se uma [geração de hardware](service-tiers-vcore.md#hardware-generations) que você deseja usar não estiver disponível em sua região (consulte [disponibilidade de hardware](service-tiers-vcore.md#hardware-availability)), você poderá solicitá-la usando as etapas a seguir.

1. Selecione o **outro** tipo de cota de solicitação de cota.

1. No campo **Descrição** , declare sua solicitação, incluindo o nome da geração de hardware e o nome da região em que você precisa.

   ![Solicitar o hardware em uma nova região](./media/quota-increase-request/hardware-in-new-region.png)

## <a name="submit-your-request"></a>Enviar a solicitação

A etapa final é preencher os detalhes restantes da sua solicitação de cota do Banco de Dados SQL do Microsoft Azure. Em seguida, selecione **Avançar: Examinar + criar>>** e, depois de revisar os detalhes da solicitação, clique em **Criar** para enviar a solicitação.

## <a name="next-steps"></a>Próximas etapas

Depois de enviar sua solicitação, ela será revisada. Você receberá uma resposta com base nas informações fornecidas no formulário.

Para obter mais informações sobre outros limites do Azure, consulte [assinatura e limites de serviço, cotas e restrições do Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).
