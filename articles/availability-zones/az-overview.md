---
title: Regiões e Zonas de Disponibilidade no Azure
description: Saiba mais sobre regiões e Zonas de Disponibilidade no Azure para atender aos seus requisitos técnicos e regulatórios.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: dad8661de55fc90c9f3d3782c402deb519d16536
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596001"
---
# <a name="regions-and-availability-zones-in-azure"></a>Regiões e Zonas de Disponibilidade no Azure

Os serviços de Microsoft Azure estão disponíveis globalmente para orientar suas operações de nuvem em um nível ideal. Você pode escolher a melhor região para suas necessidades com base nas considerações técnicas e regulatórias: recursos de serviço, residência de dados, requisitos de conformidade e latência.

## <a name="terminology"></a>Terminologia

Para entender melhor as regiões e Zonas de Disponibilidade no Azure, é útil entender os principais termos ou conceitos.

| Termo ou conceito | Descrição |
| --- | --- |
| region | Um conjunto de data centers implantado em um perímetro definido por latência e conectado por meio de uma rede regional de baixa latência. |
| geografia | Uma área do mundo que contém pelo menos uma região do Azure. As regiões geográficas definem um mercado discreto que preserva os limites de conformidade e residência de dados. As geografias permitem que os clientes com necessidades de conformidade e de residência de dados específicas mantenham seus dados e aplicativos próximos. As geografias são tolerantes a falhas para resistir à falha de região completa por meio de sua conexão à nossa infraestrutura de rede de alta capacidade dedicada. |
| Zona de disponibilidade | Locais físicos exclusivos em uma região. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. |
| região recomendada | Uma região que fornece a mais ampla variedade de recursos de serviço e foi projetada para dar suporte a Zonas de Disponibilidade agora ou no futuro. Elas são designadas na portal do Azure conforme **recomendado**. |
| região alternativa (outra) | Uma região que estende a superfície do Azure dentro de um limite de residência de dados em que também existe uma região recomendada. As regiões alternativas ajudam a otimizar a latência e a fornecer uma segunda região para as necessidades de recuperação de desastre. Eles não são projetados para dar suporte a Zonas de Disponibilidade (embora o Azure realize a avaliação regular dessas regiões para determinar se elas devem se tornar regiões recomendadas). Elas são designadas na portal do Azure como **outras**. |
| serviço básico | Um serviço principal do Azure que está disponível em todas as regiões quando a região está geralmente disponível. |
| serviço principal | Um serviço do Azure que está disponível em todas as regiões recomendadas dentro de 90 dias da disponibilidade geral da região ou da disponibilidade orientada por demanda em regiões alternativas. |
| serviço especializado | Um serviço do Azure que é a disponibilidade orientada por demanda entre regiões apoiadas por hardware personalizado/especializado. |
| serviço regional | Um serviço do Azure que é implantado regionalmente e permite que o cliente especifique a região na qual o serviço será implantado. Para obter uma lista completa, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| serviço não regional | Um serviço do Azure para o qual não há nenhuma dependência em uma região específica do Azure. Os serviços não regionais são implantados em duas ou mais regiões e, se houver uma falha regional, a instância do serviço em outra região continuará atendendo aos clientes. Para obter uma lista completa, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Regiões

Uma região é um conjunto de data centers implantados em um perímetro definido por latência e conectados por meio de uma rede regional de baixa latência. O Azure oferece a você a flexibilidade de implantar aplicativos onde você precisa, incluindo em várias regiões para fornecer resiliência entre regiões. Para obter mais informações, consulte [visão geral do pilar de resiliência](/azure/architecture/framework/resiliency/overview).

## <a name="availability-zones"></a>Zonas de Disponibilidades

Uma Zona de Disponibilidade é uma oferta de alta disponibilidade que protege aplicativos e dados contra falhas de datacenter. As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física das Zonas de Disponibilidade dentro de uma região protege os aplicativos e dados contra falhas do datacenter. Serviços com redundância de zona replicam os aplicativos e dados entre Zonas de Disponibilidade para proteger dos pontos únicos de falha. Com Zonas de Disponibilidade, o Azure oferece o melhor SLA de tempo de atividade da VM de 99,99% do setor. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explica a disponibilidade garantida do Azure como um todo.

