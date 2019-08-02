---
title: Avalie grandes números de VMs VMware para migração para o Azure com migrações para Azure | Microsoft Docs
description: Descreve como avaliar grandes números de VMs VMware para migração para o Azure usando o serviço migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/12/2019
ms.author: hamusa
ms.openlocfilehash: c9c57a07100f2ea6db86408826bf74d05c8df5aa
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868674"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Avalie grandes números de VMs VMware para migração para o Azure


Este artigo descreve como avaliar números grandes (35000) de VMs VMware locais para migração para o Azure, usando a ferramenta de avaliação de servidor migrações para Azure

As migrações para [Azure](migrate-services-overview.md) fornecem um hub de ferramentas que ajudam a descobrir, avaliar e migrar aplicativos, infraestrutura e cargas de trabalho para Microsoft Azure. O Hub inclui ferramentas de migração do Azure e ofertas de fornecedores independentes de software (ISV) de terceiros. 

Neste artigo, você aprenderá a:
> [!div class="checklist"]
> * Planeje a avaliação em escala.
> * Configure as permissões do Azure e prepare o VMware para avaliação.
> * Crie um projeto de migrações para Azure e crie uma avaliação.
> * Examine a avaliação conforme planeja a migração.


> [!NOTE]
> Se você quiser experimentar uma prova de conceito para avaliar algumas VMs antes de avaliar em escala, siga nossa [série de tutoriais](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Planejar a avaliação

Ao planejar a avaliação de um grande número de VMs VMware, há algumas coisas a considerar:

- **Planejar projetos**de migrações para Azure: Descubra como implantar projetos de migrações para Azure. Por exemplo, se seus data centers estiverem em geografias diferentes, ou se você precisar armazenar metadados relacionados à migração, à avaliação ou ao migrar em uma geografia diferente, talvez precise de vários projetos. 
- **Dispositivos de plano**: As migrações para Azure usam um dispositivo de migrações do Azure local, implantado como uma VM do VMware, para descobrir continuamente as VMs. O dispositivo monitora as alterações de ambiente, como adicionar VMs, discos ou adaptadores de rede. Ele também envia metadados e dados de desempenho sobre eles para o Azure. Você precisa descobrir quantos dispositivos você precisa implantar.
- **Planejar contas para descoberta**: O dispositivo de migrações para Azure usa uma conta com acesso a vCenter Server para descobrir VMs para avaliação e migração. Se você estiver descobrindo mais de 10.000 VMs, configure várias contas.


## <a name="planning-limits"></a>Limites de planejamento
 
Use os limites resumidos nesta tabela para planejamento.

**Planejamento** | **Limites**
--- | --- 
**Projetos de migrações para Azure** | Avalie até 35.000 VMs em um projeto.
**Dispositivo de migrações para Azure** | Um dispositivo só pode se conectar a um único vCenter Server.<br/><br/> Um dispositivo só pode ser associado a um único projeto de migrações para Azure.<br/> Um dispositivo pode descobrir até 10.000 VMs em um vCenter Server.
**Avaliação de migrações para Azure** | Você pode avaliar até 35.000 VMs em uma única avaliação.

Com esses limites em mente, aqui estão alguns exemplos de implantações:


**vCenter Server** | **VMs no servidor** | **Recomendações** | **Ação**
---|---|---
Um | < 10.000 | Um projeto de migrações para Azure.<br/> Um dispositivo.<br/> Uma conta do vCenter para descoberta. | Configure o dispositivo, conecte-se a vCenter Server com uma conta.
Um | > 10.000 | Um projeto de migrações para Azure.<br/> Vários dispositivos.<br/> Várias contas do vCenter. | Configure o dispositivo para cada 10.000 VMs.<br/><br/> Configure as contas do vCenter e divida o inventário para limitar o acesso de uma conta a menos de 10.000 VMs.<br/> Conecte cada dispositivo ao vCenter Server com uma conta.<br/> Você pode analisar dependências entre computadores que são descobertos com dispositivos diferentes.
Vários | < 10.000 |  Um projeto de migrações para Azure.<br/> Vários dispositivos.<br/> Uma conta do vCenter para descoberta. | Configurar dispositivos, conecte-se a vCenter Server com uma conta.<br/> Você pode analisar dependências entre computadores que são descobertos com dispositivos diferentes.
Vários | > 10.000 | Um projeto de migrações para Azure.<br/> Vários dispositivos.<br/> Várias contas do vCenter. | Se vCenter Server descoberta < VMs 10.000, configure um dispositivo para cada vCenter Server.<br/><br/> Se vCenter Server descoberta > VMs 10.000, configure um dispositivo para cada 10.000 VMs.<br/> Configure as contas do vCenter e divida o inventário para limitar o acesso de uma conta a menos de 10.000 VMs.<br/> Conecte cada dispositivo ao vCenter Server com uma conta.<br/> Você pode analisar dependências entre computadores que são descobertos com dispositivos diferentes.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Planejar a descoberta em um ambiente multilocatário

Se você estiver planejando um ambiente multilocatário, poderá fazer o escopo da descoberta no vCenter Server.

- Você pode definir o escopo de descoberta do dispositivo para um vCenter Server data centers, clusters ou pasta de clusters, hosts ou pasta de hosts ou VMs individuais.
- Se o seu ambiente for compartilhado entre locatários e você quiser descobrir cada locatário separadamente, você poderá usar o escopo de acesso à conta do vCenter que o dispositivo usa para descoberta. 
    - Talvez você queira fazer o escopo por pastas de VM se os locatários compartilharem hosts. As migrações para Azure não poderão descobrir VMs se a conta do vCenter tiver acesso concedido no nível da pasta da VM do vCenter. Se você pretende fazer o escopo de sua descoberta por pastas de VM, você pode fazê-lo, garantindo que a conta do vCenter tenha acesso somente leitura atribuído em um nível de VM. Saiba mais sobre a descoberta de escopo [aqui](tutorial-assess-vmware.md#scoping-discovery).

## <a name="prepare-for-assessment"></a>Preparar para avaliação

Prepare a avaliação do Azure e do VMware para o servidor. 

1. Verifique [os requisitos de suporte e as limitações do VMware](migrate-support-matrix-vmware.md).
2. Configure as permissões para sua conta do Azure para interagir com as migrações para Azure.
3. Prepare o VMware para avaliação.

Siga as instruções neste [tutorial](tutorial-prepare-vmware.md) para definir essas configurações.


## <a name="create-a-project"></a>Criar um projeto

De acordo com seus requisitos de planejamento, faça o seguinte:

1. Criar projetos de migrações para Azure.
2. Adicione a ferramenta de avaliação do servidor de migrações para projetos.

[Saiba mais](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Criar e examinar uma avaliação

1. Crie Avaliações para VMs VMware.
1. Examine as avaliações em preparação para o planejamento de migração.


Siga as instruções neste [tutorial](tutorial-assess-vmware.md) para definir essas configurações.
    

## <a name="next-steps"></a>Próximas etapas

Neste artigo você:
 
> [!div class="checklist"] 
> * Planejado para dimensionar as avaliações de migração do Azure para VMs VMware
> * Azure e VMware preparados para avaliação
> * Criou um projeto de migrações para Azure e executou avaliações
> * Avaliações revisadas em preparação para a migração.

Agora, [saiba como](concepts-assessment-calculation.md) as avaliações são calculadas e como [modificar as avaliações](how-to-modify-assessment.md).
