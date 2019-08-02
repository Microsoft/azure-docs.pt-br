---
title: Guia SKUs da máquina virtual no Portal do Cloud Partner para o Azure Marketplace
description: Descreve a guia SKUs usada na criação de uma oferta de máquina virtual no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: dceb82af73061b91676ffb7061c7495995e76667
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868715"
---
# <a name="virtual-machine-skus-tab"></a>Guia de SKUs de máquina virtual

A guia **SKUs** da página **Nova Oferta** permite criar uma ou mais SKUs e associá-las à sua nova oferta.  SKUs diferentes podem diferenciar uma solução por conjuntos de recursos, tipos de imagem VM, taxa de transferência ou escalabilidade, modelos de cobrança ou alguma outra característica.


## <a name="create-a-sku"></a>Criar uma SKU

Inicialmente, uma nova oferta não terá quaisquer SKUs associadas, assim você irá criar uma clicando em **Nova SKU**.

![Novo botão SKU na guia Nova Oferta para os módulos do IoT Edge](./media/publishvm_005.png)

<br/>

A caixa de diálogo **Mova SKU** é exibida.  Insira o identificador para a nova SKU, em seguida, clique em **OK**. (consulte abaixo para convenções de nomenclatura de identificador).  As **SKUs** agora exibem os campos disponíveis para edição.    Um asterisco anexado (*) no nome do campo indica que isso é necessário.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Guia SKU no formulário nova oferta para máquinas virtuais](./media/publishvm_006.png)

