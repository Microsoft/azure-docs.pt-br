---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: bff9457220efbb6a6b318550a5b2dd9af4ae230f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019167"
---
Siga as etapas para as circunstâncias específicas.

### <a name="unregister-a-connected-process-server"></a>Cancelar o registro de um servidor de processo conectado

1. Estabeleça uma conexão remota com o servidor de processo como Administrador.
2. No **Painel de Controle**, abra **Programas > Desinstalar um programa**.
3. Desinstale o programa **Servidor de Destino Mestre/Serviço de Mobilidade do Microsoft Azure Site Recovery**.
4. Desinstale o programa **Configuração do Microsoft Azure Site Recovery/Servidor de Processo**.
5. Após a desinstalação dos programas nas etapas 3 e 4, desinstale a **Configuração do Microsoft Azure Site Recovery/Dependências do Servidor de Processo**.

### <a name="unregister-a-disconnected-process-server"></a>Cancelar o registro de um servidor de processo desconectado

Use essas etapas apenas se não houver como reativar o computador no qual o servidor de processo está instalado.

1. Entre no servidor de configuração como um Administrador.
2. Abra um prompt de comando administrativo e navegue até `%ProgramData%\ASR\home\svsystems\bin`.
3. Execute este comando para obter uma lista de um ou mais servidores de processo.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Número: o número de série do servidor de processo.
    - IP/Nome: o endereço IP e o nome do computador que executa o servidor de processo.
    - Pulsação: última pulsação no computador do servidor de processo.
    ![A captura de tela mostra uma exibição de texto não criptografado sobre os servidores de processo e o texto Escolha um dos servidores acima para cancelar o registro. (media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Especifique número de série do servidor de processo do qual você deseja cancelar o registro.
5. O cancelamento de registro de um servidor de processo remove todos os detalhes do sistema e exibe a mensagem: **Registro do <nome_do_servidor> cancelado com sucesso (endereço_IP_do_servidor)**

