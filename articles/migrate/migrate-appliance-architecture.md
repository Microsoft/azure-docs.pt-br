---
title: Arquitetura do dispositivo de Migrações para Azure
description: Fornece uma visão geral do dispositivo de migrações para Azure usado na descoberta de servidor, avaliação e migração.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: f3a94576ef58eabf9d747c6e6c3a6372569d4cf1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785233"
---
# <a name="azure-migrate-appliance-architecture"></a>Arquitetura do dispositivo de Migrações para Azure

Este artigo descreve a arquitetura e os processos do dispositivo de migração do Azure. O dispositivo de migrações para Azure é um dispositivo leve que é implantado localmente, para descobrir servidores para migração para o Azure.

## <a name="deployment-scenarios"></a>Cenários de implantação

O dispositivo de Migrações para Azure é usado nos cenários a seguir.

**Cenário** | **Ferramenta** | **Usado para**
--- | --- | ---
**Descoberta e avaliação de servidores em execução no ambiente VMware** | Migrações para Azure: descoberta e avaliação | Descobrir servidores em execução em seu ambiente VMware<br/><br/> Execute a descoberta de inventário de software instalado, análise de dependência sem agente e descubra SQL Server instâncias e bancos de dados.<br/><br/> Coletar metadados de desempenho e de configuração do servidor para avaliações.
**Migração sem agente de servidores em execução no ambiente VMware** | Migrações para Azure: Migração de Servidor | Descubra servidores em execução em seu ambiente VMware.<br/><br/> Replique servidores sem instalar agentes neles.
**Descoberta e avaliação de servidores em execução no ambiente do Hyper-V** | Migrações para Azure: descoberta e avaliação | Descobrir servidores em execução no ambiente do Hyper-V.<br/><br/> Coletar metadados de desempenho e de configuração do servidor para avaliações.
**Descoberta e avaliação de servidores físicos ou virtualizados locais** |  Migrações para Azure: descoberta e avaliação |  Descubra servidores físicos ou virtualizados localmente.<br/><br/> Coletar metadados de desempenho e de configuração do servidor para avaliações.

## <a name="deployment-methods"></a>Métodos de implantação

O dispositivo pode ser implantado usando alguns métodos:

