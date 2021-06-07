---
title: Tutorial – agendar uma tarefa ACR
description: Neste tutorial, saiba como executar uma tarefa de registro de contêiner do Azure em um agendamento definido definindo um ou mais gatilhos de temporizador
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 13a4ccac4ea97538583c1c063a6dc61e4d25686a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96030604"
---
# <a name="tutorial-run-an-acr-task-on-a-defined-schedule"></a>Tutorial: executar uma tarefa ACR em um agendamento definido

Este tutorial mostra como executar uma [tarefa ACR](container-registry-tasks-overview.md) em uma agenda. Agende uma tarefa Configurando um ou mais *gatilhos de temporizador*. Os gatilhos de temporizador podem ser usados sozinhos ou em combinação com outros gatilhos de tarefa.

Neste tutorial, saiba mais sobre o agendamento de tarefas e:

> [!div class="checklist"]
> * Criar uma tarefa com um gatilho de temporizador
> * Gerenciar gatilhos de temporizador

O agendamento de uma tarefa é útil para cenários como o seguinte:

* Execute uma carga de trabalho de contêiner para operações de manutenção agendadas. Por exemplo, execute um aplicativo em contêiner para remover imagens desnecessárias do registro.
* Execute um conjunto de testes em uma imagem de produção durante o workday como parte de seu monitoramento de site ativo.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="about-scheduling-a-task"></a>Sobre o agendamento de uma tarefa

