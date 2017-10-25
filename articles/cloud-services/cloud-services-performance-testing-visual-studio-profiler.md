---
title: "Criação de um perfil de serviço de nuvem localmente no emulador de computação | Microsoft Docs"
services: cloud-services
description: "Investigar problemas de desempenho nos serviços de nuvem com o criador de perfil do Visual Studio"
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
tags: 
ms.assetid: 25e40bf3-eea0-4b0b-9f4a-91ffe797f6c3
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/18/2016
ms.author: kraigb
ms.openlocfilehash: 51c8192d8312dc5cf546b4c357aeecf6f19d56b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testando o desempenho de um serviço de nuvem localmente no emulador de computação do Azure usando o criador de perfis do Visual Studio
Várias técnicas e ferramentas estão disponíveis para testar o desempenho de serviços de nuvem.
Quando publica um serviço de nuvem no Azure, você pode fazer com que o Visual Studio colete dados de criação de perfil e, em seguida, os analise localmente, conforme descrito em [Criar o perfil de um aplicativo do Azure][1].
Você também pode usar o diagnóstico para acompanhar vários contadores de desempenho, conforme descrito em [Usar contadores de desempenho no Azure][2].
Também convém criar o perfil de seu aplicativo localmente no emulador de computação antes de implantá-lo na nuvem.

Este artigo aborda o método de Amostragem de CPU de criação de perfil, que pode ser executado localmente no emulador. A Amostragem de CPU é um método de criação de perfil que não é muito invasivo. Em um intervalo de amostragem designado, o criador de perfis tira um instantâneo da pilha de chamadas. Os dados são coletados durante um período de tempo e mostrados em um relatório. Esse método de criação de perfis tende a indicar onde está sendo feita a maior parte do trabalho em um aplicativo que utiliza muitos recursos de computação.  Isso oferece a oportunidade de focalizar o "afunilamento" onde seu aplicativo está gastando mais tempo.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Configurar o Visual Studio para criação de perfil
Em primeiro lugar, há algumas opções de configuração do Visual Studio que podem ser úteis ao criar perfis. Para compreender os relatórios de criação de perfis, você precisará de símbolos (arquivos .pdb) para seu aplicativo e também de símbolos para as bibliotecas do sistema. Você deve certificar-se de fazer referência aos servidores de símbolo disponíveis. Para fazer isso, no menu **Ferramentas** do Visual Studio, escolha **Opções**, escolha **Depuração** e, em seguida, **Símbolos**. Verifique se os Servidores de Símbolo da Microsoft estão listados em **Locais do arquivo de símbolos (.pdb)**.  Você também pode fazer referência a http://referencesource.microsoft.com/symbols, que pode ter arquivos de símbolos adicionais.

![Opções de símbolo][4]

Se desejar, você pode simplificar os relatórios que o criador de perfis gera definindo Apenas Meu Código. Com a opção Apenas Meu Código habilitada, as pilhas de chamadas de função são simplificadas de maneira que as chamadas inteiramente internas às bibliotecas e ao .Net Framework sejam ocultados dos relatórios. No menu **Ferramentas**, escolha **Opções**. Em seguida, expanda o nó **Ferramentas de Desempenho** e escolha **Geral**. Marque a caixa de seleção **Habilitar Apenas Meu Código para relatórios do criador de perfis**.

![Opções Apenas Meu Código][17]

Você pode usar essas instruções com um projeto existente ou com um novo projeto.  Se você criar um novo projeto para testar as técnicas descritas a seguir, escolha um projeto C# do **Serviço de Nuvem do Azure** e selecione uma **Função Web** e uma **Função de Trabalho**.

![Funções de projeto do Serviço de Nuvem do Azure][5]

Para fins de exemplo, adicione um código ao seu projeto que demore muito tempo e demonstre alguns problemas óbvios de desempenho. Por exemplo, adicione o seguinte código a um projeto de função de trabalho:

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = "";
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
    }

Chame esse código do método RunAsync na classe derivada de RoleEntryPoint da função de trabalho. (Ignore o alerta relacionado ao método estar em execução de modo sincronizado.)

        private async Task RunAsync(CancellationToken cancellationToken)
        {
            // TODO: Replace the following with your own logic.
            while (!cancellationToken.IsCancellationRequested)
            {
                Trace.TraceInformation("Working");
                Concatenator.Concatenate(10000);
            }
        }

Compile e execute seu serviço de nuvem localmente sem depuração (Ctrl+F5), com a configuração da solução definida como **Versão**. Isso garante que todos os arquivos e pastas sejam criados para executar o aplicativo localmente e garante que todos os emuladores estejam iniciados. Inicie a UI do Emulador de Computação na barra de tarefas para confirmar que sua função de trabalho esteja em execução.

## <a name="2-attach-to-a-process"></a>2: Anexar a um processo
Em vez de criar o perfil do aplicativo iniciando-o no IDE do Visual Studio 2010, você deve anexar o criador de perfis a um processo em execução. 

Para anexar o criador de perfis a um processo, no menu **Analisar**, escolha **Criador de Perfis** e **Anexar/Desanexar**.

![Opção Anexar perfil][6]

Para uma função de trabalho, localize o processo WaWorkerHost.exe.

![Processo WaWorkerHost][7]

