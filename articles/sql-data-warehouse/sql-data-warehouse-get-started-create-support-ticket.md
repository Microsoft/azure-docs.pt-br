---
title: Como criar um tíquete de suporte para o SQL Data Warehouse do Azure | Microsoft Docs
description: Como criar um tíquete de suporte no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b3ffc9794967f44708d8330d4dc5d5de11b794d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474452"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Como criar um tíquete de suporte para o SQL Data Warehouse
Caso você tenha problemas com o SQL Data Warehouse, crie um tíquete de suporte para que nossa equipe de suporte de engenharia possa ajudar você.

## <a name="create-a-support-ticket"></a>Criar um tíquete de suporte
1. Abra o [portal do Azure][Azure portal].
2. Na tela Inicial, clique na guia **Ajuda + suporte**.
   
    ![Ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. Na folha Ajuda + Suporte, clique em **Nova solicitação de suporte** e preencha a folha **Básico**.

   Selecione seu [Plano de suporte do Azure][Azure support plan].
   
   * **Gerenciamento de assinaturas, cobrança e cotas** está disponível em todos os níveis de suporte.
   * O suporte do **reparo** é fornecido por meio do suporte do [Desenvolvedor][Developer], [Standard][Standard], [Professional Direct][Professional Direct] ou [Premier][Premier]. As questões que exigem reparos são problemas vivenciados pelo cliente ao usar o Azure, em que se espera que o problema tenha sido causado pela Microsoft.
   * O **aconselhamento de desenvolvedores** e os **serviços de consultoria** estão disponíveis nos níveis de suporte [Professional Direct][Professional Direct] e [Premier][Premier]. 
     
     Se tiver um plano de suporte Premier, você também poderá relatar problemas relacionados ao SQL Data Warehouse no [portal online Microsoft Premier][Microsoft Premier online portal].  Consulte os [planos de suporte do Azure][Azure support plan] para saber mais sobre os vários planos de suporte, incluindo o escopo, tempos de resposta, preços etc.  Para ver as perguntas frequentes sobre o suporte do Azure, consulte [Perguntas frequentes do suporte do Azure][Azure support FAQs].  
        
     ![Folha de Noções básicas](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Folha1 Noções básicas](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Preencha a folha **Problema**.
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Por padrão, cada SQL Server (por exemplo, myserver.database.windows.net) tem uma **Cota de DTU** de 45.000. Essa cota é simplesmente um limite de segurança. Você pode aumentar sua cota criando um tíquete de suporte e selecionando *Cota* como o tipo de solicitação. Para calcular suas necessidades de DTU, multiplique 7.5 pelo total de [DWU][DWU] necessário. Por exemplo, você gostaria de hospedar dois DW6000s em um SQL Server, então, deve solicitar uma cota DTU de 90.000.  Você pode exibir o consumo atual de DTU na folha do SQL Server no portal. Os bancos de dados em pausa e que não estão em pausa contam como a cota de DTU. 
   > 
   > 
   
5. Preencha suas **informações de contato**.
   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Clique em **Criar** para enviar a solicitação de suporte.

## <a name="monitor-a-support-ticket"></a>Monitorar um tíquete de suporte
Depois que você enviar a solicitação de suporte, a equipe de suporte do Azure entrará em contato com você. Para verificar o status e os detalhes da solicitação, clique em **Todas as solicitações de suporte** no painel.

![Verificar o status](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Outros recursos
Além disso, você pode conectar a comunidade do SQL Data Warehouse no [Stack Overflow][Stack Overflow] ou no Fórum MSDN do [Azure SQL Data Warehouse][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

