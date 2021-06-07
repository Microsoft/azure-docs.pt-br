---
title: Configurar investigação de vida na instância de contêiner
description: Saiba como configurar investigações de atividade para reiniciar contêineres não íntegros em Instâncias de Contêiner do Azure
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: befe9693be1413abf455d915814c53aab20db53c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86169690"
---
# <a name="configure-liveness-probes"></a>Configurar investigações de atividade

Os aplicativos em contêineres podem ser executados por longos períodos de tempo, resultando em Estados desfeitos que talvez precisem ser reparados reiniciando o contêiner. As instâncias de contêiner do Azure dão suporte a investigações de vida para que você possa configurar seus contêineres dentro do grupo de contêineres para reiniciar se a funcionalidade crítica não estiver funcionando. A investigação de tempo de vida se comporta como uma [investigação de vida kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

Este artigo explica como implantar um grupo de contêineres que inclui uma investigação de atividade, demonstrando a reinicialização automática de um contêiner não íntegro simulado.

As instâncias de contêiner do Azure também dão suporte a [investigações de preparação](container-instances-readiness-probe.md), que podem ser configuradas para garantir que o tráfego atinja um contêiner somente quando ele estiver pronto para ele.

> [!NOTE]
> No momento, você não pode usar uma investigação de vida em um grupo de contêineres implantado em uma rede virtual.

## <a name="yaml-deployment"></a>Implantação do YAML

Crie um arquivo `liveness-probe.yaml` com o snippet a seguir. Esse arquivo define um grupo de contêineres composto por um contêiner NGNIX que eventualmente se tornará não íntegro.

```yaml
apiVersion: 2019-12-01
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

### <a name="start-command"></a>Comando Iniciar

A implantação inclui uma `command` propriedade que define um comando inicial que é executado quando o contêiner começa a ser executado pela primeira vez. Essa propriedade aceita uma matriz de cadeias de caracteres. Esse comando simula o contêiner entrando em um estado não íntegro.

Primeiro, ele inicia uma sessão de bash e cria um arquivo chamado `healthy` dentro do `/tmp` diretório. Em seguida, ele é suspenso por 30 segundos antes de excluir o arquivo e, em seguida, entra em uma suspensão de 10 minutos:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Comando de atividade

Essa implantação define um `livenessProbe` que dá suporte `exec` a um comando de vida que atua como a verificação de tempo de vida. Se esse comando for encerrado com um valor diferente de zero, o contêiner será eliminado e reiniciado, o sinalização do `healthy` arquivo não poderá ser encontrado. Se esse comando for encerrado com êxito com o código de saída 0, nenhuma ação será executada.

A propriedade `periodSeconds` indica que o comando deve ser executado a cada cinco segundos.

## <a name="verify-liveness-output"></a>Verificar a saída da atividade

Nos 30 primeiros segundos, o arquivo `healthy` criado pelo comando inicial existe. Quando o comando de tempo de vida verifica a `healthy` existência do arquivo, o código de status retorna 0, sinalizando êxito, portanto, não ocorrerá nenhuma reinicialização.

Após 30 segundos, o `cat /tmp/healthy` comando começa a falhar, causando a ocorrência de eventos não íntegros e de eliminação.

Esses eventos podem ser exibidos do Portal do Azure ou na CLI do Azure.

![Evento não íntegro no portal][portal-unhealthy]

Exibindo os eventos no portal do Azure, os eventos do tipo `Unhealthy` são disparados após a falha do comando de vida. O evento subsequente é do tipo `Killing` , significando uma exclusão de contêiner para que uma reinicialização possa começar. A contagem de reinicialização para o contêiner é incrementada toda vez que esse evento ocorre.

As reinicializações são concluídas no local para que os recursos como endereços IP públicos e conteúdos específicos do nó sejam preservados.

![Reinicialização de contador do portal][portal-restart]

Se a investigação de tempo de vida falhar continuamente e disparar muitas reinicializações, o contêiner entrará em um atraso de retirada exponencial.

## <a name="liveness-probes-and-restart-policies"></a>Políticas de investigação de atividade e reinicialização

As políticas de reinicialização substituem o comportamento de reinicialização acionado pelas investigações de atividade. Por exemplo, se você definir um `restartPolicy = Never` *e* uma investigação de tempo de vida, o grupo de contêineres não será reiniciado devido a uma verificação de falha de vida. O grupo de contêineres está em conformidade com a política de reinicialização do grupo de contêineres do `Never` .

## <a name="next-steps"></a>Próximas etapas

Cenários com base em tarefas podem exigir que a investigação de atividade permita reinicializações automáticas se uma função pré-requisitada não estiver funcionando corretamente. Para obter mais informações sobre a execução de contêineres com base em tarefas, consulte [Executar tarefas em contêineres nas Instâncias de Contêiner do Azure](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
