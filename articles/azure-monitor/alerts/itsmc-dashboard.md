---
title: Investigar erros usando o painel do ITSMC
description: Saiba como usar o painel de Conector de Gerenciamento de Serviços de TI para investigar erros.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 5e1acf422abf487edda3e871fa99d07212c59b3a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039522"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Investigar erros usando o painel do ITSMC

Este artigo contém informações sobre o painel de Conector de Gerenciamento de Serviços de TI (ITSMC). O painel ajuda você a investigar o status do conector.

## <a name="view-errors"></a>Exibir erros

Para exibir erros no painel:

1. Selecione **todos os recursos** e, em seguida, localize **a assistência (*seu nome de espaço de trabalho*)**.

   ![Captura de tela que mostra os recursos nos serviços do Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

2. Em **fontes de dados do espaço de trabalho** no painel esquerdo, selecione **conexões de ITSM**:

   ![Captura de tela que mostra a seleção de conexões ITSM em fontes de dados do local de trabalho.](media/itsmc-overview/add-new-itsm-connection.png)

3. Em **Resumo**, na área **conector de gerenciamento de serviços de ti** , selecione **Exibir Resumo**:

   ![Captura de tela que mostra o botão Exibir Resumo.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. Quando um gráfico for exibido na área de **conector de gerenciamento de serviços de ti** , selecione-o:

   ![Captura de tela que mostra a seleção de um grafo.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. O painel é exibido. Use-o para examinar o status e os erros em seu conector.
   
   ![Captura de tela que mostra o status do conector no painel.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Entender os elementos do Dashboard

O painel contém informações sobre os alertas que foram enviados para a ferramenta ITSM por meio desse conector. O painel é dividido em quatro partes.

### <a name="created-work-items"></a>Itens de trabalho criados 

Na área **itens de trabalho criados** , o grafo e a tabela abaixo contêm a contagem dos itens de trabalho por tipo. Se você selecionar o grafo ou a tabela, poderá ver mais detalhes sobre os itens de trabalho.

![Captura de tela que mostra um item de trabalho criado.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)

### <a name="affected-computers"></a>Computadores afetados 

Na área **computadores afetados** , a tabela lista os computadores e seus itens de trabalho associados. Ao selecionar linhas nas tabelas, você pode obter mais detalhes sobre os computadores.

A tabela contém um número limitado de linhas. Se você quiser ver todas as linhas, selecione **ver tudo**.

![Captura de tela que mostra os computadores afetados.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)

### <a name="connector-status"></a>Status do conector 

Na área **status do conector** , o grafo e a tabela abaixo contêm mensagens sobre o status do conector. Ao selecionar o grafo ou as linhas na tabela, você pode obter mais detalhes sobre as mensagens.

A tabela contém um número limitado de linhas. Se você quiser ver todas as linhas, selecione **ver tudo**.

Para saber mais sobre as mensagens na tabela, consulte [Este artigo](itsmc-dashboard-errors.md).

![Captura de tela que mostra o status do conector.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)

### <a name="alert-rules"></a>Regras de alerta 

Na área **regras de alerta** , a tabela contém informações sobre o número de regras de alerta que foram detectadas. Selecionando linhas na tabela, você pode obter mais detalhes sobre as regras detectadas.
    
A tabela contém um número limitado de linhas. Se você quiser ver todas as linhas, selecione **ver tudo**.

![Captura de tela que mostra as regras de alerta.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)
