---
title: Configurar a análise de dependência baseada em agente na avaliação do servidor de migrações para Azure
description: Este artigo descreve como configurar a análise de dependência baseada em agente na avaliação do servidor de migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: d4bf635c57bcef41cd0f3285d8a91bae4b3e0415
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752015"
---
# <a name="set-up-dependency-visualization"></a>Configurar a visualização de dependência

Este artigo descreve como configurar a análise de dependência baseada em agente no migrações para Azure: avaliação do servidor. A [análise de dependência](concepts-dependency-visualization.md) ajuda a identificar e compreender as dependências entre as máquinas que você deseja avaliar e migrar para o Azure.

## <a name="before-you-start"></a>Antes de começar

- Examine os requisitos de suporte e implantação para a análise de dependência baseada em agente para:
    - [VMs VMware](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Servidores físicos](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [VMs do Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- Verifique se você:
    - Ter um projeto de migrações para Azure. Se você não fizer isso, [crie](./create-manage-projects.md) um agora.
    - Verifique se você [adicionou](how-to-assess.md) a ferramenta migrações do Azure: Server Assessment ao projeto.
    - Configure um [dispositivo de migrações para Azure](migrate-appliance.md) para descobrir computadores locais. O dispositivo descobre computadores locais e envia metadados e dados de desempenho para migrações para Azure: avaliação do servidor. Configurar um dispositivo para:
        - [VMware](how-to-set-up-appliance-vmware.md) VMs.
        - [Hyper-V](how-to-set-up-appliance-hyper-v.md) VMs.
        - [Servidores físicos](how-to-set-up-appliance-physical.md).
- Para usar a visualização de dependência, você associa um [espaço de trabalho log Analytics](../azure-monitor/platform/manage-access.md) a um projeto de migrações para Azure:
    - Você pode anexar um espaço de trabalho somente após a configuração do dispositivo de migrações para Azure e a descoberta de máquinas no projeto de migrações para Azure.
    - Verifique se você tem um espaço de trabalho na assinatura que contém o projeto de migrações para Azure.
    - O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste da Ásia ou oeste da Europa. Os espaços de trabalho em outras regiões não podem ser associados a um projeto.
    - O espaço de trabalho deve estar em uma região em que o [Mapa do Serviço é compatível](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).
    - Você pode associar um espaço de trabalho Log Analytics novo ou existente a um projeto de migrações para Azure.
    - Você anexa o espaço de trabalho na primeira vez em que configura a visualização de dependência para um computador. Não é possível modificar o espaço de trabalho de um projeto de Migrações para Azure depois que ele foi adicionado.
    - No Log Analytics, o espaço de trabalho associado às Migrações para Azure é marcado com a chave do Projeto de Migração e o nome do projeto.

## <a name="associate-a-workspace"></a>Associar um workspace

1. Depois de descobrir as máquinas para avaliação, em **servidores**  >  **migrações para Azure: avaliação do servidor**, clique em **visão geral**.  
2. Em **migrações para Azure: avaliação de servidor**, clique em **Essentials**.
3. No **espaço de trabalho do OMS**, clique em **requer configuração**.

     ![Configurar o espaço de trabalho do Log Analytics](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. Em **Configurar espaço de trabalho do OMS**, especifique se deseja criar um novo espaço de trabalho ou usar um existente.
    - Você pode selecionar um espaço de trabalho existente de todos os espaços de trabalho na assinatura migrar projeto.
    - Você precisa de acesso de leitor ao espaço de trabalho para associá-lo.
5. Se você criar um novo espaço de trabalho, selecione um local para ele.

    ![Adicionar um novo workspace](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>Fazer o download e instalar os agente de VM

Em cada computador que você deseja analisar, instale os agentes.

> [!NOTE]
> Para computadores monitorados pelo System Center Operations Manager 2012 R2 ou posterior, você não precisa instalar o agente do MMA. Mapa do Serviço integra-se com Operations Manager. [Siga](../azure-monitor/insights/service-map-scom.md#prerequisites) as diretrizes de integração.

1. Em **migrações para Azure: avaliação de servidor**, clique em **servidores descobertos**.
2. Para cada computador que você deseja analisar com a visualização de dependência, na coluna **dependências** , clique em **requer instalação do agente**.
3. Na página **dependências** , baixe o MMA e o agente de dependência para Windows ou Linux.
4. Em **Configurar agente do MMA**, copie a ID e a chave do espaço de trabalho. Você precisará delas quando instalar o agente do MMA.

    ![Instalar os agentes](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Instalar o MMA

Instale o MMA em cada computador Windows ou Linux que você deseja analisar.

### <a name="install-mma-on-a-windows-machine"></a>Instalar o MMA em um computador Windows

Para instalar o agente em uma máquina com Windows:

1. Clique duas vezes no agente baixado.
2. Na página de **Boas-vindas**, clique em **Avançar**. Na página **Termos de Licença**, clique em **Concordo** para aceitar a licença.
3. Em **Pasta de Destino**, mantenha ou modifique a pasta de instalação padrão > **Avançar**.
4. Em **Opções de Configuração do Agente**, selecione **Azure Log Analytics** > **Avançar**.
5. Clique em **Adicionar** para adicionar um espaço de trabalho do Log Analytics. Cole a ID do workspace e a chave que você copiou do portal. Clique em **Próximo**.

Você pode instalar o agente da linha de comando ou usando um método automatizado, como Configuration Manager ou [Intigua](https://www.intigua.com/intigua-for-azure-migration).
- [Saiba mais](../azure-monitor/platform/log-analytics-agent.md#installation-options) sobre como usar esses métodos para instalar o agente do MMA.
- O agente MMA também pode ser instalado usando esse [script](https://github.com/brianbar-MSFT/Install-MMA).
- [Saiba mais](../azure-monitor/platform/agents-overview.md#supported-operating-systems) sobre os sistemas operacionais Windows com suporte do MMA.

### <a name="install-mma-on-a-linux-machine"></a>Instalar o MMA em um computador Linux

Para instalar o MMA em um computador Linux:

1. Transfira o pacote apropriado (x86 ou x64) para seu computador Linux usando scp/sftp.
2. Instale o pacote usando o argumento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Saiba mais](../azure-monitor/platform/agents-overview.md#supported-operating-systems) sobre a lista de sistemas de operacionais Linux com suporte no MMA. 

## <a name="install-the-dependency-agent"></a>Instalar o Agente de dependência

1. Para instalar o Agente de dependência em uma máquina com Windows, clique duas vezes no arquivo de instalação e siga o assistente.
2. Para instalar o Agente de dependência em uma máquina com Linux, instale como raiz usando o comando a seguir:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Saiba mais](../azure-monitor/insights/vminsights-enable-hybrid.md#dependency-agent) sobre como você pode usar scripts para instalar o Dependency agent.
- [Saiba mais](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) sobre os sistemas operacionais com suporte do Dependency Agent.


## <a name="create-a-group-using-dependency-visualization"></a>Criar um grupo usando a visualização de dependência

Agora, crie um grupo para avaliação. 


> [!NOTE]
> Os grupos para os quais você deseja visualizar dependências de grupo não devem conter mais de 10 máquinas. Se você tiver mais de 10 máquinas, divida-as em grupos menores.

1. Em **migrações para Azure: avaliação de servidor**, clique em **servidores descobertos**.
2. Na coluna **dependências** , clique em **Exibir dependências** para cada computador que você deseja examinar.
3. No mapa de dependências, você pode ver o seguinte:
    - Conexões TCP de entrada (clientes) e de saída (servidores), de e para o computador.
    - Os computadores dependentes que não têm os agentes de dependência instalados são agrupados por números de porta.
    - Os computadores dependentes com agentes de dependência instalados são mostrados como caixas separadas.
    - Processos em execução dentro da máquina. Expanda cada caixa da máquina para exibir os processos.
    - Propriedades da máquina (incluindo FQDN, sistema operacional, endereço MAC). Clique em cada caixa da máquina para exibir os detalhes.

4. Você pode procurar por dependências em períodos diferentes clicando no período no rótulo de intervalo de tempo.
    - Por padrão, o intervalo é de uma hora. 
    - Você pode modificar o intervalo, ou especificar as datas de início e de término e a duração.
    - O intervalo de tempo pode ser de até uma hora. Se você precisar de um intervalo mais longo, use Azure Monitor para consultar dados dependentes por um período mais longo.

5. Depois de identificar os computadores dependentes que você deseja agrupar, use Ctrl + clique para selecionar vários computadores no mapa e clique em **Agrupar computadores**.
6. Especifique um nome de grupo.
7. Verifique se as máquinas dependentes foram descobertas pelas Migrações para Azure.

    - Se um computador dependente não for descoberto por migrações para Azure: avaliação de servidor, você não poderá adicioná-lo ao grupo.
    - Para adicionar um computador, execute a descoberta novamente e verifique se o computador foi descoberto.

8. Se quiser criar uma avaliação para esse grupo, marque a caixa de seleção para criar uma nova avaliação para o grupo.
8. Clique em **OK** para salvar o grupo.

Depois de criar o grupo, recomendamos que você instale agentes em todos os computadores do grupo e, em seguida, visualize as dependências de todo o grupo.

## <a name="query-dependency-data-in-azure-monitor"></a>Dados de dependência de consulta em Azure Monitor

Você pode consultar dados de dependência capturados por Mapa do Serviço no espaço de trabalho Log Analytics associado ao projeto de migrações para Azure. Log Analytics é usado para gravar e executar Azure Monitor consultas de log.

- [Saiba como](../azure-monitor/insights/service-map.md#log-analytics-records) procurar mapa do serviço dados no log Analytics.
- [Obtenha uma visão geral](../azure-monitor/log-query/get-started-queries.md)  da gravação de consultas de log no [log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md).

Execute uma consulta para dados de dependência da seguinte maneira:

1. Depois de instalar os agentes, acesse o portal e clique em **Visão geral**.
2. Em **migrações para Azure: avaliação de servidor**, clique em **visão geral**. Clique na seta para baixo para expandir o **Essentials**.
3. No **espaço de trabalho do OMS**, clique no nome do espaço de trabalho.
3. Na página Log Analytics espaço de trabalho > **geral**, clique em **logs**.
4. Escreva sua consulta e clique em **executar**.

### <a name="sample-queries"></a>Consultas de exemplo

Aqui estão algumas consultas de exemplo que você pode usar para extrair dados de dependência.

- Você pode modificar as consultas para extrair seus pontos de dados preferenciais.
- [Examine](../azure-monitor/insights/service-map.md#log-analytics-records) uma lista completa de registros de dados de dependência.
- [Examine](../azure-monitor/insights/service-map.md#sample-log-searches) as consultas de exemplo adicionais.

#### <a name="sample-review-inbound-connections"></a>Exemplo: revisar conexões de entrada

Examine as conexões de entrada para um conjunto de VMs.

- Os registros na tabela para métricas de conexão (VMConnection) não representam conexões de rede física individuais.
- Várias conexões de rede física são agrupadas em uma conexão lógica.
- [Saiba mais](../azure-monitor/insights/service-map.md#connections) sobre como os dados de conexão de rede física são agregados no VMConnection.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Exemplo: resumir os dados enviados e recebidos

Este exemplo resume o volume de dados enviados e recebidos em conexões de entrada entre um conjunto de computadores.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Próximas etapas

[Crie uma avaliação](how-to-create-assessment.md) para um grupo.