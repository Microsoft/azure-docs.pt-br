---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172450"
---
Siga as etapas para suas circunstâncias específicas.

### <a name="unregister-a-connected-process-server"></a>Cancelar o registro de um servidor de processo conectado

1. Estabelece uma conexão remota ao servidor de processo como administrador.
2. No **painel de controle**, abra **programas > desinstalar um programa**.
3. O programa de desinstalação **servidor de destino do Microsoft Azure Site Recovery mobilidade mestra do serviço**.
4. O programa de desinstalação **o servidor de configuração/em processo do Microsoft Azure Site Recovery**.
5. Depois que os programas nas etapas 3 e 4 são desinstalados, desinstale **dependências de servidor de processo/configuração do Microsoft Azure Site Recovery**.

### <a name="unregister-a-disconnected-process-server"></a>Cancelar o registro de um servidor de processo desconectado

Apenas use essas etapas se não houver nenhuma maneira de reviver a máquina em que o servidor de processo está instalado.

1. Entre o servidor de configuração como um administrador.
2. Abra um prompt de comando administrativo e navegue até `%ProgramData%\ASR\home\svsystems\bin`.
3. Execute este comando para obter uma lista de um ou mais servidores de processo.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Não: o número de série do servidor de processo.
    - Nome/IP: O endereço IP e o nome do computador executando o servidor de processo.
    - Pulsação: Última pulsação da máquina do servidor de processo.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Especifique o número de série do servidor de processo que você deseja cancelar o registro.
5. Cancelar o registro de um servidor de processo de remover todos os seus detalhes do sistema e exibe a mensagem: **Cancelado com êxito o nome do servidor > (server-IP-address)**

