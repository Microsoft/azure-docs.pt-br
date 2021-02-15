---
title: Configurar a análise de dependência sem agente na avaliação do servidor de migrações para Azure
description: Configure a análise de dependência sem agente na avaliação do servidor de migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: d84c85326c6f5d87189a2c24a3b13654f157cb05
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754276"
---
# <a name="analyze-machine-dependencies-agentless"></a>Analisar dependências do computador (sem agente)

Este artigo descreve como configurar a análise de dependência sem agente em migrações para Azure: avaliação de servidor. A [análise de dependência](concepts-dependency-visualization.md) ajuda você a identificar e entender dependências entre computadores para avaliação e migração para o Azure.


> [!IMPORTANT]
> A visualização de dependência sem agente está atualmente em versão prévia para VMs VMware descobertas com a ferramenta migrações para Azure: Server Assessment.
> Os recursos podem ser limitados ou incompletos.
> Essa visualização é coberta pelo atendimento ao cliente e pode ser usada para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Limitações atuais

- No modo de exibição de análise de dependência, não é possível adicionar ou remover um servidor de um grupo no momento.
- Um mapa de dependências para um grupo de servidores não está disponível no momento.
- Em um projeto de migrações para Azure, a coleta de dados de dependência pode ser configurada simultaneamente para servidores 1000. Você pode analisar um número maior de servidores por meio do sequenciamento em lotes de 1000.

## <a name="before-you-start"></a>Antes de começar

- [Examine](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) os sistemas operacionais com suporte e as permissões necessárias.
- Verifique se você:
    - Ter um projeto de migrações para Azure. Se você não fizer isso, [crie](./create-manage-projects.md) um agora.
    - Verifique se você [adicionou](how-to-assess.md) a ferramenta migrações do Azure: Server Assessment ao projeto.
    - Configure um [dispositivo de migrações para Azure](migrate-appliance.md) para descobrir computadores locais. [Configure um dispositivo](how-to-set-up-appliance-vmware.md) para VMs VMware. O dispositivo descobre computadores locais e envia metadados e dados de desempenho para migrações para Azure: avaliação do servidor.
- Verifique se as ferramentas do VMware (posterior à 10,2) estão instaladas em cada VM que você deseja analisar.


## <a name="create-a-user-account-for-discovery"></a>Criar uma conta de usuário para descoberta

Configure uma conta de usuário para que a avaliação do servidor possa acessar a VM para descobrir dependências. [Saiba mais](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) sobre os requisitos de conta para VMs Windows e Linux.


## <a name="add-the-user-account-to-the-appliance"></a>Adicionar a conta de usuário ao dispositivo

Adicione a conta de usuário ao dispositivo.

