---
title: Sobre Migrações para Azure | Microsoft Docs
description: Fornece uma visão geral do serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5409ed799454a6bb64077ee884065fc518556142
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227819"
---
# <a name="about-azure-migrate"></a>Sobre as Migrações para Azure

Este artigo fornece uma visão geral rápida das Migrações para Azure.

As Migrações para Azure ajudam você a migrar para o Azure. As Migrações para Azure oferecem um hub centralizado para acompanhar a descoberta, a avaliação e a migração de infraestrutura, aplicativos e dados locais para o Azure. O hub fornece ferramentas do Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros. Ela oferece:

- **Plataforma de migração unificada**: Use um único portal para iniciar, executar e acompanhar sua jornada de migração para o Azure.
- **Variedade de ferramentas**: As Migrações para Azure fornecem ferramentas nativas e se integram a outros serviços do Azure, bem como a ferramentas de ISV. Selecione as ferramentas de avaliação e migração certas com base em seus requisitos organizacionais. 
- **Avaliação de Servidor das Migrações para Azure**: Use a ferramenta de Avaliação de Servidor das Migrações para Azure para avaliar VMs do VMware e Hyper-V locais para migração para o Azure.
- **Migração de Servidor das Migrações para Azure**: Use a ferramenta de Migração de Servidor para migrar VMs do VMware e Hyper-V locais, VMs de nuvem e servidores físicos para o Azure.
- **Avaliação de banco de dados das Migrações para Azure**: Avalie bancos de dados locais para migração para o Azure.
- **Migração de banco de dados das Migrações para Azure**: Migre bancos de dados locais para o Azure.


## <a name="azure-migrate-versions"></a>Versões das Migrações para Azure

Há duas versões do serviço de Migrações para Azure:

- **Versão atual**: use esta versão para criar projetos de Migrações para Azure, descobrir computadores locais e orquestrar avaliações e migrações. [Saiba mais](whats-new.md) sobre as novidades desta versão.
- **Versão anterior**: se você estava usando a versão anterior das Migrações para Azure (havia suporte apenas a avaliação de VMs do VMware locais), agora você deve usar a versão atual. Não é mais possível criar projetos das Migrações para Azure usando a versão anterior e recomendamos que você não realize novas descobertas. Para acessar projetos existentes, no portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**. No painel das Migrações para Azure, há uma notificação e um link para acessar projetos antigos das Migrações para Azure.

## <a name="isv-integration"></a>Integração de ISV

Além das ferramentas nativas do Azure, as Migrações para Azure integram-se com várias ofertas de ISV. Você identifica a ferramenta de que precisa e a adiciona a um projeto de Migrações para Azure. Você pode controlar centralmente sua jornada de migração de dentro do projeto de Migrações para Azure, entre ferramentas do Azure e do ISV.

**ISV** | **Recurso**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Avaliar
[Device 42](https://docs.device42.com/) | Avaliar
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Avaliar
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Avaliar
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Avaliar e migrar
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrar

### <a name="selecting-an-isv-tool"></a>Como selecionar uma ferramenta ISV

Depois de adicionar uma ferramenta ISV a um projeto de Migrações para Azure, comece a usar a ferramenta obtendo uma licença ou se inscrevendo para uma avaliação gratuita, de acordo com a política de ISV. Em cada ferramenta, há uma opção para se conectar às Migrações para Azure. Siga as instruções e a documentação da ferramenta para conectar a ferramenta às Migrações para Azure.

## <a name="azure-migrate-server-assessment"></a>Avaliação de Servidor das Migrações para Azure

A Avaliação de Servidor das Migrações para Azure descobre e avalia VMs do VMware e Hyper-V locais para migração para o Azure. Ela ajuda a identificar o seguinte:

- **Preparação para o Azure:** Avalie se os computadores locais estão prontos para a migração para o Azure.
- **Dimensionamento do Azure:** Estime o tamanho das VMs do Azure após a migração.
- **Estimativa de custo do Azure:** Estima custos para a execução de computadores locais no Azure.
- **Visualização de dependências:** Identifique dependências entre servidores e a melhor maneira de mover servidores dependentes para o Azure. 

A Avaliação do Servidor usa um dispositivo leve que você implanta localmente e registra na Avaliação do Servidor.

- O dispositivo descobre computadores locais, conecta-se à Avaliação do Servidor e envia continuamente metadados e dados relacionados a desempenho para as Migrações para Azure.
- A descoberta é sem agente. Nada precisa ser instalado nas VMs descobertas.
- Depois que os computadores são descobertos, você os reúne em grupos que normalmente consistem em VMs que você gostaria de migrar juntas.
- Crie uma avaliação para um grupo. Em seguida, você pode analisar a avaliação para descobrir sua estratégia de migração.

## <a name="azure-migrate-server-migration"></a>Migração de Servidor das Migrações para Azure

A Migração de Servidor das Migrações para Azure ajuda você a migrar VMs do VMware e Hyper-V locais, servidores físicos, outros computadores virtualizados e VMs de nuvem pública para o Azure. Você pode migrar computadores depois de avaliá-los ou sem uma avaliação. 

## <a name="azure-migrate-database-assessment"></a>Avaliação de banco de dados das Migrações para Azure

As Migrações para Azure integram-se com o AMD (Assistente de Migração de Dados) para avaliar bancos de dados do SQL Server locais para migração para BD SQL do Azure, Instância Gerenciada do SQL do Azure ou VMs do Azure em execução no SQL Server. O AMD fornece informações sobre possíveis problemas de bloqueio para a migração. Ele identifica recursos não compatíveis e também novos recursos dos quais você pode se beneficiar após a migração, além de ajudar a identificar o caminho certo para a migração de banco de dados. [Saiba mais](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).


## <a name="azure-migrate-database-migration"></a>Migração de banco de dados das Migrações para Azure

As Migrações para Azure integram-se ao DMS (Serviço de Migração de Banco de Dados do Azure) para migrar bancos de dados locais para o Azure. Use o DMS para migrar bancos de dados locais para VMs do Azure executando SQL, BD SQL do Azure e Instâncias Gerenciadas do SQL do Azure. [Saiba mais](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-assessment-and-migration"></a>Avaliação e migração de aplicativos Web

No hub de Migrações para Azure, você pode avaliar e migrar aplicativos Web locais para o Azure.

- **Avaliar aplicativos Web Online**: Use Assistente de Migração do Serviço de Aplicativo do Azure para avaliar sites locais para migração para o Serviço de Aplicativo do Azure.
- **Migrar aplicativos Web**: Migre aplicativos Web .NET e PHP para o Azure usando o Assistente de Migração do Serviço de Aplicativo do Azure.

[Saiba mais.](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>Migração de dados offline

Você pode usar a família de produtos Data Box offline para mover grandes quantidades de dados para o Azure. [Saiba mais](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Próximas etapas

- Experimente nossos tutoriais para avaliar [VMs VMware](tutorial-assess-vmware.md) e [VMs Hyper-V](tutorial-assess-hyper-v.md).
- [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços das Migrações para Azure.
- [Reveja as perguntas frequentes](resources-faq.md) sobre Migrações para Azure.