Se a pasta de seu projeto estiver em uma unidade de rede, o criador de perfis solicitará que você forneça outro local para salvar os relatórios de criação de perfis.

 Também é possível conectar-se a uma função web, conectando-se ao WaIISHost.exe.
Se houver vários processos de função de trabalho em seu aplicativo, você precisará usar o processID para diferenciá-los. Você pode consultar o processID programaticamente acessando o objeto Process. Por exemplo, adicionando este código ao método Run da classe derivada de RoleEntryPoint em uma função, você pode examinar o log na interface do usuário do Emulador de Computação para saber a qual processo conectar-se.

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

Para exibir o log, inicie a interface do usuário do Emulador de Computação.

![Iniciar a IU do Emulador de Computação][8]

Abra a janela do console do log da função de trabalho na interface do usuário do Emulador de Computação clicando na barra de títulos da janela do console. Você pode ver a ID do processo no log.

![Exibir ID do processo][9]

Depois de conectar-se, execute as etapas na interface do usuário do seu aplicativo (se necessário) para reproduzir o cenário.

Quando desejar parar a criação de perfis, escolha o link **Parar Criação de Perfis** .

![Opção Parar perfil][10]

## <a name="3-view-performance-reports"></a>3: Exibir relatórios de desempenho
O relatório de desempenho de seu aplicativo é exibido.

Nesse ponto, o criador de perfis interromperá a execução, salvará os dados em um arquivo .vsp e exibirá um relatório que mostra uma análise dos dados.

![Relatório do criador de perfil][11]

Se você vir String.wstrcpy no Afunilamento, clique em Apenas Meu Código para alterar a exibição para mostrar somente o código do usuário.  Se você vir String.Concat, tente pressionar o botão Mostrar Todo o Código.

Você verá o método Concatenate e o String.Concat tomando uma grande parte do tempo de execução.

![Análise do relatório][12]

Se você adicionou o código de concatenação de cadeia de caracteres deste artigo, verá um aviso na Lista de Tarefas por isso. Você também poderá ver um aviso de que há uma quantidade excessiva de coleta de lixo, devido ao número de cadeias de caracteres que são criadas e descartadas.

![Avisos do desempenho][14]

## <a name="4-make-changes-and-compare-performance"></a>4: Fazer alterações e comparar o desempenho
Você também pode comparar o desempenho antes e depois de uma alteração no código.  Interrompa o processo em execução e edite o código para substituir a operação de concatenação de cadeia de caracteres pelo uso de StringBuilder:

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

Realize outra execução de desempenho e, em seguida, compare o desempenho. No Gerenciador de Desempenho, se as execuções forem na mesma sessão, você poderá selecionar os dois relatórios, abrir o menu de atalho e escolher **Comparar Relatórios de Desempenho**. Se desejar comparar com uma execução em outra sessão de desempenho, abra o menu **Analisar** e escolha **Comparar Relatórios de Desempenho**. Especifique os dois arquivos na caixa de diálogo que é exibida.

![Opção Comparar relatórios do desempenho][15]

Os relatórios destacam as diferenças entre as duas execuções.

![Relatório da comparação][16]

Parabéns! Você começou a usar o criador de perfis.

## <a name="troubleshooting"></a>Solucionar problemas
* Verifique se você está criando o perfil de uma compilação de versão e inicie sem depurar.
* Se a opção Conectar/Desconectar não estiver habilitada no menu Criador de Perfis, execute o Assistente de Desempenho.
* Use a interface do usuário do Emulador de Computação para exibir o status do seu aplicativo. 
* Se tiver problemas para iniciar aplicativos no emulador ou para conectar o criador de perfis, desligue e reinicie o emulador de computação. Se isso não resolver o problema, tente reinicializar. Esse problema pode ocorrer se você usar o Emulador de Computação para suspender e remover implantações em execução.
* Se você tiver usado qualquer um dos comandos de criação de perfil na linha de comando, especialmente as configurações globais, verifique se VSPerfClrEnv / globaloff foi chamado e se VsPerfMon.exe foi desligado.
* Se durante a amostragem você vir a mensagem "PRF0025: nenhum dado foi coletado", verifique se o processo ao qual você se conectou tem atividade de CPU. Os aplicativos que não estão fazendo nenhum trabalho de computação podem não produzir nenhum dado de amostragem.  Também é possível que o processo tenha sido encerrado antes de qualquer amostra ter sido feita. Verifique se o método Run para uma função para a qual você esteja criando um perfil não é encerrado.

## <a name="next-steps"></a>Próximas etapas
A instrumentação de binários do Azure no emulador não tem suporte no criador de perfis do Visual Studio, mas para testar a alocação de memória, você poderá escolher essa opção ao criar um perfil. Você também pode escolher a criação de perfis simultânea, que ajuda a determinar se os threads estão perdendo tempo competindo por bloqueios, ou a criação de perfis de interação de camadas, que ajuda a rastrear problemas de desempenho ao interagir entre camadas de um aplicativo, mais frequentemente entre a camada de dados e uma função de trabalho.  Você pode exibir as consultas do banco de dados que seu aplicativo gera e usar os dados da criação de perfis para melhorar o uso do banco de dados. Para obter informações sobre a criação de perfis de interação de camadas, veja a postagem no blog [Walkthrough: Using the Tier Interaction Profiler in Visual Studio Team System 2010][3] (Passo a passo: usando o criador de perfis de interação de camadas no Visual Studio Team System 2010).

[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