1. Abra o aplicativo de gerenciamento de dispositivo. 
2. Navegue até o painel **fornecer detalhes do vCenter** .
3. Em **descobrir aplicativo e dependências em VMs**, clique em **Adicionar credenciais**
3. Escolha o **sistema operacional**, forneça um nome amigável para a conta e a senha do **nome de usuário** / **Password**
6. Clique em **Save** (Salvar).
7. Clique em **salvar e iniciar descoberta**.

    ![Adicionar conta de usuário da VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Iniciar descoberta de dependência

Escolha os computadores nos quais você deseja habilitar a descoberta de dependência. 

1. Em **migrações para Azure: avaliação de servidor**, clique em **servidores descobertos**.
2. Clique no ícone **análise de dependência** .
3. Clique em **adicionar servidores**.
4. Na página **adicionar servidores** , escolha o dispositivo que está descobrindo os computadores relevantes.
5. Na lista computador, selecione os computadores.
6. Clique em **adicionar servidores**.

    ![Iniciar descoberta de dependência](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Você pode visualizar dependências em cerca de seis horas após iniciar a descoberta de dependência. Se você quiser habilitar vários computadores, poderá usar o [PowerShell](#start-or-stop-dependency-discovery-using-powershell) para fazer isso.

## <a name="visualize-dependencies"></a>Visualizar dependências

1. Em **migrações para Azure: avaliação de servidor**, clique em **servidores descobertos**.
2. Pesquise o computador que você deseja exibir.
3. Na coluna **dependências** , clique em **Exibir dependências**
4. Altere o período de tempo para o qual você deseja exibir o mapa usando a lista suspensa **duração de tempo** .
5. Expanda o grupo de **clientes** para listar os computadores com uma dependência na máquina selecionada.
6. Expanda o grupo de **portas** para listar os computadores que têm uma dependência do computador selecionado.
7. Para navegar até o modo de exibição de mapa de qualquer um dos computadores dependentes, clique no nome do computador > **carregar o mapa do servidor**

    ![Expandir o grupo de portas do servidor e carregar o mapa do servidor](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Expandir grupo de clientes ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Expanda a máquina selecionada para exibir detalhes de nível de processo para cada dependência.

    ![Expanda servidor para mostrar processos](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> As informações do processo para uma dependência não estão sempre disponíveis. Se não estiver disponível, a dependência será representada com o processo marcado como "processo desconhecido".

## <a name="export-dependency-data"></a>Exportar dados de dependência

1. Em **migrações para Azure: avaliação de servidor**, clique em **servidores descobertos**.
2. Clique no ícone **análise de dependência** .
3. Clique em **Exportar dependências do aplicativo**.
4. Na página **Exportar dependências de aplicativo** , escolha o dispositivo que está descobrindo os computadores relevantes.
5. Selecione a hora de início e a hora de término. Observe que você pode baixar os dados somente nos últimos 30 dias.
6. Clique em **Exportar dependência**.

Os dados de dependência são exportados e baixados em um formato CSV. O arquivo baixado contém os dados de dependência em todos os computadores habilitados para análise de dependência. 

![Exportar dependências](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>Informações de dependência

Cada linha no CSV exportado corresponde a uma dependência observada no intervalo de tempo especificado. 

A tabela a seguir resume os campos no CSV exportado. Observe que os campos nome do servidor, aplicativo e processo são preenchidos somente para servidores que têm a análise de dependência sem agente habilitada.

**Nome do campo** | **Detalhes**
--- | --- 
Timeslot | O slot de intervalo durante o qual a dependência foi observada. <br/> Os dados de dependência são capturados em intervalos de 6 horas no momento.
Nome do servidor de origem | Nome do computador de origem 
Aplicativo de origem | Nome do aplicativo no computador de origem 
Processo de origem | Nome do processo no computador de origem 
Nome do servidor de destino | Nome do computador de destino
IP de destino | Endereço IP do computador de destino
Aplicativo de destino | Nome do aplicativo no computador de destino
Processo de destino | Nome do processo no computador de destino 
Porta de destino | Número da porta no computador de destino


## <a name="stop-dependency-discovery"></a>Parar descoberta de dependência

Escolha os computadores nos quais você deseja parar a descoberta de dependência. 

1. Em **migrações para Azure: avaliação de servidor**, clique em **servidores descobertos**.
2. Clique no ícone **análise de dependência** .
3. Clique em **remover servidores**.
3. Na página **remover servidores** , escolha o **dispositivo** que está descobrindo as VMs nas quais você procura parar a descoberta de dependência.
4. Na lista computador, selecione os computadores.
5. Clique em **remover servidores**.

Se você quiser parar a dependência em vários computadores, poderá usar o [PowerShell](#start-or-stop-dependency-discovery-using-powershell) para fazer isso.


## <a name="start-or-stop-dependency-discovery-using-powershell"></a>Iniciar ou parar a descoberta de dependências usando o PowerShell

Baixe o módulo do PowerShell do repositório de [exemplos Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) no github.


### <a name="log-in-to-azure"></a>Fazer logon no Azure

1. Faça logon em sua assinatura do Azure usando o cmdlet Connect-AzAccount.

    ```PowerShell
    Connect-AzAccount
    ```
    Se estiver usando o Azure governamental, use o comando a seguir.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Selecione a assinatura na qual você criou o projeto de migrações para Azure 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importar o módulo AzMig_Dependencies do PowerShell baixado

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Habilitar ou desabilitar a coleta de dados de dependência

1. Obtenha a lista de VMs do VMware descobertas em seu projeto de migrações para Azure usando os comandos a seguir. No exemplo a seguir, o nome do projeto é FabrikamDemoProject e o grupo de recursos ao qual ele pertence é FabrikamDemoRG. A lista de computadores será salva no FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    No arquivo, você pode ver o nome de exibição da VM, o status atual da coleção de dependências e a ID do ARM de todas as VMs descobertas. 

2. Para habilitar ou desabilitar dependências, crie um arquivo CSV de entrada. O arquivo deve ter uma coluna com o cabeçalho "ARM ID". Todos os cabeçalhos adicionais no arquivo CSV serão ignorados. Você pode criar o CSV usando o arquivo gerado na etapa anterior. Crie uma cópia do arquivo mantendo as VMs nas quais você deseja habilitar ou desabilitar dependências. 

    No exemplo a seguir, a análise de dependência está sendo habilitada na lista de VMs no arquivo de entrada FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    No exemplo a seguir, a análise de dependência está sendo desabilitada na lista de VMs no arquivo de entrada FabrikamDemo_VMs_Disable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Visualizar conexões de rede no Power BI

As migrações para Azure oferecem um modelo de Power BI que você pode usar para visualizar conexões de rede de vários servidores ao mesmo tempo e filtrar por processo e servidor. Para visualizar, carregue o Power BI com dados de dependência de acordo com as instruções abaixo.

1. Baixe o módulo do PowerShell e o modelo de Power BI do repositório de [exemplos Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) no github.

2. Faça logon no Azure usando as instruções abaixo: 
- Faça logon em sua assinatura do Azure usando o cmdlet Connect-AzAccount.

    ```PowerShell
    Connect-AzAccount
    ```

- Se estiver usando o Azure governamental, use o comando a seguir.

    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

- Selecione a assinatura na qual você criou o projeto de migrações para Azure 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importar o módulo AzMig_Dependencies do PowerShell baixado

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Execute o comando a seguir. Esse comando baixa os dados de dependências em um CSV e os processa para gerar uma lista de dependências exclusivas que podem ser usadas para visualização no Power BI. No exemplo abaixo, o nome do projeto é FabrikamDemoProject e o grupo de recursos ao qual ele pertence é FabrikamDemoRG. As dependências serão baixadas para computadores descobertos pelo FabrikamAppliance. As dependências exclusivas serão salvas em FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. Abrir o modelo de Power BI baixado

6. Carregue os dados de dependência baixados em Power BI.
    - Abra o modelo no Power BI.
    - Clique em **obter dados** na barra de ferramentas. 
    - Escolha **texto/CSV** de fontes de dados comuns.
    - Escolha o arquivo de dependências baixado.
    - Clique em **Carregar**.
    - Você verá que uma tabela é importada com o nome do arquivo CSV. Você pode ver a tabela na barra campos à direita. Renomeie-o para AzMig_Dependencies
    - Clique em atualizar na barra de ferramentas.

    O gráfico conexões de rede e o nome do servidor de origem, o nome do servidor de destino, o nome do processo de origem, as segmentações do nome do processo de destino devem acender com os dados importados.

7. Visualize o mapa da filtragem de conexões de rede por servidores e processos. Salve o arquivo.


## <a name="next-steps"></a>Próximas etapas

[Agrupar máquinas](how-to-create-a-group.md) para avaliação.