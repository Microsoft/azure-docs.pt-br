---
title: "Depurando um serviço de nuvem ou uma máquina virtual do Azure no Visual Studio | Microsoft Docs"
description: "Depuração de um Serviço de Nuvem ou Máquina Virtual no Visual Studio"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 945e06e0-2100-41af-b218-72347367ddab
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: 66f0a9b591b812a52dc6037dcfcb64a2c250f05c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="debugging-an-azure-cloud-service-or-virtual-machine-in-visual-studio"></a>Depurando um serviço de nuvem ou máquina virtual do Azure no Visual Studio
O Visual Studio oferece a você opções diferentes de depuração dos serviços de nuvem e das máquinas virtuais do Azure.

## <a name="debug-your-cloud-service-on-your-local-computer"></a>Depurar o serviço de nuvem no computador local
Você pode economizar tempo e dinheiro usando o emulador de computação para depurar o serviço de nuvem em um computador local. Ao depurar um serviço localmente antes de implantá-lo, você pode aprimorar a confiabilidade e o desempenho sem pagar pelo tempo de computação. No entanto, alguns erros podem ocorrer somente quando você executa um serviço de nuvem no Azure em si. Você poderá depurar esses erros se habilitar a depuração remota quando publica o serviço e depois anexar o depurador a uma instância de função.

O emulador simula o serviço de computação do Azure e o executa no ambiente local para que você possa testar e depurar o serviço de nuvem antes de implantá-lo. O emulador trata o ciclo de vida das instâncias de função e fornece acesso a recursos simulados, como armazenamento local. Quando você depura ou executa seu serviço no Visual Studio, ele inicia automaticamente o emulador como um aplicativo em segundo plano e, em seguida, implanta o serviço para o emulador. É possível usar o emulador para exibir o serviço quando ele é executado no ambiente local. Você pode executar a versão completa ou a versão expressa do emulador. (A partir do Azure 2.3, a versão expressa do emulador é o padrão). Consulte [Usando o Emulator Expresso para executar e depurar um serviço de nuvem localmente](vs-azure-tools-emulator-express-debug-run.md).

### <a name="to-debug-your-cloud-service-on-your-local-computer"></a>Para depurar o serviço de nuvem no computador local
1. Na barra de menus, escolha **Depurar**, **Iniciar Depuração** para executar o projeto de serviço de nuvem do Azure. Como alternativa, você pode pressionar F5. Será exibida uma mensagem informando que o Emulador de Computação está sendo iniciado. Quando o emulador é iniciado, o ícone da bandeja do sistema o confirma.

    ![Emulador do Azure na bandeja do sistema](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)
2. Exiba a interface do usuário do emulador de computação abrindo o menu de atalho do ícone do Azure na área de notificação e selecione **Mostrar Interface do Usuário do Emulador de Computação**.

    O painel esquerdo da IU usuário mostra os serviços que estão implantados no momento no emulador de computação e as instâncias de função que cada serviço está executando. É possível escolher o serviço ou as funções para exibir o ciclo de vida, o registro em log e as informações de diagnóstico no painel direito. Se você colocar o foco na margem superior de uma janela incluída, ela se expandirá, preenchendo o painel direito.
3. Percorra o aplicativo selecionando os comandos no menu **Depurar** e definindo pontos de interrupção no código. À medida que você percorre o aplicativo no depurador, os painéis são atualizados com o status atual do aplicativo. Quando você interrompe a depuração, a implantação do aplicativo é excluída. Se seu aplicativo incluir uma função Web e você tiver definido a propriedade de ação de Inicialização para iniciar o navegador da Web, o Visual Studio iniciará o aplicativo Web no navegador. Se você alterar o número de instâncias de uma função na configuração do serviço, deverá parar o serviço de nuvem e reiniciar a depuração de modo que você pode depurar essas novas instâncias da função.

    **Observação:** quando você interrompe a execução ou a depuração do serviço, o emulador de computação local e o emulador de armazenamento não são interrompidos. Também é preciso pará-los explicitamente na área de notificação.

