---
title: Ajustar cotas e limites no Azure Data Lake Analytics
description: Saiba como ajustar e aumentar cotas e limites em contas do ADLA (Azure Data Lake Analytics).
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 03/15/2018
ms.openlocfilehash: 416b77fd86316acb8098531257e12b591a0ff052
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710168"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Ajustar cotas e limites no Azure Data Lake Analytics

Saiba como ajustar e aumentar as cotas e os limites em contas do ADLA (Azure Data Lake Analytics). Conhecer esses limites ajudará você a entender seu comportamento de trabalho do U-SQL. Todos os limites de cota são flexíveis, portanto, é possível aumentar os limites máximos, contatando o suporte do Azure.

## <a name="azure-subscriptions-limits"></a>Limites das assinaturas do Azure

**Número máximo de contas do ADLA por assinatura por região:** 5

Você recebe o erro "Você atingiu o número máximo de contas do Data Lake Analytics permitidas (5) na região sob o nome da assinatura" ao tentar criar a sexta conta do ADLA.

Se você deseja ultrapassar esse limite, tente estas opções:

- escolha outra região, se adequado
- entre em contato com o suporte do Azure [abrindo um tíquete de suporte](#increase-maximum-quota-limits) para solicitar um aumento de cota.

## <a name="default-adla-account-limits"></a>Limites padrão da conta do ADLA

**Número máximo de unidades de análise (AUS) por conta:** 250, padrão 32

Esse é o número máximo de AUs que podem ser executadas simultaneamente em sua conta. Se o número total de AUs em execução em todos os trabalhos exceder esse limite, novos trabalhos serão colocados na fila automaticamente. Por exemplo:

- Caso você tenha um só trabalho em execução com 32 AUs, ao enviar um segundo trabalho, ele permanecerá na fila até que o primeiro trabalho seja concluído.
- Se você já tiver quatro trabalhos em execução e cada um deles estiver usando 8 AUs, ao adicionar um quinto trabalho que precise de 8 AUs, ele permanecerá na fila até que 8 AUs fiquem disponíveis.

    ![Azure Data Lake Analytics a página limites e cotas](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Número máximo de unidades de análise (AUS) por trabalho:** 250, padrão 32

Esse é o número máximo de AUs que cada trabalho individual pode ser atribuído em sua conta. Os trabalhos que recebem mais do que esse limite serão rejeitados, a menos que o remetente seja afetado por uma política de computação (limite de envio de trabalho) que forneça uma quantidade maior de AUs por trabalho. O limite superior desse valor é o limite de AU da conta.

**Número máximo de trabalhos U-SQL simultâneos por conta:** 20

Esse é o número máximo de trabalhos que podem ser executados simultaneamente em sua conta. Exceder esse valor faz com que os trabalhos mais recentes sejam enfileirados automaticamente.

## <a name="adjust-adla-account-limits"></a>Ajustar os limites da conta do ADLA

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Escolha uma conta ADLA que você já criou.
3. Clique em **Propriedades**.
4. Ajuste os valores de **Número máximo de AUs**, **Número máximo de trabalhos em execução** e **Limites de envio de trabalho** de acordo com suas necessidades.

## <a name="increase-maximum-quota-limits"></a>Aumente os limites máximos de cota

Encontre mais informações sobre os limites do Azure na [documentação sobre os limites específicos do serviço do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Abra uma solicitação de suporte no Portal do Azure.

   ![Portal de Azure Data Lake Analytics-ajuda e suporte](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

   ![Nova solicitação de suporte do portal Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)

2. Selecione o tipo de problema **Cota**.

3. Selecione sua **assinatura** (verifique se ela não é uma assinatura de "avaliação").

4. Selecione o tipo de cota **Data Lake Analytics**.

   ![Tipo de cota de solicitação de suporte de Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Na página do problema, explique seu limite de aumento solicitado com **Detalhes**, informando o motivo da necessidade dessa capacidade extra.

   ![Detalhes da solicitação de suporte do Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Verifique suas informações de contato e crie a solicitação de suporte.

A Microsoft examina sua solicitação e tenta acomodar suas necessidades de negócios assim que possível.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da Análise Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Gerenciar a Análise Azure Data Lake usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
- [Monitorar e solucionar problemas de trabalhos do Azure Data Lake Analytics usando portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
