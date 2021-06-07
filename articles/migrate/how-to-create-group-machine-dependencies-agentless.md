---
title: Configurar a análise de dependência sem agente em migrações para Azure
description: Configure a análise de dependência sem agente nas migrações para Azure.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 7966750d7c3e0f12bb9404a4d78bbc27e4075c52
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786576"
---
# <a name="analyze-server-dependencies-agentless"></a>Analisar dependências do servidor (sem agente)

Este artigo descreve como configurar a análise de dependência sem agente usando as migrações para Azure: ferramenta de descoberta e avaliação. A [análise de dependência](concepts-dependency-visualization.md) ajuda a identificar e compreender dependências entre servidores para avaliação e migração para o Azure.

> [!IMPORTANT]
> A análise de dependência sem agente está atualmente em visualização para servidores em execução em seu ambiente VMware, descoberto com a ferramenta migrações para Azure: descoberta e avaliação.
> Essa visualização é coberta pelo atendimento ao cliente e pode ser usada para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Limitações atuais

- No modo de exibição de análise de dependência, no momento, você não pode adicionar ou remover um servidor de um grupo.
- Um mapa de dependências para um grupo de servidores não está disponível no momento.
- Em um projeto de migrações para Azure, a coleta de dados de dependência pode ser habilitada simultaneamente para servidores 1000. Você pode analisar um número maior de servidores por meio do sequenciamento em lotes de 1000 servidores.

## <a name="before-you-start"></a>Antes de começar

