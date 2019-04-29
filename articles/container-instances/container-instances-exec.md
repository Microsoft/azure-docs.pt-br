---
title: Executar comandos em contêineres em execução em Instâncias de Contêiner do Azure
description: Saiba como executar um comando em um contêiner que está sendo executado em Instâncias de Contêiner do Azure
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: danlep
ms.openlocfilehash: 577e2386c352798bc21a2c78b22726128ac7cf0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60579739"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Executar um comando em uma Instância de Contêiner do Azure

As Instâncias de Contêiner do Azure oferecem suporte à execução de um comando em um contêiner em execução. A execução de um comando em um contêiner que você já iniciou é especialmente útil durante o desenvolvimento de aplicativo e solução de problemas. O uso mais comum desse recurso é iniciar um shell interativo para que você possa depurar problemas em um contêiner em execução.

## <a name="run-a-command-with-azure-cli"></a>Executar um comando com a CLI do Azure

Executar um comando em um contêiner em execução com [az container exec][az-container-exec] na [CLI do Azure][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Por exemplo, para iniciar um shell Bash em um contêiner de Nginx:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

No exemplo de saída abaixo, o shell Bash é iniciado em um contêiner do Linux em execução, fornecendo um terminal no qual `ls` é executado:

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

Neste exemplo, o Prompt de Comando é iniciado em um contêiner de Nanoserver em execução:

```console
$ az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>Grupos com vários contêineres

Se seu [grupo de contêineres](container-instances-container-groups.md) tem vários contêineres, como um contêiner de aplicativo e um sidecar de registro em log, especifique o nome do contêiner no qual deverá ser executado o comando com `--container-name`.

Por exemplo, no grupo de contêiner *mynginx* há dois contêineres, *nginx-app* e *logger*. Para iniciar um shell no contêiner *nginx aplicativo*:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Restrições

Instâncias de Contêiner do Azure atualmente oferecem suporte à inicialização de um único processo com [az container exec][az-container-exec] e você não pode passar argumentos de comando. Por exemplo, não é possível encadear comandos, como em `sh -c "echo FOO && echo BAR"`, ou executar `echo FOO`.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outras ferramentas de solução de problemas e problemas comuns de implantação em [Solucionar problemas de contêiner e implantação em Instâncias de Contêiner do Azure](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure