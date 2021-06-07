---
title: Avalie grandes números de servidores no ambiente VMware para migração para o Azure com as migrações para Azure
description: Descreve como avaliar um grande número de servidores no ambiente VMware para migração para o Azure usando o serviço migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 10b8aaeaa25e49140dbf6f31c064c7f823d23e31
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778246"
---
# <a name="assess-large-numbers-of-servers-in-vmware-environment-for-migration-to-azure"></a>Avalie grandes números de servidores no ambiente VMware para migração para o Azure


Este artigo descreve como avaliar números grandes (35000) de servidores locais no ambiente VMware para migração para o Azure, usando a ferramenta de avaliação e descoberta de migrações para Azure.

As [Migrações para Azure](migrate-services-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros. 

Neste artigo, você aprenderá como:
> [!div class="checklist"]
> * Planeje a avaliação em escala.
> * Configure as permissões do Azure e prepare o VMware para avaliação.
> * Crie um projeto de migrações para Azure e crie uma avaliação.
> * Examine a avaliação conforme planeja a migração.


> [!NOTE]
> Se você quiser experimentar uma prova de conceito para avaliar alguns servidores antes de avaliar em escala, siga nossa [série de tutoriais](./tutorial-discover-vmware.md)

## <a name="plan-for-assessment"></a>Planejar a avaliação

Ao planejar a avaliação de um grande número de servidores no ambiente VMware, há algumas coisas a considerar:

- **Planejar projetos de migrações para Azure**: Descubra como implantar projetos de migrações para Azure. Por exemplo, se seus data centers estiverem em geografias diferentes, ou se você precisar armazenar metadados relacionados à migração, à avaliação ou ao migrar em uma geografia diferente, talvez precise de vários projetos. 
- **Dispositivos de plano**: as migrações para Azure usam um dispositivo de migrações do Azure local, implantado como uma VM VMware, para descobrir continuamente os servidores. O dispositivo monitora as alterações de ambiente, como adicionar servidores, discos ou adaptadores de rede. Ele também envia metadados e dados de desempenho sobre eles para o Azure. Você precisa descobrir quantos dispositivos você precisa implantar.
- **Planejar contas para descoberta**: o dispositivo de migrações para Azure usa uma conta com acesso a vCenter Server para descobrir servidores para avaliação e migração. Se você estiver descobrindo mais de 10.000 servidores, configure várias contas, pois elas são necessárias não há nenhuma sobreposição entre os servidores descobertos de dois dispositivos em um projeto. 

> [!NOTE]
> Se você estiver configurando vários dispositivos, certifique-se de que não haja nenhuma sobreposição entre os servidores nas contas do vCenter fornecidas. Uma descoberta com essa sobreposição é um cenário sem suporte. Se um servidor for descoberto por mais de um dispositivo, isso resultará em duplicatas na descoberta e em problemas, permitindo, ao mesmo tempo, a replicação para o servidor usando o portal do Azure na migração de servidor.

## <a name="planning-limits"></a>Limites de planejamento
 
Use os limites resumidos nesta tabela para planejamento.

**Planejamento** | **Limites**
--- | --- 
**Projetos das Migrações para Azure** | Avalie até 35.000 servidores em um projeto.
**Dispositivo de Migrações para Azure** | Um dispositivo pode descobrir até 10.000 servidores em um vCenter Server.<br/> Um dispositivo só pode se conectar a um único vCenter Server.<br/> Um dispositivo só pode ser associado a um único projeto de migrações para Azure.<br/>  Qualquer número de dispositivos pode ser associado a um único projeto de migrações para Azure. <br/><br/> 
**Grupo** | Você pode adicionar até 35.000 servidores em um único grupo.
**Avaliação de migrações para Azure** | Você pode avaliar até 35.000 servidores em uma única avaliação.

Com esses limites em mente, aqui estão alguns exemplos de implantações:


**servidor vCenter** | **Servidores no servidor** | **Recomendação** | **Ação**
---|---|---|---
Um | < 10.000 | Um projeto de migrações para Azure.<br/> Um dispositivo.<br/> Uma conta do vCenter para descoberta. | Configure o dispositivo, conecte-se a vCenter Server com uma conta.
Um | > 10.000 | Um projeto de migrações para Azure.<br/> Vários dispositivos.<br/> Várias contas do vCenter. | Configure o dispositivo para cada 10.000 servidores.<br/><br/> Configure as contas do vCenter e divida o inventário para limitar o acesso de uma conta a menos de 10.000 servidores.<br/> Conecte cada dispositivo ao vCenter Server com uma conta.<br/> Você pode analisar dependências entre servidores que são descobertos com dispositivos diferentes. <br/> <br/> Verifique se não há nenhuma sobreposição entre os servidores nas contas do vCenter fornecidas. Uma descoberta com essa sobreposição é um cenário sem suporte. Se um servidor for descoberto por mais de um dispositivo, isso resultará em duplicatas na descoberta e em problemas, permitindo, ao mesmo tempo, a replicação para o servidor usando o portal do Azure na migração de servidor.
Vários | < 10.000 |  Um projeto de migrações para Azure.<br/> Vários dispositivos.<br/> Uma conta do vCenter para descoberta. | Configurar dispositivos, conecte-se a vCenter Server com uma conta.<br/> Você pode analisar dependências entre servidores que são descobertos com dispositivos diferentes.
Vários | > 10.000 | Um projeto de migrações para Azure.<br/> Vários dispositivos.<br/> Várias contas do vCenter. | Se vCenter Server descoberta < servidores 10.000, configure um dispositivo para cada vCenter Server.<br/><br/> Se vCenter Server descoberta > servidores 10.000, configure um dispositivo para todos os servidores 10.000.<br/> Configure as contas do vCenter e divida o inventário para limitar o acesso de uma conta a menos de 10.000 servidores.<br/> Conecte cada dispositivo ao vCenter Server com uma conta.<br/> Você pode analisar dependências entre servidores que são descobertos com dispositivos diferentes. <br/><br/> Verifique se não há nenhuma sobreposição entre os servidores nas contas do vCenter fornecidas. Uma descoberta com essa sobreposição é um cenário sem suporte. Se um servidor for descoberto por mais de um dispositivo, isso resultará em duplicatas na descoberta e em problemas, permitindo, ao mesmo tempo, a replicação para o servidor usando o portal do Azure na migração de servidor.



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Planejar a descoberta em um ambiente multilocatário

Se você estiver planejando um ambiente multilocatário, poderá fazer o escopo da descoberta no vCenter Server.

- Você pode definir o escopo de descoberta do dispositivo para um vCenter Server data centers, clusters ou pasta de clusters, hosts ou pasta de hosts ou servidores individuais.
- Se o seu ambiente for compartilhado entre locatários e você quiser descobrir cada locatário separadamente, você poderá usar o escopo de acesso à conta do vCenter que o dispositivo usa para descoberta. 
    - Talvez você queira fazer o escopo por pastas de VM se os locatários compartilharem hosts. As migrações para Azure não poderão descobrir servidores se a conta do vCenter tiver acesso concedido no nível da pasta da VM do vCenter. Se você pretende fazer o escopo de sua descoberta por pastas de VM, você pode fazê-lo, garantindo que a conta do vCenter tenha acesso somente leitura atribuído em um nível de servidor. [Saiba mais](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Preparar-se para avaliação

Prepare o Azure e o VMware para a ferramenta de descoberta e avaliação:

1. Verifique [os requisitos de suporte e as limitações do VMware](migrate-support-matrix-vmware.md).
2. Configure as permissões para sua conta do Azure para interagir com as migrações para Azure.
3. Prepare o VMware para avaliação.

Siga as instruções neste [tutorial](./tutorial-discover-vmware.md) para definir essas configurações.


## <a name="create-a-project"></a>Criar um projeto

De acordo com seus requisitos de planejamento, faça o seguinte:

1. Criar projetos de migrações para Azure.
2. Adicione a ferramenta de avaliação e descoberta de migrações para projetos do Azure.

[Saiba mais](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Criar e examinar uma avaliação

1. Crie Avaliações para servidores no ambiente VMware.
1. Examine as avaliações em preparação para o planejamento de migração.


Siga as instruções neste [tutorial](./tutorial-assess-vmware-azure-vm.md) para definir essas configurações.
    

## <a name="next-steps"></a>Próximas etapas

Neste artigo você:
 
> [!div class="checklist"] 
> * Planejado para dimensionar as avaliações de migração do Azure para servidores no ambiente VMware
> * Azure e VMware preparados para avaliação
> * Criou um projeto de migrações para Azure e executou avaliações
> * Avaliações revisadas em preparação para a migração.

Agora, [saiba como](concepts-assessment-calculation.md) as avaliações são calculadas e como [modificar as avaliações](how-to-modify-assessment.md).