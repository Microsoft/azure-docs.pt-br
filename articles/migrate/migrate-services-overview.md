---
title: Sobre as Migrações para Azure
description: Saiba mais sobre o serviço Migrações para Azure.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: fd8806a02e48481042eb756a0a077d801583cd2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870765"
---
# <a name="about-azure-migrate"></a>Sobre as Migrações para Azure

Este artigo fornece uma visão geral rápida do serviço de Migrações para Azure.

As Migrações para Azure oferecem um hub centralizado para avaliar e migrar para o Azure os servidores, a infraestrutura, os aplicativos e os dados locais. Elas fornecem o seguinte:

- **Plataforma de migração unificada**: Um único portal para iniciar, executar e acompanhar sua migração para o Azure.
- **Variedade de ferramentas**: Uma variedade de ferramentas para avaliação e migração. As ferramentas das Migrações para Azure incluem itens como Migrações para Azure: descoberta e avaliação. Além de Migrações para Azure: ferramenta de Migração do Servidor. As Migrações para Azure também integram-se a outros serviços do Azure e a outras ferramentas, bem como com ofertas de ISVs (fornecedores independentes de software).
- **Avaliação e de migração**: no hub de Migrações para Azure, você pode avaliar e migrar:
    - **Windows, Linux e SQL Server**: avaliar servidores locais, incluindo instâncias do SQL Server e migrá-los para máquinas virtuais do Azure ou a AVS (Solução VMware no Azure) – Versão Prévia.
    - **Bancos de dados**: Avalie bancos de dados locais e migre-os para o Banco de Dados SQL do Azure ou a Instância Gerenciada de SQL.
    - **Aplicativos Web**: Avalie aplicativos Web locais e migre-os para o Serviço de Aplicativo do Azure usando o Migration Assistant do Serviço de Aplicativo do Azure.
    - **Áreas de trabalho virtual**: Avalie o VDI (Infraestrutura de Desktop Virtual) local e migre-o para a Área de Trabalho Virtual do Windows no Azure.
    - **Dados**: Migre grandes quantidades de dados para o Azure de maneira rápida e econômica usando os produtos Azure Data Box.

## <a name="integrated-tools"></a>Ferramentas integradas

O hub das Migrações para Azure inclui as seguintes ferramentas:

