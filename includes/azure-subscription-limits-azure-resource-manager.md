| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| VMs por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md) |10.000 <sup>1</sup> por Região |10.000 por Região |
| Total de núcleos da VM por [assinatura](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região | Contate o suporte |
| Núcleos por série de VM (Dv2, F etc.) por [ssinatura](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> por região | Contate o suporte |
| [Coadministradores](../articles/billing-add-change-azure-subscription-administrator.md) por assinatura |Ilimitado |Ilimitado |
| [Contas de armazenamento](../articles/storage/common/storage-create-storage-account.md) por assinatura |200 |200<sup>2</sup> |
| [Grupos de Recursos](../articles/azure-resource-manager/resource-group-overview.md) por assinatura |800 |800 |
| [Conjuntos de disponibilidade](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) por assinatura |2.000 por região |2.000 por região |
| Leituras de API do Gerenciador de Recursos |15.000 por hora |15.000 por hora |
| Gravações de API do Gerenciador de Recursos |1.200 por hora |1.200 por hora |
| Tamanho da solicitação de API do Gerenciador de Recursos |4.194.304 bytes |4.194.304 bytes |
| Marcas por assinatura<sup>3</sup> |ilimitado |ilimitado |
| Cálculos de marca exclusivos por assinatura<sup>3</sup> | 10.000 | 10.000 |
| [Serviços de nuvem](../articles/cloud-services/cloud-services-choose-me.md) por assinatura |Não aplicável<sup>4</sup> |Não aplicável<sup>4</sup> |
| [Grupos de afinidade](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) por assinatura |Não aplicável<sup>4</sup> |Não aplicável<sup>4</sup> |

<sup>1</sup>Os limites padrão variam de acordo com o tipo de categoria oferecido, como avaliação gratuita, pré-pago e séries como Dv2, F, G etc.

<sup>2</sup>Isso inclui contas de armazenamento Standard e Premium. Se você precisar de mais de 200 contas de armazenamento, faça uma solicitação por meio do [Suporte do Azure](https://azure.microsoft.com/support/faq/). A equipe de Armazenamento do Azure examinará seu caso de negócios e poderá aprovar até 250 contas de armazenamento.

<sup>3</sup>Você pode aplicar um número ilimitado de marcas por assinatura. O número de marcas por recurso ou grupo de recursos é limitado a 15. O Gerenciador de Recursos só retorna uma [lista de valores e nomes de marca exclusivos](/rest/api/resources/tags#Tags_List) na assinatura quando o número de marcas é menor ou igual a 10.000. No entanto, ainda é possível encontrar um recurso por marca quando o número excede 10.000.  

<sup>4</sup>Esses recursos não são mais necessários com os Grupos de Recursos do Azure e o Azure Resource Manager.

> [!NOTE]
> É importante enfatizar que os núcleos de máquina virtual têm um limite regional total, bem como um limite regional por tamanho de série (Dv2, F etc.), que são aplicados separadamente.  Por exemplo, considere uma assinatura com uma VM do Leste dos EUA com um limite total de núcleos de 30, um limite de núcleos da série A de 30 e um limite de núcleos da série D de 30.  Esta assinatura teria permissão para implantar 30 VMs A1, ou 30 VMs D1 ou uma combinação das duas a fim de não exceder um total de 30 núcleos (por exemplo, 10 VMs A1 e 20 VMs D1).  
> <!-- -->
> 
> 