A tabela a seguir descreve a finalidade, o conteúdo e a formatação desses campos.  Os campos obrigatórios são indicados por um asterisco (*).

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
|  *Configurações de SKU*   |    |
| **ID DO SKU\***       | Identificador dessa SKU.  Esse nome tem um máximo de 50 caracteres, consistindo em caracteres alfanuméricos minúsculos ou traços (-), mas não pode terminar com um traço.  Não pode ser modificado depois que a oferta é publicada.  |
|  *Detalhes da SKU*   |  |
| **Título\***        | Nome amigável para a oferta para exibição no marketplace. Comprimento máximo de 50 caracteres. |
| **Resumo\***      | Descrição sucinta da oferta para exibição no marketplace. Comprimento máximo de 100 caracteres. |
| **Descrição\***  | Texto de descrição que fornece uma explicação mais detalhada da oferta.  <!-- TD: max len/guidance? 3k characters -->  |
| **Ocultar esta SKU\*** | Indica se o SKU deve estar visível no mercado para os clientes.  Você talvez queira ocultar a SKU se só a quiser disponível por meio de modelos de solução e não para compra individualmente.  Também pode ser útil para testes iniciais ou para ofertas temporárias ou sazonais. |
| **Disponibilidade na nuvem\*** | Determina em quais nuvens SKU deve estar disponível.  O padrão é a versão pública do Azure.  O Microsoft Azure Governamental é uma nuvem comunitária do governo com acesso controlado para o Governo Federal, estado, governos locais ou tribais e seus parceiros certificados.  Para obter mais informações sobre a nuvem governamental, consulte [Bem-vindo ao Azure Governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **Este é um SKU privado?\*** | Indica se a SKU é pública ou privada. O padrão é **Não** (público).  Para obter mais informações, consulte [SKUs Públicas e Privadas](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **Disponibilidade de país/região\*** | Determina quais países ou regiões do mundo a SKU estará disponível para compra. Selecione pelo menos um país/região. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Preços*   |  |
| **Modelo de licença\***| Modelo de cobrança padronizado para usar.  Se você selecionar **SKU cobrada numa base mensal**, uma seção accordion será aberta para permitir que você especifique os detalhes de preço por núcleo e se você desejar oferecer um período de avaliação gratuita.  Esta seção também permite que você exporte e importe esse agendamento de preços para o Excel. Para obter mais informações, consulte [Opções de cobrança no Azure Marketplace](../../billing-options-azure-marketplace.md). | 
|  *Imagens de VM*   |  |
| **Família do sistema operacional\*** | Indica se a VM de solução é baseada em Windows ou Linux. |
| **Selecione o tipo de Sistema Operacional** | Fornecedor específico ou a versão do sistema operacional especificado. |
| **Nome amigável do sistema operacional\*** | Nome do sistema operacional a ser exibido para os clientes.  |
| **Tamanhos de VM recomendados\*** | Habilita a seleção de até seis tamanhos de VM recomendados de uma lista padronizada.  Essa lista é passada para o portal do Azure e o Microsoft Marketplaces.  O primeiro tamanho de VM nessa lista que é válido (para essa assinatura de cliente, região, zona etc.) é definido como o padrão para esse cliente potencial.  O usuário pode alterar esse tamanho para qualquer compatível com a imagem da solução. | 
| **Abrir portas**| Portas para abrir e protocolo para dar suporte para a SKU.  Essas configurações devem corresponder a rede virtual que você configurou para a rede da VM de solução. Essas configurações entram em vigor durante a implantação de VM. No entanto, as configurações de porta podem ser modificadas depois de publicar uma SKU. Para obter mais informações, consulte [Como abrir portas em uma máquina virtual com o portal do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>Os seguintes mapeamentos de rede padrão são adicionados a todas as VMs. &emsp; Windows: 3389 -> 3389 TCP, 5986 -> 5986 TCP; &emsp; Linux: 22 -> 22, TCP (SSH). |
| **Versão do disco**  | Solução associada a VM, especificada pelo número de versão do disco e URL do disco. A versão do disco deve estar no formato da [versão semântica](https://semver.org/): `<major>.<minor>.<patch>`.  A URL é a assinatura de acesso compartilhado criado para o VHD do sistema operacional.  No entanto, você pode adicionar até oito versões de disco por SKU, somente o maior disco número de versão para um SKU aparecerá no Azure Marketplace. As outras versões só estarão visíveis por meio de APIs.  <!--TD: Add more specific link to API --> <br/> A nova seção**Novo disco de dados** seção permite que você anexe até 15 discos de dados à sua VM.  Depois de publicar uma SKU com determinada versão de VM e discos de dados associado, essa configuração não pode ser modificada.  Se versões adicionais de VM forem adicionadas à SKU, também devem suportar o mesmo número de discos de dados. <br/> Se você não tiver criado suas imagens de VM com base no Azure, você pode adicionar a atualizar este campo mais tarde.  Para obter informações sobre como criar o recurso VM associado, consulte a seção [Criar ativos técnicos de VM](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Clique em **Salvar** para salvar o progresso. Na próxima guia, você especificará se dá suporte à sua oferta [Test-Drive](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>Considerações de preço adicionais

O modelo de preços descrito acima é uma descrição básica.  Ele está passando por alterações e pode ser afetado pelas políticas de preços do Microsoft e regulamentações de impostos locais ou regionais. 

### <a name="new-core-sizes-added-on-722019"></a>Novos tamanhos de núcleo adicionados em 7/2/2019

Os editores de VM foram notificados em 2 de julho de 2019 da adição de novos preços para novos tamanhos de máquina virtual do Azure (com base no número de núcleos).  Os novos preços são para os tamanhos de núcleos 10, 44, 48, 60, 120, 208 e 416.  Para a VM existente oferece novos preços para esses tamanhos de núcleos calculados automaticamente com base nos preços atuais.  Os editores têm até 1º de agosto de 2019 para examinar os preços adicionais e fazer as alterações desejadas.  Após essa data, se ainda não tiver sido publicado novamente pelo Publicador, os preços calculados automaticamente para esses novos tamanhos de núcleo entrarão em vigor.


### <a name="simplified-currency-pricing"></a>Preço em moeda simplificada

A partir de 1º de setembro de 2018, uma nova seção chamada **Precificação simplificada de moeda** será adicionada ao portal. A Microsoft está simplificando os negócios do Azure Marketplace, permitindo preços e coleções mais previsíveis de seus clientes em todo o mundo. Essa simplificação incluirá a redução do número de moedas nas quais faturamos seus clientes.  Para obter mais informações, consulte [Atualização de uma VM existente da oferta no Azure Marketplace](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Informações adicionais sobre impostos e preços

* A Microsoft classifica alguns países/regiões como países remetidos pelo *imposto*.  Em tais países/regiões, a Microsoft coleta impostos de clientes, em seguida, paga (remete) impostos para o governo.  Em outros países/regiões, os parceiros são normalmente responsáveis por coletar impostos de seus clientes e pagar impostos para o governo. Se você optar por vender nos últimos países/regiões, deverá ter a capacidade de calcular e pagar impostos locais.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Os preços não são alteráveis depois que uma oferta entra no ar. Porém, ainda será possível adicionar/remover regiões com suporte. 
* A Microsoft cobra do cliente da VM do Azure padrão as taxas de uso, além dos valores de SKUs programados.
* Os preços são definidos para todas as regiões em moeda local nas taxas de moeda disponíveis no momento da definição de preços.  <!-- TD: Meaning? - Offer created, published, other? -->
* Para definir o preço de cada região individualmente, exporte a planilha de preços, aplicar preços personalizados e importar. 


## <a name="next-steps"></a>Próximas etapas

Opcionalmente, você especificará informações de [unidade de teste](./cpp-test-drive-tab.md) se estiver dando suporte a esse recurso; caso contrário, você fornecerá dados do [Marketplace](./cpp-marketplace-tab.md) para sua oferta.