## <a name="debug-a-cloud-service-in-azure"></a>Depurar um perfil de serviço de nuvem no Azure
Para depurar um serviço de nuvem em um computador remoto, você deve habilitar essa funcionalidade explicitamente quando implanta o serviço de nuvem para que os serviços exigidos (msvsmon.exe, por exemplo) sejam instalados nas máquinas virtuais que executam suas instâncias de função. Se você não habilitou a depuração remota quando publicou o serviço, será preciso publicar novamente o serviço com a depuração remota habilitada.

Se você habilitar a depuração remota para um serviço de nuvem, ela não mostrará degradação de desempenho nem incorrerá em cobranças adicionais. Você não deve usar a depuração remota em um serviço de produção, pois os clientes que usam o serviço podem ser negativamente afetados.

> [!NOTE]
> Ao publicar um serviço de nuvem no Visual Studio, você pode habilitar o **IntelliTrace** para qualquer função nesse serviço que se destine ao .NET Framework 4 ou .NET Framework 4.5. Usando o **IntelliTrace**, você pode examinar os eventos que ocorreram em uma instância de função no passado e reproduzir o contexto no tempo em que ocorreu. Consulte [Depurando um serviço de nuvem publicado com o IntelliTrace e o Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016) e [Usando o IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx).
>
>

### <a name="to-enable-remote-debugging-for-a-cloud-service"></a>Para habilitar a depuração remota para um serviço de nuvem
1. Abra o menu de atalho do projeto do Azure e selecione **Publicar**.
2. Selecione o ambiente de **Preparo** e a configuração de **Depuração**.

    Isso é apenas uma diretriz. Você pode optar por executar ambientes de teste em um ambiente de Produção. No entanto, você poderá afetar negativamente os usuários ao habilitar a depuração remota no ambiente de Produção. É possível escolher a configuração de Versão, mas a configuração de Depuração facilita a depuração.

    ![Escolher a configuração de Depuração](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)
