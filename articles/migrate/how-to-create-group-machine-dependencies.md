---
title: Agrupar máquinas usando dependências de máquina com as Migrações para Azure | Microsoft Docs
description: Descreve como criar uma avaliação usando dependências de máquina com o serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 7cde18f2da764a055443900e7daf160f72e2eeb5
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68301647"
---
# <a name="set-up-dependency-visualization-for-assessment"></a>Configurar a visualização de dependência para avaliação

Este artigo descreve como configurar o mapeamento de dependência nas migrações para Azure: Avaliação do servidor.

O mapeamento de dependência ajuda a Visualizar dependências entre computadores que você deseja avaliar e migrar.

- Nas migrações para Azure: Avaliação do servidor, você reúne computadores para avaliação. Geralmente, os computadores que você deseja migrar juntos.
- Normalmente, você usa o mapeamento de dependência quando deseja avaliar grupos com níveis de confiança mais altos.
- O mapeamento de dependência ajuda a verificar dependências entre computadores antes de executar uma avaliação e migração.
- Mapear e Visualizar dependências ajuda a planejar efetivamente sua migração para o Azure. Ele ajuda a garantir que nada seja deixado para trás, evitando interrupções surpresa durante a migração.
- Usando o mapeamento, você pode descobrir sistemas interdependentes que precisam ser migrados juntos. Você também pode identificar se um sistema em execução ainda está atendendo aos usuários ou é um candidato para encerramento em vez de migração.

