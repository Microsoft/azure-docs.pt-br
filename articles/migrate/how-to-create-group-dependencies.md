---
title: Refinar um grupo de avaliação com o mapeamento do grupo de dependência nas Migrações para Azure | Microsoft Docs
description: Descreve como refinar uma avaliação usando o mapeamento de dependência de grupo no serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 3ee528cc68a2a5637e85dc1d5ef68203916138e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596877"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Refinar um grupo usando o mapeamento de dependências de grupo

Este artigo descreve como refinar a um grupo visualizando as dependências de todas as máquinas no grupo. Normalmente, você usa esse método quando quer refinar as associações de um grupo existente, verificando as dependências do grupo, antes de executar uma avaliação. Refinar um grupo usando a visualização de dependências pode ajudá-lo a planejar de forma eficaz sua migração para o Azure. Você pode descobrir todos os sistemas interdependentes que precisam migrar juntos. Isso ajuda a garantir que nada seja deixado para trás e que interrupções surpresa não ocorram quando você estiver migrando para o Azure.


> [!NOTE]
> Os grupos para os quais você deseja visualizar dependências de grupo não devem conter mais de 10 máquinas. Caso você tenha mais de 10 máquinas no grupo, recomendamos que o divida em grupos menores para aproveitar a funcionalidade de visualização de dependências.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prepare-for-dependency-visualization"></a>Preparar para visualização de dependências
As migrações para Azure aproveita a solução Mapa do serviço nos logs do Azure Monitor para habilitar a visualização de dependência das máquinas.

> [!NOTE]
> A funcionalidade de visualização de dependências não está disponível no Azure Governamental.

### <a name="associate-a-log-analytics-workspace"></a>Associar um espaço de trabalho do Log Analytics
Para aproveitar a visualização de dependência, você precisa associar um espaço de trabalho do Log Analytics novo ou existente a um projeto das Migrações para Azure. Você só pode criar ou anexar um workspace na mesma assinatura em que o projeto de migração é criado.

