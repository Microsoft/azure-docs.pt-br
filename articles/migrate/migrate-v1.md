---
title: Trabalhar com a versão anterior das Migrações para Azure | Microsoft Docs
description: Fornece um resumo para trabalhar com a versão anterior das Migrações para Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7cf87c3a146f51666a2c24c7cd0d6e9425159225
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228400"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>Trabalhar com a versão anterior das Migrações para Azure

Este artigo fornece informações sobre como trabalhar com a versão anterior das Migrações para Azure.


Há duas versões do serviço de Migrações para Azure:

- **Versão atual**: use a versão atual para criar projetos das Migrações para Azure, descobrir computadores locais e orquestrar avaliações e migrações. [Saiba mais](whats-new.md) sobre as novidades desta versão.
- **Versão anterior**: se você estava usando a versão anterior das Migrações para Azure (havia suporte apenas a avaliação de VMs do VMware locais), agora deve usar a versão atual. Se você ainda precisa usar projetos das Migrações para Azure criados na versão anterior, isto é o que você pode e não pode fazer:
    - Você não pode mais criar projetos de migração.
    - Recomendamos que você não execute novas descobertas.
    - Você ainda pode acessar os projetos existentes.
    - Você ainda pode executar avaliações.
    

## <a name="upgrade-between-versions"></a>Upgrade entre versões

Não é possível fazer upgrade de projetos ou componentes na versão anterior para a nova versão. Você precisa [criar um novo projeto das Migrações para Azure](how-to-add-tool-first-time.md) e adicionar ferramentas de avaliação e migração a ele.

## <a name="find-projects-from-previous-version"></a>Localizar projetos da versão anterior

Localize projetos da versão anterior da seguinte maneira:

1. No portal do Azure > **Todos os serviços**, pesquise e selecione **Migrações para Azure**. 
2. No painel Migrações para Azure, há uma notificação e um link para acessar projetos antigos das Migrações para Azure.
3. Clique no link para abrir projetos v1.


## <a name="create-an-assessment"></a>Criar uma avaliação

Após as VMs terem sido descobertas no portal, você pode agrupá-las e criar uma avaliações.

- Você pode criar como avaliações locais imediatamente após as VMs serem descobertas no portal.
- Para avaliações baseadas em desempenho, recomendamos que você aguarde pelo menos um dia antes de criar uma avaliação baseada em desempenho para obter recomendações de tamanho confiável.

Crie uma avaliação da seguinte maneira:

1. Na página **Visão geral** do projeto, clique em **Criar avaliação**.
2. Clique em **Exibir tudo** para examinar as propriedades da avaliação.
3. Crie o grupo e especifique um nome de grupo.
4. Selecione as máquinas que deseja adicionar ao grupo.
5. Clique em **Criar avaliação**, para criar o grupo e a avaliação.
6. Depois que a avaliação é criada, é possível exibi-la em **Visão geral** > **Painel**.
7. Clique em **Exportar avaliação**, para baixá-la como um arquivo do Excel.

Se você quiser atualizar uma avaliação existente com os dados de desempenho mais recentes, poderá usar o comando **Recalcular** na avaliação para atualizá-la.

## <a name="review-an-assessment"></a>Examinar uma avaliação 

Uma avaliação tem três estágios:

- Uma avaliação começa com uma análise de adequação para descobrir se as máquinas são compatíveis no Azure.
- Estimativas de dimensionamento.
- Estimativa de custo mensal.

Uma máquina só passa para um estágio posterior se passar pelo anterior. Por exemplo, se um computador falhar na verificação de adequação, será marcado como não adequado para o Azure, e o dimensionamento e o custo não serão calculados.


### <a name="review-azure-readiness"></a>Examinar a Preparação para o Azure

O modo de exibição da preparação para o Azure na avaliação mostra o status de preparação de cada VM.

