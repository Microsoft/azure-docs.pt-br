---
title: Solucionar problemas do agente Linux do Azure
description: Resolva problemas com o agente Linux do Azure.
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 62b462d8e75fc291ac599ac99dbe4fb3a74fde2b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878690"
---
# <a name="troubleshoot-the-azure-linux-agent"></a>Solucionar problemas do agente Linux do Azure

O [agente Linux do Azure](../extensions/agent-linux.md) permite que uma VM (máquina virtual) se comunique com o controlador de malha (o servidor físico subjacente no qual a VM está hospedada) no endereço IP 168.63.129.16.

>[!NOTE]
>Esse endereço IP é um endereço IP público virtual que facilita a comunicação e não deve ser bloqueado. Para obter mais informações, consulte [o que é o endereço IP 168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="before-you-begin"></a>Antes de começar

Verifique o status do agente e a versão para verificar se ele ainda tem suporte. Consulte [suporte mínimo de versão para agentes de máquina virtual no Azure](/troubleshoot/azure/virtual-machines/support-extensions-agent-version) para verificar o suporte à versão ou consulte as [perguntas frequentes do WALinuxAgent](https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output) para obter as etapas para localizar o status e a versão.

## <a name="troubleshoot-a-not-ready-status"></a>Solucionar problemas de status não pronto

1. Verifique o status do serviço do agente Linux do Azure para verificar se ele está em execução. O nome do serviço pode ser **walinuxagent** ou **waagent**.

   ```
   root@nam-u18:/home/nam# service walinuxagent status
   ● walinuxagent.service - Azure Linux Agent
      Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
      Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
    Main PID: 1036 (python3)
       Tasks: 4 (limit: 4915)
      CGroup: /system.slice/walinuxagent.service
              ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
              └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers
   Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
   root@nam-u18:/home/nam#
   ```

   Se o serviço estiver em execução, reinicie-o para resolver o problema. Se o serviço for interrompido, inicie-o, aguarde alguns minutos e verifique o status novamente.

1. Verifique se a atualização automática está habilitada. Verifique a configuração de atualização automática em **/etc/waagent.conf**.

   ```
   AutoUpdate.Enabled=y
   ```

   Para obter mais informações sobre como atualizar o agente Linux do Azure, consulte [como atualizar o agente Linux do Azure em uma VM](../extensions/update-linux-agent.md).

1. Verifique se a VM pode se conectar ao controlador de malha. Use uma ferramenta como a rotação para testar se a VM pode se conectar ao 168.63.129.16 nas portas 80, 443 e 32526. Se a VM não se conectar conforme o esperado, verifique se a comunicação de saída pelas portas 80, 443 e 32526 está aberta no firewall local na VM. Se esse endereço IP estiver bloqueado, o agente de VM poderá exibir um comportamento inesperado.

## <a name="advanced-troubleshooting"></a>Solução de problemas avançada

Os eventos para solução de problemas do agente Linux do Azure são registrados no arquivo **/var/log/waagent.log** .

### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Não é possível conectar-se ao WireServer IP (IP do host)

O erro a seguir aparece no arquivo **/var/log/waagent.log** quando a VM não consegue acessar o IP WireServer no servidor host.

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

Para resolver o problema:

* Conecte-se à VM usando SSH e, em seguida, tente acessar a URL a seguir da ondulação: http://168.63.129.16/?comp=versions .
* Verifique se há problemas que possam ser causados por um firewall, um proxy ou outra fonte que possa estar bloqueando o acesso ao endereço IP 168.63.129.16.
* Verifique se o Linux IPTables ou um firewall de terceiros está bloqueando o acesso às portas 80, 443 e 32526.

## <a name="next-steps"></a>Próximas etapas

Para solucionar os problemas do agente Linux do Azure, [entre em contato com o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).