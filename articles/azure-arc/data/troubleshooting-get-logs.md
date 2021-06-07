---
title: Obter logs para solucionar problemas dos serviços de dados habilitados para o Azure Arc
description: Saiba como obter arquivos de log de um controlador de dados para solucionar problemas de serviços de dados habilitados para o Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0c4cff7583f08fe27649cee464fcef802cddd88f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93234029"
---
# <a name="get-logs-to-troubleshoot-azure-arc-enabled-data-services"></a>Obter logs para solucionar problemas dos serviços de dados habilitados para o Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, você precisa:

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. Para obter mais informações, consulte [instalar ferramentas de cliente para implantar e gerenciar os serviços de dados de arco do Azure](./install-client-tools.md).
* Uma conta de administrador para entrar no controlador de dados habilitado para Arc do Azure.

## <a name="get-log-files"></a>Obter arquivos de log

Você pode obter logs de serviço em todos os pods ou pods específicos para fins de solução de problemas. Uma maneira é usar ferramentas de kubernetes padrão, como o `kubectl logs` comando. Neste artigo, você usará a [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] ferramenta, que torna mais fácil obter todos os logs de uma vez.

1. Entre no controlador de dados com uma conta de administrador.

   ```console
   azdata login
   ```

2. Execute o seguinte comando para despejar os logs:

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   Por exemplo:

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

O controlador de dados cria os arquivos de log no diretório de trabalho atual em um subdiretório chamado `logs` . 

## <a name="options"></a>Opções

O `azdata arc dc debug copy-logs` comando fornece as seguintes opções para gerenciar a saída:

* Gere os arquivos de log para um diretório diferente usando o `--target-folder` parâmetro.
* Compacte os arquivos omitindo o `--skip-compress` parâmetro.
* Dispare e inclua despejos de memória omitindo `--exclude-dumps` . Não recomendamos esse método, a menos que Suporte da Microsoft tenha solicitado os despejos de memória. Obter um despejo de memória requer que a configuração do controlador de dados `allowDumps` seja definida como `true` quando o controlador de dados for criado.
* Filtre para coletar logs para apenas um pod ( `--pod` ) ou contêiner ( `--container` ) específico por nome.
* Filtre para coletar logs de um recurso personalizado específico passando os `--resource-kind` parâmetros e `--resource-name` . O `resource-kind` valor do parâmetro deve ser um dos nomes de definição de recurso personalizados. Você pode recuperar esses nomes usando o comando `kubectl get customresourcedefinition` .

Com esses parâmetros, você pode substituir o `<parameters>` no exemplo a seguir: 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

Por exemplo:

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

A hierarquia de pastas a seguir é um exemplo. Ele é organizado pelo nome do pod, pelo contêiner e, em seguida, pela hierarquia de diretório dentro do contêiner.

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

## <a name="next-steps"></a>Próximas etapas

[azdata arc dc debug copy-logs](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)