**Preparação** | **State** | **Detalhes**
--- | --- | ---
Pronto para o Azure | Sem problemas de compatibilidade. O computador pode ser migrado no estado em que se encontra para o Azure e será inicializado no Azure com suporte completo do Azure. | Para VMs que estão prontas, o Migrações para Azure recomenda um tamanho de VM no Azure.
Condicionalmente pronta para o Azure | O computador pode ser inicializado no Azure, mas poderá não ter suporte completo do Azure. Por exemplo, um computador com uma versão mais antiga do Windows Server que não tenha suporte no Azure. | As Migrações para Azure explicam os problemas de preparação e fornecem etapas de correção.
Não pronta para o Azure |  A VM não é inicializada no Azure. Por exemplo, se uma VM tiver um disco com mais de 4 TB, ela não poderá ser hospedada no Azure. | As Migrações para Azure explicam os problemas de preparação e fornecem etapas de correção.
Preparação desconhecida | As Migrações para Azure não podem identificar a Preparação para o Azure, geralmente porque os dados não estão disponíveis.


#### <a name="azure-vm-properties"></a>Propriedades da VM do Azure
A preparação leva em consideração uma série de propriedades da VM para identificar se ela pode ser executada no Azure.


**Propriedade** | **Detalhes** | **Preparação**
--- | --- | ---
**Tempo de inicialização** | BIOS com suporte. UEFI sem suporte. | Condicionalmente pronto se o tipo de inicialização for UEFI.
**Núcleos** | Núcleo dos computadores < = o número máximo de núcleos (128) com suporte para uma VM do Azure.<br/><br/> Se o histórico de desempenho estiver disponível, as Migrações para Azure considerarão os núcleos utilizados.<br/>Se um <br/>fator de conforto for especificado nas configurações de avaliação, o número de núcleos utilizados será multiplicado pelo fator de conforto.<br/><br/> Se não houver histórico de desempenho, as Migrações para Azure usarão os núcleos alocados, sem aplicar o fator de conforto. | Pronto, se for menor que ou igual aos limites.
**Memória** | O tamanho da memória do computador < = a memória máxima (3892 GB no Azure M series Standard_M128m&nbsp;<sup>2</sup>) para uma VM do Azure. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Se o histórico de desempenho estiver disponível, as Migrações para Azure considerarão a memória utilizada.<br/><br/>Se um fator de conforto for especificado, a memória utilizada será multiplicada pelo fator de conforto.<br/><br/> Se não houver histórico, a memória alocada será usada sem a aplicação do fator de conforto.<br/><br/> | Se estiver pronto dentro dos limites.
**Disco de armazenamento** | O tamanho alocado de um disco deve ser de 4 TB (4096 GB) ou menos.<br/><br/> O número de discos anexados à máquina deve ser de 65 ou menos, incluindo o disco do sistema operacional. | Se estiver pronto dentro dos limites.
**Rede** | Um computador deve ter 32 NICs ou menos conectados a ele. | Se estiver pronto dentro dos limites.

#### <a name="guest-operating-system"></a>Sistema operacional convidado

Junto com as propriedades da VM, as Migrações para Azure também examinam o sistema operacional convidado da VM local para identificar se ela pode ser executada no Azure.

- As Migrações para Azure consideram o sistema operacional especificado no vCenter Server.
- Como a descoberta feita pelas Migrações para Azure tem como base o dispositivo, ela não tem uma maneira de verificar se o sistema operacional em execução dentro da VM é o mesmo especificado no vCenter Server.

A lógica a seguir é usada.