- Verifique se você [criou um projeto](./create-manage-projects.md) com a ferramenta migrações para Azure: descoberta e avaliação adicionada a ele.
- Examine [os requisitos do VMware](migrate-support-matrix-vmware.md#vmware-requirements) para executar a análise de dependência.
- Examine [os requisitos do dispositivo](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) antes de configurar o dispositivo.
- [Revise os requisitos de análise de dependência](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) antes de habilitar a análise de dependência em servidores.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Implantar e configurar o dispositivo de migrações para Azure

1. [Examine](migrate-appliance.md#appliance---vmware) os requisitos para implantar o dispositivo de migrações para Azure.
2. Examine as URLs do Azure que o dispositivo precisará acessar nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls).
3. [Examine os dados](migrate-appliance.md#collected-data---vmware) que o dispositivo coleta durante a descoberta e a avaliação.
4. [Observe](migrate-support-matrix-vmware.md#port-access-requirements) os requisitos de acesso da porta para o dispositivo.
5. [Implante o dispositivo de migrações para Azure](how-to-set-up-appliance-vmware.md) para iniciar a descoberta. Para implantar o dispositivo, você baixa e importa um modelo OVA no VMware para criar um servidor em execução no seu vCenter Server. Depois de implantar o dispositivo, você precisa registrá-lo no projeto e configurá-lo para iniciar a descoberta.
6. Ao configurar o dispositivo, você precisa especificar o seguinte no Gerenciador de configuração de dispositivo:
    - Os detalhes do vCenter Server ao qual você deseja se conectar.
    - vCenter Server credenciais com escopo para descobrir os servidores em seu ambiente VMware.
    - Credenciais do servidor que podem ser credenciais de domínio/Windows (não domínio)/Linux (não domínio). [Saiba mais](add-server-credentials.md) sobre como fornecer credenciais e como as manipulamos.​

## <a name="verify-permissions"></a>Verificar permissões

- Você precisa [criar um vCenter Server conta somente leitura](./tutorial-discover-vmware.md#prepare-vmware) para descoberta e avaliação. A conta somente leitura precisa de privilégios habilitados para operações de convidado de **máquinas virtuais**  >  , a fim de interagir com os servidores para coletar dados de dependência.
- Você precisa de uma conta de usuário para que as migrações para Azure possam acessar o servidor para coletar dados de dependência. [Saiba mais](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) sobre os requisitos de conta para servidores Windows e Linux.

### <a name="add-credentials-and-initiate-discovery"></a>Adicionar credenciais e iniciar a descoberta

1. Abra o Gerenciador de configuração do dispositivo, conclua as verificações de pré-requisitos e o registro do dispositivo.
2. Navegue até o painel **gerenciar credenciais e fontes de descoberta** .
1.  Na **etapa 1: fornecer credenciais de vCenter Server**, clique em **Adicionar credenciais** para fornecer credenciais para a conta de vCenter Server que o dispositivo usará para descobrir servidores em execução no vCenter Server.
1. Na **etapa 2: fornecer vCenter Server detalhes**, clique em **Adicionar origem de descoberta** para selecionar o nome amigável para credenciais na lista suspensa, especifique o **endereço IP/FQDN** do painel de instância vCenter Server :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="3 no Gerenciador de configuração de dispositivo para obter detalhes de vCenter Server":::
1. Na **etapa 3: fornecer credenciais de servidor para executar o inventário de software, a análise de dependência sem agente e a descoberta de instâncias de SQL Server e bancos de dados**, clique em **Adicionar credenciais** para fornecer várias credenciais de servidor para iniciar o inventário de software.
1. Clique em **Iniciar descoberta** para iniciar a descoberta do vCenter Server.

 Após a conclusão da descoberta de vCenter Server, o dispositivo inicia a descoberta de aplicativos, funções e recursos instalados (inventário de software). Durante o inventário de software, as credenciais dos servidores adicionados serão iteradas em relação aos servidores e validadas para análise de dependência sem agente. Você pode habilitar a análise de dependência sem agente para servidores do Portal. Somente os servidores em que a validação é realizada com sucesso podem ser selecionados para habilitar a análise de dependências sem agente.

## <a name="start-dependency-discovery"></a>Iniciar descoberta de dependência

Selecione os servidores nos quais você deseja habilitar a descoberta de dependência.

1. Em **migrações para Azure: descoberta e avaliação**, clique em **servidores descobertos**.
2. Escolha o **nome do dispositivo** cuja descoberta você deseja examinar.
1. Você pode ver o status de validação dos servidores em coluna de **dependências (sem agente)** .
1. Clique na lista suspensa **análise de dependência** .
1. Clique em **adicionar servidores**.
1. Na página **adicionar servidores** , selecione os servidores nos quais você deseja habilitar a análise de dependência. Você pode habilitar o mapeamento de dependência somente nos servidores em que a validação foi bem-sucedida. O próximo ciclo de validação será executado 24 horas após o último carimbo de data/hora de validação.
1. Depois de selecionar os servidores, clique em **adicionar servidores**.

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="Iniciar análise de dependência":::

Você pode visualizar dependências em cerca de seis horas depois de habilitar a análise de dependência em servidores. Se você quiser habilitar simultaneamente vários servidores para análise de dependência, poderá usar o [PowerShell](#start-or-stop-dependency-analysis-using-powershell) para fazer isso.

## <a name="visualize-dependencies"></a>Visualizar dependências

1. Em **migrações para Azure: descoberta e avaliação**, clique em **servidores descobertos**.
1. Escolha o **nome do dispositivo** cuja descoberta você deseja examinar.
1. Pesquise o servidor cujas dependências você deseja examinar.
1. Na coluna **dependências (sem agente)** , clique em **Exibir dependências**
1. Altere o período de tempo para o qual você deseja exibir o mapa usando a lista suspensa **duração de tempo** .
1. Expanda o grupo de **clientes** para listar os servidores com uma dependência no servidor selecionado.
1. Expanda o grupo de **portas** para listar os servidores que têm uma dependência do servidor selecionado.
1. Para navegar até a exibição de mapa de qualquer um dos servidores dependentes, clique no nome do servidor > **carregar mapa do servidor** 
 :::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="expandir grupo de portas do servidor e carregar mapa do servidor":::
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="Expandir grupo de clientes":::

8. Expanda o servidor selecionado para exibir detalhes de nível de processo para cada dependência.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="Expanda servidor para mostrar processos":::

> [!NOTE]
> As informações do processo para uma dependência não estão sempre disponíveis. Se não estiver disponível, a dependência será representada com o processo marcado como "processo desconhecido".

## <a name="export-dependency-data"></a>Exportar dados de dependência

1. Em **migrações para Azure: descoberta e avaliação**, clique em **servidores descobertos**.
2. Clique na lista suspensa **análise de dependência** .
3. Clique em **Exportar dependências do aplicativo**.
4. Na página **Exportar dependências do aplicativo** , escolha o nome do dispositivo que está descobrindo os servidores desejados.
5. Selecione a hora de início e a hora de término. Observe que você pode baixar os dados somente nos últimos 30 dias.
6. Clique em **Exportar dependência**.

Os dados de dependência são exportados e baixados em um formato CSV. O arquivo baixado contém os dados de dependência em todos os servidores habilitados para análise de dependência. 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="Exportar dependências":::

### <a name="dependency-information"></a>Informações de dependência

Cada linha no CSV exportado corresponde a uma dependência observada no intervalo de tempo especificado.

A tabela a seguir resume os campos no CSV exportado. Observe que os campos nome do servidor, aplicativo e processo são preenchidos somente para servidores que têm a análise de dependência sem agente habilitada.

**Nome do campo** | **Detalhes**
--- | --- 
Timeslot | O slot de intervalo durante o qual a dependência foi observada. <br/> Os dados de dependência são capturados em intervalos de 6 horas no momento.
Nome do servidor de origem | Nome do servidor de origem 
Aplicativo de origem | Nome do aplicativo no servidor de origem  
Processo de origem | Nome do processo no servidor de origem  
Nome do servidor de destino | Nome do servidor de destino
IP de destino | Endereço IP do servidor de destino
Aplicativo de destino | Nome do aplicativo no servidor de destino
Processo de destino | Nome do processo no servidor de destino  
Porta de destino | Número da porta no servidor de destino

## <a name="stop-dependency-discovery"></a>Parar descoberta de dependência

Selecione os servidores nos quais você deseja interromper a descoberta de dependência.

1. Em **migrações para Azure: descoberta e avaliação**, clique em **servidores descobertos**.
1. Escolha o **nome do dispositivo** cuja descoberta você deseja examinar.
1. Clique na lista suspensa **análise de dependência** .
1. Clique em **remover servidores**.
1. Na página **remover servidores** , selecione o servidor que você deseja parar para a análise de dependência.
1. Depois de selecionar os servidores, clique em **remover servidores**.

Se você quiser parar a dependência simultaneamente em vários servidores, poderá usar o [PowerShell](#start-or-stop-dependency-analysis-using-powershell) para fazer isso.

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>Iniciar ou parar a análise de dependência usando o PowerShell

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

2. Selecione a assinatura na qual você criou o projeto 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importar o módulo AzMig_Dependencies do PowerShell baixado

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Habilitar ou desabilitar a coleta de dados de dependência

1. Obtenha a lista de servidores descobertos em seu projeto usando os comandos a seguir. No exemplo a seguir, o nome do projeto é FabrikamDemoProject e o grupo de recursos ao qual ele pertence é FabrikamDemoRG. A lista de servidores será salva no FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    No arquivo, você pode ver o nome de exibição do servidor, o status atual da coleção de dependências e a ID do ARM de todos os servidores descobertos. 

2. Para habilitar ou desabilitar dependências, crie um arquivo CSV de entrada. O arquivo deve ter uma coluna com o cabeçalho "ARM ID". Todos os cabeçalhos adicionais no arquivo CSV serão ignorados. Você pode criar o CSV usando o arquivo gerado na etapa anterior. Crie uma cópia do arquivo mantendo os servidores nos quais você deseja habilitar ou desabilitar dependências. 

    No exemplo a seguir, a análise de dependência está sendo habilitada na lista de servidores no arquivo de entrada FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    No exemplo a seguir, a análise de dependência está sendo desabilitada na lista de servidores no arquivo de entrada FabrikamDemo_VMs_Disable.csv.

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

    - Selecione a assinatura na qual você criou o projeto

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. Importar o módulo AzMig_Dependencies do PowerShell baixado

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Execute o comando a seguir. Esse comando baixa os dados de dependências em um CSV e os processa para gerar uma lista de dependências exclusivas que podem ser usadas para visualização no Power BI. No exemplo abaixo, o nome do projeto é FabrikamDemoProject e o grupo de recursos ao qual ele pertence é FabrikamDemoRG. As dependências serão baixadas para servidores descobertos pelo FabrikamAppliance. As dependências exclusivas serão salvas em FabrikamDemo_Dependencies.csv

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

[Servidores de grupo](how-to-create-a-group.md) para avaliação.