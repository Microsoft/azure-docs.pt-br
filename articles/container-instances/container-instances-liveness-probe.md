---
title: Configurar investigações de atividade nas Instâncias de Contêiner do Azure
description: Saiba como configurar investigações de atividade para reiniciar contêineres não íntegros em Instâncias de Contêiner do Azure
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: 89b76fc68c113b7931894c0cf003ffd846c646ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583786"
---
# <a name="configure-liveness-probes"></a>Configurar investigações de atividade

Os aplicativos em contêineres podem ser executados por longos períodos de tempo, resultando em estados quebrados que talvez precisem ser reparados reiniciando o contêiner. As Instâncias de Contêiner do Azure dão suporte a investigações de atividade para incluir configurações de modo que o contêiner possa ser reiniciado se a funcionalidade crítica não estiver funcionando.

Este artigo explica como implantar um grupo de contêineres que inclui uma investigação de atividade, demonstrando a reinicialização automática de um contêiner não íntegro simulado.

## <a name="yaml-deployment"></a>Implantação do YAML

Crie um arquivo `liveness-probe.yaml` com o snippet a seguir. Esse arquivo define um grupo de contêineres composto por um contêiner NGNIX que eventualmente se tornará não íntegro.

```yaml
apiVersion: 2018-06-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Execute o seguinte comando para implantar esse grupo de contêineres com a configuração YAML acima:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Comando inicial

A implantação define um comando inicial que será executado quando o contêiner for iniciado pela primeira, definido pela propriedade `command` que aceita uma matriz de cadeias de caracteres. Neste exemplo, ela inicia uma sessão de bash e crie um arquivo chamado `healthy` dentro do diretório `/tmp` por meio deste comando:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Em seguida, ficará suspenso por 30 segundos antes de excluir o arquivo, depois entra em um modo de suspensão de 10 minutos.

### <a name="liveness-command"></a>Comando de atividade

Essa implantação define um `livenessProbe`, que dá suporte a um comando de atividade `exec` que atua como a verificação de atividade. Se a saída desse comando for um valor diferente de zero, o contêiner será interrompido e reiniciado, sinalizando que não foi possível encontrar o arquivo `healthy`. Se a o comando for encerrado com êxito com o código de saída 0, nenhuma ação será executada.

A propriedade `periodSeconds` indica que o comando deve ser executado a cada cinco segundos.

## <a name="verify-liveness-output"></a>Verificar a saída da atividade

Nos 30 primeiros segundos, o arquivo `healthy` criado pelo comando inicial existe. Quando o comando de atividade verificar a existência do arquivo `healthy`, o código de status retornará um zero, indicando sucesso, portanto nenhuma reinicialização será executada.

Depois de 30 segundos, o `cat /tmp/healthy` começará a falhar, provocando a falta de integridade e eventos de eliminação.

Esses eventos podem ser exibidos do Portal do Azure ou na CLI do Azure.

![Evento não íntegro no portal][portal-unhealthy]

Ao exibir os eventos no Portal do Azure, os eventos do tipo `Unhealthy` serão acionados após a falha do comando de atividade. O evento subsequente será do tipo `Killing`, indicando a exclusão do contêiner para que a reinicialização possa começar. A contagem de reinicialização para o contêiner aumentará sempre que isso ocorrer.

As reinicializações ocorrem in-loco, portanto, recursos como endereços IP públicos e conteúdo específico ao nó serão preservados.

![Reinicialização de contador do portal][portal-restart]

Se investigação de atividade falhar continuamente e acionar muitas reinicializações, seu contêiner inserirá uma atraso de retirada exponencial.

## <a name="liveness-probes-and-restart-policies"></a>Políticas de investigação de atividade e reinicialização

As políticas de reinicialização substituem o comportamento de reinicialização acionado pelas investigações de atividade. Por exemplo, se você definir um `restartPolicy = Never` *e* uma investigação de atividade, o grupo de contêineres não reiniciará no caso de galha de investigação de atividade. Em vez disso, o grupo de contêineres seguirá a política de reinicialização do grupo de contêineres de `Never`.

## <a name="next-steps"></a>Próximas etapas

Cenários com base em tarefas podem exigir que a investigação de atividade permita reinicializações automáticas se uma função pré-requisitada não estiver funcionando corretamente. Para obter mais informações sobre a execução de contêineres com base em tarefas, consulte [Executar tarefas em contêineres nas Instâncias de Contêiner do Azure](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
