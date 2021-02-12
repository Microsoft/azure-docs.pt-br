---
title: O servidor de licenças da Área de Trabalho Remota não está disponível quando você se conecta a uma VM do Azure | Microsoft Docs
description: Aprenda a solucionar problemas de falhas de RDP, porque nenhum servidor de licenças de Área de Trabalho Remota está disponível | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 6b5864d40d2a4d8f8d6cf404df29f909a73f04e2
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832036"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>O servidor de licenças da Área de Trabalho Remota não está disponível quando você se conecta a uma VM do Azure

Este artigo ajuda a resolver o problema quando você não pode se conectar a uma máquina virtual (VM) do Azure porque nenhum servidor de licenças de Área de Trabalho Remota está disponível para fornecer uma licença.

## <a name="symptoms"></a>Sintomas

Quando você tenta se conectar a uma máquina virtual (VM), você enfrenta os seguintes cenários:

- A captura de tela da VM mostra que o sistema operacional está totalmente carregado e aguardando credenciais.
- As seguintes mensagens de erro são exibidas ao tentar fazer uma conexão de RDP (protocolo RDP):

  - A sessão remota foi desconectada porque não há servidores de licenças de Área de Trabalho Remota disponíveis para fornecer uma licença.

  - Nenhum servidor de licenças de área de trabalho remota está disponível. Os Serviços de Área de Trabalho Remota deixarão de funcionar porque este computador passou do período de cortesia e não contatou pelo menos um servidor de licenças válido do Windows Server 2008. Selecione esta mensagem para abrir a Configuração do Servidor Host da Sessão da Área de Trabalho Remota para usar o Diagnóstico de Licenciamento.

No entanto, é possível conectar-se à VM normalmente usando uma sessão administrativa:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Causa

Esse problema ocorre se um servidor de licenças de Área de Trabalho Remota não estiver disponível para fornecer uma licença e iniciar uma sessão remota. Isso pode ser causado por vários cenários, mesmo que uma função do Host de Sessão da Área de Trabalho Remota tenha sido configurada na VM:

- Nunca houve uma função de licenciamento de Área de Trabalho Remota no ambiente e o período de tolerância, de 180 dias, acabou.
- Uma licença de Área de Trabalho Remota foi instalada no ambiente, mas nunca foi ativada.
- Uma licença de Área de Trabalho Remota no ambiente não tem CALs (Licenças de Acesso para Cliente) injetadas para configurar a conexão.
- Uma licença de Área de Trabalho Remota foi instalada no ambiente. Existem CALs disponíveis, mas não foram configuradas corretamente.
- Uma licença de Remote Desktop tem CALs e foi ativada. No entanto, alguns outros problemas no servidor de licenças de Área de Trabalho Remota evitam que ele forneça licenças no ambiente.

## <a name="solution"></a>Solução

Para resolver esse problema, [faça backup do disco de SO](../windows/snapshot-copy-managed-disk.md) e siga estas etapas:

1. Conecte-se à VM usando uma sessão administrativa:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Se você não conseguir se conectar à VM usando uma sessão administrativa, poderá usar o [Console Serial da Máquina Virtual no Azure](serial-console-windows.md) para acessar a VM da seguinte maneira:

    1. Acesse o console serial selecionando **suporte & solução**  >  **de problemas console serial (versão prévia)**. Se o recurso estiver habilitado na VM, você poderá conectar a VM com êxito.

    2. Crie um novo canal para uma instância de CMD. Digite **CMD** para iniciar o canal e obter o nome do canal.

    3. Alterne para o canal que executa a instância do CMD. Nesse caso, deve ser o canal 1:

       ```
       ch -si 1
       ```

    4. Selecione **Enter** novamente e insira um nome de usuário e senha válidos, ID de domínio ou local, para a VM.

2. Verifique se a VM tem uma função de Host da Sessão da Área de Trabalho Remota habilitada. Se a função estiver habilitada, certifique-se de que ele está funcionando corretamente. Abra uma instância de CMD com privilégios e siga estas etapas:

    1. Use o seguinte comando para verificar o status da função do Host da Sessão da Área de Trabalho Remota:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Se esse comando retornar um valor 0, isso significa que a função está desabilitada e você pode ir para a etapa 3.

    2. Use o seguinte comando para verificar as políticas e reconfigurar conforme necessário:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode

        reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Se o valor de **LicensingMode** for definido para qualquer valor diferente de 4, por usuário, defina o valor como 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Se o valor **SpecifiedLicenseServers** não existir, ou se tiver informações incorretas do servidor de licenças, altere-o da seguinte maneira:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Após fazer quaisquer alterações no registro, reinicie a VM.

    4. Se você não tiver CALs, remova a função de Host da Sessão da Área de Trabalho Remota. O protocolo RDP será definido como normal. Permite apenas duas conexões RDP simultâneas para a VM:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Se a VM tiver a função de licenciamento da Área de Trabalho Remota e ela não for usada, você também poderá remover essa função:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Certifique-se de que a VM possa conectar-se ao servidor de licenças da Área de Trabalho Remota. Você pode testar a conectividade com a porta 135 entre a VM e o servidor de licenças: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Se não houver um servidor de licenças de Área de Trabalho Remota no ambiente e você quiser um, poderá [instalar um serviço de função de licenciamento de Área de Trabalho Remota](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc731765(v=ws.11)). Em seguida [configurar o licenciamento do RDS](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383).

4. Se um servidor de licenças de Área de Trabalho Remota estiver configurado e íntegro, certifique-se de que o servidor de licenças de Área de Trabalho Remota está ativado com CALs.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se você ainda precisar de ajuda, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema.
