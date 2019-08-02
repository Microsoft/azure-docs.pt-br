---
title: Adicionar um script a um plano de recuperação para recuperação de desastre com o Azure Site Recovery | Microsoft Docs
description: Saiba como adicionar um script do VMM a um plano de recuperação para recuperação de desastre de VMs do Hyper-V em nuvens do VMM.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: ea6d969ed6612f947e3c73c438738bd98ac2bb30
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "64700454"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Adicionar um script a um plano de recuperação

Este artigo descreve como criar um script do System Center VMM (Virtual Machine Manager) e adicioná-lo a um plano de recuperação no [Azure Site Recovery](site-recovery-overview.md).

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Pré-requisitos

Você pode usar scripts do PowerShell em seus planos de recuperação. Para poder ser acessado no plano de recuperação, você deve criar o script e colocá-lo na biblioteca do VMM. Leve em conta o seguinte enquanto você escreve o script:

* Certifique-se de que os scripts usem blocos try-catch para que as exceções sejam tratadas normalmente.
    - Se houver uma exceção no script, ele será interrompido e a tarefa será mostrada como com falha.
    - Se ocorrer um erro, o restante do script não será executado.
    - Se ocorrer um erro quando você executa um failover não planejado, o plano de recuperação continuará.
    - Se ocorrer um erro quando você executa um failover planejado, o plano de recuperação será interrompido. Corrija o script, verifique se ele funciona conforme esperado e execute novamente o plano de recuperação.
        - O comando `Write-Host` não funciona em um script de plano de recuperação. Se você usar o comando `Write-Host` em um script, o script falhará. Para criar, crie um script de proxy que por sua vez executa o script principal. Para colocar toda a saída no pipe, use o comando **\>\>** .
        - O script expira se não retornar em até 600 segundos.
        - Se nada for escrito em STDERR, o script será classificado como com falha. Essa informação é exibida nos detalhes de execução do script.

* Os scripts em um plano de recuperação são executados no contexto da conta de serviço VMM. Verifique se que essa conta tem permissões de leitura para o compartilhamento remoto no qual o script está localizado. Teste o script para ser executado com o mesmo nível de direitos de usuário da conta de serviço do VMM.
* Os cmdlets do VMM são entregues em um módulo do Windows PowerShell. O módulo é instalado quando você instala o console do VMM. Para carregar o módulo em seu script, use o seguinte comando no script: 

    `Import-Module -Name virtualmachinemanager`

    Para obter mais informações, consulte [Introdução ao Windows PowerShell e ao VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Verifique se você possui pelo menos um servidor de biblioteca na sua implantação do VMM. Por padrão, o caminho de compartilhamento da biblioteca de um servidor VMM pode ser encontrado localmente no servidor VMM. O nome da pasta é MSCVMMLibrary.

  Se o caminho de compartilhamento da biblioteca for remoto (ou local, mas não compartilhado com MSCVMMLibrary), configure o compartilhamento da seguinte forma, usando \\libserver2.contoso.com\share\ como um exemplo:
  
  1. Abra o Editor do Registro e vá para **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.

  1. Altere o valor de **ScriptLibraryPath** para  **\\\libserver2.contoso.com\share\\** . Especifique o FQDN completo. Forneça permissões para o local de compartilhamento. Esse é o nó raiz do compartilhamento. Para verificar o nó raiz, no VMM, vá para o nó raiz na biblioteca. O caminho que abre é a raiz do caminho. Esse é o caminho que você deve usar na variável.

  1. Teste o script usando uma conta de usuário com o mesmo nível de direitos de usuário da conta de serviço do VMM. O uso desses direitos de usuário verifica se os scripts autônomos testados são executados da mesma forma que são executados nos planos de recuperação. No servidor VMM, defina a política de execução a ser ignorada da seguinte maneira:

     a. Abra o console do **Windows PowerShell de 64 bits** como um administrador.
     
     b. Digite **Set-executionpolicy bypass**. Para saber mais, confira [Usando o cmdlet Set-ExecutionPolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Defina **Set-executionpolicy bypass** somente no console do PowerShell de 64 bits. Se você defini-lo para o console do PowerShell de 32 bits, os scripts não serão executados.

## <a name="add-the-script-to-the-vmm-library"></a>Adicionar o script à biblioteca do VMM

Se você tiver um site de origem do VMM, poderá criar um script no servidor do VMM. Em seguida, inclua o script em seu plano de recuperação.

1. No compartilhamento de biblioteca, crie uma nova pasta. Por exemplo, \<nome do servidor do VMM > \MSSCVMMLibrary\RPScripts. Coloque a pasta nos servidores VMM de origem e de destino.
1. Crie o script. Por exemplo, nomeie o script RPScript. Verifique se o script funciona conforme o esperado.
1. Coloque o script na pasta \<NomeDoServidorVMM>\MSSCVMMLibrary nos servidores VMM de origem e de destino.

## <a name="add-the-script-to-a-recovery-plan"></a>Adicionar o script a um plano de recuperação

Depois que VMs ou grupos de replicação são adicionados a um plano de recuperação e este é criado, você pode adicionar o script ao grupo.

1. Abra o plano de recuperação.
1. Na lista**Etapa**, selecione um item. Em seguida, selecione **Script** ou **Ação Manual**.
1. Especifique se você deseja adicionar o script ou a ação antes ou depois do item selecionado. Para mover a posição do script para cima ou para baixo, selecione os botões **Mover para cima** e **Mover para baixo**.
1. Se você adicionar um script do VMM, selecione **Failover no script do VMM**. Em **Caminho do Script**, insira o caminho relativo para o compartilhamento. Por exemplo, digite **\RPScripts\RPScript.PS1**.
1. Se você adicionar um runbook da Automação do Azure, especifique a conta da Automação em que o runbook está localizado. Em seguida, selecione o script de runbook do Azure que você deseja usar.
1. Faça um failover do plano de recuperação para garantir que o script funciona conforme esperado.


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [execução de failovers](site-recovery-failover.md).

