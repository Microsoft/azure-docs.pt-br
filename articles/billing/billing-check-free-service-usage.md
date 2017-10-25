---
title: "Monitorar e acompanhar o uso de serviços gratuitos – Azure | Microsoft Docs"
description: "Saiba como verificar o uso de serviços gratuitos. Use o Portal do Azure e o csv de uso."
services: 
documentationcenter: 
author: amberbhargava
manager: amberb
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: 27ff6c92904a0b32cd4a37c8b1930adc121a7231
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Verificar o uso de serviços gratuitos incluídos em sua conta gratuita do Azure 

Você não será cobrado por serviços incluídos gratuitamente em sua conta gratuita do Azure, a menos que exceda os limites desses serviços. Para permanecer dentro dos limites, você pode usar o Portal do Azure ou o arquivo de uso para monitorar e acompanhar o uso dos serviços gratuitos. 

## <a name="check-usage-on-the-azure-portal"></a>Verificar o uso no Portal do Azure

1.  Faça logon no [Portal do Azure]( http://portal.azure.com).

2.  Na parte inferior da navegação à esquerda, selecione **Mais serviços**.

3.  Selecione **Assinaturas**.

4.  Selecione a assinatura que você criou quando se inscreveu na conta gratuita.

    ![Captura de tela que mostra todas as assinaturas](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  A seção de visão geral mostra informações essenciais sobre sua assinatura, como a ID de assinatura, o tipo de oferta e o nome da assinatura. Você também pode encontrar informações sobre quando seu crédito de conta gratuita deve expirar.

    ![Captura de tela que mostra informações essenciais da assinatura](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Role para baixo para localizar informações sobre o custo atual e previsto. O custo inclui o uso de serviços não incluídos em sua conta gratuita e o uso que excede os limites dos serviços gratuitos. 

    ![Captura de tela que mostra informações de custos da assinatura](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  A parte final da seção de visão geral tem uma tabela sobre o uso dos serviços gratuitos. 

    ![Captura de tela que mostra o uso de serviços gratuitos](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    A tabela contém as seguintes colunas:

* **Nome do medidor:** identifica a unidade de medida para o medidor sendo consumido. Para saber mais sobre o serviço de mapeamento de medidor, consulte [Entender o serviço gratuito de mapeamento de medidor](billing-understand-free-service-meter-mapping.md). 
* **Uso/Limite:** o uso e o limite para o medidor no mês atual. Você também pode encontrar essas informações na barra de status.
* **Status:** status de uso do medidor. Com base no seu padrão de uso, você poderá ter um desses status.
  * **Não está em uso:** você não usou o medidor ou o uso desse medidor não alcançou o sistema de cobrança.
  * **Excedido em \<Data>:** você excedeu o limite do medidor em \<Data>.
  * **Provavelmente não excederá:** é improvável que você exceda o limite do medidor.
  * **Excede em \<Data>:** você provavelmente excederá o limite do medidor em \<Data>.


## <a name="check-usage-through-the-usage-file"></a>Verificar o uso por meio do arquivo de uso

O arquivo de uso fornece informações granulares sobre sua assinatura do Azure. Você pode baixar o arquivo de uso mensal e diário no Centro de Contas do Azure. Para saber como baixar o arquivo de uso e entender o acesso necessário, consulte [Obter fatura e uso](billing-download-azure-invoice-daily-usage-date.md). Para saber mais sobre as colunas no arquivo de uso, consulte [Compreender os termos na sua utilização](billing-understand-your-usage.md). 

O arquivo de uso contém informações sobre o uso de serviços gratuitos e pagos. Os medidores de serviços gratuitos teriam **Gratuito** acrescentado ao final do nome do medidor. Para localizar os medidores gratuitos, abra o arquivo no Excel e filtre a **coluna Categoria do Medidor** para as células que contenham o texto **– Gratuito** (use Filtros de Texto &rarr; filtro Contém) &nbsp;

![Captura de tela que mostra o uso de serviços gratuitos](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)


## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
