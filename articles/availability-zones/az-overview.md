---
title: Regiões e Zonas de Disponibilidade no Azure
description: Saiba mais sobre regiões e Zonas de Disponibilidade no Azure para atender aos seus requisitos técnicos e regulatórios.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: dae5319e6c8b87d6a9eef98875ad7e8da623e65c
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955793"
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
| serviço principal | Um serviço do Azure que está disponível em todas as regiões recomendadas dentro de 12 meses da disponibilidade geral de região/serviço ou da disponibilidade orientada por demanda em regiões alternativas. |
| serviço especializado | Um serviço do Azure que é a disponibilidade orientada por demanda entre regiões apoiadas por hardware personalizado/especializado. |
| serviço regional | Um serviço do Azure que é implantado regionalmente e permite que o cliente especifique a região na qual o serviço será implantado. Para obter uma lista completa, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| serviço não regional | Um serviço do Azure para o qual não há nenhuma dependência em uma região específica do Azure. Os serviços não regionais são implantados em duas ou mais regiões e, se houver uma falha regional, a instância do serviço em outra região continuará atendendo aos clientes. Para obter uma lista completa, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Regiões

Uma região é um conjunto de data centers implantados em um perímetro definido por latência e conectados por meio de uma rede regional de baixa latência. O Azure oferece a você a flexibilidade de implantar aplicativos onde você precisa, incluindo em várias regiões para fornecer resiliência entre regiões. Para obter mais informações, consulte [visão geral do pilar de resiliência](/azure/architecture/framework/resiliency/overview).

## <a name="availability-zones"></a>Zonas de Disponibilidades

Uma zona de disponibilidade é uma oferta de alta disponibilidade que protege seus aplicativos e dados de falhas do datacenter. As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física das Zonas de Disponibilidade dentro de uma região protege os aplicativos e dados contra falhas do datacenter. Serviços com redundância de zona replicam os aplicativos e dados entre Zonas de Disponibilidade para proteger dos pontos únicos de falha. Com Zonas de Disponibilidade, o Azure oferece o melhor SLA de tempo de atividade da VM de 99,99% do setor. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explica a disponibilidade garantida do Azure como um todo.

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

- **Fundamental** – disponível em todas as regiões recomendadas e alternativas quando a região está disponível para o público geral ou dentro de 12 meses de um novo serviço básico ficando disponível para o público geral.
- **Mainstream** – disponível em todas as regiões recomendadas dentro de 12 meses da disponibilidade geral de região/serviço; orientado por demanda em regiões alternativas (muitas já estão implantadas em um grande subconjunto de regiões alternativas).
- Ofertas de serviços direcionadas **especializadas** , geralmente focadas no setor ou com suporte por hardware personalizado/especializado. Disponibilidade orientada por demanda entre regiões (muitas já estão implantadas em um grande subconjunto de regiões recomendadas).

Para ver quais serviços são implantados em uma determinada região, bem como o roteiro futuro para visualização ou disponibilidade geral dos serviços em uma região, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all).

Se uma oferta de serviço não estiver disponível em uma região específica, você poderá compartilhar seu interesse entrando em contato com seu representante de vendas da Microsoft.

| Tipo de região | Não regional | Foundational | Base | Especializada | Zonas de Disponibilidades | Residência de dadosResidência de dados |
| --- | --- | --- | --- | --- | --- | --- |
| Recomendadas | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Controlado por demanda | :heavy_check_mark: | :heavy_check_mark: |
| Alternativo | :heavy_check_mark: | :heavy_check_mark: | Controlado por demanda | Controlado por demanda | N/D | :heavy_check_mark: |

### <a name="services-by-category"></a>Serviços por categoria

Conforme mencionado anteriormente, o Azure classifica os serviços em três categorias: fundamental, predominante e especializado. As categorias de serviço são atribuídas em disponibilidade geral. Geralmente, os serviços iniciam seu ciclo de vida como um serviço especializado e, à medida que a demanda e a utilização aumentam, podem ser promovidas ao básico ou básico. A tabela a seguir lista a categoria de serviços como básico, principal ou especializado. Você deve observar o seguinte sobre a tabela:

