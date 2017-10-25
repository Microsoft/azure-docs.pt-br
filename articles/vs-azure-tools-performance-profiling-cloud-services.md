---
title: "Testando o desempenho de um serviço de nuvem | Microsoft Docs"
description: "Testar o desempenho de um serviço de nuvem usando o criador de perfil do Visual Studio"
services: visual-studio-online
documentationcenter: n/a
author: kraigb
manager: ghogen
editor: 
ms.assetid: 7a5501aa-f92c-457c-af9b-92ea50914e24
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: eafcc2f9d53bcdae63036df070e5adec24cbc252
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="testing-the-performance-of-a-cloud-service"></a>Testando o desempenho de um serviço de nuvem
## <a name="overview"></a>Visão geral
Você pode testar o desempenho de um serviço de nuvem das seguintes maneiras:

* Use o Diagnóstico do Azure para coletar informações sobre solicitações e conexões e para examinar estatísticas de site que mostram o desempenho do serviço da perspectiva do cliente. Para começar, consulte [Configuração de diagnóstico para os Serviços de Nuvem do Azure e Máquinas Virtuais](http://go.microsoft.com/fwlink/p/?LinkId=623009).
* Use o criador de perfil do Visual Studio para obter uma análise detalhada dos aspectos computacionais de como o serviço é executado. Como descrito neste tópico, você pode usar o criador de perfil para medir o desempenho à medida que um serviço é executado no Azure. Para saber mais sobre como usar o criador de perfil para medir o desempenho à medida que um serviço é executado localmente em um emulador de computação, consulte [Testando o desempenho de um Serviço de Nuvem de Azure localmente no emulador de computação usando o criador de perfil do Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=262845).

## <a name="choosing-a-performance-testing-method"></a>Escolhendo um método de teste de desempenho
### <a name="use-azure-diagnostics-to-collect"></a>Use o Diagnóstico do Azure para coletar:
* Estatísticas em páginas ou em da Web, como solicitações e conexões.
* Estatísticas sobre funções, como a frequência com que uma função é reiniciada.
* Informações gerais sobre o uso de memória, como a porcentagem de tempo que o coletor de lixo ocupa ou o conjunto de memória de uma função em execução.

### <a name="use-the-visual-studio-profiler-to"></a>Use o criador de perfil do Visual Studio para:
* Determinar quais funções demoram mais.
* Medir quanto tempo leva cada parte de um programa de computação intensivo.
* Comparar relatórios detalhados de desempenho de duas versões de um serviço.
* Analisar a alocação de memória em mais detalhes do que o nível de alocações de memória individuais.
* Analisar problemas de simultaneidade em código multi-threaded.

Quando você usa o criador de perfil, pode coletar dados quando um serviço de nuvem é executado localmente ou no Azure.

### <a name="collect-profiling-data-locally-to"></a>Colete dados de perfil localmente para:
* Testar o desempenho de uma parte de um serviço de nuvem, como a execução de uma função de trabalho específica, que não exige uma carga simulada realística.
* Testar o desempenho de um serviço de nuvem isoladamente, em condições controladas.
* Testar o desempenho de um serviço de nuvem antes de implantá-lo no Azure.
* Testar o desempenho de um serviço de nuvem em particular, sem afetar as implantações existentes.
* Testar o desempenho do serviço sem incorrer em encargos para execução no Azure.

### <a name="collect-profiling-data-in-azure-to"></a>Colete dados de perfil no Azure para:
* Testar o desempenho de um serviço de nuvem sob uma carga real ou simulada.
* Usar o método de instrumentação de coletar dados de criação de perfil, como este tópico descreverá posteriormente.
* Testar o desempenho do serviço no mesmo ambiente, como quando o serviço é executado na produção.

Você geralmente simula uma carga para testar serviços de nuvem em condições normais ou de estresse.

## <a name="profiling-a-cloud-service-in-azure"></a>Criando um perfil de serviço de nuvem no Azure
Ao publicar seu serviço de nuvem do Visual Studio, você poderá analisar o serviço e especificar as configurações de criação de perfil que fornecem as informações desejadas. Uma sessão de criação de perfil é iniciada para cada instância de uma função. Para obter mais informações sobre como publicar seu serviço do Visual Studio, consulte [Publicando em um Serviço de Nuvem do Azure do Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Para saber mais sobre o perfil de desempenho no Visual Studio, consulte [Guia de criação de perfis de desempenho para iniciantes](https://msdn.microsoft.com/library/azure/ms182372.aspx) e [Analisando o desempenho do aplicativo usando ferramentas de criação de perfil](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

> [!NOTE]
> Você pode habilitar o IntelliTrace ou a criação de perfis quando publicar seu serviço de nuvem. Não é possível habilitar ambos.
> 
> 

### <a name="profiler-collection-methods"></a>Métodos de coleção do criador de perfil
Você pode usar diferentes métodos de coleção para a criação de perfil com base nos seus problemas de desempenho:

* **Amostragem de CPU** - esse método coleta estatísticas de aplicativo que são úteis para a análise inicial de problemas de utilização de CPU. A amostragem de CPU é o método sugerido para iniciar a maioria das investigações de desempenho. Há pouco impacto sobre o aplicativo para o qual o perfil está sendo criado quando você coleta dados de amostragem de CPU.
* **Instrumentação** - esse método coleta dados detalhados de tempo úteis para a análise concentrada e para analisar problemas de desempenho de entrada/saída. O método de instrumentação registra cada entrada, saída e a chamada de função das funções em um módulo durante uma execução da criação de perfil. Este método é útil para coletar informações detalhadas de tempo sobre uma seção de seu código e para compreender o impacto das operações de entrada e de saída no desempenho do aplicativo. Este método está desabilitado para um computador que esteja executando um sistema operacional de 32 bits. Esta opção só estará disponível quando você executar o serviço de nuvem no Azure, e não localmente no emulador de computação.
* **Alocação de memória do .NET** -esse método coleta dados de alocação de memória do .NET Framework usando o método de criação de perfil de amostragem. Os dados coletados incluem o número e o tamanho dos objetos alocados.
* **Simultaneidade** - esse método coleta dados de contenção de recursos e dados de execução de thread e processo úteis na análise de aplicativos multi-threaded e multiprocessados. O método de simultaneidade coleta dados para cada evento que bloqueia a execução de seu código, como quando um thread aguarda que o acesso bloqueado a um recurso de aplicativo seja liberado. Este método é útil para analisar aplicativos multi-threaded.
* Você também pode habilitar **Criação de Perfil de Interação de Camadas**, que oferece informações adicionais sobre os tempos de execução de chamadas ADO.NET síncronas em funções de aplicativos de várias camadas que se comunicam com um ou mais bancos de dados. Você pode coletar dados de interação de camadas com qualquer um dos métodos de criação de perfil. Para saber mais sobre a criação de perfil de interação de camadas, consulte [Exibição de interações de camada](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## <a name="configuring-profiling-settings"></a>Definindo configurações de criação de perfil
A seguinte ilustração mostra como definir as configurações de criação de perfil na caixa de diálogo Publicar Aplicativo do Azure.

![Definir configurações de criação de perfil](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

> [!NOTE]
> Para habilitar a caixa de seleção **Habilitar criação de perfil** , você deve ter o criador de perfil instalado no computador local que está usando para publicar seu serviço de nuvem. Por padrão, o criador de perfil é instalado quando você instala o Visual Studio.
> 
> 

### <a name="to-configure-profiling-settings"></a>Para definir configurações de criação de perfil
1. No Gerenciador de Soluções, abra o menu de atalho para o seu projeto do Azure e escolha **Publicar**. Para obter etapas detalhadas sobre como publicar um serviço de nuvem, consulte [Publicando um serviço na nuvem usando as ferramentas do Azure](http://go.microsoft.com/fwlink/p?LinkId=623012).
2. Na caixa de diálogo **Publicar Aplicativo do Azure**, escolha a guia **Configurações Avançadas**.
3. Para habilitar a criação de perfil, marque a caixa de seleção **Habilitar criação de perfil** .
4. Para definir suas configurações de criação de perfil, escolha o hiperlink **Configurações** . A caixa de diálogo Configurações de Criação de Perfil aparecerá.
5. Dos botões de opção **Qual método de criação de perfil você gostaria de usar** , escolha o tipo de criação de perfil de que você precisa.
6. Para coletar os dados de criação de perfil de interação de camada, marque a caixa de seleção **Habilitar Criação de Perfil de Interação de Camada** .
7. Para salvar as configurações, escolha o **OK** botão.
   
    Quando você publica este aplicativo, essas configurações são usadas para criar a sessão de criação de perfil para cada função.

## <a name="viewing-profiling-reports"></a>Exibindo relatórios de criação de perfil
Uma sessão de criação de perfil é gerada para cada instância de uma função no serviço de nuvem. Para exibir seus relatórios de criação de perfil de cada sessão do Visual Studio, você pode exibir a janela Gerenciador de Servidores e escolher o nó de computação do Azure para selecionar uma instância de uma função. Em seguida, será possível exibir o relatório de criação de perfil como mostrado na ilustração a seguir.

![Exibir relatórios de criação de perfil do Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>Para exibir relatórios de criação de perfil
1. Para exibir a janela Gerenciador de Servidores no Visual Studio, na barra de menus, escolha Exibir, Gerenciador de Servidores.
2. Escolha o nó de Computação do Azure e escolha o nó de implantação do Azure para o serviço de nuvem que você selecionou para o perfil ao publicar no Visual Studio.
3. Para exibir relatórios de criação de perfil para uma instância, escolha a função no serviço, abra o menu de atalho para uma instância específica e, em seguida, escolha **Exibir Relatório de Criação de Perfil**.
   
    O relatório, um arquivo .vsp, agora é baixado do Azure e o status do download é exibido no log de atividades do Azure. Quando o download é concluído, o relatório de criação de perfil é exibido em uma guia no editor do Visual Studio denominada <Role name>*<Instance Number>*<identifier>.vsp. Serão exibidos dados de resumo para o relatório.
4. Para mostrar diferentes modos de exibição do relatório, na lista Modo de Exibição Atual, escolha o tipo de exibição desejado. Para saber mais, consulte [Exibições de relatório de ferramentas de criação de perfil](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## <a name="next-steps"></a>Próximas etapas
[Depurando serviços de nuvem](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Publicando em um Serviço de Nuvem do Azure do Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

