---
title: Agendamento de trabalhos
description: Use o agendamento de trabalhos para gerenciar tarefas.
ms.topic: how-to
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: 4661c9fc22868870af147998467c9f355f30580e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219665"
---
# <a name="schedule-jobs-for-efficiency"></a>Agendar trabalhos para eficiência

O agendamento de trabalhos em lotes permite priorizar os trabalhos que você deseja executar primeiro, levando em conta as tarefas que têm dependências em outras tarefas. Quando você agenda os trabalhos, se certifica de usar a quantidade mínima de recursos. Os nós podem ser descomissionados quando não forem necessários; as tarefas que dependem de outras tarefas são criadas a tempo, otimizando os fluxos de trabalho. Apenas um trabalho por vez é executado. Um novo não é iniciado até que o anterior seja concluído. Você pode definir seu trabalho como preenchimento automático. 

## <a name="benefit-of-job-scheduling"></a>Benefício do agendamento de trabalho

O benefício do agendamento de trabalhos é que você pode especificar um agendamento para a criação de trabalhos. As tarefas que você agenda usando a tarefa do gerenciador de trabalhos são associadas a um trabalho. A tarefa do gerenciador de trabalhos criará tarefas para o trabalho. Para fazer isso, a tarefa do gerenciador de trabalhos precisa ser autenticada na conta do Lote. Use o token de acesso AZ_BATCH_AUTHENTICATION_TOKEN. O token permitirá o acesso ao restante do trabalho. 

## <a name="use-the-portal-to-schedule-a-job"></a>Usar o portal para agendar um trabalho

   1. Entre no [Portal do Azure](https://portal.azure.com/).

   2. Selecione a conta do Lote na qual você deseja agendar trabalhos.

   3. Selecione **Adicionar** para criar um novo agendamento de trabalho e preencher o **formulário Básico**.



![Agendar um trabalho][1]

**ID de agendamento de trabalho**: O identificador exclusivo desse agendamento de trabalho.

**Nome de exibição**: O nome para exibição do trabalho não precisa ser exclusivo, mas tem um comprimento máximo de 1024 caracteres.

**Não executar até**: Especifica a primeira vez em que o trabalho será executado. Se você não definir isso, o agendamento ficará pronto para executar trabalhos imediatamente.

**Não executar após**: Nenhum trabalho é executado após a hora definida aqui. Se você não especificar uma hora, estará criando um agendamento de trabalho recorrente, que permanece ativo até que você o finalize explicitamente.

**Intervalo de recorrência**: Você pode especificar o intervalo de tempo entre os trabalhos. É possível agendar apenas um trabalho por vez. Portanto, se for o momento de criar um novo trabalho em um agendamento de trabalho, mas o trabalho anterior ainda estiver em execução, o serviço de Lote não criará o novo trabalho até que o trabalho anterior seja concluído.  

**Janela de início**: Aqui você especifica o intervalo de tempo, do momento em que o agendamento indica quando um trabalho deve ser criado até quando ele deve ser concluído. Se o trabalho atual não for concluído durante a janela, o trabalho seguinte não será iniciado.

Na parte inferior do formulário básico, você especificará o pool no qual deseja que o trabalho seja executado. Para localizar as informações de ID do pool, selecione **Atualizar**. 

![Especificar pool][2]


**ID do pool**: Identifique o pool no qual o trabalho será executado.

**Tarefa de configuração do trabalho**: Selecione **Atualizar** para nomear a tarefa do gerenciador de trabalhos, bem como as tarefas de preparação e liberação do trabalho, se você as estiver usando.

**Prioridade**: Dê um nível de prioridade ao trabalho.

**Tempo máximo**: Defina a quantidade máxima de tempo de execução do trabalho. Se ele não for concluído dentro do período, o Lote encerrará o trabalho. Se você não definir isso, não haverá limite de tempo para o trabalho.

**Contagem máx. de novas tentativas de tarefas**: Especifique o número de vezes que uma tarefa pode ser repetida até um máximo de quatro vezes. Isso não é o mesmo que o número de repetições que uma chamada à API pode ter.

**Quando todas as tarefas estão concluídas**: O padrão é nenhuma ação.

**Quando uma tarefa falha**: O padrão é nenhuma ação. Uma tarefa falhará se o número de tentativas for esgotado ou se houver um erro ao iniciar a tarefa. 

Depois de selecionar **Salvar**, se você acessar **Agendamentos de trabalho** no painel de navegação esquerdo, poderá acompanhar a execução do trabalho. Para isso, selecione **Informações de execução**.


## <a name="for-more-information"></a>Para obter mais informações

Para gerenciar um trabalho usando o CLI do Azure, consulte [Agendamento de trabalhos do Lote do Azure](/cli/azure/batch/job-schedule).

## <a name="next-steps"></a>Próximas etapas

[Crie dependências de tarefas para executar tarefas que dependam de outras tarefas](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add-job-schedule-02.png
[2]: ./media/batch-job-schedule/add-job-schedule-03.png


