---
title: Monitorar e acompanhar o uso do serviço gratuito do Azure
description: Saiba como verificar o uso do serviço gratuito no arquivo CSV portal e o uso do Azure.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3543bed7f699fd149ca7f2a6f61e9eb5aad5f1a3
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491418"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Verificar o uso do serviço gratuito incluído com sua conta gratuita do Azure

Você não seja cobrado pelos serviços incluídos gratuitamente com uma conta gratuita do Azure, a menos que você exceda os limites dos serviços. Para permanecer em limites, você pode usar o portal do Azure ou o arquivo de uso para monitorar e acompanhar o uso do serviço gratuito.

## <a name="check-usage-in-the-azure-portal"></a>Verificar o uso no portal do Azure

1.  Entre no [Portal do Azure](https://portal.azure.com).

2.  Na área de navegação à esquerda, selecione **todos os serviços**.

3.  Selecione **Assinaturas**.

4.  Selecione a assinatura que você criou quando se inscreveu na conta gratuita.

    ![Captura de tela que mostra todas as assinaturas](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  A seção Visão geral mostra informações essenciais sobre sua assinatura. Por exemplo, ID da assinatura, tipo de oferta e o nome da assinatura. Você também pode encontrar informações quando seu crédito da conta gratuita expira.

    ![Captura de tela que mostra informações essenciais da assinatura](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Role para baixo para localizar informações sobre seus custos atuais e previstas. O custo inclui o uso de serviço não está incluído com sua conta gratuita e o uso exceder os limites de serviços gratuitos.

    ![Captura de tela que mostra informações de custos da assinatura](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  A parte final da seção de visão geral tem uma tabela que mostra o uso do serviço gratuito.

    ![Captura de tela que mostra o uso de serviços gratuitos](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    A tabela tem as seguintes colunas:

* **Nome do medidor:** identifica a unidade de medida para o medidor sendo consumido. Para saber mais sobre o serviço de mapeamento de medidor, confira [Entender o serviço gratuito de mapeamento de medidor](billing-understand-free-service-meter-mapping.md).
* **Uso/Limite:** uso e o limite para o medidor no mês atual. Você também pode encontrar essas informações na barra de status.
* **Status:** status de uso do medidor. Com base no seu padrão de uso, você pode ter um dos status a seguir:
  * **Não está em uso:** você não usou o medidor ou o uso desse medidor não alcançou o sistema de cobrança.
  * **Excedido em \<Data>:** você excedeu o limite do medidor em \<Data>.
  * **Provavelmente não excederá:** é improvável que você exceda o limite do medidor.
  * **Excede em \<Data>:** é improvável que você exceda o limite do medidor em \<Data>.

## <a name="check-usage-with-the-usage-file"></a>Verifique o uso com o arquivo de uso

O arquivo de uso fornece informações detalhadas para sua assinatura do Azure. Você pode baixar o arquivo de uso mensal e diário do Centro de contas do Azure. Para saber como baixar o arquivo de uso e entender o acesso necessário, consulte [Obter fatura e uso](billing-download-azure-invoice-daily-usage-date.md). Para saber mais sobre as colunas no arquivo de uso, consulte [Compreender os termos na sua utilização](billing-understand-your-usage.md).

O arquivo de uso possui informações sobre o uso de serviços gratuitos e pagos. Os medidores de serviços gratuitos teriam **Gratuito** acrescentado ao final do nome do medidor. Para localizar os medidores gratuitos, abra o arquivo no excel e do filtro de **coluna categoria do medidor** para células que contenham texto **– gratuito** (Use filtros de texto &rarr; filtro contém).


![Captura de tela que mostra o uso de serviços gratuitos](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas
- [Atualizar sua assinatura](billing-upgrade-azure-subscription.md)
