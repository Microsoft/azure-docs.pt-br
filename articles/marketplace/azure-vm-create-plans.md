---
title: Criar planos para uma oferta de máquina virtual no Azure Marketplace
description: Saiba como criar planos para uma oferta de máquina virtual no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: c700cce5f50fda7f7a5773e07549e93da1c9f7a2
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956247"
---
# <a name="how-to-create-plans-for-a-virtual-machine-offer"></a>Como criar planos para uma oferta de máquina virtual

Na página **visão geral do plano** (selecione no menu do nav esquerdo no Partner Center) você pode fornecer uma variedade de opções de plano na mesma oferta. Uma oferta requer pelo menos um plano (anteriormente chamado de SKU), que pode variar de acordo com o público monetização, a região do Azure, os recursos ou as imagens de VM.

Você pode criar até 100 planos para cada oferta: até 45 deles podem ser privados. Saiba mais sobre planos privados em [ofertas privadas no Microsoft Commercial Marketplace](private-offers.md).

Depois de criar seus planos, selecione a guia **Visão geral do plano** para exibir:

- Nomes de plano
- Modelos de licença
- Público-alvo (público ou privado)
- Status da publicação atual
- Ações disponíveis

As ações disponíveis no painel **visão geral do plano** variam dependendo do status atual do plano.

- Se o status do plano for um rascunho, selecione **Excluir rascunho**.
- Se o status do plano for publicado e estiver ativo, selecione **Interromper o plano de venda** ou **Sincronizar público-alvo privado**.

## <a name="create-a-new-plan"></a>Criar um plano

Selecione **+ criar novo plano** na parte superior.

Na caixa de diálogo **novo plano** , insira uma **ID de plano** exclusiva para cada plano nesta oferta. Essa ID será visível para os clientes no endereço Web do produto. Use apenas letras minúsculas e números, traços ou sublinhados e, no máximo, 50 caracteres.

> [!NOTE]
> A ID do plano não poderá ser alterada depois que você selecionar a opção **Criar**.

Insira um **nome de plano**. Os clientes veem esse nome quando estão decidindo qual plano selecionar dentro de sua oferta. Crie um nome exclusivo que indica claramente as diferenças entre os planos. Por exemplo, você pode inserir os planos **Windows Server** com *Pago conforme o uso*, *BYOL*, *Avançado* e *Enterprise*.

Selecione **Criar**. Isso abre a página **configuração do plano** .

## <a name="plan-setup"></a>Configuração do plano

Defina a configuração de alto nível para o tipo de plano, especifique se ele reutiliza uma configuração técnica de outro plano e identifique as regiões do Azure em que o plano deve estar disponível. Suas seleções aqui determinam quais campos são exibidos em outros painéis para o mesmo plano.

### <a name="reuse-technical-configuration"></a>Reutilizar configuração técnica

Se você tiver mais de um plano do mesmo tipo e os pacotes forem idênticos entre eles, você poderá selecionar **Este plano reutiliza a configuração técnica de outro plano**. Essa opção permite selecionar um dos outros planos do mesmo tipo para essa oferta e permite que você reutilize a configuração técnica dele.

> [!NOTE]
> Quando você reutiliza a configuração técnica de outro plano, toda a guia de **Configuração técnica** desaparece desse plano. Os detalhes de configuração técnica do outro plano, incluindo todas as atualizações que você fizer no futuro, também serão usadas para esse plano. Essa configuração não pode ser alterada após a publicação do plano.

### <a name="azure-regions"></a>Regiões do Azure

Seu plano precisa ser disponibilizado em pelo menos uma região do Azure.

Selecione **Azure global** para disponibilizar seu plano para clientes em todas as regiões globais do Azure que têm integração com o Marketplace comercial. Para obter mais informações, confira [Disponibilidade geográfica e suporte a moeda](marketplace-geo-availability-currencies.md).

