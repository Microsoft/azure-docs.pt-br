---
title: Configurar o Monitor de Desempenho de Rede para circuitos do ExpressRoute – Azure | Microsoft Docs
description: Configure o monitoramento de rede baseado em nuvem (NPM) para circuitos do Azure ExpressRoute. Isso abrange o monitoramento através do emparelhamento privado do ExpressRoute e emparelhamento da Microsoft.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 180075f13be2cc2507a78e3d10a67a49a0c0cb12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60839958"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Configurar o Monitor de Desempenho de Rede para ExpressRoute

Este artigo ajuda você a configurar uma extensão do Monitor de Desempenho de Rede para monitorar o ExpressRoute. O Monitor de Desempenho de Rede (NPM) é uma solução de monitoramento de rede baseado em nuvem que monitora a conectividade entre implantações de nuvem do Azure e instalações locais (Filiais etc.). O NPM faz parte dos logs do Azure Monitor. O NPM oferece uma extensão para o ExpressRoute, que permite que você monitore o desempenho da rede sobre os circuitos do ExpressRoute, que são configurados para usar o emparelhamento privado ou emparelhamento Microsoft. Ao configurar o NPM para ExpressRoute, você poderá detectar problemas de rede para identificar e eliminar. Esse serviço também está disponível para a nuvem do Azure governamental.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Você pode:

* Monitorar perda e latência entre vários VNets e definir alertas

* Monitorar todos os caminhos (incluindo caminhos redundantes) na rede

* Solucionar problemas de rede transitórios e pontuais que são difíceis de replicar

* Ajudar a determinar um segmento específico na rede que é responsável pelo desempenho degradado

* Obter a taxa de transferência por rede virtual (se você tiver agentes instalados em cada VNet)

* Consulte o estado do sistema do ExpressRoute de um ponto anterior no tempo

## <a name="workflow"></a>Fluxo de trabalho

Agentes de monitoramento são instalados em vários servidores, locais e no Azure. Os agentes se comunicam entre si, mas não enviam dados, enviam pacotes de handshake de TCP. A comunicação entre os agentes permite que o Azure mapeie a topologia de rede e o caminho que o tráfego pode levar.

1. Crie um workspace NPM. Isso é igual a um espaço de trabalho do Log Analytics.
2. Instale e configure agentes de software. (Caso deseje monitorar somente o Emparelhamento da Microsoft, você não precisará instalar e configurar agentes de software): 
    * Instale agentes de monitoramento nos servidores locais e nas VMs do Azure (para emparelhamento privado).
    * Defina as configurações nos servidores de agente de monitoramento para permitir que os agentes de monitoramentos se comuniquem. (Abra as portas do firewall etc.)
3. Configure as regras de grupo de segurança de rede (NSG) para permitir que o agente de monitoramento instalado nas VMs do Azure se comunique com os agentes de monitoramento local.
4. Configurar monitoramento: Detecte automaticamente e gerencie as redes que estão visíveis no NPM.

Se você já estiver usando o Monitor de Desempenho de Rede para monitorar outros objetos ou serviços e já tiver o Workspace em uma das regiões com suporte, pode ignorar as Etapas 1 e 2 e começar a configuração pela Etapa 3.

## <a name="configure"></a>Etapa 1: Criar um workspace

Criar um workspace na assinatura que tem o link das VNETs ao(s) circuito(s) do ExpressRoute.

