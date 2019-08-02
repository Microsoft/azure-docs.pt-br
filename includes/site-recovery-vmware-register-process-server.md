---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67534555"
---
1. Estabelece uma Conexão de área de trabalho remota para o computador que executa o servidor de processo. 
2. Execute cspsconfigtool.exe para iniciar a ferramenta de configuração do servidor de processo do Azure Site Recovery.
    - A ferramenta é iniciada automaticamente na primeira vez que entra no servidor de processo.
    - Se não abrir automaticamente, clique em seu atalho na área de trabalho.

3. Na **servidor de configuração IP ou FQDN**, especifique o nome ou endereço IP do servidor de configuração com o qual registrar o servidor de processo.
4. Na **porta do servidor de configuração**, certifique-se de que a 443 é especificada. Isso é a porta na qual o servidor de configuração escuta para solicitações.
5. Na **frase secreta de Conexão**, especifique a senha que você especificou ao configurar o servidor de configuração. Para localizar a frase secreta:
    -  No servidor de configuração, navegue até a pasta de instalação do Site Recovery * *\home\svssystems\bin\** :
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Execute o comando para imprimir a senha atual abaixo:
    ```
    genpassphrase.exe -n
    ```

6. Na **porta de transferência de dados**, deixe o valor padrão, a menos que você especificou uma porta personalizada.

7. Clique em **salvar** salvar as configurações e registrar o servidor de processo.

    
    ![Registrar o servidor de processo](./media/site-recovery-vmware-register-process-server/register-ps.png)
