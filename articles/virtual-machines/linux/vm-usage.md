---
title: Noções básicas sobre o uso de máquina virtual do Azure | Microsoft Docs
description: Noções básicas dos detalhes de uso da máquina virtual
services: virtual-machines
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: ''
tags: azure-virtual-machine
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: bc341492779e6ed5aef2505a98ad8a116866fe4d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695275"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Noções básicas sobre o uso de máquina virtual do Azure
Ao analisar os dados de uso do Azure, podem ser obtidas informações poderosas sobre o consumo - insights que podem permitir um melhor gerenciamento e alocação de custos em toda a sua organização. Este documento fornece um aprofundamento em seus detalhes de consumo de Computação do Azure. Para obter mais detalhes sobre o uso geral do Azure, navegue até [Entendendo sua fatura](https://docs.microsoft.com/azure/billing/billing-understand-your-bill).

## <a name="download-your-usage-details"></a>Faça o download dos seus detalhes de uso
Para começar, [faça o download dos seus detalhes de uso](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv). A tabela a seguir fornece os valores de definição e exemplo de uso para Máquinas Virtuais implantadas através do Azure Resource Manager. Este documento não contém informações detalhadas para VMs implantadas através do nosso modelo clássico.


| Campos             | Significado                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Valores de exemplo                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Data de Uso         | A data na qual o recurso foi usado.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  “11/23/2017”                                                                                                                                                                                                                                                                                                                                                     |
| ID de medidor           | Identifica o serviço de nível superior ao qual esse uso pertence.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | “Máquinas Virtuais”                                                                                                                                                                                                                                                                                                                                               |
| Subcategoria de medidor | O identificador de medidor cobrado. <ul><li>Para o uso de Hora de Computação, há um medidor para cada Tamanho da VM + SO (Windows, Não Windows) + Região.</li><li>Para o uso do software Premium, há um medidor para cada tipo de software. A maioria das imagens de software Premium tem medidores diferentes para cada tamanho de núcleo. Para obter mais informações, visite a [Página de Preços de Computação.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | “2005544f-659d-49c9-9094-8e0aea1be3a5”                                                                                                                                                                                                                                                                                                                           |
| Nome do medidor         | Isto é específico para cada serviço no Azure. Para o cálculo, é sempre "Horas de Computação".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | “Horas de Computação”                                                                                                                                                                                                                                                                                                                                                  |
| Região do medidor       | Identifica o local do datacenter para determinados serviços que são cobrados com base no local do datacenter.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  “Leste do Japão”                                                                                                                                                                                                                                                                                                                                                       |
| Unidade               | Identifica a unidade em que o serviço é cobrado. Recursos de computação são cobrados por hora.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | “Horas”                                                                                                                                                                                                                                                                                                                                                          |
| Consumido           | A quantidade do recurso que foi consumida naquele dia. Para Computação, contamos por cada minuto que a VM correu por uma hora determinada (até 6 decimais de precisão).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    “1”, “0.5”                                                                                                                                                                                                                                                                                                                                                    |
| Local do recurso  | Identifica o datacenter onde o recurso está sendo executado.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | “Leste do Japão”                                                                                                                                                                                                                                                                                                                                                        |
| Serviço consumido   | O serviço da plataforma do Azure que você usou.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft.Compute"                                                                                                                                                                                                                                                                                                                                              |
| Grupo de recursos     | O grupo de recursos no qual o recurso implantado está sendo executado. Para obter mais informações, consulte [Visão geral do Azure Resource Manager.](https://docs.microsoft.com/azure/virtual-machines/linux/vm-usage)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    "MyRG"                                                                                                                                                                                                                                                                                                                                                        |
| ID da instância        | O identificador do recurso. O identificador contém o nome especificado para o recurso quando ele foi criado. Para as VMs, a ID da Instância conterá o SubscriptionId, ResourceGroupName e VMName (ou o nome do conjunto de dimensionamento para o uso do conjunto de dimensionamento).                                                                                                                                                                                                                                                                                                                                                                                                                    | "/assinaturas/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1”<br><br>ou<br><br>"/assinaturas/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1”                                                                                           |
| Marcas               | Marca atribuída ao recurso. Use marcas para agrupar registros de cobrança. Saiba como [marcar suas Máquinas Virtuais.](tag.md) Isso está disponível somente para VMs do Gerenciador de Recursos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{"myDepartment":"RD","myUser":"myName"}"                                                                                                                                                                                                                                                                                                                        |
| Informações Adicionais    | Metadados específicos ao serviço. Para as VMs, nós preenchemos os seguintes dados no campo de informações adicionais: <ul><li>Tipo de Imagem - especifica a imagem que foi executada. Localize a lista completa de cadeias de caracteres suportadas abaixo em Tipos de Imagem.</li><li>Tipo de Serviço: o tamanho que você implantou.</li><li>VMName: nome da sua VM. Este campo é preenchido apenas para VMs de conjunto de dimensionamento. Se for necessário o Nome da VM para VMs de conjunto de dimensionamento, será possível localizá-lo na cadeia de caracteres da ID da Instância acima.</li><li>UsageType: Isso especifica o tipo de uso que isso representa.<ul><li>ComputeHR é o uso de Hora de Computação para a VM subjacente, como Standard_D1_v2.</li><li>ComputeHR_SW é a carga de software Premium se a VM estiver utilizando o software Premium, como o Microsoft R Server.</li></ul></li></ul>    | Máquinas Virtuais {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}<br><br>Conjuntos de dimensionamento de Máquinas Virtuais {"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}<br><br>Software Premium {"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"} |

## <a name="image-type"></a>Tipo de Imagem
Para algumas imagens na galeria do Azure, o tipo de imagem é preenchido no campo Informações Adicionais. Isso permite aos usuários entender e rastrear o que implantaram em sua Máquina Virtual. Os seguintes valores são preenchidos nesse campo com base na imagem que você implementou:
  - BitRock 
  - Canônico 
  - FreeBSD 
  - Abrir Lógico 
  - Oracle 
  - SLES para SAP 
  - Versão prévia do SQL Server 14 no Windows Server 2012 R2 Preview 
  - SUSE
  - SUSE Premium
  - Dispositivo de Nuvem StorSimple 
  - Red Hat
  - Red Hat para aplicativos de negócios SAP     
  - Red Hat para SAP HANA 
  - Windows Client BYOL 
  - Windows Server BYOL 
  - Versão prévia do Windows Server 

## <a name="service-type"></a>Tipo de serviço
O campo de tipo de serviço no campo Informações Adicionais corresponde ao tamanho exato da VM implantada. VMs de armazenamento Premium (baseadas em SSD) e VMs de armazenamento não Premium (baseadas em HDD) possuem o mesmo preço. Se você implantar um tamanho baseado em SSD, como Standard\_DS2\_v2, você visualizará o tamanho não SSD (‘Standard\_D2\_v2 VM’) na coluna Subcategoria do Medidor e o Tamanho SSD (‘Standard\_DS2\_v2’) no campo Informações Adicionais.

## <a name="region-names"></a>Nomes de região
O nome da região preenchido no campo Local do Recurso nos detalhes de uso varia do nome de região utilizado no Azure Resource Manager. Aqui está um mapeamento entre os valores de região:

|    **Nome da região do Gerenciador de Recursos**       |    **Local do recurso em Detalhes de uso**    |
|--------------------------|------------------------------------------|
|    australiaeast         |    Leste da Austrália                               |
|    australiasoutheast    |    Sudeste da Austrália                          |
|    brazilsouth           |    Sul do Brasil                              |
|    Canadá Central         |    CA Central                            |
|    CanadaEast            |    Leste do Canadá                               |
|    CentralIndia          |    Índia Central                            |
|    centralus             |    Centro dos EUA                            |
|    chinaeast             |    Leste da China                            |
|    chinanorth            |    Norte da China                           |
|    eastasia              |    Ásia Oriental                             |
|    eastus                |    East US                               |
|    eastus2               |    Leste dos EUA 2                             |
|    GermanyCentral        |    Alemanha Central                            |
|    GermanyNortheast      |    Nordeste da Alemanha                          |
|    japaneast             |    Leste do Japão                               |
|    japanwest             |    Oeste do Japão                               |
|    KoreaCentral          |    Coreia Central                            |
|    KoreaSouth            |    Sul da Coreia                              |
|    northcentralus        |    Centro-Norte dos EUA                      |
|    northeurope           |    Norte da Europa                          |
|    southcentralus        |    Centro-Sul dos Estados Unidos                      |
|    southeastasia         |    Sudeste Asiático                        |
|    SouthIndia            |    Sul da Índia                              |
|    UKNorth               |    Norte do Reino Unido                              |
|    uksouth               |    Sul do Reino Unido                              |
|    UKSouth2              |    Sul do Reino Unido 2                            |
|    ukwest                |    Oeste do Reino Unido                               |
|    USDoDCentral          |    DoD Central dos EUA                        |
|    USDoDEast             |    DoD do Leste dos EUA                           |
|    USGovArizona          |    Gov. dos EUA – Arizona                         |
|    usgoviowa             |    USGov Iowa                            |
|    USGovTexas            |    Gov. dos EUA – Texas                           |
|    usgovvirginia         |    USGov Virginia                        |
|    westcentralus         |    Centro-Oeste dos EUA                       |
|    westeurope            |    Europa Ocidental                           |
|    WestIndia             |    Índia Ocidental                               |
|    westus                |    Oeste dos EUA                               |
|    westus2               |    Oeste dos EUA 2                             |


## <a name="virtual-machine-usage-faq"></a>Perguntas Frequentes sobre Máquina Virtual
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Quais recursos são cobrados ao implantar uma VM?    
As VMs adquirem custos para a própria VM, qualquer software Premium em execução na VM, a conta de armazenamento\disco gerenciado associado à VM e as transferências de largura de banda de rede da VM.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Como posso saber se uma VM está utilizando o Benefício Híbrido do Azure no CSV de Uso?
Se você implantar utilizando o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), será cobrado pela taxa de VM não-Windows, já que está trazendo sua própria licença para a nuvem. Na sua fatura, você pode distinguir as VMs do Gerenciador de Recursos que estão executando em Benefício Híbrido do Azure porque possuem ou o “Windows\_Server BYOL” ou o “Windows\_Client BYOL” na coluna ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Como é o plano Básico. Há tipos Standard para VM diferenciados no CSV de uso?
São oferecidas VMs dos planos Básico e Standard da série A. Se você implantar uma VM do plano Básico, na subcategoria do medidor ela terá uma cadeia de caracteres “Básica”. Se você implantar uma VM do plano Standard de série A, o tamanho da VM aparecerá como “A1 VM”, já que o plano Standard é o padrão. Para saber mais sobre as diferenças entre os planos Básico e Standard, consulte a[Página de Preços](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Quais são os tamanhos ExtraSmall, Small, Medium, Large e ExtraLarge?
ExtraSmall - ExtraLarge são os nomes herdados para Standard \_A0 – Standard\_A4. Nos registros de uso de VM clássica, você poderá visualizar essa convenção utilizada, caso tenha implantado esses tamanhos.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Qual é a diferença entre a Região do Medidor e o Local do Recurso?
A Região do Medidor está associada à medição. Para alguns serviços do Azure que utilizam um preço para todas as regiões, o campo Região do Medidor pode ficar em branco. No entanto, como as VMs têm preços dedicados por região para Máquinas Virtuais, esse campo é preenchido. Da mesma forma, o Local do Recurso para Máquinas Virtuais é o local onde a VM é implantada. As regiões do Azure em ambos os campos são as mesmas, embora possam ter uma convenção de cadeia de caracteres diferente para o nome da região.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Por que o valor ImageType está em branco no campo Informações Adicionais?
O campo ImageType é preenchido somente para um subconjunto de imagens. Se você não implantou uma das imagens acima, o ImageType está em branco.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Por que o VMName está em branco nas Informações Adicionais?
O VMName é preenchido apenas no campo Informações Adicionais para VMs em um conjunto de dimensionamento. O campo InstanceID contém o nome da VM para VMs de conjunto sem dimensionamento.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>O que o ComputeHR significa no campo UsageType nas Informações Adicionais?
ComputeHR significa Hora de Computação, que representa o evento de uso para o custo da infraestrutura subjacente. Se o UsageType for ComputeHR\_SW, o evento de uso representa a carga de software Premium para a VM.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Como saber se sou cobrado pelo software Premium?
Ao explorar qual melhor Imagem de VM se adapta às suas necessidades, certifique-se de verificar o [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). A imagem possui a taxa do plano de software. Se você visualizar "Gratuito" para a taxa, não há custo adicional para o software. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Qual a diferença entre Microsoft.ClassicCompute e Microsoft.Compute no serviço Consumido?
O Microsoft.ClassicCompute representa os recursos clássicos implantados através do Azure Service Manager. Se você implantar através do Gerenciador de Recursos, o Microsoft.Compute será preenchido no serviço consumido. Saiba mais sobre os [modelos de Implantação do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Por que o campo InstanceID está em branco para o uso da minha Máquina Virtual?
Se você implantar através do modelo de implantação clássico, a cadeia de caracteres da InstanceID não estará disponível.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Por que as marcas para as minhas VMs não estão fluindo para os detalhes de uso?
As marcas somente fluem para o CSV de Uso apenas para VMs de Gerenciador de Recursos. As marcas de recurso clássicas não estão disponíveis nos detalhes de uso.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Como a quantidade consumida pode ser mais de 24 horas por dia?
No modelo Clássico, a cobrança para recursos é agregada no nível de Serviço de Nuvem. Se você tiver mais de uma VM em um Serviço de Nuvem que utiliza o mesmo medidor de cobrança, seu uso será agregado em conjunto. As VMs implantadas através do Gerenciador de Recursos são faturadas no nível de VM, portanto, essa agregação não será aplicada.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Por que os preços não estão disponíveis para os tamanhos DS/FS/GS/LS na página de preços?
As VMs com capacidade de armazenamento Premium são cobradas na mesma taxa que as VMs com capacidade de armazenamento não Premium. Apenas os custos de armazenamento diferem. Visite a [página de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre seus detalhes de uso, consulte [Entenda sua cobrança do Microsoft Azure.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill
)