- Para anexar um espaço de trabalho do Log Analytics a um projeto, em **Visão geral**, acesse a seção **Essenciais** do projeto e clique em **Requer configuração**

    ![Associar o espaço de trabalho do Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Durante a associação de um workspace, haverá a opção de criar um workspace ou anexar um existente:
    - Ao criar um workspace, você precisará especificar um nome para ele. O workspace será criado em uma região na mesma [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que o projeto de migração.
    - Ao anexar um workspace, você pode escolher entre todos os workspaces disponíveis na mesma assinatura que o projeto de migração. Observe que são listados somente os workspaces que foram criados em uma região [com suporte para o Mapa do Serviço](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). É necessário ser capaz de anexar a um workspace, verifique se você tem acesso de 'Leitura' no workspace.

> [!NOTE]
> Não é possível alterar o workspace associado a um projeto de migração.

### <a name="download-and-install-the-vm-agents"></a>Fazer o download e instalar os agente de VM
Para ver as dependências de um grupo, você precisa baixar e instalar agentes em cada computador local que fizer parte do grupo. Além disso, se você tiver máquinas sem conectividade com a Internet, será necessário fazer o download e instalar o [gateway do Log Analytics](../azure-monitor/platform/gateway.md) nelas.

1. Em **Visão Geral**, clique em **Gerenciar** > **Grupos** e vá para o grupo necessário.
2. Na lista de computadores, na coluna **Agente de dependência**, clique em **Requer instalação** para ver instruções sobre como baixar e instalar os agentes.
3. Na página **Dependências**, faça baixe e instale o MMA (Microsoft Monitoring Agent) e o Agente de dependência em cada VM que faz parte do grupo.
4. Copie a ID e a chave do workspace. Você precisará delas quando instalar o MMA nas máquinas locais.

### <a name="install-the-mma"></a>Instalar o MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Instalar o agente em um computador com Windows

Para instalar o agente em uma máquina com Windows:

1. Clique duas vezes no agente baixado.
2. Na página de **Boas-vindas**, clique em **Avançar**. Na página **Termos de Licença**, clique em **Concordo** para aceitar a licença.
3. Em **Pasta de Destino**, mantenha ou modifique a pasta de instalação padrão > **Avançar**.
4. Em **Opções de Configuração do Agente**, selecione **Azure Log Analytics** > **Avançar**.
5. Clique em **Adicionar** para adicionar um espaço de trabalho do Log Analytics. Cole a ID do workspace e a chave que você copiou do portal. Clique em **Avançar**.

Você pode instalar o agente na linha de comando ou usando um método automatizado como o System Center Configuration Manager. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sobre como usar esses métodos para instalar o agente do MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>Instalar o agente em um computador com Linux

Para instalar o agente em uma máquina com Linux:

1. Transfira o pacote apropriado (x86 ou x64) para seu computador Linux usando scp/sftp.
2. Instale o pacote usando o argumento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

#### <a name="install-the-agent-on-a-machine-monitored-by-system-center-operations-manager"></a>Instalar o agente em um computador monitorado pelo System Center Operations Manager

Para computadores monitorados pelo Operations Manager 2012 R2 ou posterior, não é necessário para instalar o agente do MMA. O Mapa do Serviço tem uma integração com o Operations Manager que aproveita o MMA do Operations Manager para coletar os dados de dependência necessários. Você pode habilitar a integração usando [estas](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) diretrizes. No entanto, observe que o Dependency Agent precisa estar instalado nesses computadores.

### <a name="install-the-dependency-agent"></a>Instalar o Agente de dependência
1. Para instalar o Agente de dependência em uma máquina com Windows, clique duas vezes no arquivo de instalação e siga o assistente.
2. Para instalar o Agente de dependência em uma máquina com Linux, instale como raiz usando o comando a seguir:

    ```sh InstallDependencyAgent-Linux64.bin```

Saiba mais sobre o suporte do agente de Dependência para os sistemas operacionais [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) e [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems).

[Saiba mais](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) sobre como você pode usar scripts para instalar o Dependency agent.

## <a name="refine-the-group-based-on-dependency-visualization"></a>Refinar o grupo com base na visualização de dependências
Depois de instalar os agentes em todas as máquinas do grupo, você pode visualizar as dependências do grupo e refiná-lo seguindo as etapas abaixo.

1. No projeto das Migrações para Azure, em **Gerenciar**, clique em  **Grupos** e selecione o grupo.
2. Na página do grupo, clique em  **Exibir Dependências**, para abrir o mapa de dependências de grupo.
3. O mapa de dependências do grupo mostra os seguintes detalhes:
   - Conexões TCP de entrada (clientes) e saída (servidores) para/de todos os computadores que fazem parte do grupo
       - As máquinas dependentes que não têm o agente de dependência e o MMA instalado são agrupadas por números de porta
       - As máquinas dependentes que têm o agente de dependência e o MMA instalados aparecem como caixas separadas
   - Quanto aos processos em execução na máquina, você pode expandir cada caixa para exibir os processos
   - Quanto às propriedades como o nome de domínio totalmente qualificado, o sistema operacional, o endereço MAC de cada máquina, você pode clicar na caixa de cada máquina para exibir esses detalhes

     ![Exibir dependências de grupo](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Para exibir dependências mais granulares, clique no intervalo de tempo para modificá-las. Por padrão, o intervalo é de uma hora. Você pode modificar o intervalo, ou especificar as datas de início e de término e a duração.

   > [!NOTE]
   >    Atualmente, a interface do usuário de visualização de dependência não dá suporte à seleção de um intervalo de tempo maior do que uma hora. Registra em log para usar o Azure Monitor [consultar os dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-a-group) em um período mais longo.

4. Verifique as máquinas dependentes, o processo em execução dentro de cada máquina e identifique as máquinas que devem ser adicionadas ou removidas do grupo.
5. Use Ctrl+Clique para selecionar máquinas no mapa para adicionar ou remover do grupo.
    - Você só pode adicionar máquinas que foram descobertas.
    - Adicionar e remover máquinas de um grupo invalida as últimas avaliações dele.
    - Opcionalmente, você pode criar uma nova avaliação ao modificar o grupo.
5. Clique em **OK** para salvar o grupo.

    ![Adicionar ou remover máquinas](./media/how-to-create-group-dependencies/add-remove.png)

Se você quiser verificar as dependências de uma máquina específica que é exibida no mapa de dependência de grupo, [configure o mapeamento de dependência da máquina](how-to-create-group-machine-dependencies.md).

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Consultar dados de dependência de logs do Azure Monitor

Os dados capturados pelo mapa do serviço de dependência estão disponíveis para a consulta no espaço de trabalho do Log Analytics associado ao seu projeto de migrações para Azure. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) sobre as tabelas de dados de mapa do serviço de consulta no Azure Monitor registra em log. 

Para executar as consultas do Kusto:

1. Depois de instalar os agentes, acesse o portal e clique em **Visão geral**.
2. Em **visão geral**, acesse a seção **Essentials** do projeto e clique no nome do workspace fornecido, ao lado de **Workspace do OMS**.
3. Na página do espaço de trabalho do Log Analytics, clique em **Geral** > **Logs**.
4. Escreva sua consulta para coletar dados de dependência usando os logs do Azure Monitor. Encontre consultas de exemplo na próxima seção.
5. Execute a consulta clicando em Executar. 

[Saiba mais](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) sobre como escrever consultas do Kusto. 

## <a name="sample-azure-monitor-logs-queries"></a>Exemplo Azure Monitor registra as consultas

A seguir é consultas de exemplo, você pode usar para extrair dados de dependência. Você pode modificar as consultas para extrair seus pontos de dados preferencial. Uma lista completa dos campos nos registros de dados de dependência está disponível [aqui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records). Encontre mais exemplos de consultas [aqui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).

### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Resumir as conexões de entrada em um conjunto de computadores

Observe que os registros na tabela para métricas de conexão, VMConnection, não representam conexões de rede física individual. Várias conexões de rede física são agrupadas em uma conexão lógica. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) sobre conexão de rede física como os dados são agregados em um único registro lógico na VMConnection. 

```
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

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Resumir o volume de dados enviados e recebidos nas conexões de entrada entre um conjunto de máquinas

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
- [Saiba mais](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) sobre as perguntas frequentes na visualização de dependência.
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