Selecione **Azure governamental** para disponibilizar seu plano na região [do Azure governamental](../azure-government/documentation-government-welcome.md) . Essa região fornece acesso controlado para clientes de entidades federais, estaduais, locais ou tribais dos EUA, bem como para parceiros qualificados para atendê-los. Você, como editor, é responsável por eventuais controles de conformidade, medidas de segurança e melhores práticas. O Azure Government usa datacenters isolados fisicamente e redes (localizadas apenas nos EUA).

Antes de publicar para o [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md), teste e valide seu plano no ambiente, pois certos pontos de extremidade podem ser diferentes. Para configurar e testar seu plano, solicite uma conta de avaliação na página de [Avaliação do Microsoft Azure Government](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Depois que o plano for publicado e estiver disponível em uma região específica do Azure, você não poderá remover essa região.

### <a name="azure-government-certifications"></a>Certificações do Azure Government

Essa opção só será visível se você tiver selecionado **Azure Government** como a região do Azure na seção anterior.

Os serviços do Azure Government manipulam os dados que estão sujeitos a determinadas normas e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para oferecer reconhecimento das suas certificações para esses programas, você pode fornecer até 100 links que as descrevem. Eles podem ser links para sua listagem diretamente no programa ou links para descrições de sua conformidade com eles em seus sites. Esses links são visíveis somente para clientes do Azure Government.

Selecione **salvar rascunho** antes de continuar para a próxima guia no menu do plano esquerdo da navegação, **lista de planos**.

## <a name="plan-listing"></a>Listagem de planos

Configure os detalhes da listagem do plano. Esse painel exibe informações específicas, que podem ser diferentes de outros planos na mesma oferta.

### <a name="plan-name"></a>Nome do plano

Esse campo é preenchido automaticamente com o nome que você atribuiu ao seu plano quando o criou. Esse nome aparece no Azure Marketplace como o título deste plano. Ele é limitado a 100 caracteres.

### <a name="plan-summary"></a>Resumo do plano

Forneça um breve resumo do seu plano, não da oferta. Este resumo é limitado a 100 caracteres.

### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software exclusivo e descreva as diferenças entre os planos em sua oferta. Descreva apenas o plano, não a oferta. A descrição do plano pode conter até 2.000 caracteres.

Selecione **salvar rascunho** antes de continuar para a próxima guia no menu do plano de navegação à esquerda, **preços e disponibilidade**.

## <a name="pricing-and-availability"></a>Preços e disponibilidade

Neste painel, você configura:

- Mercados em que esse plano está disponível. Cada plano deve estar disponível em pelo menos um [mercado](marketplace-geo-availability-currencies.md).
- O preço por hora.
- Se deseja tornar o plano visível para todos ou apenas para clientes específicos (um público-alvo privado).

### <a name="markets"></a>Mercados

Cada plano precisa estar disponível em pelo menos um mercado. A maioria dos mercados é selecionada por padrão. Para editar a lista, selecione **Editar mercados** e marque ou desmarque as caixas de seleção de cada local do mercado em que esse plano deve (ou não deve) estar disponível para compra. Os usuários em mercados selecionados ainda podem implantar a oferta em todas as regiões do Azure selecionadas na seção ["configuração do plano"](#plan-setup) .

Selecione **selecionar somente imposto da Microsoft remetido** para selecionar somente países/regiões nos quais a Microsoft remete vendas e impostos sobre o uso em seu nome. A publicação na China é limitada a planos que são *Gratuitos* ou BYOL (*traga sua própria licença*).

Se você já tiver definido preços para seu plano em dólares americanos (US$) e adicionar outra localização de mercado, o preço do novo mercado será calculado de acordo com as tarifas de câmbio atuais. Sempre examine o preço de cada mercado antes de publicar. Examine seus preços selecionando **Exportar preços (xlsx)** depois de salvar as alterações.

Quando você remove um mercado, os clientes desse mercado que estiverem usando implantações ativas não poderão criar implantações nem escalar verticalmente as implantações existentes deles. As implantações existentes não são afetadas.

Selecione **salvar** para continuar.

### <a name="pricing"></a>Preços

Para o **modelo de licença**, selecione **plano de cobrança mensal com base no uso** para configurar os preços para esse plano ou **traga sua própria licença** para permitir que os clientes usem esse plano com a licença existente.

Para um plano cobrado mensalmente baseado em uso, use uma das três opções de entrada de preço a seguir:

- **Por núcleo** – forneça preços por núcleo em USD. A Microsoft calcula o preço por tamanho de núcleo e o converte em moedas locais usando a taxa de câmbio atual.
- **Por tamanho de núcleo** – forneça o preço por tamanho de núcleo em USD. A Microsoft calcula o preço e o converte em moedas locais usando a taxa de câmbio atual.
- **Por mercado e tamanho de núcleo** – forneça preços para cada tamanho de núcleo para todos os mercados. Você pode importar os preços de uma planilha.

Insira um **preço por núcleo** e, em seguida, selecione **preço por tamanho de núcleo** para ver uma tabela de cálculos de preço/hora.

> [!NOTE]
> Salve as alterações de preços para habilitar a exportação de dados sobre preços. Depois que um preço para um mercado em seu plano for publicado, ele não poderá ser alterado posteriormente. Para garantir que os preços sejam adequados antes de publicá-los, exporte a planilha de preços e examine os preços em cada mercado.

### <a name="free-trial"></a>Avaliação gratuita

Você pode oferecer uma **avaliação gratuita** de um, três ou seis meses para seus clientes.

### <a name="plan-visibility"></a>Visibilidade do plano

Você pode criar cada plano para ser visível para todos ou apenas a um público-alvo previamente selecionado. Atribua associações neste público-alvo restrito usando as IDs da assinatura do Azure.

**Público**: seu plano pode ser visto por todos.

**Particular**: torne seu plano visível apenas para um público selecionado. Depois de ele ser publicado como um plano privado, você pode atualizar o público-alvo ou alterá-lo para público. Depois de você tornar um plano público, ele precisa permanecer como público. Ele não pode ser alterado de volta para um plano privado.

Atribua o público que terá acesso a este plano privado usando a **ID de assinatura do Azure** s. Opcionalmente, inclua uma **Descrição** de cada ID de assinatura do Azure que você atribui. Adicione até 10 IDs de assinatura manualmente ou até 20.000 se você estiver importando uma planilha CSV. As IDs de assinatura do Azure são representadas como GUIDs e todas as letras devem estar em minúsculas.

> [!NOTE]
> Um público-alvo privado ou restrito é diferente do público-alvo de versão prévia que você definiu no painel **Versão prévia**. Um público-alvo de versão prévia pode acessar sua oferta *antes* de ela ser publicada como ativa no Azure Marketplace. Embora a escolha de público-alvo privado se aplique somente a um plano específico, o público-alvo da versão prévia pode ver todos os planos públicos e privados para fins de validação.

Não há suporte para ofertas privadas com assinaturas do Azure estabelecidas por meio de um revendedor do CSP (programa de provedor de soluções na nuvem).

### <a name="hide-plan"></a>Ocultar plano

Se a máquina virtual for destinada a ser usada apenas indiretamente quando referenciada por outro modelo de solução ou aplicativo gerenciado, marque essa caixa de seleção para publicar a máquina virtual, mas ocultá-la de clientes que podem estar pesquisando ou procurando por ela diretamente.

Os planos ocultos não são compatíveis com links de versão prévia.

Selecione **salvar rascunho** antes de continuar para a próxima guia no menu do plano esquerdo da navegação, **configuração técnica**.

## <a name="technical-configuration"></a>Configurações técnicas

Forneça as imagens e outras propriedades técnicas associadas a este plano.

> [!NOTE]
> Esta guia não será exibida se você tiver configurado esse plano para reutilizar pacotes de outro na guia **configuração do plano** .

### <a name="operating-system"></a>Sistema operacional

Selecione a família de sistemas operacionais **Windows** ou **Linux** .

Selecione a **versão** do Windows ou o **fornecedor** do Linux.

Insira um **nome amigável de so** para o sistema operacional. Esse nome é visível para os clientes.

### <a name="recommended-vm-sizes"></a>Tamanhos de VM recomendados

Selecione o link para escolher até seis tamanhos de máquina virtual recomendados para exibição no Azure Marketplace.

### <a name="open-ports"></a>Abrir portas

Adicione portas públicas ou privadas abertas em uma máquina virtual implantada.

### <a name="properties"></a>Propriedades

Selecione se sua VM **dá suporte à rede acelerada**. Para obter detalhes, consulte [rede acelerada](https://go.microsoft.com/fwlink/?linkid=2124513).

### <a name="generations"></a>Gerações

A geração de uma máquina virtual define o hardware virtual que ele usa. Com base nas necessidades do seu cliente, você pode publicar uma VM de geração 1, uma VM de geração 2 ou ambas.

1. Ao criar uma nova oferta, selecione um **tipo de geração** e insira os detalhes solicitados:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-1.png" alt-text="Uma exibição da seção detalhes da geração no Partner Center.":::

2. Para adicionar outra geração a um plano, selecione **Adicionar geração**...

    :::image type="content" source="./media/create-vm/azure-vm-generations-add.png" alt-text="Uma exibição do link ' Adicionar geração '.":::

    ... e insira os detalhes solicitados:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-3.png" alt-text="Uma exibição da janela detalhes da geração.":::

<!--    The **Generation ID** you choose will be visible to customers in places such as product URLs and ARM templates (if applicable). Use only lowercase, alphanumeric characters, dashes, or underscores; it cannot be modified once published.
-->
3. Para atualizar uma VM existente que tenha uma geração 1 já publicada, edite detalhes na página de **configuração técnica** .

Para saber mais sobre as diferenças entre os recursos de geração 1 e geração 2, consulte [suporte para VMs de geração 2 no Azure](../virtual-machines/generation-2.md).

### <a name="vm-images"></a>Imagens de VM

Forneça uma versão de disco e o URI de SAS (Assinatura de Acesso Compartilhado) para as imagens de máquina virtual. Adicione até 16 discos de dados para cada imagem de VM. Forneça apenas uma nova versão de imagem por plano em um envio especificado. Depois que uma imagem for publicada, você não poderá editá-la, mas poderá excluí-la. A exclusão de uma versão impede que os usuários novos e existentes implantem uma nova instância da versão excluída.

Esses dois campos obrigatórios são mostrados na imagem anterior acima:

- **Versão do disco**: a versão da imagem que você está fornecendo.
- **Link do VHD do so**: o local em sua conta de armazenamento do Azure para o VHD do sistema operacional. Para saber como obter um URI de SAS, consulte [obter URI de assinatura de acesso compartilhado para sua imagem de VM](azure-vm-get-sas-uri.md).

Discos de dados (selecione **adicionar disco de dados (máximo 16)**) também são URIs de assinatura de acesso compartilhado VHD que são armazenados em suas contas de armazenamento do Azure. Adicione apenas uma imagem por envio em um plano.

Independentemente de qual sistema operacional que você usa, adicione apenas o número mínimo de discos de dados que a solução exige. Durante a implantação, os clientes não podem remover discos que fazem parte de uma imagem, mas podem sempre adicionar discos durante ou após a implantação.

Selecione **salvar rascunho** e, em seguida, selecione a **visão geral do plano ←** na parte superior esquerda para ver o plano que você acabou de criar.

Depois que a imagem da VM for publicada, você poderá excluir a imagem do armazenamento do Azure.

## <a name="next-steps"></a>Próximas etapas

- [Revenda por meio de CSPs](azure-vm-create-resell-csp.md)
