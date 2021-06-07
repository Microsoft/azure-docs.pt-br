---
title: Suporte de avaliação do VMware nas Migrações para Azure
description: Saiba mais sobre o suporte de avaliação para servidores em execução no ambiente VMware com a descoberta e avaliação de migrações para Azure
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 4d51fc13e3587c21a7340b35db10d3cf36ab74b5
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557540"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matriz de suporte para avaliação do VMware 

Este artigo resume os pré-requisitos e requisitos de suporte ao descobrir e avaliar servidores em execução no ambiente VMware para migração para o Azure, usando a ferramenta [migrações para Azure: descoberta e avaliação](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) . Para avaliar servidores, você cria um projeto, que adiciona a ferramenta migrações do Azure: descoberta e avaliação para o projeto. Depois que a ferramenta for adicionada, implante o dispositivo do Migrações para Azure. O dispositivo descobre continuamente servidores locais e envia metadados de configuração e desempenho para o Azure. Após a conclusão da descoberta, você coleta servidores descobertos em grupos e executa uma avaliação de um grupo.

Se você quiser migrar servidores VMware para o Azure, examine a [matriz de suporte de migração](migrate-support-matrix-vmware-migration.md).



## <a name="limitations"></a>Limitações

**Requisito** | **Detalhes**
--- | ---
**Limites do projeto** | Você pode criar vários projetos em uma assinatura do Azure.<br/><br/> Você pode descobrir e avaliar até 50.000 servidores do ambiente VMware em um único [projeto](migrate-support-matrix.md#project). Um projeto também pode incluir servidores físicos e servidores do ambiente Hyper-V, até os limites de avaliação.
**Discovery** | O dispositivo de migrações para Azure pode descobrir até 10.000 servidores em um vCenter Server.
**Avaliação** | Você pode adicionar até 35.000 servidores em um único grupo.<br/><br/> Você pode avaliar até 35.000 servidores em uma única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre as avaliações.


## <a name="vmware-requirements"></a>Requisitos do VMware

**VMware** | **Detalhes**
--- | ---
**vCenter Server** | Os servidores que você deseja descobrir e avaliar devem ser gerenciados por vCenter Server versão 5,5, 6,0, 6,5, 6,7 ou 7,0.<br/><br/> Atualmente, não há suporte para a descoberta de servidores fornecendo detalhes do host ESXi no dispositivo.
**Permissões** | Migrações para Azure: a descoberta e a avaliação precisam de uma vCenter Server conta somente leitura para descoberta e avaliação.<br/><br/> Se você quiser executar a descoberta de inventário de software e análise de dependência sem agente, a conta precisará de privilégios habilitados para operações de convidado de **máquinas virtuais**  >  .

## <a name="server-requirements"></a>Requisitos de servidor
**VMware** | **Detalhes**
--- | ---
**SO com suporte** | Todos os sistemas operacionais Windows e Linux podem ser avaliados quanto à migração.
**Storage** | Há suporte para discos anexados a controladores SCSI, IDE e SATA.


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de dispositivo para as Migrações para Azure

As Migrações para Azure usam o dispositivo das [Migrações para Azure](migrate-appliance.md) para descoberta e avaliação. Você pode implantar o dispositivo como um servidor em seu ambiente VMware usando um modelo OVA, importado para vCenter Server ou usando um [script do PowerShell](deploy-appliance-script.md).

- Saiba mais sobre os [requisitos de dispositivo](migrate-appliance.md#appliance---vmware) para VMware.
- No Azure Governamental, você deve implantar o dispositivo [usando um script](deploy-appliance-script-government.md).
- Examine as URLs que o dispositivo precisa para acessar em nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls) .


## <a name="port-access-requirements"></a>Requisitos de acesso à porta

**Dispositivo** | **Conexão**
--- | ---
**Dispositivo** | Conexões de entrada na porta TCP 3389 para permitir conexões de área de trabalho remota para o dispositivo.<br/><br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento de dispositivos usando a URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Conexões de saída na porta 443 (HTTPS) para enviar metadados de descoberta e desempenho para as Migrações para Azure.
**Servidor vCenter** | Conexões de entrada na porta TCP 443 para permitir que o dispositivo colete metadados de configuração e desempenho para avaliações. <br/><br/> O dispositivo é conectado ao vCenter na porta 443 por padrão. Se o vCenter Server for detectado em uma porta diferente, você pode modificar a porta ao configurar a descoberta.
**Hosts ESXi** | Se você quiser executar a [descoberta do inventário de software](how-to-discover-applications.md)ou a [análise de dependência sem agente](concepts-dependency-visualization.md#agentless-analysis), o dispositivo se conectará aos hosts ESXi na porta TCP 443, para descobrir o inventário de software e as dependências nos servidores.

## <a name="application-discovery-requirements"></a>Requisitos de descoberta de aplicativos

Além de descobrir servidores, migrações para Azure: a descoberta e a avaliação podem descobrir inventário de software em execução em servidores. A descoberta de aplicativos permite identificar e planejar um caminho de migração adaptado para suas cargas de trabalho locais.

**Suporte** | **Detalhes**
--- | ---
**Servidores com suporte** | Atualmente com suporte somente para servidores em seu ambiente VMware. Você pode executar a descoberta de aplicativos em até 10000 servidores, de cada dispositivo de migrações para Azure.
**Sistemas operacionais** | Há suporte para servidores que executam todas as versões do Windows e Linux.
**Requisitos de VM** | Para executar a descoberta do inventário de software, as ferramentas do VMware devem ser instaladas e executadas em servidores. <br/><br/> A versão das ferramentas do VMware deve ser posterior à 10.2.0.<br/><br/> Os servidores do Windows precisam ter o PowerShell versão 2.0 ou posterior instalado.
**Discovery** | A descoberta de aplicativos em servidores é executada no vCenter Server, usando as ferramentas do VMware instaladas nos servidores. O dispositivo reúne as informações sobre o inventário de software do vCenter Server, usando APIs vSphere. A descoberta de aplicativos é sem agente. Nenhum agente está instalado no servidor e o dispositivo não se conecta diretamente aos servidores. O WMI e o SSH devem estar habilitados e disponíveis em servidores Windows e Linux, respectivamente.
**vCenter Server conta de usuário** | A vCenter Server conta somente leitura usada para avaliação, precisa de privilégios habilitados para operações de convidado de **máquinas virtuais**  >  , a fim de interagir com os servidores para a descoberta de aplicativos.
**Acesso ao servidor** | Você pode adicionar várias credenciais de domínio e não domínio (Windows/Linux) no Gerenciador de configuração do dispositivo para descoberta de aplicativos.<br/><br/> Você precisa de uma conta de usuário convidado para servidores Windows e uma conta de usuário regular/normal (acesso não sudo) para todos os servidores Linux.
**Acesso à porta** | O dispositivo de migrações para Azure deve ser capaz de se conectar à porta TCP 443 em hosts ESXi que executam servidores nos quais você deseja executar a descoberta de aplicativos. O vCenter Server retorna uma conexão de host ESXi para baixar o arquivo que contém os detalhes do inventário de software.

## <a name="requirements-for-discovery-of-sql-server-instances-and-databases"></a>Requisitos para a descoberta de instâncias de SQL Server e bancos de dados

A [descoberta de aplicativos](how-to-discover-applications.md) identifica as instâncias de SQL Server. Usando essas informações, o dispositivo tenta se conectar às respectivas instâncias de SQL Server por meio da autenticação do Windows ou das credenciais de autenticação SQL Server fornecidas no dispositivo. Uma vez conectado, o dispositivo coleta dados de desempenho e configuração de SQL Server instâncias e bancos de dado. Os dados de configuração do SQL Server são atualizados uma vez a cada 24 horas e os dados de desempenho são capturados a cada 30 segundos.

**Suporte** | **Detalhes**
--- | ---
**Servidores com suporte** | Atualmente com suporte somente para SQL Servers em seu ambiente VMware. Você pode descobrir até 300 SQL Server instâncias ou 6000 bancos de dados SQL, o que for menor.
**Servidores Windows** | Há suporte para o Windows Server 2008 e superior.
**Servidores Linux** | Não tem suporte no momento.
**Mecanismo de autenticação** | Há suporte para a autenticação do Windows e do SQL Server. Você pode fornecer credenciais de ambos os tipos de autenticação no Gerenciador de configuração de dispositivo.
**Acesso ao SQL Server** | As migrações para Azure exigem uma conta de usuário do Windows que seja membro da função de servidor sysadmin.
**Versões do SQL Server** | Há suporte para SQL Server 2008 e superior.
**SQL Server edições** | As edições Enterprise, Standard, Developer e Express têm suporte.
**Configuração SQL com suporte** | Atualmente, há suporte para a descoberta de apenas instâncias de SQL Server autônomas e bancos de dados correspondentes.<br/> Não há suporte para a identificação do cluster de failover e o grupos de disponibilidade Always On.
**Serviços SQL com suporte** | Há suporte apenas para SQL Server Mecanismo de Banco de Dados. <br/> Não há suporte para a descoberta de SQL Server Reporting Services (SSRS), SQL Server Integration Services (SSIS) e SQL Server Analysis Services (SSAS).

> [!Note]
> As Migrações para Azure criptografarão a comunicação entre o dispositivo das Migrações para Azure e as instâncias do SQL Server de origem (com a propriedade de conexão Encrypt definida como TRUE). Essas conexões são criptografadas com [**TrustServerCertificate**](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (definido como TRUE); a camada de transporte usará o SSL para criptografar o canal e ignorar a cadeia de certificados para validar a confiança. O servidor do dispositivo precisa ser configurado para [**confiar na autoridade raiz do certificado**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Se nenhum certificado tiver sido provisionado no servidor quando ele foi inicializado, o SQL Server vai gerar um certificado autoassinado usado para criptografar pacotes de logon. [**Saiba mais**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

## <a name="dependency-analysis-requirements-agentless"></a>Requisitos de análise de dependência (sem agente)

A [análise de dependência](concepts-dependency-visualization.md) ajuda a identificar dependências entre servidores locais que você deseja avaliar e migrar para o Azure. A tabela resume os requisitos para configurar a análise de dependência sem agente.

**Suporte** | **Detalhes**
--- | --- 
**Servidores com suporte** | Atualmente com suporte somente para servidores em seu ambiente VMware.
**Servidores Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bits).<br/>Microsoft Windows Server 2008 (32 bits).
**Servidores Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 e posterior.
**Requisitos do servidor** | As ferramentas do VMware (posteriores a 10.2.0) devem ser instaladas e executadas nos servidores que você deseja analisar.<br/><br/> Os servidores devem ter o PowerShell versão 2,0 ou posterior instalado.
**Método de descoberta** |  As informações de dependência entre servidores são coletadas do vCenter Server, usando as ferramentas do VMware instaladas no servidor. O dispositivo reúne as informações do vCenter Server, usando APIs vSphere. Nenhum agente está instalado no servidor e o dispositivo não se conecta diretamente aos servidores. O WMI e o SSH devem estar habilitados e disponíveis em servidores Windows e Linux, respectivamente.
**conta do vCenter** | A conta somente leitura usada pela migração do Azure para avaliação precisa de privilégios habilitados para **máquinas virtuais > operações de convidado**.
**Permissões do Windows Server** |  Uma conta de usuário (local ou domínio) com permissões administrativas em servidores.
**Conta do Linux** | Conta de usuário raiz ou uma conta com essas permissões em arquivos/bin/netstat e/bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.<br/><br/> Defina esses recursos usando os seguintes comandos: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat
**Acesso à porta** | O dispositivo de migrações para Azure deve ser capaz de se conectar à porta TCP 443 em hosts ESXi que executam os servidores cujas dependências você deseja descobrir. O vCenter Server retorna uma conexão de host ESXi para baixar o arquivo que contém os dados de dependência.


## <a name="dependency-analysis-requirements-agent-based"></a>Requisitos de análise de dependência (baseado em agente)

A [análise de dependência](concepts-dependency-visualization.md) ajuda a identificar dependências entre servidores locais que você deseja avaliar e migrar para o Azure. A tabela resume os requisitos para configurar a análise de dependência baseada em agente.

**Requisito** | **Detalhes** 
--- | --- 
**Antes da implantação** | Você deve ter um projeto em vigor, com a ferramenta migrações para Azure: descoberta e avaliação adicionada ao projeto.<br/><br/>  Você implanta a visualização de dependência depois de configurar um dispositivo de migrações para Azure para descobrir seus servidores locais.<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Saiba como](how-to-assess.md) adicionar uma ferramenta de descoberta e avaliação a um projeto existente.<br/> Aprenda a configurar o dispositivo de Migrações para Azure para avaliação de [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) ou servidores físicos.
**Servidores com suporte** | Com suporte para todos os servidores no seu ambiente local.
**Log Analytics** | As Migrações para Azure usam a solução [Mapa do Serviço](../azure-monitor/insights/service-map.md) nos [logs do Azure Monitor](../azure-monitor/log-query/log-query-overview.md) para visualização de dependência.<br/><br/> Você associa um espaço de trabalho Log Analytics novo ou existente a um projeto. O espaço de trabalho para um projeto não pode ser modificado após ser adicionado. <br/><br/> O espaço de trabalho deve estar na mesma assinatura que o projeto.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste da Ásia ou oeste da Europa. Os espaços de trabalho em outras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar em uma região em que o [Mapa do Serviço é compatível](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> No Log Analytics, o espaço de trabalho associado à migração do Azure é marcado com a chave do projeto e o nome do projeto.
**Agentes necessários** | Em cada servidor que você deseja analisar, instale os seguintes agentes:<br/><br/> O [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> O [agente de dependência](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se os servidores locais não estiverem conectados à Internet, você precisará baixar e instalar Log Analytics gateway neles.<br/><br/> Saiba mais sobre a instalação do [agente de dependência](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Espaço de Trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma assinatura que o projeto.<br/><br/> As Migrações para Azure são compatível com os espaços de trabalho que residem nas regiões leste dos EUA, sudeste da Ásia e oeste da Europa.<br/><br/>  O espaço de trabalho deve estar em uma região em que o [Mapa do Serviço é compatível](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> O espaço de trabalho para um projeto não pode ser modificado após ser adicionado.
**Custo** | A solução Mapa do Serviço não incorrerá em cobranças pelos primeiros 180 dias (a partir do dia em que você associa o espaço de trabalho Log Analytics ao projeto)/<br/><br/> Após 180 dias, os encargos do Log Analytics Standard serão aplicados.<br/><br/> O uso de qualquer solução que não seja o Mapa do Serviço no workspace do Log Analytics associado gerará [encargos padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics Standard.<br/><br/> Quando o projeto é excluído, o espaço de trabalho não é excluído junto com ele. Após a exclusão do projeto, o uso do Mapa do Serviço não será mais gratuito e cada nó será cobrado de acordo com a camada paga do workspace do Log Analytics/<br/><br/>Se você tiver projetos criados antes da disponibilidade geral de Migrações para Azure (disponibilidade geral - 28 de fevereiro de 2018), pode ter gerado encargos adicionais do Mapa do Serviço. Para garantir o pagamento após apenas 180 dias, recomendamos que você crie um novo projeto, já que os espaços de trabalho existentes antes da disponibilidade geral ainda são passíveis de cobrança.
**Gerenciamento** | Ao registrar agentes no espaço de trabalho, você usa a ID e a chave fornecidas pelo projeto.<br/><br/> Você pode usar o espaço de trabalho do Log Analytics fora de Migrações para Azure.<br/><br/> Se você excluir o projeto associado, o espaço de trabalho não será excluído automaticamente. [Exclua-o manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Não exclua o espaço de trabalho criado pelas migrações para Azure, a menos que você exclua o projeto. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.
**Conectividade com a Internet** | Se os servidores não estiverem conectados à Internet, você precisará instalar o Log Analytics gateway neles.
**Azure Governamental** | A análise de dependência baseada em agente não é compatível.


## <a name="next-steps"></a>Próximas etapas

- [Examine](best-practices-assessment.md) as melhores práticas para a criação de avaliações.
- [Prepare-se para a avaliação do VMware](./tutorial-discover-vmware.md).