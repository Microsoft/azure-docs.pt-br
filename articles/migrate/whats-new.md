---
title: Novidades nas Migrações para Azure | Microsoft Docs
description: Fornece uma visão geral do serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 06/10/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2c3bc596076f3ec4f9d41f0da819ddd386fee63c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811040"
---
# <a name="whats-new-in-azure-migrate"></a>Novidades nas Migrações para Azure

As [Migrações para Azure](migrate-services-overview.md) ajudam a descobrir, avaliar e migrar servidores, aplicativos e dados para a nuvem do Microsoft Azure. Este artigo resume os novos recursos nas Migrações para Azure.



## <a name="azure-migrate-new-version"></a>Nova versão das Migrações para Azure

Uma nova versão das Migrações para Azure foi lançada em julho de 2019. 

- **Versão atual (nova)** : use esta versão para criar projetos de Migrações para Azure, descobrir computadores locais e orquestrar avaliações e migrações. 
- **Versão anterior**: para o cliente que usa a versão anterior das Migrações para Azure (havia suporte apenas a avaliação de VMs do VMware locais), agora você deve usar a versão atual. Na versão anterior, você não pode mais criar projetos das Migrações para Azure ou executar novas descobertas. Você ainda pode acessar projetos existentes. Para fazer isso no portal do Azure > Todos os serviços, pesquise Migrações para Azure. Nas notificações das Migrações para Azure, há um link para acessar projetos antigos das Migrações para Azure.


## <a name="azure-migrate-features"></a>Recursos das Migrações para Azure

A nova versão das Migrações para Azure oferece inúmeros recursos novos:


- **Plataforma de migração unificada**: as Migrações para Azure agora fornecem um único portal para centralizar, gerenciar e acompanhar sua jornada de migração para o Azure, com um fluxo de implantação e uma experiência de portal aprimorados.
- **Ferramentas de avaliação e de migração**: as Migrações para Azure fornecem ferramentas nativas e se integram a outros serviços do Azure, bem como a ferramentas de ISV (fornecedor independente de software). [Saiba mais](migrate-services-overview.md#isv-integration) sobre a integração com ISV.
- **Avaliação das Migrações para Azure**: usando a ferramenta de Avaliação de Servidor das Migrações para Azure, é possível avaliar VMs do VMware e Hyper-V quanto à migração para o Azure. Também é possível avaliar quanto à migração usando outros serviços do Azure e ferramentas ISV.
- **Migração das Migrações para Azure**: usando a ferramenta de Migração de Servidor das Migrações para Azure, é possível migrar VMs do VMware e Hyper-V locais para o Azure, além de servidores físicos, outros servidores virtualizados e VMs de nuvem pública/privada. Além disso, é possível migrar para o Azure usando as ferramentas de ISV.
- **Dispositivo de Migrações para Azure**: as Migrações para Azure implantam um dispositivo leve para descoberta e avaliação de VMs do VMware e Hyper-V locais.
    - Esse dispositivo é usado pela Avaliação de Servidor das Migrações para Azure e pela Migração de Servidor das Migrações para Azure para uma migração sem agente.
    - O dispositivo descobre continuamente os metadados do servidor e os dados de desempenho para fins de avaliação e migração.  
- **Migração da VM do VMware**:  A Migração de Servidor das Migrações para Azure oferecem alguns métodos para migrar VMs do VMware locais para o Azure.  Uma migração sem agente que usa o dispositivo de Migrações para Azure e uma migração baseada em agente que usa um dispositivo de replicação e implanta um agente em cada VM que você deseja migrar. [Saiba mais](server-migrate-overview.md)
 - **Avaliação e migração do banco de dados**: nas Migrações para Azure, é possível avaliar bancos de dados locais quanto à migração para o Azure usando o Assistente de Migração de Dados do Azure. É possível migrar bancos de dados usando o Serviço de Migração de Banco de Dados do Azure.
- **Migração de aplicativos Web**: é possível avaliar os aplicativos Web usando uma URL de ponto de extremidade pública com o Serviço de Aplicativo do Azure. Para a migração de aplicativos .NET internos, é possível baixar e executar o Assistente de Migração do Serviço de Aplicativo. 
- **Data Box**: importe grandes quantidades de dados offline para o Azure usando o Azure Data Box nas Migrações para Azure.


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](https://azure.microsoft.com/pricing/details/azure-migrate/) sobre os preços das Migrações para Azure.
- [Reveja as perguntas frequentes](resources-faq.md) sobre Migrações para Azure.
- Experimente nossos tutoriais para avaliar [VMs VMware](tutorial-assess-vmware.md) e [VMs Hyper-V](tutorial-assess-hyper-v.md).