[Saiba mais](concepts-dependency-visualization.md#how-does-it-work) sobre a visualização de dependência.

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de ter [criado](how-to-add-tool-first-time.md) um projeto de migrações para Azure.
- Se você já tiver criado um projeto, certifique-se de ter [adicionado](how-to-assess.md) a migração do Azure: Ferramenta de avaliação do servidor.
- Verifique se você descobriu seus computadores nas migrações para Azure; Você pode fazer isso Configurando um dispositivo de migrações para Azure para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O dispositivo descobre computadores locais e envia metadados e dados de desempenho para migrações para o Azure: Avaliação do servidor. [Saiba mais](migrate-appliance.md).


**Recursos** | **Observação**
--- | ---
Disponibilidade | A visualização de dependência não está disponível no Azure governamental.
Mapa do Serviço | A visualização de dependência usa Mapa do Serviço solução em logs de Azure Monitor. [Mapa do serviço](../azure-monitor/insights/service-map-configure.md) descobre e mostra automaticamente as conexões entre os servidores.
Agentes | Para usar a visualização de dependência, instale alguns agentes nos computadores que você deseja mapear:<br/> - O agente de [log Analytics do Azure](../azure-monitor/platform/log-analytics-agent.md) (anteriormente conhecido como Microsoft Monitoring Agent (MMA).<br/> -Mapa do Serviço agente de dependência.<br/><br/> Para automatizar a instalação do agente, você pode usar uma ferramenta de implantação, como System Center Configuration Manager ou uma ferramenta de parceiro, como [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), que tem uma solução de implantação de agente para migrações para Azure.
Agente de dependência | Examine o suporte do agente de dependência para [Windows](/azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) e [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems).<br/><br/> [Saiba mais](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) sobre como usar scripts para instalar o Dependency Agent.
Agente de Log Analytics (MMA) | [Saiba mais](../azure-monitor/platform/log-analytics-agent.md#install-and-configure-agent) sobre os métodos de instalação do MMA.<br/><br/> Para computadores monitorados pelo System Center Operations Manager 2012 R2 ou posterior, você não precisa instalar o agente do MMA. Mapa do Serviço integra-se com Operations Manager. Você pode habilitar a integração usando [estas](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) diretrizes. No entanto, observe que o agente de dependência precisa estar instalado nesses computadores.<br/><br/> [Examine](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) os sistemas operacionais Linux com suporte no agente de log Analytics.
Grupos de avaliação | Os grupos para os quais você deseja visualizar dependências de grupo não devem conter mais de 10 máquinas. Se você tiver mais de 10 máquinas, divida-as em grupos menores para visualizar as dependências.

## <a name="associate-a-log-analytics-workspace"></a>Associar um espaço de trabalho do Log Analytics

Para usar a visualização de dependência, você precisa associar um [espaço de trabalho log Analytics](../azure-monitor/platform/manage-access.md) a um projeto de migrações para Azure.

- Você pode anexar um espaço de trabalho somente na assinatura do projeto de migrações para Azure.
- Você pode anexar um espaço de trabalho existente ou criar um novo.
- Você anexa o espaço de trabalho na primeira vez em que configura a visualização de dependência para um computador.
- Você pode anexar um espaço de trabalho somente após descobrir computadores no projeto de migrações para Azure. Você pode fazer isso Configurando um dispositivo de migrações para Azure para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O dispositivo descobre computadores locais e envia metadados e dados de desempenho para migrações para o Azure: Avaliação do servidor. [Saiba mais](migrate-appliance.md).

Anexe um espaço de trabalho da seguinte maneira:

1. Nas **migrações para Azure: Avaliação**do servidor, clique em **visão geral**. Se você ainda não tiver adicionado a ferramenta de avaliação do servidor, [faça isso primeiro](how-to-assess.md).
2. Em **visão geral**, clique na seta para baixo para expandir o **Essentials**.
3. No **espaço de trabalho do OMS**, clique em **requer configuração**.
4. Em **Configurar espaço de trabalho**, especifique se deseja criar um novo espaço de trabalho ou usar um existente:

    ![Adicionar espaço de trabalho](./media/how-to-create-group-machine-dependencies/workspace.png)

    - Depois de especificar um nome para um novo espaço de trabalho, ele é criado na mesma geografia que o projeto de migrações para Azure.
    - Ao anexar um workspace, você pode escolher entre todos os workspaces disponíveis na mesma assinatura que o projeto de migração.
    - Você precisa de acesso de leitor ao espaço de trabalho para poder anexá-lo.
    - Você não pode modificar o espaço de trabalho associado a um projeto depois que ele é anexado.

## <a name="download-and-install-the-vm-agents"></a>Fazer o download e instalar os agente de VM

Baixe e instale os agentes em cada computador local que você deseja visualizar com o mapeamento de dependência.

1. Nas **migrações para Azure: Avaliação**do servidor, clique em **servidores**descobertos.
2. Para cada computador para o qual você deseja usar a visualização de dependência, clique em **requer instalação do agente**.
3. Na página **dependências** de um computador > **Baixe e instale o MMA**, baixe o agente apropriado e instale-o conforme descrito abaixo.
4. Em **baixar e instalar o agente de dependência**, baixe o agente apropriado e instale-o conforme descrito abaixo.
5. Em **Configurar agente do MMA**, copie a ID e a chave do espaço de trabalho. Você precisará delas quando instalar o agente do MMA.

### <a name="install-the-mma"></a>Instalar o MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Instalar o agente em um computador com Windows

Para instalar o agente em uma máquina com Windows:

1. Clique duas vezes no agente baixado.
2. Na página de **Boas-vindas**, clique em **Avançar**. Na página **Termos de Licença**, clique em **Concordo** para aceitar a licença.
3. Em **Pasta de Destino**, mantenha ou modifique a pasta de instalação padrão > **Avançar**.
4. Em **Opções de Configuração do Agente**, selecione **Azure Log Analytics** > **Avançar**.
5. Clique em **Adicionar** para adicionar um espaço de trabalho do Log Analytics. Cole a ID do workspace e a chave que você copiou do portal. Clique em **Avançar**.

#### <a name="install-the-agent-on-a-linux-machine"></a>Instalar o agente em um computador com Linux

Para instalar o agente em uma máquina com Linux:

1. Transfira o pacote apropriado (x86 ou x64) para seu computador Linux usando scp/sftp.
2. Instale o pacote usando o argumento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

### <a name="install-the-dependency-agent"></a>Instalar o Agente de dependência
1. Para instalar o Agente de dependência em uma máquina com Windows, clique duas vezes no arquivo de instalação e siga o assistente.
2. Para instalar o Agente de dependência em uma máquina com Linux, instale como raiz usando o comando a seguir:

    ```sh InstallDependencyAgent-Linux64.bin```

## <a name="create-a-group-using-dependency-visualization"></a>Criar um grupo usando a visualização de dependência

1. Nas **migrações para Azure: Avaliação**do servidor, clique em **servidores**descobertos.
2. Na coluna **dependências** , clique em **Exibir dependências** para cada computador que você deseja examinar.
3. No mapa de dependências, você pode ver o seguinte:
    - Conexões TCP de entrada (clientes) e de saída (servidores), de e para o computador.
    - Os computadores dependentes que não têm os agentes de dependência instalados são agrupados por números de porta.
    - Os computadores dependentes com agentes de dependência instalados são mostrados como caixas separadas.
    - Processos em execução dentro da máquina. Expanda cada caixa da máquina para exibir os processos.
    - Propriedades da máquina (incluindo FQDN, sistema operacional, endereço MAC). Clique em cada caixa da máquina para exibir os detalhes.

4. Você pode procurar por dependências em períodos diferentes clicando no período no rótulo de intervalo de tempo. Por padrão, o intervalo é de uma hora. Você pode modificar o intervalo, ou especificar as datas de início e de término e a duração.

    > [!NOTE]
    > O intervalo de tempo pode ser de até uma hora. Se você precisar de um intervalo mais longo, use Azure Monitor para consultar dados dependentes por um período mais longo.

5. Depois de identificar os computadores dependentes que você deseja agrupar, use Ctrl + clique para selecionar vários computadores no mapa e clique em **Agrupar computadores**.
6. Especifique um nome de grupo.
7. Verifique se as máquinas dependentes foram descobertas pelas Migrações para Azure.

    - Se um computador dependente não for descoberto por migrações para Azure: Avaliação do servidor, você não pode adicioná-lo ao grupo.
    - Para adicionar um computador, execute a descoberta novamente e verifique se o computador foi descoberto.

8. Se quiser criar uma avaliação para esse grupo, marque a caixa de seleção para criar uma nova avaliação para o grupo.
8. Clique em **OK** para salvar o grupo.

Depois de criar o grupo, recomendamos que você instale agentes em todos os computadores do grupo e, em seguida, visualize as dependências de todo o grupo.

## <a name="query-dependency-data-in-azure-monitor"></a>Dados de dependência de consulta em Azure Monitor

Você pode consultar dados de dependência capturados por Mapa do Serviço no espaço de trabalho Log Analytics associado ao projeto de migrações para Azure. Log Analytics é usado para gravar e executar Azure Monitor consultas de log.

- [Saiba como](../azure-monitor/insights/service-map.md#log-analytics-records) procurar mapa do serviço dados no log Analytics.
- [Obtenha uma visão geral](../azure-monitor/log-query/get-started-queries.md) da gravação de consultas de log no [log Analytics](../azure-monitor/log-query/get-started-portal.md).

Execute uma consulta para dados de dependência da seguinte maneira:

1. Depois de instalar os agentes, acesse o portal e clique em **Visão geral**.
2. Nas **migrações para Azure: Avaliação**do servidor, clique em **visão geral**. Clique na seta para baixo para expandir o **Essentials**.
3. No **espaço de trabalho do OMS**, clique no nome do espaço de trabalho.
3. Na página Log Analytics espaço de trabalho > **geral**, clique em **logs**.
4. Escreva sua consulta e clique em **executar**.

### <a name="sample-queries"></a>Consultas de exemplo

Fornecemos um número de consultas de exemplo que você pode usar para extrair dados de dependência.

- Você pode modificar as consultas para extrair seus pontos de dados preferenciais.
- [Examine](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) uma lista completa de registros de dados de dependência.
- [Examine](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) as consultas de exemplo adicionais.

#### <a name="sample-review-inbound-connections"></a>Amostra: Revisar conexões de entrada

Examine as conexões de entrada para um conjunto de VMs.

- Os registros na tabela para métricas de conexão (VMConnection) não representam conexões de rede física individuais.
- Várias conexões de rede física são agrupadas em uma conexão lógica.
- [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) sobre como os dados de conexão de rede física são agregados no VMConnection.

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

#### <a name="sample-summarize-sent-and-received-data"></a>Amostra: Resumir dados enviados e recebidos

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