3. Siga as etapas comuns, mas marque a caixa de seleção **Habilitar depurador remoto para todas as funções** na guia **Configurações Avançadas**.

    ![Configuração de Depuração](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### <a name="to-attach-the-debugger-to-a-cloud-service-in-azure"></a>Para anexar o depurador a um serviço de nuvem no Azure
1. No Gerenciador de Servidores, expanda o nó do seu serviço de nuvem.
2. Abra o menu de atalho da função ou da instância de função à qual você deseja anexar e selecione **Anexar Depurador**.

    Se você depurar uma função, o depurador do Visual Studio anexará a cada instância dessa função. O depurador será interrompido em um ponto de interrupção da primeira instância de função que executa essa linha de código e atende a todas as condições desse ponto de interrupção. Se você depurar uma instância, o depurador será anexado apenas a essa instância e interrompido em um ponto de interrupção somente quando essa instância específica executar essa linha de código e atender às condições do ponto de interrupção.

    ![Anexar Depurador](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)
3. Depois que o depurador é anexado a uma instância, a depuração ocorre como de costume. O depurador é anexado automaticamente ao processo de host apropriado para sua função. Dependo da função, o depurador será anexado a w3wp.exe, a WaWorkerHost.exe ou a WaIISHost.exe. Para verificar o processo ao qual o depurador é anexado, expanda o nó da instância no Gerenciador de Servidores. Consulte [Arquitetura de função do Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) para obter mais informações sobre os processos do Azure.

    ![Caixa de diálogo Selecionar tipo de código](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)
4. Para identificar os processos aos quais o depurador é anexado, abra a caixa de diálogo Processos escolhendo, na barra de menus, Depurar, Windows, Processos. (Teclado: Ctrl+Alt+Z) Para desanexar um processo específico, abra o menu de atalho correspondente e clique em **Desanexar Processo**. Se preferir, localize o nó da instância no Gerenciador de Servidores, encontre o processo, abra o respectivo menu de atalho e clique em **Desanexar Processo**.

    ![Processos de depuração](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

> [!WARNING]
> Evite paradas longas em pontos de interrupção durante a depuração remota. O Azure trata um processo que foi interrompido por um pouco mais de alguns minutos como sem resposta e para de enviar tráfego para essa instância. Se você parar por muito tempo, o msvsmon.exe será desanexado do processo.
>
>

Para desanexar o depurador de todos os processos em sua instância ou função, abra o menu de atalho da função ou instância que você está depurando e selecione **Desanexar Depurador**.

## <a name="limitations-of-remote-debugging-in-azure"></a>Limitações da depuração remota no Azure
No SDK 2.3 do Azure, a depuração remota tem as limitações a seguir.

* Com a depuração remota habilitada, você não pode publicar um serviço de nuvem no qual qualquer função tenha mais de 25 instâncias.
* O depurador usa as portas 30400 a 30424, 31400 a 31424 e 32400 a 32424. Se você tentar usar algumas delas, não será possível publicar o serviço e uma das seguintes mensagens de erro aparecerá no log de atividades do Azure:

  * Erro ao validar o arquivo .cscfg em relação ao arquivo .csdef.
    O intervalo de portas reservado 'range' para o ponto de extremidade Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector da função 'role' é sobreposto por uma porta ou um intervalo já definido.
  * Falha na alocação. Tente novamente mais tarde, reduzindo o tamanho da VM ou o número de instâncias de função, ou tente implantar em uma região diferente.

## <a name="debugging-azure-virtual-machines"></a>Depurando máquinas virtuais do Azure
Você pode depurar programas que são executados em máquinas virtuais do Azure usando o Gerenciador de Servidores no Visual Studio. Quando você habilita a depuração remota em uma máquina virtual do Azure, o Azure instala a extensão da depuração remota na máquina virtual. Em seguida, você poderá anexá-la a processos na máquina virtual e depurar normalmente.

> [!NOTE]
> As máquina virtuais criadas por meio da pilha do gerenciador de recursos do Azure podem ser remotamente depuradas usando o Cloud Explorer no Visual Studio 2015. Para saber mais, consulte [Gerenciando recursos do Azure com o Cloud Explorer](http://go.microsoft.com/fwlink/?LinkId=623031).
>
>

### <a name="to-debug-an-azure-virtual-machine"></a>Para depurar uma máquina virtual do Azure
1. No Gerenciador de Servidores, expanda o nó Máquinas Virtuais e selecione o nó da máquina virtual que deseja depurar.
2. Abra o menu de contexto e selecione **Habilitar Depuração**. Quando perguntado se você tem certeza de que deseja habilitar a depuração na máquina virtual, selecione **Sim**.

    O Azure instala a extensão da depuração remota na máquina virtual para habilitar a depuração.

    ![Comando Depuração habilitada para máquina virtual](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Log de atividades do Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)
3. Depois que a extensão de depuração remota concluir a instalação, abra o menu de contexto da máquina virtual e selecione **Anexar Depurador...**

    O Azure obtém uma lista dos processos na máquina virtual e os mostra na caixa de diálogo Anexar ao Processo.

    ![Comando Anexar depurador](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)
4. Na caixa de diálogo **Anexar ao Processo**, escolha **Selecionar** a fim de limitar a lista de resultados para mostrar apenas os tipos de código que você deseja depurar. Você pode depurar um código gerenciado de 32 ou 64 bits, código nativo ou ambos.

    ![Caixa de diálogo Selecionar tipo de código](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)
5. Clique nos processos que você deseja depurar na máquina virtual e selecione **Anexar**. Por exemplo, você pode escolher o processo w3wp.exe se quiser depurar um aplicativo Web na máquina virtual. Consulte [Depurar um ou mais processos no Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) e [Arquitetura de função do Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) para obter mais informações.

## <a name="create-a-web-project-and-a-virtual-machine-for-debugging"></a>Criar um projeto da Web e uma máquina virtual para depuração
Antes de publicar seu projeto do Azure, talvez seja útil testá-lo em um ambiente independente que ofereça suporte a cenários de teste e depuração, e onde seja possível instalar programas de teste e monitoramento. Uma maneira de fazer isso é depurar remotamente seu aplicativo em uma máquina virtual.

Os projetos ASP.NET do Visual Studio oferecem uma opção para criar uma máquina virtual útil que você pode usar para teste de aplicativos. A máquina virtual inclui pontos de extremidade que geralmente são necessários, como PowerShell, área de trabalho remota e Implantação da Web.

### <a name="to-create-a-web-project-and-a-virtual-machine-for-debugging"></a>Para criar um projeto da Web e uma máquina virtual para depuração
1. No Visual Studio, crie um novo aplicativo Web do ASP.NET.
2. Na caixa de diálogo Novo Projeto do ASP.NET, na seção Azure, escolha **Máquina Virtual** na caixa de listagem suspensa. Deixe a caixa de seleção **Criar recursos remotos** marcada. Selecione **OK** para continuar.

    A caixa de diálogo **Criar máquina virtual no Azure** é exibida.

    ![Caixa de diálogo Criar projeto da Web do ASP.NET](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **Observação:** você será solicitado a entrar na conta do Azure, caso ainda não tenha entrado.

1. Selecione as diversas configurações para a máquina virtual e escolha **OK**. Consulte [Máquinas Virtuais](http://go.microsoft.com/fwlink/?LinkId=623033) para obter mais informações.

    Insira o nome DNS que será o nome da máquina virtual.

    ![Caixa de diálogo Criar máquina virtual no Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    O Azure cria a máquina virtual e provisiona e configura os pontos de extremidade, como Área de Trabalho Remota e Implantação da Web.
2. Depois que a máquina virtual estiver totalmente configurada, selecione o nó da máquina virtual no Gerenciador de Servidores.
3. Abra o menu de contexto e selecione **Habilitar Depuração**. Quando perguntado se você tem certeza de que deseja habilitar a depuração na máquina virtual, selecione **Sim**.

    O Azure instala a extensão da depuração remota na máquina virtual para habilitar a depuração.

    ![Comando Depuração habilitada para máquina virtual](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Log de atividades do Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)
4. Publique seu projeto, como descrito em [Como implantar um projeto Web usando a publicação de um clique no Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx). Como você deseja depurar na máquina virtual, na página **Configurações** do assistente **Publicar Web**, selecione **Depurar** como a configuração. Isso garante que os símbolos de código estejam disponíveis durante a depuração.

    ![Configurações de publicação](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)
5. Em **Opções de Publicação de Arquivo**, selecione **Remover arquivos adicionais no destino** se o projeto já tiver sido implantado anteriormente.
6. Depois de publicar o projeto, no menu de contexto da máquina virtual no Gerenciador de Servidores, selecione **Anexar depurador...**

    O Azure obtém uma lista dos processos na máquina virtual e os mostra na caixa de diálogo Anexar ao Processo.

    ![Comando Anexar depurador](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)
7. Na caixa de diálogo **Anexar ao Processo**, escolha **Selecionar** a fim de limitar a lista de resultados para mostrar apenas os tipos de código que você deseja depurar. Você pode depurar um código gerenciado de 32 ou 64 bits, código nativo ou ambos.

    ![Caixa de diálogo Selecionar tipo de código](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)
8. Clique nos processos que você deseja depurar na máquina virtual e selecione **Anexar**. Por exemplo, você pode escolher o processo w3wp.exe se quiser depurar um aplicativo Web na máquina virtual. Consulte [Depurar um o mais processos no Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
* Use o **Intellitrace** para coletar um log de chamadas e eventos de um servidor de liberação. Consulte [Depurando um serviço de nuvem publicado com o IntelliTrace e o Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016).
* Use o **Diagnóstico do Azure** para registrar em log informações detalhadas do código em execução nas funções, estejam elas em execução no ambiente de desenvolvimento ou no Azure. Consulte [Coletando dados de log usando o Diagnóstico do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).