**Sistema operacional** | **Detalhes** | **Preparação**
--- | --- | ---
Windows Server 2016 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure
Windows Server 2012 R2 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure
Windows Server 2012 e todos os SPs | O Azure fornece suporte total. | Pronto para o Azure
Windows Server 2008 R2 e todos os SPs | O Azure fornece suporte total.| Pronto para o Azure
Windows Server 2008 (32 bits e 64 bits) | O Azure fornece suporte total. | Pronto para o Azure
Windows Server 2003, 2003 R2 | Sem suporte e precisa de um [CSA (contrato personalizado de suporte)](https://aka.ms/WSosstatement) para ter suporte no Azure. | Condicionalmente pronto para o Azure, considere atualizar o sistema operacional antes de migrar para o Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Sem suporte. O computador pode ser inicializado no Azure, mas não há suporte ao sistema operacional fornecido pelo Azure. | Condicionalmente pronto para o Azure, considere atualizar o sistema operacional antes de migrar para o Azure.
Cliente do Windows 7, 8 e 10 | O Azure oferece suporte apenas [com a Assinatura do Visual Studio.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Condicionalmente pronta para o Azure
Windows 10 Pro Desktop | O Azure oferece suporte com [Direitos de Hospedagem multilocatário.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Condicionalmente pronta para o Azure
Windows Vista, XP Professional | Sem suporte. O computador pode ser inicializado no Azure, mas não há suporte ao sistema operacional fornecido pelo Azure. | Condicionalmente pronto para o Azure, considere atualizar o sistema operacional antes de migrar para o Azure.
Linux | O Azure endossa esses [sistemas operacionais Linux](../virtual-machines/linux/endorsed-distros.md). Outros sistemas operacionais podem ser inicializados no Azure, mas recomendamos atualizar o sistema operacional para uma versão endossada antes de migrar para o Azure. | Pronto para o Azure se a versão é aprovada.<br/><br/>Condicionalmente pronto se a versão não é aprovada.
Outros sistemas operacionais<br/><br/> Por exemplo, Oracle Solaris, sistema operacional Apple Mac OS, FreeBSD, etc. | O Azure não endossa esses sistemas operacionais. O computador pode ser inicializado no Azure, mas não há suporte do sistema operacional fornecido pelo Azure. | Condicionalmente pronto para o Azure, é recomendável atualizar o sistema operacional antes de migrar para o Azure.  
Sistema operacional especificado como **Outros** no vCenter Server | As Migrações para Azure não podem identificar o sistema operacional neste caso. | Preparação desconhecida. Certifique-se de que o sistema operacional em execução dentro da VM tem suporte no Azure.
Sistemas operacionais de 32 bits | O computador pode ser inicializado no Azure, mas o Azure pode não fornecer suporte completo. | Condicionalmente pronto para o Azure, considere atualizar o sistema operacional do computador de 32 bits para 64 bits antes de migrar para o Azure.


### <a name="review-sizing"></a>Revisar dimensionamento

 A recomendação de tamanho das Migrações para Azure depende do critério de tamanho especificado nas propriedades de avaliação.

- Se o dimensionamento for baseado em desempenho, a recomendação de tamanho considerará o histórico de desempenho das VMs (CPU e memória) e dos discos (IOPS e taxa de transferência).
- Se o critério de dimensionamento for “como local”, a recomendação de tamanho no Azure será baseada no tamanho da VM local. O dimensionamento de disco é baseado no tipo de armazenamento especificado nas propriedades de avaliação (o padrão é discos Premium). As Migrações para Azure não consideram os dados de desempenho para a VM e para os discos.

### <a name="review-cost-estimates"></a>Examinar estimativas de custo

As estimativas de custo mostram o total de computação e armazenamento da execução das máquinas virtuais no Azure, junto com os detalhes de cada máquina.

- As estimativas de custo são calculadas usando as recomendações de tamanho para uma máquina virtual e seus discos, e as propriedades de avaliação.
- Os custos mensais estimados de computação e armazenamento são agregados para todas as VMs no grupo.
- A estimativa de custo é para executar a VM local como VMs de IaaS (infraestrutura como serviço) do Azure. As Migrações para Azure não consideram nenhum custo de PaaS (Plataforma como Serviço) ou SaaS (Software como Serviço).

### <a name="review-confidence-rating-performance-based-assessment"></a>Examinar a classificação de confiança (avaliação baseada em desempenho)

Cada avaliação baseada em desempenho é associada a uma classificação de confiança.

- Uma classificação de confiança varia de uma a cinco estrelas (uma é a mais baixa e cinco, a mais alta).
- A classificação de confiança é atribuída a uma avaliação com base na disponibilidade de pontos de dados necessários para calcular a avaliação.
- A classificação de confiança de uma avaliação ajuda a estimar a confiabilidade das recomendações de tamanho fornecidas pelas Migrações para Azure.
- A classificação de confiança não está disponível para as avaliações locais "no estado em que se encontram".

Para fazer o dimensionamento com base no desempenho, a Migrações para Azure precisa do seguinte:
- Dados de utilização da CPU.
- Dados de memória da VM.
- Para cada disco anexado à VM, é necessário ter os dados da taxa de transferência e a IOPS do disco.
- Para cada adaptador de rede anexado a uma VM, as Migrações para Azure precisam da entrada/saída da rede.
- Se algum dos itens acima não estiver disponível, as recomendações de tamanho (e, portanto, as classificações de confiança) poderão não ser confiáveis.


Dependendo da porcentagem de pontos de dados disponível, as classificações de confiança possíveis são resumidas na tabela.

**Disponibilidade dos pontos de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 estrela
21%-40% | 2 estrelas
41%-60% | 3 estrelas
61%-80% | 4 estrelas
81%-100% | 5 estrelas


#### <a name="assessment-issues-affecting-confidence-ratings"></a>Problemas de avaliação que afetam as classificações de confiança

Uma avaliação pode não ter todos os pontos de dados disponíveis devido a vários motivos:

- Você não fez o perfil do ambiente durante a avaliação. Por exemplo, se você criar a avaliação com duração de desempenho definida como um dia, precisará aguardar pelo menos um dia após iniciar a descoberta ou até que todos os pontos de dados sejam coletados.
- Algumas VMs foram desligadas durante o período sujeito à avaliação. Se alguma VM foi desligada durante parte do tempo, as Migrações para Azure não poderão coletar dados de desempenho para esse período.
- Algumas VMs foram criadas durante o período de cálculo da avaliação. Por exemplo, se você criou uma avaliação usando o histórico de desempenho do mês passado, mas criou várias VMs no ambiente há uma semana, o histórico de desempenho das novas VMs não aparecerá em todo esse período.

> [!NOTE]
> Se a classificação de confiança de alguma avaliação estiver abaixo de cinco estrelas, aguarde pelo menos um dia para que o dispositivo crie o perfil do ambiente e recalcule a avaliação. Se não fizer isso, o dimensionamento baseado em desempenho poderá não ser confiável. Se você não quiser recalcular, recomendamos mudar para dimensionamento como local, com a alteração das propriedades de avaliação.



## <a name="create-groups-using-dependency-visualization"></a>Criar grupos usando a visualização de dependência

Além de criar grupos manualmente, você pode criar grupos usando a visualização de dependência.
- Normalmente, você usa esse método quando quer avaliar grupos com níveis mais altos de confiança por meio da verificação das dependências entre computadores, antes de executar uma avaliação.
- A visualização de dependências pode ajudá-lo a planejar de forma eficaz sua migração para o Azure. Isso ajudará a garantir que nada seja deixado para trás e que interrupções surpresa não ocorram quando você estiver migrando para o Azure.
- Você pode descobrir todos os sistemas interdependentes que precisam migrar juntos, bem como identificar se um sistema em execução ainda está atendendo aos usuários ou é candidato para encerramento em vez de migração.
- As Migrações para Azure usam a solução Mapa do Serviço no Azure Monitor para habilitar a visualização de dependência.

> [!NOTE]
> A visualização de dependência não está disponível no Azure Governamental.

Para configurar a visualização de dependência, associe um espaço de trabalho do Log Analytics a um projeto das Migrações para Azure, instale agentes nos computadores nos quais deseja visualizar dependências e crie grupos usando as informações de dependência. 



### <a name="associate-a-log-analytics-workspace"></a>Associar um espaço de trabalho do Log Analytics

Para usar a visualização de dependência, associe um espaço de trabalho do Log Analytics a um projeto de migração. Você só pode criar ou anexar um workspace na mesma assinatura em que o projeto de migração é criado.

1. Para anexar um espaço de trabalho do Log Analytics a um projeto, em **Visão geral** > **Essentials**, clique em **Requer configuração**.
2. Você pode criar um novo espaço de trabalho ou anexar um existente:
  - Para criar um novo espaço de trabalho, especifique um nome. O espaço de trabalho será criado em uma região na mesma [geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que o projeto de migração.
  - Ao anexar um workspace, você pode escolher entre todos os workspaces disponíveis na mesma assinatura que o projeto de migração. Somente os espaços de trabalho que foram criados [em uma região do Mapa do Serviço com suporte](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions) são listados. Para anexar um espaço de trabalho, verifique se você tem acesso de 'Leitura' ao espaço de trabalho.

> [!NOTE]
> Não é possível alterar o espaço de trabalho associado a um projeto de migração.

### <a name="download-and-install-vm-agents"></a>Baixar e instalar os agentes de VM

Depois de configurar um espaço de trabalho, baixe e instale agentes em cada computador local que deseja avaliar. Além disso, se você tiver máquinas sem conectividade com a Internet, será necessário fazer o download e instalar o [gateway do Log Analytics](../azure-monitor/platform/gateway.md) nelas.

1. Em **Visão geral**, clique em **Gerenciar** > **Máquinas**e selecione a máquina exigida.
2. Na coluna **Dependências**, clique em **Instalar agentes**.
3. Na página **Dependências**, baixe e instale o MMA (Microsoft Monitoring Agent) e o Agente de dependência em cada VM que você deseja avaliar.
4. Copie a ID e a chave do workspace. Você precisará delas quando instalar o MMA na máquina local.

> [!NOTE]
> Para automatizar a instalação de agentes, use uma ferramenta de implantação, como o System Center Configuration Manager, ou use uma ferramenta de parceiro, como o [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), que oferece uma solução de implantação do agente para as Migrações para Azure.


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>Instalar o agente MMA em um computador com Windows

Para instalar o agente em uma máquina com Windows:

1. Clique duas vezes no agente baixado.
2. Na página de **Boas-vindas**, clique em **Avançar**. Na página **Termos de Licença**, clique em **Concordo** para aceitar a licença.
3. Em **Pasta de Destino**, mantenha ou modifique a pasta de instalação padrão > **Avançar**.
4. Em **Opções de Configuração do Agente**, selecione **Azure Log Analytics** > **Avançar**.
5. Clique em **Adicionar** para adicionar um espaço de trabalho do Log Analytics. Cole a ID do workspace e a chave que você copiou do portal. Clique em **Próximo**.

Você pode instalar o agente na linha de comando ou usando um método automatizado, como o System Center Configuration Manager. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sobre como usar esses métodos para instalar o agente do MMA.

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>Instalar o agente MMA em um computador com Linux

Para instalar o agente em uma máquina com Linux:

1. Transfira o pacote apropriado (x86 ou x64) para seu computador Linux usando scp/sftp.
2. Instale o pacote usando o argumento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Saiba mais](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) sobre a lista de sistemas de operacionais Linux com suporte no MMA.

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>Instalar o agente MMA em um computador monitorado pelo Operations Manager

Para computadores monitorados pelo System Center Operations Manager 2012 R2 ou posterior, não é necessário instalar o agente do MMA. O Mapa do Serviço integra-se ao MMA do Operations Manager para coletar os dados de dependência necessários. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). O agente de dependência precisa ser instalado.


### <a name="install-the-dependency-agent"></a>Instalar o Agente de dependência

1. Para instalar o Agente de dependência em uma máquina com Windows, clique duas vezes no arquivo de instalação e siga o assistente.
2. Para instalar o Agente de dependência em uma máquina com Linux, instale como raiz usando o comando a seguir:

    ```sh InstallDependencyAgent-Linux64.bin```

- Saiba mais sobre o suporte do agente de Dependência para os sistemas operacionais [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) e [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems).
- [Saiba mais](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) sobre como você pode usar scripts para instalar o Dependency agent.


### <a name="create-a-group-with-dependency-mapping"></a>Criar um grupo com mapeamento de dependências

1. Depois de instalar os agentes, acesse o portal e clique em **Gerenciar** > **Máquinas**.
2. Pesquise pela máquina na qual você instalou os agentes.
3. Agora, a coluna **Dependências** da máquina deve aparecer como **Exibir Dependências**. Clique na coluna para exibir as dependências da máquina.
4. O mapa de dependências da máquina mostra os seguintes detalhes:
    - Conexões TCP de entrada (clientes) e saída (servidores) para/da máquina
        - As máquinas dependentes que não têm o agente de dependência e o MMA instalados são agrupadas por números de porta.
        - As máquinas dependentes que têm o agente de dependência e o MMA instalados aparecem como caixas separadas.
    - Quanto aos processos em execução na máquina, você pode expandir cada caixa para exibir os processos
    - As propriedades da máquina, incluindo o FQDN, o sistema operacional e o endereço MAC, são mostradas. Você pode clicar na caixa de cada computador para exibir detalhes.

4. Você pode exibir dependências de períodos diferentes clicando no período no rótulo de intervalo de tempo. Por padrão, o intervalo é de uma hora. Você pode modificar o intervalo, ou especificar as datas de início e de término e a duração.

   > [!NOTE]
   >    Há suporte a um intervalo de tempo de até uma hora. Use os logs do Azure Monitor para [consultar os dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) por mais tempo.

5. Depois de identificar máquinas dependentes que você deseja agrupar, use Ctrl+Clique para selecionar várias máquinas no mapa e clique em **Agrupar máquinas**.
6. Especifique um nome de grupo. Verifique se as máquinas dependentes foram descobertas pelas Migrações para Azure.

    > [!NOTE]
    > Se um computador dependente não for descoberta pelas Migrações para Azure, você não poderá adicioná-lo ao grupo. Para adicionar essas máquinas ao grupo, você precisa executar o processo de descoberta novamente com o escopo correto no vCenter Server e certificar-se de que a máquina seja descoberta pelas Migrações para Azure.  

7. Se quiser criar uma avaliação para esse grupo, marque a caixa de seleção para criar uma nova avaliação para o grupo.
8. Clique em **OK** para salvar o grupo.

Após o grupo ser criado, é recomendável instalar agentes em todas as máquinas do grupo e refinar o grupo visualizando a dependência de todo o grupo.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Consultar dados de dependência em logs do Azure Monitor

Os dados de dependência capturados pelo Mapa do Serviço ficam disponíveis para consulta no espaço de trabalho do Log Analytics associado ao seu projeto das Migrações para Azure. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) sobre as tabelas de dados do Mapa do Serviço a serem consultadas nos logs do Azure Monitor. 

Para executar as consultas do Kusto:

1. Depois de instalar os agentes, acesse o portal e clique em **Visão geral**.
2. Em **visão geral**, acesse a seção **Essentials** do projeto e clique no nome do workspace fornecido, ao lado de **Workspace do OMS**.
3. Na página do espaço de trabalho do Log Analytics, clique em **Geral** > **Logs**.
4. Crie sua consulta para coletar dados de dependência usando os logs do Azure Monitor. Encontre consultas de exemplo na próxima seção.
5. Execute a consulta clicando em Executar. 

[Saiba mais](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) sobre como codificar consultas Kusto. 

### <a name="sample-azure-monitor-logs-queries"></a>Consultas aos logs do Azure Monitor de exemplo

Veja a seguir as consultas de exemplo que você pode usar para extrair dados de dependência. Você pode modificar as consultas para extrair seus pontos de dados preferenciais. Uma lista completa dos campos nos registros de dados de dependência está disponível [aqui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records). Encontre mais consultas de exemplo [aqui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Resumir conexões de entrada em um conjunto de computadores

Os registros na tabela para métricas de conexão, a VMConnection, não representam conexões de rede física individuais. Várias conexões de rede física são agrupadas em uma conexão lógica. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) sobre como os dados de conexão de rede física são agregados em um único registro lógico em VMConnection. 

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

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Resumir o volume de dados enviados e recebidos em conexões de entrada entre um conjunto de computadores

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
[Saiba mais](migrate-services-overview.md) sobre a versão mais recente das Migrações para Azure.
