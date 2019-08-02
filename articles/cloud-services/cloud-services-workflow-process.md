---
title: Fluxo de trabalho da arquitetura de VM do Windows Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos processos de fluxo de trabalho quando você implanta um serviço.
services: cloud-services
documentationcenter: ''
author: genlin
manager: Willchen
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: e7b3146ffa0f4b828f1a28d3bc51b26db194244c
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249513"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Fluxo de trabalho da arquitetura de VM clássica do Windows Azure 
Este artigo fornece uma visão geral dos processos de fluxo de trabalho que ocorrem quando você implanta ou atualiza um recurso do Azure, como uma máquina virtual. 

> [!NOTE]
>O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: Resource Manager e clássico. Este artigo aborda o uso do modelo de implantação clássica.

O diagrama a seguir apresenta a arquitetura dos recursos do Azure.

![Fluxo de trabalho do Azure](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>Noções básicas do fluxo de trabalho
   
**R**. RDFE/FFE é o caminho de comunicação do usuário para a malha. RDFE (Front-End RedDog) é a API exposta publicamente que é o front-end para o Portal de Gerenciamento e o API de Gerenciamento de Serviços, como o Visual Studio, o MMC do Azure e assim por diante.  Todas as solicitações do usuário passam por RDFE. FFE (front-end da malha) é a camada que converte solicitações do RDFE em comandos de malha. Todas as solicitações de RDFE percorrem o FFE para alcançar os controladores de malha.

**B**. O controlador de malha é responsável por manter e monitorar todos os recursos no data center. Ele se comunica com agentes de host de malha no sistema operacional de malha, enviando informações como a versão do SO convidado, o pacote de serviço, a configuração de serviço e o estado do serviço.

**C**. O agente de host reside no host OSsystem e é responsável por configurar o sistema operacional convidado e se comunicar com o agente convidado (WindowsAzureGuestAgent) para atualizar a função em direção a um estado de meta pretendido e fazer verificações de pulsação com o agente convidado. Se o agente do host não receber resposta de pulsação por 10 minutos, o agente do host reiniciará o sistema operacional convidado.

**C2**. O WaAppAgent é responsável por instalar, configurar e atualizar o WindowsAzureGuestAgent. exe.

**D**.  WindowsAzureGuestAgent é responsável pelo seguinte:

1. Configurar o sistema operacional convidado, incluindo Firewall, ACLs, recursos de LocalStorage, pacote de serviço e configuração e certificados. Configuração do SID para a conta de usuário na qual a função será executada.
2. Comunicando o status da função à malha.
3. Iniciando o WaHostBootstrapper e monitorando-o para certificar-se de que a função está no estado de meta.

**E**. WaHostBootstrapper é responsável por:

1. Ler a configuração da função e iniciar todas as tarefas e processos apropriados para configurar e executar a função.
2. Monitorando todos os seus processos filho.
3. Gerando o evento StatusCheck no processo de host da função.

**F**. IISConfigurator será executado se a função estiver configurada como uma função Web do IIS completa (ela não será executada para as funções do SDK 1,2 HWC). É responsável por:

1. Iniciando os serviços padrão do IIS
2. Configurando o módulo Rewrite na configuração da Web
3. Configurando o AppPool para a função configurada no modelo de serviço
4. Configurando o log do IIS para apontar para a pasta DiagnosticStore LocalStorage
5. Configurando permissões e ACLs
6. O site reside em% RoleRoot%: \sitesroot\0, e o AppPool aponta para esse local para executar o IIS. 

**G**. As tarefas de inicialização são definidas pelo modelo de função e iniciadas pelo WaHostBootstrapper. As tarefas de inicialização podem ser configuradas para serem executadas em segundo plano de forma assíncrona, e o bootstrapper de host iniciará a tarefa de inicialização e continuará em outras tarefas de inicialização. As tarefas de inicialização também podem ser configuradas para serem executadas no modo simples (padrão), no qual o bootstrapper de host aguardará a conclusão da execução da tarefa de inicialização e retornará um código de saída com êxito (0) antes de continuar com a próxima tarefa de inicialização.

**H**. Essas tarefas fazem parte do SDK e são definidas como plug-ins na definição de serviço da função (. csdef). Quando expandido para tarefas de inicialização, o **DiagnosticsAgent** e o **RemoteAccessAgent** são exclusivos, pois cada um deles define duas tarefas de inicialização, uma regular e outra que tem um parâmetro **/blockStartup** . A tarefa de inicialização normal é definida como uma tarefa de inicialização em segundo plano para que possa ser executada em segundo plano enquanto a própria função está em execução. A tarefa de inicialização **/blockStartup** é definida como uma tarefa de inicialização simples para que o WaHostBootstrapper aguarde sua saída antes de continuar. A tarefa **/blockStartup** aguarda a conclusão da inicialização da tarefa regular e, em seguida, sai e permite que o bootstrapper do host continue. Isso é feito para que o diagnóstico e o acesso de RDP possam ser configurados antes do início dos processos de função (isso é feito por meio da tarefa/blockStartup). Isso também permite que o diagnóstico e o acesso do RDP continuem em execução depois que o bootstrapper do host tiver concluído as tarefas de inicialização (isso é feito por meio da tarefa normal).

**I**. WaWorkerHost é o processo de host padrão para funções de trabalho normais. Esse processo de host hospeda todas as DLLs da função e o código de ponto de entrada, como OnStart e Run.

**J**. WaWebHost é o processo de host padrão para funções Web se elas estiverem configuradas para usar o HWC (núcleo de Web Hospedáal) compatível com o SDK 1,2. As funções podem habilitar o modo HWC removendo o elemento da definição de serviço (. csdef). Nesse modo, todo o código e as DLLs do serviço são executados do processo WaWebHost. IIS (w3wp) não é usado e não há AppPools configuradas no Gerenciador do IIS porque o IIS é hospedado dentro de WaWebHost. exe.

**K**. WaIISHost é o processo de host para o código de ponto de entrada de função para funções Web que usam o IIS completo. Esse processo carrega a primeira DLL encontrada que usa a classe **RoleEntryPoint** e executa o código dessa classe (OnStart, Run, OnStop). Todos os eventos **RoleEnvironment** (como StatusCheck e Changed) criados na classe RoleEntryPoint são gerados nesse processo.

**L**. W3WP é o processo de trabalho padrão do IIS que será usado se a função estiver configurada para usar o IIS completo. Isso executa o AppPool configurado no IISConfigurator. Qualquer evento RoleEnvironment (como StatusCheck e alterado) que são criados aqui são gerados nesse processo. Observe que os eventos RoleEnvironment serão acionados em ambos os locais (WaIISHost e w3wp. exe) se você se inscrever em eventos em ambos os processos.

## <a name="workflow-processes"></a>Processos de fluxo de trabalho

1. Um usuário faz uma solicitação, como carregar arquivos. cspkg e. cscfg, informando a um recurso para parar ou fazer uma alteração de configuração, e assim por diante. Isso pode ser feito por meio da portal do Azure ou de uma ferramenta que usa o API de Gerenciamento de Serviços, como o recurso de publicação do Visual Studio. Essa solicitação vai para RDFE para fazer todo o trabalho relacionado à assinatura e, em seguida, comunicar a solicitação ao FFE. O restante dessas etapas de fluxo de trabalho é implantar um novo pacote e iniciá-lo.
2. O FFE localiza o pool de computadores correto (com base na entrada do cliente, como grupo de afinidade ou localização geográfica, além de entrada da malha, como disponibilidade do computador) e se comunica com o controlador de malha mestre nesse pool de computadores.
3. O controlador de malha localiza um host que tem núcleos de CPU disponíveis (ou cria um novo host). O pacote de serviço e a configuração são copiados para o host, e o controlador de malha se comunica com o agente do host no sistema operacional do host para implantar o pacote (configurar DIPs, portas, SO convidado e assim por diante).
4. O agente de host inicia o sistema operacional convidado e se comunica com o agente convidado (WindowsAzureGuestAgent). O host envia pulsações ao convidado para garantir que a função esteja funcionando em direção ao seu estado de meta.
5. O WindowsAzureGuestAgent configura o sistema operacional convidado (firewall, ACLs, LocalStorage e assim por diante), copia um novo arquivo de configuração XML para c:\Config e, em seguida, inicia o processo de WaHostBootstrapper.
6. Para funções Web do IIS completas, WaHostBootstrapper inicia IISConfigurator e informa a ele para excluir quaisquer AppPools existentes para a função Web do IIS.
7. WaHostBootstrapper lê as tarefas de **inicialização** de E:\RoleModel.xml e começa a executar tarefas de inicialização. O WaHostBootstrapper aguarda até que todas as tarefas de inicialização simples tenham sido concluídas e retornaram uma mensagem de "êxito".
8. Para funções Web do IIS completas, WaHostBootstrapper informa IISConfigurator para configurar o AppPool do IIS e aponta para `E:\Sitesroot\<index>`o site `<index>` , onde é um índice baseado em 0 no `<Sites>` número de elementos definidos para o serviço.
9. WaHostBootstrapper iniciará o processo de host dependendo do tipo de função:
    1. **Função de trabalho**: O WaWorkerHost. exe foi iniciado. WaHostBootstrapper executa o método OnStart (). Depois de retornar, WaHostBootstrapper começa a executar o método Run () e, em seguida, marca a função simultaneamente como pronto e a coloca na rotação do balanceador de carga (se InputEndpoints for definido). WaHostBootsrapper, em seguida, entra em um loop de verificação do status da função.
    1. **Função Web do SDK 1,2 HWC**: WaWebHost foi iniciado. WaHostBootstrapper executa o método OnStart (). Depois de retornar, WaHostBootstrapper começa a executar o método Run () e, em seguida, marca simultaneamente a função como pronto e a coloca na rotação do balanceador de carga. WaWebHost emite uma solicitação aquecimento (GET/do.rd_runtime_init). Todas as solicitações da Web são enviadas para WaWebHost. exe. WaHostBootsrapper, em seguida, entra em um loop de verificação do status da função.
    1. **Função Web do IIS completa**: a aIISHost foi iniciada. WaHostBootstrapper executa o método OnStart (). Depois de retornar, ele começa a executar o método Run () e, em seguida, marca a função simultaneamente como pronto e a coloca na rotação do balanceador de carga. WaHostBootsrapper, em seguida, entra em um loop de verificação do status da função.
10. As solicitações da Web de entrada para uma função Web do IIS completa acionam o IIS para iniciar o processo W3WP e atender à solicitação, o mesmo que faria em um ambiente IIS local.

## <a name="log-file-locations"></a>Locais do arquivo de log

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Esse log contém alterações no serviço, incluindo início, interrupções e novas configurações. Se o serviço não for alterado, você poderá esperar ver grandes intervalos de tempo nesse arquivo de log.
- C:\Logs\WaAppAgent.Log.  
Esse log contém as atualizações de status e as notificações de pulsação e é atualizado a cada 2-3 segundos.  Esse log contém uma exibição histórica do status da instância e informará quando a instância não estiver no estado pronto.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaWebHost**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**Logs do IIS**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Logs de eventos do Windows**

`D:\Windows\System32\Winevt\Logs`
 