1. No [Portal do Azure](https://portal.azure.com), selecione a Assinatura que tem as VNETs emparelhadas com o seu circuito do ExpressRoute. Depois, pesquise o "Monitor de Desempenho de Rede" na lista de serviços do **Marketplace**. Na volta, clique para abrir a página **Monitor de Desempenho de Rede**.

   >[!NOTE]
   >Você pode criar um novo workspace ou usar um existente. Se você quiser usar um workspace existente, certifique-se de que o workspace tenha sido migrado para a nova linguagem de consulta. [Mais informações...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. Na parte inferior da página principal **Monitor de Desempenho de Rede**, clique em **Criar** para abrir a página **Monitor de Desempenho de Rede – Criar nova solução**. Clique em **Espaço de Trabalho do Log Analytics – selecionar um espaço de trabalho** para abrir a página Espaços de Trabalho. Clique em **+ Criar Novo Workspace** para abrir a página de Workspace.
3. Sobre o **espaço de trabalho do Log Analytics** página, selecione **criar novo**, em seguida, defina as seguintes configurações:

   * Espaço de Trabalho do Log Analytics - digite um nome para o espaço de trabalho.
   * Assinatura: se você tiver várias assinaturas, selecione aquela que você deseja associar ao novo Workspace.
   * Grupo de recursos: crie um grupo de recursos ou use um existente.
   * Local - esse local é usado para especificar o local da conta de armazenamento que é usada para os logs de conexão do agente.
   * Tipo de preço - selecione um tipo de preço.
  
     >[!NOTE]
     >O circuito do ExpressRoute pode estar em qualquer lugar do mundo. Ele não precisa estar na mesma região que o workspace.
     >
  
     ![workspace](./media/how-to-npm/4.png)<br><br>
4. Clique em **OK** para salvar e implantar o modelo de configurações. Depois que o modelo for validado, clique em **Criar** para implantar o Workspace.
5. Depois que o Workspace for implantado, navegue até o recurso **NetworkMonitoring(name)** que você criou. Valide as configurações e clique em **Solução requer configuração adicional**.

   ![configuração adicional](./media/how-to-npm/5.png)

## <a name="agents"></a>Etapa 2: Instalar e configurar agentes

### <a name="download"></a>2.1: Baixar o arquivo de instalação do agente

1. Vá para a guia **Configurações comuns** da página **Configuração do Monitor de Desempenho de Rede** para o recurso. Clique no agente que corresponde ao processador do servidor da seção **Instalar agentes do Log Analytics** e baixe o arquivo de instalação.
2. Em seguida, copie a **ID do Workspace** e a **Chave Primária** para o Bloco de Notas.
3. Na seção **Configurar agentes do Log Analytics para monitoramento usando o protocolo TCP**, faça o download do Script do Powershell. O script do PowerShell ajuda você a abrir a porta de firewall relevante para as transações de TCP.

   ![Script do PowerShell](./media/how-to-npm/7.png)

### <a name="installagent"></a>2.2: Instalar um agente de monitoramento em cada servidor de monitoramento (em cada VNET que você deseja monitorar)

É recomendável que você instale pelo menos dois agentes em cada lado da conexão de ExpressRoute para redundância (por exemplo, no local, VNETs do Azure). O agente deve ser instalado em um servidor Windows (2008 SP1 ou posterior). Não há suporte para o monitoramento de circuitos do ExpressRoute usando o sistema operacional de área de trabalho do Windows e o sistema operacional Linux. Use as etapas a seguir para instalar os agentes:
   
  >[!NOTE]
  >Efetuar push de agentes pelo SCOM (incluindo [MMA](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)) talvez não possa detectar consistentemente a localização, se estiverem hospedados no Azure. É recomendável não utilizar esses agentes em VNETs do Azure para monitorar o ExpressRoute.
  >

1. Execute a **Instalação** para instalar o agente em cada servidor que deseja usar para o monitoramento de ExpressRoute. O servidor utilizado no monitoramento pode ser uma VM ou local e deve ter acesso à Internet. É necessário instalar pelo menos um agente local e um agente em cada segmento de rede que você deseja monitorar no Azure.
2. Na página de **Boas-vindas**, clique em **Avançar**.
3. Na página **Termos de Licença**, leia a licença e clique em **Aceito**.
4. Na página **Pasta de Destino**, altere ou mantenha a pasta de instalação padrão e clique em **Avançar**.
5. Sobre o **opções de instalação do agente** página, que você pode optar por conectar o agente do Operations Manager ou de logs do Azure Monitor. Ou, você poderá deixar as opções em branco se quiser configurar o agente mais tarde. Após fazer suas seleções, clique em **Avançar**.

   * Se você optar por conectar-se ao **Azure Log Analytics**, cole a **ID do Workspace** e a **Chave do Workspace** (Chave Primária) que você copiou para o Bloco de Notas na seção anterior. Em seguida, clique em **Avançar**.

     ![ID e a Chave](./media/how-to-npm/8.png)
   * Se você optar por conectar-se ao **Operations Manager**, na página **Configuração de Grupo de Gerenciamento**, digite o **Nome do Grupo de Gerenciamento**, **Servidor de Gerenciamento** e **Porta do Servidor de Gerenciamento**. Em seguida, clique em **Avançar**.

     ![Operations Manager](./media/how-to-npm/9.png)
   * Na página **Conta de Ação de Agente**, escolha a conta **Sistema Local** ou a **Conta de Computador Local ou Domínio**. Em seguida, clique em **Avançar**.

     ![Conta](./media/how-to-npm/10.png)
6. Na página **Pronto para Instalar**, examine suas escolhas e clique em **Instalar**.
7. Na página **Configuração concluída com êxito**, clique em **Concluir**.
8. Após concluir, o Microsoft Monitoring Agent aparecerá no Painel de Controle. Você pode examinar sua configuração e verificar se o agente está conectado aos logs do Azure Monitor. Quando conectado, o agente exibe uma mensagem dizendo: **O Microsoft Monitoring Agent conectou-se com êxito ao serviço Microsoft Operations Management Suite**.

9. Repita este procedimento para cada VNET que precisa ser monitorada.

### <a name="proxy"></a>2.3: Definir configurações de proxy (opcional)

Caso esteja usando um proxy da Web para acessar a Internet, utilize as etapas a seguir para definir configurações de proxy para o Microsoft Monitoring Agent. Execute essas etapas para cada servidor. Se você tiver vários servidores que precisa configurar, talvez seja mais fácil usar um script para automatizar esse processo. Se tiver, consulte [Definir configurações de proxy para o Microsoft Monitoring Agent usando um script](../log-analytics/log-analytics-windows-agent.md).

Para definir configurações de proxy para o Microsoft Monitoring Agent usando o Painel de Controle:

1. Abrir o **Painel de Controle**.
2. Abra o **Microsoft Monitoring Agent**.
3. Clique na guia **Configurações de Proxy** .
4. Selecione **Usar um servidor proxy** e digite a URL e o número da porta, se for necessário. Se o servidor proxy requer autenticação, digite o nome de usuário e senha para acessar o servidor proxy.

   ![proxy](./media/how-to-npm/11.png)

### <a name="verifyagent"></a>2.4: Verificar a conectividade do agente

É possível verificar facilmente se os agentes estão se comunicando.

1. Em um servidor com o agente de monitoramento, abra o **Painel de Controle**.
2. Abra o **Microsoft Monitoring Agent**.
3. Clique na guia **Azure Log Analytics**.
4. No **Status** coluna, você deverá ver que o agente conectou com êxito para os logs do Azure Monitor.

   ![status](./media/how-to-npm/12.png)

### <a name="firewall"></a>2.5: Abrir as portas do firewall nos servidores do agente de monitoramento

Para usar o protocolo TCP, você deve abrir as portas de firewall para garantir que os agentes de monitoramento possam se comunicar.

Você pode executar um script do PowerShell para criar as chaves do Registro exigidas pelo Monitor de Desempenho de Rede. Este script também cria regras de Firewall do Windows para permitir que os agentes monitorem conexões TCP entre si. As chaves do Registro criadas pelo script especificam se é preciso registrar os logs de depuração e o caminho para o arquivo de logs. Também é definida a porta TCP de agente usada para comunicação. Os valores para essas chaves são definidos automaticamente pelo script. Você não deve alterar manualmente essas chaves.

A porta 8084 é aberta por padrão. Você pode usar uma porta personalizada fornecendo o parâmetro 'portNumber' para o script. No entanto, caso faça isso, você deverá especificar a mesma porta para todos os servidores nos quais o script é executado.

>[!NOTE]
>O script do PowerShell 'EnableRules' configura regras de Firewall do Windows somente no servidor em que o script é executado. Se tiver um firewall de rede, você deverá verificar se ele permite o tráfego destinado à porta TCP usada pelo Monitor de Desempenho de Rede.
>
>

Nos servidores do agente, abra uma janela do PowerShell com privilégios administrativos. Execute o script do PowerShell [EnableRules](https://aka.ms/npmpowershellscript) (que você baixou anteriormente). Não use nenhum parâmetro.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="opennsg"></a>Etapa 3: Configurar regras do grupo de segurança de rede

Para monitorar servidores do agente que estão no Azure, você deve configurar regras do NSG (grupo de segurança de rede) para permitir o tráfego TCP em uma porta usada pelo NPM para transações sintéticas. A porta padrão é 8084. Isso permite que um agente de monitoramento instalado na VM do Azure se comunique com um agente de monitoramento local.

Para obter mais informações sobre os NSG, consulte [Grupos de Segurança de Rede](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Certifique-se de que você instalou os agentes (o agente do servidor local e o agente do servidor do Azure) e executou o script do PowerShell antes de continuar com esta etapa.
>

## <a name="setupmonitor"></a>Etapa 4: Descobrir conexões de emparelhamento

1. Navegue até o bloco de visão geral do Monitor de Desempenho de Rede ao acessar a página **Todos os Recursos**, então clique no Workspace de NPM na lista de permissões.

   ![workspace de NPM](./media/how-to-npm/npm.png)
2. Clique no bloco de visão geral do **Monitor de Desempenho de Rede** para exibir o painel. O painel contém uma página do ExpressRoute, que mostra que o ExpressRoute está em um 'estado não configurado'. Clique em **Instalação de Recurso** para abrir a página de configuração do Monitor de Desempenho de Rede.

   ![configuração de recurso](./media/how-to-npm/npm2.png)
3. Na página de configuração, navegue até a guia 'Emparelhamentos do ExpressRoute', localizada no painel esquerdo. Depois, clique em **Descobrir Agora**.

   ![descobrir](./media/how-to-npm/13.png)
4. Quando a descoberta estiver concluída, você verá uma lista que contém os seguintes itens:
   * Todas as conexões de emparelhamento da Microsoft no(s) circuito(s) da Rota Expressa associados a essa assinatura.
   * Todas as conexões de emparelhamento privado que se conectam às VNets associadas a essa assinatura.
            
## <a name="configmonitor"></a>Etapa 5: Configurar monitores

Nesta seção, você configura os monitores. Siga as etapas para o tipo de emparelhamento que você deseja monitorar: **emparelhamento privado**, ou **emparelhamento da Microsoft**.

### <a name="private-peering"></a>Emparelhamento privado

Para emparelhamento privado, quando a descoberta estiver concluída, você verá as regras para o **Nome do Circuito** exclusivo e o **Nome do VNet**. Inicialmente, essas regras estão desabilitadas.

![regras](./media/how-to-npm/14.png)

1. Marque a caixa de seleção **Monitorar este emparelhamento**.
2. Marque a caixa de seleção **Habilitar Health Monitoring para este emparelhamento**.
3. Escolha as condições de monitoramento. Você pode definir limites personalizados gerar eventos de integridade digitando os valores de limite. Sempre que o valor da condição ultrapassar o limite selecionado para o par de rede/sub-rede selecionado, será gerado um evento de integridade.
4. Clique no botão AGENTES LOCAIS **Adicionar Agentes** para adicionar os servidores locais dos quais você deseja monitorar a conexão de emparelhamento privado. Certifique-se de que você escolha somente os agentes que tenham conectividade com o ponto de extremidade de serviço Microsoft que você especificou na seção para a etapa 2. Os agentes locais devem poder alcançar o terminal usando a conexão ExpressRoute.
5. Salve as configurações.
6. Depois de habilitar as regras e selecionar os valores e os agentes que deseja monitorar, há uma espera de aproximadamente 30 a 60 minutos para os valores começarem a preencher e os blocos de **Monitoramento do ExpressRoute** se tornarem disponíveis.

### <a name="microsoft-peering"></a>Emparelhamento da Microsoft

Para emparelhamento da Microsoft, clique na(s) conexão(ões) de emparelhamento da Microsoft que você deseja monitorar e defina as configurações.

1. Marque a caixa de seleção **Monitorar este emparelhamento**. 
2. (Opcional) Você pode alterar o ponto de extremidade de serviço do Microsoft de destino. Por padrão, o NPM escolhe um ponto de extremidade de serviço do Microsoft como o destino. NPM monitora a conectividade dos seus servidores locais para esse ponto de extremidade de destino por meio do ExpressRoute. 
    * Para alterar esse ponto de extremidade de destino, clique no link **(Editar)** sob **destino:** e selecione outra extremidade de destino do serviço Microsoft na lista de URLs.
      ![Editar o destino](./media/how-to-npm/edit_target.png)<br>

    * Você pode usar um endereço IP ou URL personalizado. Essa opção é particularmente relevante se você estiver usando o Microsoft emparelhamento para estabelecer uma conexão com os serviços de PaaS do Azure, como o Armazenamento do Azure, bancos de dados SQL e sites oferecidos em endereços IP públicos. Para fazer isso, clique no link **(use URL personalizado ou endereço IP)** na parte inferior da lista de URLs e insira o ponto de extremidade público do serviço de PaaS do Azure conectado por meio do emparelhamento da ExpressRoute Microsoft.
    ![URL Personalizada](./media/how-to-npm/custom_url.png)<br>

    * Se você estiver usando essas configurações opcionais, certifique-se de que apenas o serviço ponto de extremidade Microsoft está selecionado aqui. O ponto de extremidade deve ser conectada ao ExpressRoute e pode ser acessado pelos agentes locais.
3. Marque a caixa de seleção **Habilitar Health Monitoring para este emparelhamento**.
4. Escolha as condições de monitoramento. Você pode definir limites personalizados gerar eventos de integridade digitando os valores de limite. Sempre que o valor da condição ultrapassar o limite selecionado para o par de rede/sub-rede selecionado, será gerado um evento de integridade.
5. Clique no botão AGENTES LOCAIS **Adicionar Agentes** para adicionar os servidores locais dos quais você deseja monitorar a conexão de emparelhamento da Microsoft. Certifique-se de que você escolha somente os agentes que tenham conectividade com os pontos de extremidade de serviço Microsoft que você especificou na seção para a etapa 2. Os agentes locais devem poder alcançar o terminal usando a conexão ExpressRoute.
6. Salve as configurações.
7. Depois de habilitar as regras e selecionar os valores e os agentes que deseja monitorar, há uma espera de aproximadamente 30 a 60 minutos para os valores começarem a preencher e os blocos de **Monitoramento do ExpressRoute** se tornarem disponíveis.

## <a name="explore"></a>Etapa 6: Exibir blocos de monitoramento

Depois de ver os blocos de monitoramentos, seus circuitos do ExpressRoute e os recursos de conexão estarão sendo monitorados pelo NPM. Você pode clicar no bloco do emparelhamento da Microsoft para fazer uma busca detalhada sobre a integridade das conexões do emparelhamento da Microsoft.

![blocos de monitoramento](./media/how-to-npm/15.png)

### <a name="dashboard"></a>Página Monitor de Desempenho de Rede

A página NPM contém uma página para ExpressRoute que mostra uma visão geral da integridade de circuitos e emparelhamentos do ExpressRoute.

![painel](./media/how-to-npm/dashboard.png)

### <a name="circuits"></a>Lista de circuitos

Para exibir uma lista de todos os circuitos do ExpressRoute monitorados, clique no bloco de **circuitos do ExpressRoute**. É possível selecionar um circuito e exibir seu estado de integridade, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência. Os gráficos são interativos. É possível selecionar uma janela de tempo personalizada para plotar os gráficos. Você pode arrastar o mouse sobre uma área do gráfico para ampliar e ver os pontos de dados refinados.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend"></a>Tendência de Perda, Latência e Taxa de Transferência

A largura de banda, latência e perda de gráficos são interativas. Você pode aplicar o zoom em qualquer seção destes gráficos usando os controles do mouse. Também é possível ver as largura de banda, latência e perda de dados para outros intervalos ao clicar em **Data/Hora**, localizado abaixo do botão Ações no canto superior esquerdo.

![tendência](./media/how-to-npm/16.png)

### <a name="peerings"></a>Lista de emparelhamentos

Para exibir uma lista de todas as conexões de redes virtuais sobre o emparelhamento privado, clique no bloco **Emparelhamentos Privados** no painel. Aqui, é possível selecionar uma conexão de rede virtual e exibir seu estado de integridade, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência.

![lista de circuitos](./media/how-to-npm/peerings.png)

### <a name="nodes"></a>Modo de exibição de nós

Para exibir uma lista de todos os links entre os nós locais e pontos de extremidade de serviço de máquinas virtuais do Azure e Microsoft para a conexão de emparelhamento de ExpressRoute escolhido, clique em **exibir links do nó**. Você pode exibir o status de integridade de cada link, bem como a tendência de perda e latência associados a eles.

![modo de exibição de nós](./media/how-to-npm/nodes.png)

### <a name="topology"></a>Topologia de circuito

Para exibir a topologia de circuito, clique no bloco **Topologia**. Isso leva você para a exibição de topologia do circuito ou emparelhamento selecionado. O diagrama de topologia fornece a latência de cada segmento na rede. Cada salto de camada 3 é representado por um nó do diagrama. Clicar em um salto revela mais detalhes sobre ele.

É possível aumentar o nível de visibilidade para incluir saltos locais ao mover a barra do controle deslizante para baixo dos **Filtros**. Mover a barra do controle deslizante para a esquerda ou direita amplia/reduz o número de saltos no grafo de topologia. A latência em cada segmento é visível, o que permite o isolamento mais rápido de segmentos de alta latência na sua rede.

![filtros](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Exibição de topologia detalhada de um circuito

Essa exibição mostra as conexões de VNet.
![topologia detalhada](./media/how-to-npm/17.png)
