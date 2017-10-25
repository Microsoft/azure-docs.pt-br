---
title: Criar alertas no OMS Log Analytics | Microsoft Docs
description: "Alertas no Log Analytics identificam informações importantes em seu repositório do OMS e podem notificar proativamente problemas ou invocar ações para tentar corrigi-los.  Este artigo descreve como criar uma regra de alerta e detalha as diferentes ações que elas podem executar."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2017
ms.author: bwren
ms.openlocfilehash: c34fb7295e8f386f0e7cf2c1db6b26a3e49eae98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-alert-rules-in-log-analytics"></a>Trabalhar com regras de alerta no Log Analytics
Os alertas são criados por regras de alerta que executam pesquisas de log automaticamente em intervalos regulares.  Eles criarão um registro de alerta se os resultados corresponderem a critérios específicos.  A regra pode então executar automaticamente uma ou mais ações para notificar você proativamente do alerta ou invocar outro processo.   

Este artigo descreve os processos para criar e editar regras de alerta usando o portal do OMS.  Para obter detalhes sobre as diferentes configurações e como implementar a lógica necessária, confira [Noções básicas sobre alertas de Log Analytics](log-analytics-alerts.md).

>[!NOTE]
> Atualmente, não é possível criar nem modificar uma regra de alerta usando o Portal do Azure. 

## <a name="create-an-alert-rule"></a>Criar uma regra de alerta

Para criar uma regra de alerta usando o portal do OMS, você começa criando uma pesquisa de log para os registros que devem invocar o alerta.  O botão **alerta** estará disponível para que você possa criar e configurar a regra de alerta.

>[!NOTE]
> Atualmente, podem ser criadas no máximo 250 regras de alerta em um espaço de trabalho do OMS. 