* **Gatilho com expressão cron** – o gatilho de temporizador para uma tarefa usa uma *expressão cron*. A expressão é uma cadeia de caracteres com cinco campos especificando o minuto, a hora, o dia, o mês e o dia da semana para disparar a tarefa. Há suporte para frequências de até uma vez por minuto.

  Por exemplo, a expressão `"0 12 * * Mon-Fri"` dispara uma tarefa às 12h UTC em cada dia da semana. Consulte os [detalhes](#cron-expressions) mais adiante neste artigo.
* **Vários gatilhos de temporizador** -a adição de vários temporizadores a uma tarefa é permitida, desde que as agendas sejam diferentes.
    * Especifique vários gatilhos de temporizador ao criar a tarefa ou adicione-os mais tarde.
    * Opcionalmente, nomeie os gatilhos para facilitar o gerenciamento, ou as tarefas ACR fornecerão nomes de gatilho padrão.
    * Se os agendamentos de temporizador se sobrepõem por vez, as tarefas ACR disparam a tarefa no horário agendado para cada temporizador.
* **Outros gatilhos de tarefa** – em uma tarefa disparada por temporizador, você também pode habilitar gatilhos com base na [confirmação do código-fonte](container-registry-tutorial-build-task.md) ou em [atualizações da imagem base](container-registry-tutorial-base-image-update.md). Como outras tarefas de ACR, você também pode [executar manualmente][az-acr-task-run] uma tarefa agendada.

## <a name="create-a-task-with-a-timer-trigger"></a>Criar uma tarefa com um gatilho de temporizador

### <a name="task-command"></a>Comando de tarefa

Primeiro, preencha a seguinte variável de ambiente do shell com um valor apropriado para seu ambiente. Esta etapa não é estritamente necessária, mas torna a execução dos comandos da CLI do Azure com várias linhas neste tutorial um pouco mais fácil. Se você não preencher a variável de ambiente, será necessário substituir manualmente cada valor sempre que ele aparecer nos comandos de exemplo.

[![Inserir inicialização](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
```

Ao criar uma tarefa com o comando [AZ ACR Task Create][az-acr-task-create] , você pode opcionalmente adicionar um gatilho de temporizador. Adicione o `--schedule` parâmetro e passe uma expressão cron para o temporizador.

Como um exemplo simples, a tarefa a seguir dispara a execução da `hello-world` imagem do registro de contêiner da Microsoft todos os dias às 21:00 UTC. A tarefa é executada sem um contexto de código-fonte.

```azurecli
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd mcr.microsoft.com/hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Execute o comando [az acr task show][az-acr-task-show] para ver se o gatilho do temporizador está configurado. Por padrão, o gatilho de atualização da imagem base também está habilitado.

```azurecli
az acr task show --name timertask --registry $ACR_NAME --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
timertask linux       Enabled                           BASE_IMAGE, TIMER
```

## <a name="trigger-the-task"></a>Disparar a tarefa

Dispare a tarefa manualmente com [AZ ACR tarefa executada][az-acr-task-run] para garantir que ela esteja configurada corretamente:

```azurecli
az acr task run --name timertask --registry $ACR_NAME
```

Se o contêiner for executado com êxito, a saída será semelhante à seguinte. A saída foi condensada para mostrar as principais etapas

```output
Queued a run with ID: cf2a
Waiting for an agent...
2020/11/20 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2020/11/20 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2020/11/20 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Após a hora agendada, execute o comando [AZ ACR Task List-executes][az-acr-task-list-runs] para verificar se o temporizador disparou a tarefa como esperado:

```azurecli
az acr task list-runs --name timertask --registry $ACR_NAME --output table
```

Quando o temporizador for bem-sucedido, a saída será semelhante ao seguinte:

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
ca15      timertask  linux       Succeeded  Timer      2020-11-20T21:00:23Z  00:00:06
ca14      timertask  linux       Succeeded  Manual     2020-11-20T20:53:35Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Gerenciar gatilhos de temporizador

Use os comandos do [temporizador de tarefa AZ ACR][az-acr-task-timer] para gerenciar os gatilhos de temporizador de uma tarefa ACR.

### <a name="add-or-update-a-timer-trigger"></a>Adicionar ou atualizar um gatilho de temporizador

Depois que uma tarefa é criada, adicione opcionalmente um gatilho de temporizador usando o comando [AZ ACR Task cronômetro Add][az-acr-task-timer-add] . O exemplo a seguir adiciona um nome de gatilho de temporizador *timer2* a *TimerTask* criado anteriormente. Esse temporizador dispara a tarefa todos os dias às 10:30 UTC.

```azurecli
az acr task timer add \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Atualize o agendamento de um gatilho existente ou altere seu status usando o comando [AZ ACR Task timer Update][az-acr-task-timer-update] . Por exemplo, atualize o gatilho chamado *timer2* para disparar a tarefa em 11:30 UTC:

```azurecli
az acr task timer update \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Listar gatilhos de timer

O comando [AZ ACR Task timer List][az-acr-task-timer-list] mostra os gatilhos de temporizador configurados para uma tarefa:

```azurecli
az acr task timer list --name timertask --registry $ACR_NAME
```

Saída de exemplo:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Remover um gatilho de temporizador

Use o comando [AZ ACR Task timer remove][az-acr-task-timer-remove] para remover um gatilho de temporizador de uma tarefa. O exemplo a seguir remove o gatilho *timer2* de *TimerTask*:

```azurecli
az acr task timer remove \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Expressões cron

As tarefas ACR usam a biblioteca [NCronTab](https://github.com/atifaziz/NCrontab) para interpretar expressões cron. As expressões com suporte em tarefas ACR têm cinco campos obrigatórios separados por espaço em branco:

`{minute} {hour} {day} {month} {day-of-week}`

O fuso horário usado com as expressões cron é UTC (tempo Universal Coordenado). As horas estão no formato de 24 horas.

> [!NOTE]
> As tarefas de ACR não dão suporte ao `{second}` `{year}` campo ou em expressões cron. Se você copiar uma expressão cron usada em outro sistema, certifique-se de remover esses campos, se eles forem usados.

Cada campo pode ter um dos seguintes tipos de valores:

|Type  |Exemplo  |Quando disparado  |
|---------|---------|---------|
|Um valor específico |<nobr>`"5 * * * *"`</nobr>|a cada hora às 5 minutos após a hora|
|Todos os valores (`*`)|<nobr>`"* 5 * * *"`</nobr>|a cada minuto da hora começando em 5:00 UTC (60 vezes por dia)|
|Um intervalo (`-` operador)|<nobr>`"0 1-3 * * *"`</nobr>|3 vezes por dia, às 1:00, 2:00 e 3:00 UTC|
|Um conjunto de valores (`,` operador)|<nobr>`"20,30,40 * * * *"`</nobr>|3 vezes por hora, a 20 minutos, 30 minutos e 40 minutos após a hora|
|Um valor de intervalo (`/` operador)|<nobr>`"*/10 * * * *"`</nobr>|6 vezes por hora, em 10 minutos, 20 minutos e assim por diante, após a hora

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Exemplos de cron

|Exemplo|Quando disparado  |
|---------|---------|
|`"*/5 * * * *"`|uma vez a cada cinco minutos|
|`"0 * * * *"`|uma vez a cada hora|
|`"0 */2 * * *"`|uma vez a cada duas horas|
|`"0 9-17 * * *"`|uma vez a cada hora, de 9:00 a 17:00 UTC|
|`"30 9 * * *"`|às 9:30 UTC todos os dias|
|`"30 9 * * 1-5"`|às 9:30 UTC a cada dia da semana|
|`"30 9 * Jan Mon"`|às 9:30 UTC a cada segunda-feira em janeiro|

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover todos os recursos que você criou nesta série de tutoriais, incluindo o registro de contêiner ou registros, a instância de contêiner, o cofre de chaves e a entidade de serviço, emita os seguintes comandos:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar tarefas de registro de contêiner do Azure que são disparadas automaticamente por um temporizador. 

Para obter um exemplo de como usar uma tarefa agendada para limpar repositórios em um registro, consulte [limpar automaticamente as imagens de um registro de contêiner do Azure](container-registry-auto-purge.md).

Para obter exemplos de tarefas disparadas por confirmações de código-fonte ou atualizações de imagem de base, consulte outros artigos na [série de tutoriais de tarefas do ACR](container-registry-tutorial-quick-task.md).



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