Uma Zona de Disponibilidade em uma região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se você criar três ou mais VMs em três zonas em uma região do Azure, as VMs serão efetivamente distribuídas em três domínios de falha e três domínios de atualização. A plataforma Azure reconhece essa distribuição entre domínios de atualização para garantir que as VMs em diferentes zonas não estejam agendadas para serem atualizadas ao mesmo tempo.

Compila alta disponibilidade na arquitetura do aplicativo, colocalizando os recursos de computação, armazenamento, rede e dados em uma zona e replicando em outras zonas. Os serviços do Azure que dão suporte a Zonas de Disponibilidade enquadram-se em duas categorias:

- **Serviços zonais** – onde um recurso é fixado em uma zona específica (por exemplo, máquinas virtuais, discos gerenciados, endereços IP padrão) ou
- **Serviços com redundância de zona** – quando a plataforma do Azure é replicada automaticamente entre zonas (por exemplo, armazenamento com redundância de zona, banco de dados SQL).

Para obter uma continuidade de negócios abrangente no Azure, compile a arquitetura do aplicativo usando a combinação de Zonas de Disponibilidade com pares de regiões do Azure. É possível replicar os aplicativos e dados de maneira síncrona usando Zonas de Disponibilidade em uma região do Azure para alta disponibilidade e replicação assíncrona em regiões do Azure para proteção de recuperação de desastre.
 
![exibição conceitual de uma zona reduzindo-se a uma região](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Os identificadores de zona de disponibilidade (os números 1, 2 e 3 na imagem acima) são mapeados logicamente para as zonas físicas reais para cada assinatura de forma independente. Isso significa que a disponibilidade Zona 1 em uma determinada assinatura pode se referir a uma zona física diferente da Zona 1 de disponibilidade em uma assinatura diferente. Como consequência, é recomendável não confiar nas IDs de zona de disponibilidade em assinaturas diferentes para o posicionamento da máquina virtual.

## <a name="region-and-service-categories"></a>Categorias de região e serviço

A abordagem do Azure sobre a disponibilidade de serviços do Azure entre regiões é melhor descrita pela expressa de serviços disponibilizados em regiões recomendadas e regiões alternativas.

- **Região recomendada** -uma região que fornece a mais ampla variedade de recursos de serviço e é projetada para dar suporte a zonas de disponibilidade agora ou no futuro. Elas são designadas na portal do Azure conforme **recomendado**.
- **Região alternativa (outro)** – uma região que estende a superfície do Azure dentro de um limite de residência de dados em que também existe uma região recomendada. As regiões alternativas ajudam a otimizar a latência e a fornecer uma segunda região para as necessidades de recuperação de desastre. Eles não são projetados para dar suporte a Zonas de Disponibilidade (embora o Azure realize a avaliação regular dessas regiões para determinar se elas devem se tornar regiões recomendadas). Elas são designadas na portal do Azure como **outras**.

### <a name="comparing-region-types"></a>Comparando tipos de região

Os serviços do Azure são agrupados em três categorias: serviços básicos, básicos e especializados. A política geral do Azure sobre a implantação de serviços em qualquer região específica é controlada principalmente por tipo de região, categorias de serviço e demanda do cliente:

- **Fundamental** – disponível em todas as regiões recomendadas e alternativas quando a região está disponível para o público geral ou dentro de 90 dias de um novo serviço básico ficando disponível para o público geral.
- **Base** – disponível em todas as regiões recomendadas dentro de 90 dias da disponibilidade geral da região; orientado por demanda em regiões alternativas (muitas já estão implantadas em um grande subconjunto de regiões alternativas).
- Ofertas de serviços direcionadas **especializadas** , geralmente focadas no setor ou com suporte por hardware personalizado/especializado. Disponibilidade orientada por demanda entre regiões (muitas já estão implantadas em um grande subconjunto de regiões recomendadas).

Para ver quais serviços são implantados em uma determinada região, bem como o roteiro futuro para visualização ou disponibilidade geral dos serviços em uma região, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all).

Se uma oferta de serviço não estiver disponível em uma região específica, você poderá compartilhar seu interesse entrando em contato com seu representante de vendas da Microsoft.

| Tipo de região | Não regional | Foundational | Base | Especializada | Zonas de Disponibilidades | Residência de dadosResidência de dados |
| --- | --- | --- | --- | --- | --- | --- |
| Recomendadas | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Controlado por demanda | :heavy_check_mark: | :heavy_check_mark: |
| Alternativo | :heavy_check_mark: | :heavy_check_mark: | Controlado por demanda | Controlado por demanda | N/D | :heavy_check_mark: |