- Alguns serviços não são regionais. Para obter informações e uma lista de serviços não regionais, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/).
- As máquinas virtuais de geração mais antigas não são listadas. Para obter mais informações, consulte a documentação em [gerações anteriores de tamanhos de máquina virtual](../virtual-machines/sizes-previous-gen.md)
- . Os serviços não são atribuídos a uma categoria até a disponibilidade geral (GA). Para obter informações e uma lista de serviços de visualização, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). 

> [!div class="mx-tableFixed"]
> | Foundational                          | Base                                        | Especializada                                          |
> |---------------------------------------|---------------------------------------------------|------------------------------------------------------|
> | Contas de Armazenamento                      | Gerenciamento de API                                    | API do Azure para FHIR                                   |
> | Gateway de Aplicativo                   | Configuração de Aplicativos                                 | Azure Analysis Services                              |
> | Serviço de Backup do Azure                          | Serviço de Aplicativo                                       | Serviços cognitivas do Azure: detector de anomalias           |
> | Azure Cosmos DB                       | Automação                                        | Serviços cognitivas do Azure: Visão Personalizada              |
> | Azure Data Lake Storage Gen2          | Azure Active Directory Domain Services            | Serviços cognitivas do Azure: reconhecedor de formulário            |
> | Azure ExpressRoute                    | Azure Bastion                                     | Serviços cognitivas do Azure: personalizador               |
> | IP público do Azure                       | Cache Redis do Azure                             | Serviços cognitivas do Azure: QnA Maker                  |
> | Banco de Dados SQL do Azure                    | Pesquisa Cognitiva do Azure                            | Banco de Dados do Azure para MariaDB                           |
> | SQL do Azure: Instância Gerenciada          | Serviços Cognitivos do Azure                          | Serviço de Migração de Banco de Dados do Azure                     |
> | Serviços de Nuvem                        | Serviços Cognitivos do Azure: Pesquisa Visual Computacional         | HSM Dedicado do Azure                                  |
> | Serviços de nuvem: Av2-Series            | Serviços cognitivas do Azure: Content Moderator       | Gêmeos Digitais do Azure                                  |
> | Serviços de nuvem: Dv2-Series            | Serviços cognitivas do Azure: rosto                    | Bot de integridade do Azure                                     |
> | Serviços de nuvem: Dv3-Series            | Serviços cognitivas do Azure: leitor de imersão        | Azure HPC Cache                                      |
> | Serviços de nuvem: Ev3-Series            | Serviços cognitivas do Azure: Reconhecimento vocal  | Azure Lab Services                                   |
> | Serviços de nuvem: IPs de nível de instância    | Serviços cognitivas do Azure: serviços de fala         | Azure NetApp Files                                   |
> | Serviços de nuvem: IP Reservado           | Serviços Cognitivos do Azure: Análise de Texto          | Serviço Azure SignalR                                |
> | Armazenamento em disco                          | Serviços cognitivas do Azure: Tradutor              | Serviço de nuvem do Azure Spring                           |
> | Hubs de Eventos                            | Azure Data Explorer                               | Azure Time Series Insights                           |
> | Key Vault                             | Azure Data Share                                  | Solução VMware no Azure                                |
> | Balanceador de carga                         | Banco de Dados do Azure para MySQL                          | Solução VMware no Azure pela CloudSimple                 |
> | Barramento de Serviço                           | Banco de Dados do Azure para PostgreSQL                     | Serviços de nuvem: série H                             |
> | Service Fabric                        | Azure Databricks                                  | Catálogo de Dados                                         |
> | Armazenamento: camadas de armazenamento de BLOBs quentes/frias  | Proteção contra DDoS do Azure                             | Data Lake Analytics                                  |
> | Armazenamento: Managed Disks                | Azure DevTest Labs                                | Azure Machine Learning Studio (clássico)              |
> | Conjuntos de Dimensionamento de Máquinas Virtuais            | Firewall do Azure                                    | Âncoras Espaciais                                      |
> | Máquinas Virtuais                      | Gerenciador de Firewall do Azure                            | Armazenamento: Armazenamento de Arquivos                             |
> | Máquinas virtuais: Av2-Series          | Funções do Azure                                   | StorSimple                                           |
> | Máquinas virtuais: Bs-Series           | Hub IoT do Azure                                     | Ultra Armazenamento em Disco                                   |
> | Máquinas virtuais: DSv2-Series         | AKS (Serviço de Kubernetes do Azure)                    | Video Indexer                                        |
> | Máquinas virtuais: DSv3-Series         | Azure Machine Learning                            | Máquinas virtuais: DASv4-Series                       |
> | Máquinas virtuais: Dv2-Series          | Azure Monitor: Application Insights               | Máquinas virtuais: DAv4-Series                        |
> | Máquinas virtuais: Dv3-Series          | Azure Monitor: Log Analytics                      | Máquinas virtuais: DCsv2-Series                       |
> | Máquinas virtuais: ESv3-Series         | Link Privado do Azure                                | Máquinas virtuais: EASv4-Series                       |
> | Máquinas virtuais: Ev3-Series          | Red Hat OpenShift no Azure                           | Máquinas virtuais: EAv4-Series                        |
> | Máquinas virtuais: IPs de nível de instância  | Azure Site Recovery                               | Máquinas virtuais: HBv1-Series                        |
> | Máquinas virtuais: IP Reservado         | Stream Analytics do Azure                            | Máquinas virtuais: HBv2-Series                        |
> | Rede Virtual                       | Azure Synapse Analytics                           | Máquinas virtuais: HCv1-Series                        |
> | Gateway de VPN                           | Lote                                             | Máquinas virtuais: série H                           |
> |                                       | Serviços de nuvem: série M                          | Máquinas virtuais: LSv2-Series                        |
> |                                       | Instâncias de Contêiner                               | Máquinas virtuais: Mv2-Series                         |
> |                                       | Registro de Contêiner                                | Máquinas virtuais: NCv3-Series                        |
> |                                       | Data Factory                                      | Máquinas virtuais: NDv2-Series                        |
> |                                       | Grade de Eventos                                        | Máquinas virtuais: NVv3-Series                        |
> |                                       | HDInsight                                         | Máquinas virtuais: NVv4-Series                        |> 
> |                                       | Aplicativos Lógicos                                        | Máquinas virtuais: SAP HANA em Instâncias Grandes do Azure  |
> |                                       | Serviços de Mídia                                    |                                                      |
> |                                       | Observador de Rede                                   |                                                      |
> |                                       | Hubs de Notificação                                 |                                                      |
> |                                       | Armazenamento de blob Premium                              |                                                      |
> |                                       | Armazenamento de arquivos Premium                             |                                                      |
> |                                       | Máquinas virtuais: Ddsv4-Series                    |                                                      |
> |                                       | Máquinas virtuais: Ddv4-Series                     |                                                      |
> |                                       | Máquinas virtuais: Dsv4-Series                     |                                                      |
> |                                       | Máquinas virtuais: Dv4-Series                      |                                                      |
> |                                       | Máquinas virtuais: Edsv4-Series                    |                                                      |
> |                                       | Máquinas virtuais: Edv4-Series                     |                                                      |
> |                                       | Máquinas virtuais: Esv4-Series                     |                                                      |
> |                                       | Máquinas virtuais: Ev4-Series                      |                                                      |
> |                                       | Máquinas virtuais: Fsv2-Series                     |                                                      |
> |                                       | Máquinas virtuais: série M                        |                                                      |
> |                                       | WAN Virtual                                       |                                                      |


## <a name="next-steps"></a>Próximas etapas

- [Regiões que dão suporte a Zonas de Disponibilidade no Azure](az-region.md)
- [Modelos de início rápido](https://aka.ms/azqs)
