---
title: Agendar tarefas do registro de contêiner do Azure
description: Defina temporizadores para executar uma tarefa de registro de contêiner do Azure em um agendamento definido.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/27/2019
ms.author: danlep
ms.openlocfilehash: 680f0268e85d41f8061dc96db1779ab6c22b944a
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310553"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Executar uma tarefa ACR em um agendamento definido

Este artigo mostra como executar uma [tarefa ACR](container-registry-tasks-overview.md) em uma agenda. Agende uma tarefa Configurando um ou mais *gatilhos*de temporizador. 

O agendamento de uma tarefa é útil para cenários como o seguinte:

* Execute uma carga de trabalho de contêiner para operações de manutenção agendadas. Por exemplo, execute um aplicativo em contêiner para remover imagens desnecessárias do registro.
* Execute um conjunto de testes em uma imagem de produção durante o workday como parte de seu monitoramento de site ativo.

Você pode usar o Azure Cloud Shell ou uma instalação local do CLI do Azure para executar os exemplos neste artigo. Se você quiser usá-lo localmente, a versão 2.0.68 ou posterior será necessária. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Sobre o agendamento de uma tarefa

* **Gatilho com expressão cron** – o gatilho de temporizador para uma tarefa usa uma *expressão cron*. A expressão é uma cadeia de caracteres com cinco campos especificando o minuto, a hora, o dia, o mês e o dia da semana para disparar a tarefa. Há suporte para frequências de até uma vez por minuto. 

  Por exemplo, a expressão `"0 12 * * Mon-Fri"` dispara uma tarefa às 12h UTC em cada dia da semana. Consulte os [detalhes](#cron-expressions) mais adiante neste artigo.
* **Vários gatilhos** de temporizador-a adição de vários temporizadores a uma tarefa é permitida, desde que as agendas sejam diferentes. 
    * Especifique vários gatilhos de temporizador ao criar a tarefa ou adicione-os mais tarde.
    * Opcionalmente, nomeie os gatilhos para facilitar o gerenciamento, ou as tarefas ACR fornecerão nomes de gatilho padrão.
    * Se os agendamentos de temporizador se sobrepõem por vez, as tarefas ACR disparam a tarefa no horário agendado para cada temporizador. 
* **Outros gatilhos de tarefa** – em uma tarefa disparada por temporizador, você também pode habilitar gatilhos com base na [confirmação do código-fonte](container-registry-tutorial-build-task.md) ou em [atualizações da imagem base](container-registry-tutorial-base-image-update.md). Assim como outras tarefas de ACR, você também pode [disparar manualmente][az-acr-task-run] uma tarefa agendada.

## <a name="create-a-task-with-a-timer-trigger"></a>Criar uma tarefa com um gatilho de temporizador

Ao criar uma tarefa com o comando [AZ ACR Task Create][az-acr-task-create] , você pode opcionalmente adicionar um gatilho de temporizador. Adicione o `--schedule` parâmetro e passe uma expressão cron para o temporizador. 

Como um exemplo simples, o comando a seguir dispara a `hello-world` execução da imagem do Hub do Docker todos os dias às 21:00 UTC. A tarefa é executada sem um contexto de código-fonte.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --context /dev/null \
  --cmd hello-world \
  --schedule "0 21 * * *"
```

Execute o comando [AZ ACR Task show][az-acr-task-show] para ver que o gatilho do temporizador está configurado. Por padrão, o gatilho de atualização da imagem base também está habilitado.

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Dispare a tarefa manualmente com [AZ ACR tarefa executada][az-acr-task-run] para garantir que ela esteja configurada corretamente:

```azurecli
az acr task run --name mytask --registry myregistry
```

Se o contêiner for executado com êxito, a saída será semelhante à seguinte:

```console
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Após a hora agendada, execute o comando [AZ ACR Task List-executes][az-acr-task-list-runs] para verificar se o temporizador disparou a tarefa como esperado:

```azurecli
az acr task list runs --name mytask --registry myregistry --output table
``` 

Quando o temporizador for bem-sucedido, a saída será semelhante ao seguinte:

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```
            
## <a name="manage-timer-triggers"></a>Gerenciar gatilhos de temporizador

Use os comandos do temporizador de [tarefa AZ ACR][az-acr-task-timer] para gerenciar os gatilhos de temporizador de uma tarefa ACR.

### <a name="add-or-update-a-timer-trigger"></a>Adicionar ou atualizar um gatilho de temporizador

Depois que uma tarefa é criada, adicione opcionalmente um gatilho de temporizador usando o comando [AZ ACR Task cronômetro Add][az-acr-task-timer-add] . O exemplo a seguir adiciona um nome de gatilho de temporizador *timer2* a MyTask criado anteriormente. Esse temporizador dispara a tarefa todos os dias às 10:30 UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Atualize o agendamento de um gatilho existente ou altere seu status usando o comando [AZ ACR Task timer Update][az-acr-task-timer-update] . Por exemplo, atualize o gatilho chamado *timer2* para disparar a tarefa em 11:30 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Listar gatilhos de timer

O comando [AZ ACR Task timer List][az-acr-task-timer-list] mostra os gatilhos de temporizador configurados para uma tarefa:

```azurecli
az acr task timer list --name mytask --registry myregistry
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

Use o comando [AZ ACR Task timer remove][az-acr-task-timer-remove] para remover um gatilho de temporizador de uma tarefa. O exemplo a seguir remove o gatilho *timer2* de MyTask:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Expressões cron

As tarefas ACR usam a biblioteca [NCronTab](https://github.com/atifaziz/NCrontab) para interpretar expressões cron. As expressões com suporte em tarefas ACR têm cinco campos obrigatórios separados por espaço em branco:

`{minute} {hour} {day} {month} {day-of-week}`

O fuso horário usado com as expressões cron é UTC (tempo Universal Coordenado). As horas estão no formato de 24 horas.

> [!NOTE]
> As tarefas de ACR não dão `{second}` suporte `{year}` ao campo ou em expressões cron. Se você copiar uma expressão cron usada em outro sistema, certifique-se de remover esses campos, se eles forem usados.

Cada campo pode ter um dos seguintes tipos de valores:

|Tipo  |Exemplo  |Quando disparado  |
|---------|---------|---------|
|Um valor específico |<nobr>"5 * * * *"</nobr>|a cada hora às 5 minutos após a hora|
|Todos os valores (`*`)|<nobr>"* 5 * * *"</nobr>|a cada minuto da hora começando em 5:00 UTC (60 vezes por dia)|
|Um intervalo (`-` operador)|<nobr>"0 1-3 * * *"</nobr>|3 vezes por dia, às 1:00, 2:00 e 3:00 UTC|  
|Um conjunto de valores (`,` operador)|<nobr>"20, 30, 40 * * * *"</nobr>|3 vezes por hora, a 20 minutos, 30 minutos e 40 minutos após a hora|
|Um valor de intervalo (`/` operador)|<nobr>"*/10 * * * *"</nobr>|6 vezes por hora, em 10 minutos, 20 minutos e assim por diante, após a hora

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


## <a name="next-steps"></a>Próximas etapas

Para obter exemplos de tarefas disparadas por confirmações de código-fonte ou atualizações de imagem de base, confira a [série de tutoriais de tarefas de ACR](container-registry-tutorial-quick-task.md).



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