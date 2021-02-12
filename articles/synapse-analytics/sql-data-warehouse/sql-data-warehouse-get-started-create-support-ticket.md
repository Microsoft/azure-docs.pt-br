---
title: Solicite aumentos de cota e obtenha suporte
description: Como criar uma solicitação de suporte para o Azure Synapse Analytics no portal do Azure. Solicitar aumentos de cota ou obter suporte para resoluções de problemas.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: acc05d6367a0dc4334bc1d9e3cbb340bdb8d24c0
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677412"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Solicitar aumentos de cota ou obter suporte para o Azure Synapse Analytics

Este artigo descreve como solicitar um tíquete de suporte para o Azure Synapse Analytics no portal do Azure. Esse processo permite que você solicite um aumento de cota ou envie uma solicitação de suporte técnico para a equipe de suporte de engenharia.

## <a name="create-a-support-ticket"></a>Criar um tíquete de suporte

Siga as etapas a seguir para criar uma nova solicitação de suporte para o Azure Synapse Analytics no portal do Azure.

1. No menu do [Portal do Azure](https://portal.azure.com), selecione **Ajuda + suporte**.

   ![Link de ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. Em **Ajuda + suporte**, selecione **Nova solicitação de suporte**.

    ![Criar uma solicitação de suporte](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Examinar seu [plano de suporte do Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * **Gerenciamento de assinaturas, cobrança e cotas** está disponível em todos os níveis de suporte.
   * O suporte de **reparo** é fornecido por meio do suporte para o [Desenvolvedor](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) ou [Premier](https://azure.microsoft.com/support/plans/premier/). As questões que exigem reparos são problemas vivenciados pelo cliente ao usar o Azure, em que se espera que o problema tenha sido causado pela Microsoft.
   * O **aconselhamento de desenvolvedores** e os **serviços de consultoria** estão disponíveis nos níveis de suporte [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) e [Premier](https://azure.microsoft.com/support/plans/premier/).

   Se você tem um plano de suporte Premier, também pode relatar problemas do Azure Synapse Analytics no portal do [Microsoft Premier Online](https://premier.microsoft.com/). Confira os [planos de suporte do Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) para saber mais sobre os vários planos de suporte, incluindo escopos, tempos de resposta, preços etc.  Para ver as perguntas frequentes sobre esse tipo de suporte, confira [Perguntas frequentes do suporte do Azure](https://azure.microsoft.com/support/faq/).

1. Em **Tipo de problema**, selecione o tipo apropriado. Caso o problema seja relacionado a consertos e correções, selecione **Problemas técnicos**. Para solicitações de aumento de cota, selecione **Limites de serviço e assinatura (cotas)** .

   ![Selecione um tipo de problema](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > O restante deste artigo aborda as solicitações de aumento de cota. Mas você também pode selecionar **Problemas técnicos** aqui para solicitações de suporte à resolução de problemas. Se você selecionar **Problemas técnicos**, precisará fornecer um resumo e identificar o tipo de problema escolhendo **Selecionar tipo de problema**. Você verá as opções de tipos de problemas e poderá escolher o seu. Se as soluções apresentadas não se relacionam com seu problema, selecione **Avançar:Detalhes** e preencha o formulário para enviar o tíquete de suporte.

1. Para solicitações de aumento de cota, selecione **Azure Synapse Analytics** para ver o **Tipo de cota**. Em seguida, selecione **Avançar: Soluções >>** .

   ![Selecionar um tipo de cota](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. Na janela **detalhes** , selecione **Inserir detalhes** para inserir informações adicionais.

   ![Link "fornecer detalhes"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Tipos de solicitação de cota

Selecionar **Inserir detalhes** exibe a janela **detalhes da cota** que permite que você adicione informações adicionais. As seções a seguir descrevem as diferentes solicitações de cota disponíveis para o Azure Synapse Analytics.

### <a name="synapse-sql-pool-data-warehouse-units-dwus-per-server"></a>Synapse DWUs (unidades de data warehouse do pool do SQL) por servidor

Siga as etapas a seguir para solicitar um aumento de DWUs por servidor.

1. Selecione o tipo de cota de **DWUs do pool do SQL Synapse por servidor** .

1. Selecione o **recurso** ao qual você deseja aplicar o aumento de cota usando a lista suspensa.

1. Insira sua nova cota na seção **cota de solicitação** .

1. Selecione **Salvar e continuar**.

   ![Detalhes da cota de DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)


### <a name="servers-per-subscription"></a>Servidores por assinatura

Para solicitar um aumento no número de servidores por assinatura, você precisará concluir as seguintes etapas:

1. Selecione os **SQL Servers por assinatura** como o tipo de cota.

1. Na lista de **Locais**, selecione a região do Azure a ser usada. A cota é relativa a cada região e por assinatura.

1. No campo **cota de solicitação** , insira sua solicitação para o número máximo de servidores nessa região.

   ![Detalhes de cota de servidores](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)



1. Selecione **Salvar e continuar**.

Alguns tipos de oferta não estão disponíveis em todas as regiões. Você poderá ver o seguinte erro:

![Erro de acesso à região](./media/sql-data-warehouse-get-started-create-support-ticket/region-access-error.png)

### <a name="enable-subscription-access-to-a-region"></a>Habilitar o acesso à assinatura para uma região

Para habilitar o acesso à região de uma assinatura, você precisará concluir as seguintes etapas:  

1. Selecione o tipo de cota de **acesso à região do pool do SQL Synapse (data warehouse)** .

1. Selecione a região escolhendo um **local** na lista suspensa.

1. Indique o requisito de desempenho do DWU na seção **DWU necessária** .

1. Insira sua **Descrição dos requisitos de negócios**. 

1. Selecione **Salvar e continuar**.

![Acesso à região](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-region.png)


### <a name="for-other-quota-requests"></a>Para outras solicitações de cota

Selecione **outra solicitação de cota** no menu suspenso tipo de cota para outros tipos de solicitação de cota:

![Outros detalhes da cota](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details.png)

## <a name="submit-your-request"></a>Enviar a solicitação

A etapa final é preencher os detalhes restantes da sua solicitação de suporte do Banco de Dados SQL do Microsoft Azure. Em seguida, selecione **Avançar: revisar + criar>>**.

![Examinar criar detalhes](./media/sql-data-warehouse-get-started-create-support-ticket/review-create-details.png)

Depois de examinar os detalhes da solicitação, selecione **criar** para enviar a solicitação.

![Criar tíquete](./media/sql-data-warehouse-get-started-create-support-ticket/create-ticket.png)

## <a name="monitor-a-support-ticket"></a>Monitorar um tíquete de suporte

Depois que você enviar a solicitação de suporte, a equipe de suporte do Azure entrará em contato com você. Para verificar o status e os detalhes da solicitação, selecione **todas as solicitações de suporte** no painel.

![Verificar o status](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Outros recursos

Você também pode se conectar com a Comunidade do Azure Synapse Analytics em [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) ou por meio da [página de perguntas do Microsoft Q&uma pergunta para o Azure Synapse Analytics](/answers/topics/azure-synapse-analytics.html).