**Ferramenta** | **Avaliar e migrar** | **Detalhes**
--- | --- | ---
**Migrações para Azure: descoberta e avaliação** | Descobrir e avaliar servidores, incluindo o SQL | Descubra e avalie as VMs do VMware locais, as VMs do Hyper-V e os servidores físicos para se preparar para a migração para o Azure.
**Migrações para Azure: Migração de Servidor** | Migrar servidores | Migre VMs do VMware, VMs do Hyper-V, servidores físicos, outros servidores virtualizados e VMs da nuvem pública para o Azure.
**Assistente de migração de dados** | Avalie os bancos de dados do SQL Server para migração para o Banco de Dados SQL do Azure, a Instância Gerenciada de SQL do Azure ou as VMs do Azure que executam o SQL Server. | O Assistente de Migração de Dados é uma ferramenta autônoma criada para avaliar SQL Servers. Ele ajuda a identificar possíveis problemas que bloqueiam a migração. Ele identifica recursos sem suporte e novos recursos dos quais você pode se beneficiar após a migração e o caminho certo para a migração de banco de dados. [Saiba mais](/sql/dma/dma-overview).
**Serviço de Migração de Banco de Dados do Azure** | Migrar bancos de dados locais para VMs do Azure executando o SQL Server, o Banco de Dados SQL do Azure ou Instâncias Gerenciadas de SQL | [Saiba mais](../dms/dms-overview.md) sobre o Serviço de Migração de Banco de Dados.
**Movere** | Avaliar servidores | [Saiba mais](#movere) sobre o Movere.
**Migration Assistant de Aplicativo Web** | Avalie os aplicativos Web locais e migre-os para o Azure. |  Use Assistente de Migração do Serviço de Aplicativo do Azure para avaliar sites locais para migração para o Serviço de Aplicativo do Azure.<br/><br/> Use o Migration Assistant para migrar aplicativos Web .NET e PHP para o Azure. [Saiba mais](https://appmigration.microsoft.com/) sobre o Migration Assistant do Serviço de Aplicativo do Azure.
**Azure Data Box** | Migrar dados offline | Use os produtos Azure Data Box offline para mover grandes quantidades de dados offline para o Azure. [Saiba mais](../databox/index.yml).

> [!NOTE]
> Caso esteja no Azure Government, as ofertas externas de ferramentas integradas e ISVs não poderão enviar dados às Migrações para Azure. Você pode usar as ferramentas de maneira independente.

## <a name="isv-integration"></a>Integração de ISV

O recurso Migrações para Azure integra-se a várias ofertas de ISV. 

**ISV**    | **Recurso**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrar servidores.
[Cloudamize](https://www.cloudamize.com/platform) | Avaliar servidores.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Avaliar e migrar servidores.
[Device42](https://docs.device42.com/) | Avaliar servidores.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Avaliar VDI.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrar servidores.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Avaliar servidores.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Avaliar servidores e bancos de dados.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2152102) | Migrar servidores.

## <a name="azure-migrate-discovery-and-assessment-tool"></a>Migrações para Azure: ferramenta de avaliação e descoberta

Migrações para Azure: a ferramenta de avaliação e descoberta avaliava e descobre VMs do VMware e VMs do Hyper-V locais, bem como servidores físicos para executar a migração para o Azure. 

Veja o que a ferramenta faz:

- **Preparação para o Azure**: avalia se os servidores locais estão prontos para executar a migração para o Azure.
- **Dimensionamento do Azure**: estima o tamanho dos nós de VMs do Azure, da configuração do SQL do Azure e da Solução VMware no Azure após a migração.
- **Estimativa de custo do Azure**: Estima custos para a execução de computadores locais no Azure.
- **Análise de dependência**: Identifica dependências entre servidores e estratégias de otimização para mover servidores interdependentes para o Azure. Saiba mais sobre o processo de Avaliação e Descoberta usando uma [análise de dependência](concepts-dependency-visualization.md).

O processo de Avaliação e Descoberta usa um [dispositivo leve das Migrações para Azure](migrate-appliance.md) implantado localmente.

- O dispositivo é executado em uma VM ou servidor físico. Você pode instalá-lo facilmente usando um modelo baixado.
- O dispositivo descobrirá servidores locais. Ele também enviará metadados e dados de desempenho do servidor de modo contínuo às Migrações para Azure.
- A descoberta do dispositivo é sem agente. Nada será instalado nos servidores descobertos.
- Após a descoberta do dispositivo, será possível reunir os servidores descobertos em grupos e executar avaliações em cada um deles.


## <a name="azure-migrate-server-migration-tool"></a>Migrações para Azure: Ferramenta Migração de Servidor

Migrações para Azure – A ferramenta de Migração de Servidor ajuda a executar a migração de servidores para o Azure:

**Migrar** | **Detalhes**
--- | ---
VMs VMware locais | Migre VMs para o Azure usando a migração sem agente ou baseada em agente.<br/><br/> Para executar a migração sem agente, a Migração de Servidor usará o mesmo dispositivo das Migrações para Azure que também poderá ser usado pela Descoberta e Avaliação para descobrir e avaliar VMs do VMware.<br/><br/> Para a migração baseada em agente, a Migração de Servidor usa um dispositivo de replicação.
VMs do Hyper-V locais | Migre VMs para o Azure.<br/><br/> A Migração de Servidor usa agentes do provedor instalados no host Hyper-V para a migração.
Servidores físicos e locais ou servidores hospedados em outras nuvens | É possível migrar servidores físicos para o Azure. Também é possível migrar servidores virtualizados e VMs de outras nuvens públicas, tratando-os como servidores físicos para fins de migração. A Migração de Servidor usa um dispositivo de replicação para a migração.


## <a name="selecting-assessment-and-migration-tools"></a>Selecionando ferramentas de avaliação e migração

No hub das Migrações para Azure, selecione a ferramenta que você deseja usar para executar uma avaliação ou migração. Além disso, adicione a ferramenta a um projeto. Se você adicionar uma ferramenta de ISV ou o Movere:

- Para começar, obtenha uma licença ou inscreva-se para obter uma avaliação gratuita seguindo as instruções da ferramenta. Cada ISV ou ferramenta especifica o licenciamento de ferramentas.
- Cada ferramenta tem uma opção para se conectar às Migrações para Azure. Siga as instruções da ferramenta para se conectar.
- Controle a migração em todas as ferramentas de dentro do projeto.

## <a name="movere"></a>Movere

O Movere é uma plataforma de SaaS (software como serviço). Ele aumenta a business intelligence com a apresentação precisa de ambientes de TI inteiros em um único dia. Organizações e empresas crescem, mudam e otimizam digitalmente. Conforme elas fazem isso, o Movere fornece a confiança necessária para ver e controlar os ambientes, seja qual for a plataforma, o aplicativo ou a geografia.

A Microsoft [adquiriu](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) o Movere e ele não é mais vendido como uma oferta autônoma. O Movere está disponível por meio dos programas de Avaliação de Soluções Microsoft e Programa de Economia de Nuvem da Microsoft. [Saiba mais](https://www.movere.io) sobre o Movere.

Incentivamos você a examinar também as Migrações para Azure – nosso serviço de migração interno. As Migrações para Azure fornecem um hub central para simplificar sua migração na nuvem. O Hub tem suporte abrangente para cargas de trabalho como servidores físicos e virtuais, bancos de dados e aplicativos. A visibilidade de ponta a ponta permite que você acompanhe facilmente o progresso durante a descoberta, a avaliação e a migração.

Com as ferramentas do Azure e do ISV parceiro internas, as migrações para Azure têm uma ampla gama de recursos, incluindo:

- Descoberta de servidores virtuais e físicos.
- Dimensionamento correto com base no desempenho.
- Planejamento de custo.
- Avaliações baseadas em importação.
- Análise de dependência de aplicativos sem agente.

Se você estiver procurando ajuda de especialistas para começar, a Microsoft tem um [Provedor de Serviços Gerenciados Especialista no Azure](https://azure.microsoft.com/partners) para orientá-lo. Confira o [site das Migrações para Azure](https://azure.microsoft.com/services/azure-migrate/). 

## <a name="azure-migrate-versions"></a>Versões das Migrações para Azure

Há duas versões do serviço de Migrações para Azure.

- **Versão atual**: use esta versão para criar projetos, descobrir servidores locais, bem como orquestrar avaliações e migrações. [Saiba mais](whats-new.md) sobre as novidades desta versão.
- **Versão anterior**: A versão anterior das Migrações para Azure, também conhecida como Migrações para Azure (versão clássica), é compatível apenas com a avaliação de VMs VMware locais. As Migrações para Azure (versão clássica) serão desativadas em fevereiro de 2024. Depois de fevereiro de 2024, a versão clássica das Migrações para Azure não terá mais suporte, e os metadados de inventário em projetos clássicos serão excluídos. Não é possível fazer upgrade de projetos ou componentes na versão anterior para a nova versão. É preciso [criar um projeto](create-manage-projects.md) e [adicionar ferramentas de avaliação e migração](./create-manage-projects.md) a ele. Use os tutoriais para entender como usar as ferramentas de avaliação e migração disponíveis. Se você tiver um workspace do Log Analytics anexado a um projeto clássico, poderá anexá-lo a um projeto da versão atual depois de excluir o projeto clássico.

    Para acessar projetos existentes, no portal do Azure, pesquise e selecione **Migrações para Azure**. O painel **Migrações para Azure** tem uma notificação e um link para acessar projetos anteriores.

## <a name="next-steps"></a>Próximas etapas

- Experimente nossos tutoriais para avaliar [VMs do VMware](./tutorial-discover-vmware.md), [VMs do Hyper-V](./tutorial-discover-hyper-v.md) ou [servidores físicos](./tutorial-discover-physical.md).
- [Reveja as perguntas frequentes](resources-faq.md) sobre Migrações para Azure.