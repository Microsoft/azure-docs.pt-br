---
title: Configurar investigação de preparação na instância de contêiner
description: Saiba como configurar uma investigação para garantir que os contêineres nas instâncias de contêiner do Azure recebam solicitações somente quando estiverem prontos
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 3e89086d66f284df35e36dc8f1d68bb09264843f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86169656"
---
# <a name="configure-readiness-probes"></a>Configurar investigações de preparação

Para aplicativos em contêineres que atendem ao tráfego, talvez você queira verificar se o contêiner está pronto para lidar com solicitações de entrada. As instâncias de contêiner do Azure dão suporte a investigações de preparação para incluir configurações para que seu contêiner não possa ser acessado sob determinadas condições. A investigação de preparação se comporta como uma [investigação de preparação de kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/). Por exemplo, um aplicativo de contêiner pode precisar carregar um conjunto de dados grande durante a inicialização e você não deseja que ele receba solicitações durante esse tempo.

Este artigo explica como implantar um grupo de contêineres que inclui uma investigação de prontidão, para que um contêiner só receba tráfego quando a investigação tiver sucesso.

As instâncias de contêiner do Azure também dão suporte a [testes de vida](container-instances-liveness-probe.md), que podem ser configurados para fazer com que um contêiner não íntegro seja reiniciado automaticamente.

> [!NOTE]
> No momento, você não pode usar uma investigação de preparação em um grupo de contêineres implantado em uma rede virtual.

## <a name="yaml-configuration"></a>Configuração do YAML

Por exemplo, crie um `readiness-probe.yaml` arquivo com o trecho a seguir que inclui uma investigação de preparação. Esse arquivo define um grupo de contêineres que consiste em um contêiner que executa um pequeno aplicativo Web. O aplicativo é implantado a partir da `mcr.microsoft.com/azuredocs/aci-helloworld` imagem pública. Esse aplicativo em contêiner também é demonstrado em [implantar uma instância de contêiner no Azure usando o CLI do Azure](container-instances-quickstart.md) e outros guias de início rápido.

```yaml
apiVersion: 2019-12-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>Comando Iniciar

A implantação inclui uma `command` propriedade que define um comando inicial que é executado quando o contêiner começa a ser executado pela primeira vez. Essa propriedade aceita uma matriz de cadeias de caracteres. Este comando simula uma hora em que o aplicativo Web é executado, mas o contêiner não está pronto. 

Primeiro, ele inicia uma sessão do Shell e executa um `node` comando para iniciar o aplicativo Web. Ele também inicia um comando para dormir por 240 segundos, após o qual ele cria um arquivo chamado `ready` dentro do `/tmp` diretório:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Comando de preparação

Esse arquivo YAML define um `readinessProbe` que dá suporte `exec` a um comando de preparação que atua como a verificação de preparação. Este exemplo de comando de preparação testa a existência do `ready` arquivo no `/tmp` diretório.

Quando o `ready` arquivo não existe, o comando de preparação é encerrado com um valor diferente de zero; o contêiner continua em execução, mas não pode ser acessado. Quando o comando for encerrado com êxito com o código de saída 0, o contêiner estará pronto para ser acessado. 

A `periodSeconds` Propriedade designa que o comando de preparação deve ser executado a cada 5 segundos. A investigação de preparação é executada durante o tempo de vida do grupo de contêineres.

## <a name="example-deployment"></a>Exemplo de implantação

Execute o seguinte comando para implantar um grupo de contêineres com a configuração YAML anterior:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Exibir verificações de preparação

Neste exemplo, durante os primeiros 240 segundos, o comando de preparação falha quando verifica a `ready` existência do arquivo. O código de status retornado sinaliza que o contêiner não está pronto.

Esses eventos podem ser exibidos do Portal do Azure ou na CLI do Azure. Por exemplo, o portal mostra que os eventos do tipo `Unhealthy` são disparados na falha do comando de preparação. 

![Evento não íntegro no portal][portal-unhealthy]

## <a name="verify-container-readiness"></a>Verificar preparação do contêiner

Depois de iniciar o contêiner, você pode verificar se ele não está acessível inicialmente. Após o provisionamento, obtenha o endereço IP do grupo de contêineres:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Tente acessar o site enquanto a investigação de prontidão falhar:

```bash
wget <ipAddress>
```

A saída mostra que o site não está acessível inicialmente:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

Após 240 segundos, o comando de preparação é executado com sucesso, sinalizando que o contêiner está pronto. Agora, quando você executar o `wget` comando, ele terá sucesso:

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

Quando o contêiner estiver pronto, você também poderá acessar o aplicativo Web navegando até o endereço IP usando um navegador da Web.

> [!NOTE]
> A investigação de preparação continua a ser executada durante o tempo de vida do grupo de contêineres. Se o comando de preparação falhar em um momento posterior, o contêiner novamente se tornará inacessível. 
> 

## <a name="next-steps"></a>Próximas etapas

Uma investigação de prontidão pode ser útil em cenários que envolvem grupos de vários contêineres que consistem em contêineres dependentes. Para obter mais informações sobre cenários de vários contêineres, consulte [grupos de contêineres em instâncias de contêiner do Azure](container-instances-container-groups.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