- O dispositivo pode ser implantado usando um modelo para servidores em execução no VMware ou no ambiente do Hyper-V ([modelo OVA para VMware](how-to-set-up-appliance-vmware.md) ou [VHD para Hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Se você não quiser usar um modelo, poderá implantar o dispositivo para o ambiente do VMware ou do Hyper-V usando um [script do instalador do PowerShell](deploy-appliance-script.md).
- No Azure governamental, você deve implantar o dispositivo usando um script do instalador do PowerShell. Consulte as etapas de implantação [aqui](deploy-appliance-script-government.md).
- Para servidores físicos ou virtualizados locais ou qualquer outra nuvem, você sempre implanta o dispositivo usando um script do instalador do PowerShell. Consulte as etapas de implantação [aqui](how-to-set-up-appliance-physical.md).
- Links para download estão disponíveis nas tabelas a seguir.

## <a name="appliance-services"></a>Serviços de dispositivo

O dispositivo tem os seguintes serviços:

- **Gerenciador de configuração de dispositivo**: Este é um aplicativo Web que pode ser configurado com detalhes de origem para iniciar a descoberta e a avaliação de servidores. 
- **Agente de descoberta**: o agente coleta metadados de configuração do servidor que podem ser usados para criação como avaliações locais.
- **Agente de avaliação**: o agente coleta metadados de desempenho do servidor que podem ser usados para criar avaliações baseadas em desempenho.
- **Serviço de atualização automática**: o serviço mantém todos os agentes em execução no dispositivo atualizados. Ele é executado automaticamente uma vez a cada 24 horas.
- **Agente Dra**: Orquestra a replicação de servidor e coordena a comunicação entre servidores replicados e o Azure. Usado somente ao replicar servidores no Azure usando a migração sem agente.
- **Gateway**: envia dados replicados para o Azure. Usado somente ao replicar servidores no Azure usando a migração sem agente.
- **Descoberta e agente de avaliação do SQL**: envia os metadados de configuração e desempenho de instâncias de SQL Server e bancos de dados para o Azure.

> [!Note]
> Os últimos 3 serviços estão disponíveis apenas no dispositivo usado para descoberta e avaliação de servidores em execução em seu ambiente VMware.<br/> A descoberta e a avaliação de instâncias e bancos de dados do SQL Server em execução no ambiente VMware já estão em versão prévia. Para experimentar esse recurso, use [**este link**](https://aka.ms/AzureMigrate/SQL) para criar um projeto na região **Leste da Austrália**. Se você já tiver um projeto no Leste da Austrália e quiser experimentar esse recurso, verifique se concluiu esses [**pré-requisitos**](how-to-discover-sql-existing-project.md) no portal.

## <a name="discovery-and-collection-process"></a>Processo de descoberta e coleta

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Arquitetura do dispositivo":::

O dispositivo se comunica com as fontes de descoberta usando o processo a seguir.

**Processo** | **Dispositivo VMware** | **Dispositivo Hyper-V** | **Dispositivo físico**
---|---|---|---
**Iniciar descoberta** | O dispositivo se comunica com o servidor vCenter na porta TCP 443 por padrão. Se o servidor vCenter escutar em uma porta diferente, você poderá configurá-lo no Gerenciador de configuração de dispositivo. | O dispositivo se comunica com os hosts Hyper-V na porta WinRM 5985 (HTTP). | O dispositivo se comunica com servidores Windows pela porta WinRM 5985 (HTTP) com servidores Linux pela porta 22 (TCP).
**Coletar metadados de configuração e desempenho** | O dispositivo coleta os metadados de servidores em execução no vCenter Server usando APIs vSphere conectando-se na porta 443 (porta padrão) ou em qualquer outra porta vCenter Server escuta. | O dispositivo coleta os metadados de servidores em execução em hosts Hyper-V usando uma sessão modelo CIM (CIM) com hosts na porta 5985.| O dispositivo coleta metadados de servidores Windows usando a sessão modelo CIM (CIM) com servidores na porta 5985 e de servidores Linux usando a conectividade SSH na porta 22.
**Enviar dados de descoberta** | O dispositivo envia os dados coletados para migrações para Azure: descoberta e avaliação e migrações para Azure: migração de servidor pela porta SSL 443.<br/><br/> O dispositivo pode se conectar ao Azure pela Internet ou via ExpressRoute (requer emparelhamento da Microsoft). | O dispositivo envia os dados coletados para migrações para Azure: descoberta e avaliação sobre a porta SSL 443.<br/><br/> O dispositivo pode se conectar ao Azure pela Internet ou via ExpressRoute (requer emparelhamento da Microsoft).| O dispositivo envia os dados coletados para migrações para Azure: descoberta e avaliação sobre a porta SSL 443.<br/><br/> O dispositivo pode se conectar ao Azure pela Internet ou via ExpressRoute (requer emparelhamento da Microsoft).
**Frequência de coleta de dados** | Os metadados de configuração são coletados e enviados a cada 30 minutos. <br/><br/> Os metadados de desempenho são coletados a cada 20 segundos e agregados para enviar um ponto de dados para o Azure a cada 10 minutos. <br/><br/> Os dados de inventário de software são enviados para o Azure uma vez a cada 12 horas. <br/><br/> Os dados de dependência sem agente são coletados a cada 5 minutos, agregados no dispositivo e enviados ao Azure a cada 6 horas. <br/><br/> Os dados de configuração do SQL Server são atualizados uma vez a cada 24 horas e os dados de desempenho são capturados a cada 30 segundos.| Os metadados de configuração são coletados e enviados a cada 30 minutos. <br/><br/> Os metadados de desempenho são coletados a cada 30 segundos e agregados para enviar um ponto de dados para o Azure a cada 10 minutos.|  Os metadados de configuração são coletados e enviados a cada 30 minutos. <br/><br/> Os metadados de desempenho são coletados a cada 5 minutos e agregados para enviar um ponto de dados para o Azure a cada 10 minutos.
**Avaliar e migrar** | Você pode criar avaliações dos metadados coletados pelo dispositivo usando as migrações para Azure: ferramenta de descoberta e avaliação.<br/><br/>Além disso, você também pode iniciar a migração de servidores em execução em seu ambiente VMware usando migrações para Azure: ferramenta de migração de servidor para orquestrar a replicação de servidor sem agente.| Você pode criar avaliações dos metadados coletados pelo dispositivo usando as migrações para Azure: ferramenta de descoberta e avaliação. | Você pode criar avaliações dos metadados coletados pelo dispositivo usando as migrações para Azure: ferramenta de descoberta e avaliação.

## <a name="next-steps"></a>Próximas etapas

[Examine](migrate-appliance.md) a matriz de suporte do dispositivo.