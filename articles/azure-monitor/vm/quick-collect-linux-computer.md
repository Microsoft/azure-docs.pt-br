---
title: 'Início Rápido: Coletar dados de um Computador Linux híbrido com o Azure Monitor'
description: Neste início rápido, você aprenderá a implantar o agente do Log Analytics para computadores Linux em execução fora do Azure e habilitará a coleta de dados com Logs do Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/24/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: dbe51930ec92ec4f89738dc5d543003f45acebf9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733815"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Início Rápido: Coletar dados de um computador Linux em um ambiente híbrido com o Azure Monitor

O [Azure Monitor](../overview.md) pode coletar dados diretamente dos computadores Linux físicos ou virtuais em seu ambiente em um único workspace do Log Analytics para correlação e análise detalhadas. A instalação do [agente do Log Analytics](../agents/log-analytics-agent.md) permite que o Azure Monitor colete dados de um datacenter ou de outro ambiente de nuvem. Este guia de início rápido mostra como configurar e coletar dados do servidor Linux com algumas etapas simples. Para saber mais sobre VMs Linux do Azure, confira [Coletar dados sobre máquinas virtuais do Azure](./quick-collect-azurevm.md).  

Para entender a configuração com suporte, confira [Sistemas operacionais com suporte](../agents/agents-overview.md#supported-operating-systems) e [Configuração de firewall de rede](../agents/log-analytics-agent.md#network-requirements).
 
Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar um workspace

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Escolha **workspaces do Log Analytics**.

    ![Como encontrar um workspace do Log Analytics no portal do Azure](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Selecione **Criar** e, em seguida, selecione opções para os seguintes itens:

   * Forneça um nome para o novo **Espaço de Trabalho do Log Analytics**, como *DefaultLAWorkspace*.  
   * Selecione uma **Assinatura** a vincular escolhendo uma na lista suspensa, se a selecionada por padrão não é adequada.
   * Para **Grupo de Recursos**, selecione um grupo de recursos existente que contém uma ou mais máquinas virtuais do Azure.  
   * Selecione o **Local** no qual as VMs serão implantadas.  Para obter mais informações, consulte em quais [regiões o Log Analytics está disponível](https://azure.microsoft.com/regions/services/).
   * Se você estiver criando um workspace em uma nova assinatura feita depois de 2 de abril de 2018, ele usará o plano de preços *por GB* e a opção de selecionar um tipo de preço não estará disponível.  Se você estiver criando um workspace para uma assinatura existente feita antes de 2 de abril ou uma assinatura associada a uma inscrição de EA existente, selecione seu tipo de preço preferido.  Para obter mais informações sobre os tipos específicos, consulte [Detalhes de preço do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Como criar um workspace do Log Analytics no portal do Azure](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Depois de fornecer as informações necessárias no painel do **workspace do Log Analytics**, selecione **OK**.  

Enquanto as informações são verificadas e o workspace é criado, você pode acompanhar seu progresso no menu **Notificações**. 

## <a name="obtain-workspace-id-and-key"></a>Obter a ID do workspace e a chave

Antes de instalar o agente do Log Analytics para Linux, você precisa da ID do espaço de trabalho e da chave para o espaço de trabalho do Log Analytics. Essas informações são exigidas pelo script de wrapper de agente para configurar o agente corretamente e garantir que ele pode se comunicar com o Azure Monitor com êxito.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. No canto superior esquerdo do portal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, insira **Log Analytics**. A lista filtra com base em sua entrada, à medida que você digita. Escolha **workspaces do Log Analytics**.

2. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho criado anteriormente. Você deve ter atribuído a ele o nome de **DefaultLAWorkspace**.

3. Selecione o **Gerenciamento de agentes**:
 
4. Em seguida, selecione **Servidores Linux**.

5. O valor à direita da **ID do Workspace** e da **Chave primária**. Copie e cole os dois em seu editor favorito.

## <a name="install-the-agent-for-linux"></a>Instalar o agente para Linux

As etapas a seguir configuram a instalação do agente para Log Analytics no Azure e na nuvem do Azure Governamental.  

>[!NOTE]
>O agente do Log Analytics para Linux não pode ser configurado para se reportar a mais de um espaço de trabalho do Log Analytics.  

Se seu computador Linux precisa se comunicar através de um servidor proxy para o Log Analytics, a configuração de proxy pode ser especificada na linha de comando, incluindo `-p [protocol://][user:password@]proxyhost[:port]`.  A propriedade *proxyhost* aceita um nome de domínio totalmente qualificado ou o endereço IP do servidor proxy. 

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

1. Para configurar o computador Linux para se conectar ao workspace do Log Analytics, execute o seguinte comando fornecendo a ID do workspace e a chave primária copiadas anteriormente. O comando a seguir baixa o agente, valida sua soma de verificação e o instala. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    O seguinte comando inclui o parâmetro de proxy `-p` e a sintaxe de exemplo quando a autenticação é exigida pelo servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Para configurar o computador Linux para se conectar ao workspace do Log Analytics na nuvem do Azure Governamental, execute o seguinte comando fornecendo a ID do workspace e a chave primária copiadas anteriormente. O comando a seguir baixa o agente, valida sua soma de verificação e o instala. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    O seguinte comando inclui o parâmetro de proxy `-p` e a sintaxe de exemplo quando a autenticação é exigida pelo servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```

3. Reinicie o agente executando o seguinte comando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Coletar dados de desempenho e eventos

O Azure Monitor pode coletar eventos especificados do Syslog do Linux e de contadores de desempenho especificados para análise e geração de relatórios de longo prazo. Além disso, o recurso pode realizar uma ação quando detectar uma condição específica. Siga estas etapas para configurar a coleta de eventos do Syslog do Linux e de vários contadores de desempenho comuns para começar.  

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite o Log Analytics. A lista filtra com base em sua entrada, à medida que você digita. Selecione **workspaces do Log Analytics** e, em sua lista de workspaces do Log Analytics, selecione o workspace que você está procurando e selecione **Configurações avançadas** do workspace **log Analytics**.

2. Selecione **Dados** e **Syslog**.  

3. Adicione o sysloglog digitando o nome do log. Insira **Syslog** e, em seguida, selecione o sinal de adição **+** .  

4. Na tabela, desmarque as severidades **Informações**, **Aviso** e **Depuração**. 

5. Selecione **Salvar** na parte superior da página para salvar a configuração.

6. Selecione **Dados de Desempenho do Linux** para habilitar a coleta de contadores de desempenho em um computador Linux. 

7. Quando você configura os contadores de desempenho do Linux para um novo espaço de trabalho do Log Analytics pela primeira vez, você tem a opção de criar rapidamente vários contadores comuns. Eles são listados com uma caixa de seleção ao lado de cada um.

    ![Contadores de desempenho padrão do Linux selecionados no Azure Monitor](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Selecione **Aplicar configuração abaixo aos meus computadores** e, em seguida, selecione **Adicionar os contadores de desempenho selecionados**. Eles são adicionados e predefinidos com um intervalo de amostragem de coleta de dez segundos.  

8. Selecione **Salvar** na parte superior da página para salvar a configuração.

## <a name="view-data-collected"></a>Exibir os dados coletados

Agora que você habilitou a coleta de dados, vamos executar um exemplo simples de pesquisa de logs para ver alguns dados do computador de destino.  

1. No workspace selecionado, no painel esquerdo, selecione **Logs**.

2. Na página de consulta de logs, digite `Perf` no editor de consulta e selecione **Executar**.
 
    ![Pesquisa de logs do Log Analytics](media/quick-collect-linux-computer/log-analytics-portal-queryexample.png)

    Por exemplo, a consulta na imagem a seguir retornou 10 mil registros de desempenho. Os resultados serão significativamente menores.

    ![Resultado da pesquisa de logs do Log Analytics](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá remover o agente do computador Linux e excluir o espaço de trabalho do Log Analytics.  

Para remover o agente, execute o comando a seguir no computador Linux. O argumento *--purge* remove completamente o agente e sua configuração.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Para excluir o workspace, selecione o workspace do Log Analytics criado anteriormente e, na página de recursos, selecione **Excluir**.

![Excluir um recurso do Log Analytics](media/quick-collect-linux-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você está coletando dados operacionais e de desempenho do computador Linux local, você pode facilmente começar a explorar, analisar e agir em relação aos dados coletados *gratuitamente*.  

Para saber como exibir e analisar os dados, continue lendo o tutorial.

> [!div class="nextstepaction"]
> [Exibir ou analisar dados no Log Analytics](../logs/log-analytics-tutorial.md)