### <a name="services-by-category"></a>Serviços por categoria

Conforme mencionado anteriormente, o Azure classifica os serviços em três categorias: fundamental, predominante e especializado. As categorias de serviço são atribuídas em disponibilidade geral. Geralmente, os serviços iniciam seu ciclo de vida como um serviço especializado e, à medida que a demanda e a utilização aumentam, podem ser promovidas ao básico ou básico. A tabela a seguir lista a categoria de serviços como Fundação, o básico. Você deve observar o seguinte sobre a tabela:

- Alguns serviços não são regionais. Para obter informações e uma lista de serviços não regionais, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/).
- A geração mais antiga de serviços ou máquinas virtuais não está listada. Para obter mais informações, consulte a documentação em [gerações anteriores de tamanhos de máquina virtual](../virtual-machines/sizes-previous-gen.md)
- . Os serviços não são atribuídos a uma categoria até a disponibilidade geral (GA). Para obter informações e uma lista de serviços de visualização, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). 

> [!div class="mx-tableFixed"]
> | Foundational                           | Base                                        | 
> |----------------------------------------|---------------------------------------------------|
> | Contas de Armazenamento                       | Gerenciamento de API                                    | 
> | Gateway de Aplicativo                    | Configuração de Aplicativos                                 | 
> | Serviço de Backup do Azure                           | Serviço de Aplicativo                                       | 
> | Azure Cosmos DB                        | Automação                                        | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory Domain Services            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | IP público do Azure                        | Cache Redis do Azure                             | 
> | Banco de Dados SQL do Azure                     | Pesquisa Cognitiva do Azure                            | 
> | Instância Gerenciada do Azure SQL             | Serviços Cognitivos do Azure                          | 
> | Armazenamento em Disco                           | Serviços Cognitivos do Azure: Pesquisa Visual Computacional         | 
> | Hubs de Eventos                             | Serviços cognitivas do Azure: Content Moderator       | 
> | Key Vault                              | Serviços cognitivas do Azure: rosto                    | 
> | Balanceador de carga                          | Serviços cognitivas do Azure: leitor de imersão        | 
> | Barramento de Serviço                            | Serviços cognitivas do Azure: Reconhecimento vocal  | 
> | Service Fabric                         | Serviços cognitivas do Azure: serviços de fala         | 
> | Armazenamento: camadas de armazenamento de BLOBs quentes/frias   | Serviços Cognitivos do Azure: Análise de Texto          | 
> | Armazenamento: Managed Disks                 | Serviços cognitivas do Azure: Tradutor              | 
> | Conjuntos de Dimensionamento de Máquinas Virtuais             | Azure Data Explorer                               | 
> | Máquinas Virtuais                       | Azure Data Share                                  | 
> | Máquinas virtuais: host dedicado do Azure | Banco de Dados do Azure para MySQL                          | 
> | Máquinas virtuais: Av2-Series           | Banco de Dados do Azure para PostgreSQL                     | 
> | Máquinas virtuais: Bs-Series            | Proteção contra DDoS do Azure                             | 
> | Máquinas virtuais: DSv2-Series          | Firewall do Azure                                    | 
> | Máquinas virtuais: DSv3-Series          | Gerenciador de Firewall do Azure                            | 
> | Máquinas virtuais: Dv2-Series           | Azure Functions                                   | 
> | Máquinas virtuais: Dv3-Series           | Hub IoT do Azure                                     |     
> | Máquinas virtuais: ESv3-Series          | AKS (Serviço de Kubernetes do Azure)                    | 
> | Máquinas virtuais: Ev3-Series           | Azure Machine Learning                            | 
> | Rede Virtual                        | Azure Monitor: Application Insights               | 
> | Gateway de VPN                            | Azure Monitor: Log Analytics                      | 
> |                                        | Link Privado do Azure                                | 
> |                                        | Red Hat OpenShift no Azure                           | 
> |                                        | Azure Site Recovery                               | 
> |                                        | Azure Stream Analytics                            | 
> |                                        | Azure Synapse Analytics                           | 
> |                                        | Lote                                             | 
> |                                        | Serviços de nuvem: série M                          | 
> |                                        | Instâncias de Contêiner                               | 
> |                                        | Registro de Contêiner                                | 
> |                                        | Data Factory                                      | 
> |                                        | Grade de Eventos                                        | 
> |                                        | HDInsight                                         |  
> |                                        | Aplicativos Lógicos                                        | 
> |                                        | Serviços de Mídia                                    | 
> |                                        | Observador de Rede                                   | 
> |                                        | Hubs de Notificação                                 | 
> |                                        | Armazenamento de blob Premium                              | 
> |                                        | Armazenamento de arquivos Premium                             | 
> |                                        | Máquinas virtuais: Ddsv4-Series                    | 
> |                                        | Máquinas virtuais: Ddv4-Series                     | 
> |                                        | Máquinas virtuais: Dsv4-Series                     | 
> |                                        | Máquinas virtuais: Dv4-Series                      | 
> |                                        | Máquinas virtuais: Edsv4-Series                    | 
> |                                        | Máquinas virtuais: Edv4-Series                     | 
> |                                        | Máquinas virtuais: Esv4-Series                     | 
> |                                        | Máquinas virtuais: Ev4-Series                      | 
> |                                        | Máquinas virtuais: Fsv2-Series                     | 
> |                                        | Máquinas virtuais: série M                        | 
> |                                        | WAN Virtual                                       | 