1. Na página de Visão Geral do OMS, clique em **Pesquisa de Log**.
2. Crie uma nova consulta de pesquisa de log ou selecione uma pesquisa de log salva. 
3. Clique em **Alerta** na parte superior da página para abrir a tela **Adicionar Regra de Alerta**.
4. Configure a regra de alerta usando as informações em [Detalhes de regras de alerta](#details-of-alert-rules) abaixo.
6. Clique em **Salvar** para concluir a regra de alerta.  Ela começa a ser executada imediatamente.


## <a name="edit-an-alert-rule"></a>Editar uma regra de alerta
Você pode obter uma lista de todas as regras de alerta no menu **Alertas** nas Configurações do **Log Analytics**.  

![Gerenciar alertas](./media/log-analytics-alerts/configure.png)

1. No console do OMS, selecione o bloco **Configurações** .
2. Selecione **Alertas**.

Você pode executar várias ações nessa exibição.

* Desabilite uma regra selecionando **Desativar** ao lado dela.
* Edite uma regra de alerta clicando no ícone de lápis ao lado dela.
* Remova uma regra de alerta clicando no ícone de **X** ao lado dela. 

## <a name="details-of-alert-rules"></a>Detalhes de regras de alerta
Ao criar ou editar uma regra de alerta no portal do OMS, você trabalha com a página **Adicionar Regra de Alerta** ou **Editar Regra de Alerta**.  As tabelas a seguir descrevem os campos nesta tela.

![Regra de Alerta](media/log-analytics-alerts/add-alert-rule.png)

### <a name="alert-information"></a>Informações de alerta
Essas são as configurações básicas para a regra de alerta e os alertas que ela cria.

| Propriedade | Descrição |
|:--- |:---|
| Nome | Nome exclusivo para identificar a regra de alerta. Esse nome é incluído em todos os alertas criados pela regra.  |
| Descrição | Descrição opcional da regra de alerta. |
| Severity |Gravidade dos alertas criados por essa regra. |

### <a name="search-query-and-time-window"></a>Consulta de pesquisa e janela de tempo
A consulta de pesquisa e a janela de tempo que retornam os registros que estão sendo avaliados para determinar se devem ser criado alertas.

| Propriedade | Descrição |
|:--- |:---|
| Consulta de pesquisa | Essa é a consulta que será executada.  Os registros retornados por essa consulta serão usados para determinar se um alerta é criado.<br><br>Selecione **Usar a consulta de pesquisa atual** para usar a consulta atual ou escolher uma pesquisa salva existente da lista.  A sintaxe da consulta é fornecida na caixa de texto, na qual você pode modificá-la, se necessário. |
| Janela de tempo |Especifica o intervalo de tempo para a consulta.  A consulta retorna somente os registros que foram criados dentro desse intervalo de tempo atual.  Este pode ser qualquer valor entre 5 minutos e 24 horas.  Ele deve ser maior que ou igual à frequência de alerta.  <br><br> Por exemplo, se a janela de tempo está definida para 60 minutos e a consulta é executada às 13:15h, somente os registros criados entre 12:15h e 13:15h serão retornados. |

Quando você fornece a janela de tempo para a regra de alerta, o número de registros existentes que correspondem aos critérios de pesquisa para a janela de tempo será exibido.  Isso pode ajudar a determinar a frequência com que o número de resultados que você espera será fornecido.

### <a name="schedule"></a>Agenda
Define a frequência com que a consulta de pesquisa é executada.

| Propriedade | Descrição |
|:--- |:---|
| Frequência de alerta | Especifica a frequência com que a consulta deve ser executada. Pode ser qualquer valor entre 5 minutos e 24 horas. Deve ser igual a ou menor que a janela de tempo.  Se o valor for maior que a janela de tempo, haverá o risco de que registros sejam perdidos.<br><br>Por exemplo, considere uma janela de tempo de 30 minutos e uma frequência de 60 minutos.  Se a consulta for executada à 1:00, retornará registros entre 12:30 e 1:00.  A próxima vez em que a consulta será executada é às 2:00, quando ela retornará registros entre 1:30 e 2:00.  Todos os registros criados entre 1:00 e 1:30 nunca seriam avaliados. |


### <a name="generate-alert-based-on"></a>Gerar alerta com base em
Define os critérios que serão avaliados nos resultados da consulta de pesquisa a fim de determinar se um alerta deve ser criado.  Esses detalhes serão diferentes dependendo do tipo de regra de alerta que você selecionar.  Obtenha detalhes sobre os tipos de regra de alerta diferentes em [Noções básicas sobre alertas no Log Analytics](log-analytics-alerts.md).

| Propriedade | Descrição |
|:--- |:---|
| Suprimir alertas | Quando você ativa a supressão da regra de alerta, as ações da regra são desabilitadas por um período definido depois de criar um novo alerta. A regra ainda estará em execução e criará registros de alerta se os critérios forem atendidos. Isso serve para permitir que você tenha tempo de corrigir o problema sem executar ações duplicadas. |

#### <a name="number-of-results-alert-rules"></a>Número de regras de alerta resultantes

| Propriedade | Descrição |
|:--- |:---|
| Número de resultados |Um alerta será criado se o número de registros retornados pela consulta for **maior que** ou **menor que** o valor que você fornecer.  |

#### <a name="metric-measurement-alert-rules"></a>Regras de alerta com medição métrica

| Propriedade | Descrição |
|:--- |:---|
| Valor de agregação | Valor limite que cada valor de agregação nos resultados deve exceder para ser considerado uma violação. |
| Disparar alerta com base em | O número de violações para um alerta ser criado.  Especifique **Total de violações** para qualquer combinação de violações no conjunto de resultados ou **Violações consecutivas** para exigir que as violações devam ocorrer em amostras consecutivas. |

### <a name="actions"></a>Ações
Regras de alerta sempre criarão um [registro de alerta](#alert-records) quando o limite for atingido.  Você também pode definir uma ou mais respostas a serem executadas, como enviar um email ou iniciar um runbook.



#### <a name="email-actions"></a>Ações de email
Ações de email enviam um email com os detalhes do alerta para um ou mais destinatários.

| Propriedade | Descrição |
|:--- |:---|
| Notificação por email |Especifique **Sim** se desejar que um email seja enviado quando o alerta for disparado. |
| Assunto |Assunto do email.  Não é possível modificar o corpo do email. |
| Destinatários |Endereços de todos os destinatários de email.  Se você especificar mais de um endereço, separe os endereços com ponto e vírgula (;). |

#### <a name="webhook-actions"></a>Ações de Webhook
Ações de Webhook permitem invocar um processo externo por meio de uma única solicitação HTTP POST.

| Propriedade | Descrição |
|:--- |:---|
| webhook |Especifique **Sim** se desejar chamar um webhook quando o alerta for disparado. |
| URL de Webhook |A URL do webhook. |
| Incluir a carga JSON personalizada |Selecione esta opção se você deseja substituir a carga padrão por uma carga personalizada. |
| Insira sua carga JSON personalizada |A carga personalizada para o webhook.  Consulte a seção anterior para ver os detalhes. |

#### <a name="runbook-actions"></a>Ações de runbook
Ações de runbook iniciam um runbook na Automação do Azure. 

>[!NOTE]
> Você deve ter a solução de Automação instalada no espaço de trabalho para que essa ação seja habilitada. 


| Propriedade | Descrição |
|:--- |:---|
| Runbook | Especifique **Sim** se desejar iniciar um runbook da Automação do Azure quando o alerta for disparado.  |
| Conta de automação | Especifica a conta de Automação da qual os runbooks são selecionados.  Essa é a conta de Ação que está vinculada ao espaço de trabalho. |
| Selecionar um runbook | Selecione o runbook que você deseja iniciar quando um alerta for criado. |
| Executar em | Selecione **Azure** para executar o runbook na nuvem.  Selecione **Hybrid worker** para executar o runbook em um agente com [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md ) instalado.  |




## <a name="next-steps"></a>Próximas etapas
* Instale a [solução de Gerenciamento de Alertas](log-analytics-solution-alert-management.md) para analisar os alertas criados no Log Analytics junto com aqueles coletados do SCOM (System Center Operations Manager).
* Leia mais sobre [pesquisas de log](log-analytics-log-searches.md) que podem gerar alertas.
* Conclua um passo a passo para [configurar um webhook](log-analytics-alerts-webhooks.md) com uma regra de alerta.  
* Aprenda a criar [runbooks na Automação do Azure](https://azure.microsoft.com/documentation/services/automation) para corrigir os problemas identificados por alertas.