### <a name="specialized-services"></a>Serviços especializados
Conforme mencionado anteriormente, o Azure classifica os serviços em três categorias: fundamental, predominante e especializado. As categorias de serviço são atribuídas em disponibilidade geral. Geralmente, os serviços iniciam seu ciclo de vida como um serviço especializado e, à medida que a demanda e a utilização aumentam, podem ser promovidas ao básico ou básico. A tabela a seguir lista os serviços especializados. 

> [!div class="mx-tableFixed"]
> | Especializada                                          |
> |------------------------------------------------------|
> | API do Azure para FHIR                                   |
> | Azure Analysis Services                              |
> | Serviços cognitivas do Azure: detector de anomalias           |
> | Serviços cognitivas do Azure: Visão Personalizada              |
> | Serviços cognitivas do Azure: reconhecedor de formulário            |
> | Serviços cognitivas do Azure: personalizador               |
> | Serviços cognitivas do Azure: QnA Maker                  |
> | Banco de Dados do Azure para MariaDB                           |
> | Serviço de Migração de Banco de Dados do Azure                     |
> | HSM Dedicado do Azure                                  |
> | Gêmeos Digitais do Azure                                  |
> | Azure Health Bot                                     |
> | Azure HPC Cache                                      |
> | Azure Lab Services                                   |
> | Azure NetApp Files                                   |
> | Serviço Azure SignalR                                |
> | Serviço de nuvem do Azure Spring                           |
> | Azure Time Series Insights                           |
> | Solução VMware no Azure                                |
> | Solução VMware no Azure pela CloudSimple                 |
> | Data Lake Analytics                                  |
> | Azure Machine Learning Studio (clássico)              |
> | Âncoras Espaciais                                      |
> | Armazenamento: Armazenamento de Arquivos                             |
> | Ultra Armazenamento em Disco                                   |
> | Video Indexer                                        |
> | Máquinas virtuais: DASv4-Series                       |
> | Máquinas virtuais: DAv4-Series                        |
> | Máquinas virtuais: DCsv2-Series                       |
> | Máquinas virtuais: EASv4-Series                       |
> | Máquinas virtuais: EAv4-Series                        |
> | Máquinas virtuais: HBv1-Series                        |
> | Máquinas virtuais: HBv2-Series                        |
> | Máquinas virtuais: HCv1-Series                        |
> | Máquinas virtuais: série H                           |
> | Máquinas virtuais: LSv2-Series                        |
> | Máquinas virtuais: Mv2-Series                         |
> | Máquinas virtuais: NCv3-Series                        |
> | Máquinas virtuais: NDv2-Series                        |
> | Máquinas virtuais: NVv3-Series                        |
> | Máquinas virtuais: NVv4-Series                        | 
> | Máquinas virtuais: SAP HANA em Instâncias Grandes do Azure  |




## <a name="next-steps"></a>Próximas etapas

- [Regiões que dão suporte a Zonas de Disponibilidade no Azure](az-region.md)
- [Modelos de início rápido](https://aka.ms/azqs)
