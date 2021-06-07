---
title: Planejar uma oferta de máquina virtual-Microsoft Commercial Marketplace
description: Este artigo descreve os requisitos para publicar uma oferta de máquina virtual no Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 9169d691b58ad4a59796123c64f856128a0f8c83
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879319"
---
# <a name="how-to-plan-a-virtual-machine-offer"></a>Como planejar uma oferta de máquina virtual

Este artigo explica as diferentes opções e requisitos para publicar uma oferta de VM (máquina virtual) no Marketplace comercial. As ofertas de VM são ofertas que podem ser implantadas e cobradas por meio do Azure Marketplace.

Antes de começar, [crie uma conta do Marketplace comercial no Partner Center](./partner-center-portal/create-account.md) e verifique se sua conta está inscrita no programa comercial do Marketplace.

### <a name="technical-fundamentals"></a>Conceitos técnicos

O processo de criação, criação e teste de ofertas leva tempo e requer experiência na plataforma Azure e nas tecnologias usadas para criar sua oferta. Sua equipe de engenharia deve ter um conhecimento prático de [máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [armazenamento do Azure](https://azure.microsoft.com/services/?filter=storage#storage)e [rede do Azure](https://azure.microsoft.com/services/?filter=networking#networking), bem como proficiência no [design e na arquitetura de aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/). Consulte estes recursos técnicos adicionais: 

- Tutoriais
  - [VMs do Linux](../virtual-machines/linux/tutorial-manage-vm.md)
  - [VMs do Windows](../virtual-machines/windows/tutorial-manage-vm.md)

- Exemplos
  - [Exemplos da CLI do Azure para VMs do Linux](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Azure PowerShell para VMs do Linux](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)
  - [Exemplos da CLI do Azure para VMs do Windows](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Azure PowerShell para VMs do Windows](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

## <a name="technical-requirements"></a>Requisitos técnicos

As ofertas de VM têm os seguintes requisitos técnicos:

- Você deve preparar um VHD (disco rígido virtual) do sistema operacional. Os VHDs de disco de dados são opcionais. Isso é explicado em mais detalhes abaixo.
- O cliente pode cancelar sua oferta a qualquer momento.
- Você deve criar pelo menos um plano para sua oferta. Seu plano é cobrado com base na [opção de licenciamento](#licensing-options) que você selecionar.
   > [!IMPORTANT]
   > Cada imagem de VM em um plano deve ter o mesmo número de discos de dados.

Uma VM contém dois componentes:

- **VHD de operação** – contém o sistema operacional e a solução que é implantada com sua oferta. O processo de preparação do VHD difere dependendo se é uma VM baseada em Linux, Windows ou personalizada.
- **VHDs de disco de dados** (opcional) – armazenamento dedicado e persistente para uma VM. Não use a solução o VHD do sistema operacional (por exemplo, a unidade C:) para armazenar informações persistentes. 
    - Você pode incluir até 16 discos de dados.
    - Use um VHD por disco de dados, mesmo se o disco estiver em branco.

    > [!NOTE]
    > Independentemente de qual sistema operacional que você usa, adicione apenas o número mínimo de discos de dados necessários para a solução. Os clientes não podem remover discos que fazem parte de uma imagem no momento da implantação, mas podem sempre adicionar discos durante ou após a implantação.

Para obter instruções detalhadas sobre como preparar seus ativos técnicos, consulte [criar uma máquina virtual usando uma base aprovada](azure-vm-create-using-approved-base.md) ou [criar uma máquina virtual usando sua própria imagem](azure-vm-create-using-own-image.md).

## <a name="preview-audience"></a>Público-alvo de versão prévia

Um público de visualização pode acessar sua oferta de VM antes de ser publicado em tempo real no Azure Marketplace para testar a funcionalidade de ponta a ponta antes de publicá-la em tempo real. Na página do **público de visualização** , você pode definir um público de visualização limitado. 

> [!NOTE]
> Um público de visualização difere de um plano privado. Um plano privado é aquele que você disponibiliza apenas para um público específico que escolher. Isso permite que você negocie um plano personalizado com clientes específicos. Para obter mais informações, consulte a próxima seção: planos.

Você pode enviar convites para endereços de email da MSA (conta da Microsoft) ou do Azure Active Directory (Azure AD). Adicione até 10 endereços de email manualmente ou importe até 20 com um arquivo. csv. Se sua oferta já estiver ativa, você ainda poderá definir um público de visualização para testar quaisquer alterações ou atualizações na sua oferta.

## <a name="plans-and-pricing"></a>Planos e preços

As ofertas de VM exigem pelo menos um plano. Um plano define o escopo e os limites da solução e os preços associados. Você pode criar vários planos para sua oferta a fim de fornecer a seus clientes opções de licenciamento e técnicas diferentes, bem como avaliações gratuitas. Consulte [planos e preços para ofertas de mercado comercial](plans-pricing.md) para obter diretrizes gerais sobre planos, incluindo modelos de preços, avaliações gratuitas e planos privados. 

As VMs são totalmente habilitadas para comércio, usando modelos de licenciamento pago pelo uso ou BYOL (traga sua própria licença). A Microsoft hospeda a transação comercial e cobra o cliente em seu nome. Você tem a vantagem de usar a relação de pagamento preferencial entre o cliente e a Microsoft, incluindo os Contratos Enterprise. Para obter mais informações, consulte [recursos de transação do Marketplace comercial](./marketplace-commercial-transaction-capabilities-and-considerations.md).

> [!NOTE]
> O pagamento antecipado do Azure (anteriormente chamado de compromisso monetário) associado a um Contrato Enterprise pode ser usado em relação ao uso do Azure de sua VM, mas não contra suas taxas de licenciamento de software.

### <a name="licensing-options"></a>Opções de licenciamento

Ao se preparar para publicar uma nova oferta de VM, você precisa decidir qual opção de licenciamento escolher. Isso determinará quais informações adicionais você precisará fornecer posteriormente ao criar sua oferta no Partner Center.

Estas são as opções de licenciamento disponíveis para as ofertas de VM:

| Opção de licenciamento | Processo de transação |
| --- | --- |
| Avaliação gratuita | Ofereça aos seus clientes uma avaliação gratuita de um, três ou seis meses. |
| Test drive | Essa opção permite que seus clientes avaliem as VMs sem custo adicional para elas. Eles não precisam ser um cliente do Azure existente para se envolver com a experiência de avaliação. Para obter detalhes, consulte [o que é um test drive?](./what-is-test-drive.md) |
| BYOL | A opção traga seu próprio licenciamento permite que seus clientes tragam licenças de software existentes para o Azure.\* |
| Com base no uso | Também conhecido como pré-pago, essa opção permite que seus clientes paguem por hora. |
| Demonstração interativa  | Dê aos seus clientes uma experiência guiada de sua solução usando uma demonstração interativa. O benefício é que você pode oferecer uma experiência de avaliação sem ter que fornecer uma configuração complicada de sua solução complexa. |
|

\* Como Publicador, você dá suporte a todos os aspectos da transação de licença de software, incluindo (mas não se limitando a) ordem, preenchimento, medição, cobrança, faturamento, pagamento e coleção.

O exemplo a seguir mostra uma oferta de VM no Azure Marketplace que tem preços com base no uso.

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="Tela de oferta da VM de exemplo.":::

### <a name="private-plans"></a>Planos privados

Você pode restringir a descoberta e a implantação de sua VM a um conjunto específico de clientes publicando a imagem e os preços como um plano privado. Os planos privados desbloqueiam a capacidade de você criar ofertas exclusivas para seus clientes mais próximos e oferecer softwares e termos personalizados. Os termos personalizados permitem a você destacar uma variedade de cenários, incluindo negócios de campos especializados com preços e termos especializados, e também acesso antecipado a software de versão limitada. Os planos privados permitem que você forneça preços ou produtos específicos a um conjunto limitado de clientes.

Para obter mais informações, consulte [planos e preços para ofertas de Marketplace comercial](plans-pricing.md) e [ofertas privadas no Microsoft Commercial Marketplace](private-offers.md).

## <a name="test-drives"></a>Test drives

Você pode optar por habilitar um test drive para sua VM. As unidades de teste oferecem aos clientes acesso a um ambiente pré-configurado por um número fixo de horas. Você pode habilitar as unidades de teste para qualquer opção de publicação, no entanto, esse recurso tem requisitos adicionais. Para saber mais sobre as unidades de teste, consulte [o que é um test drive?](what-is-test-drive.md). Para obter informações sobre como configurar diferentes tipos de unidades de teste, consulte [testar a configuração técnica](test-drive-technical-configuration.md).

> [!TIP]
> Uma test drive é diferente de uma [avaliação gratuita](plans-pricing.md#free-trials). Você pode oferecer um test drive, uma avaliação gratuita ou ambos. Ambos fornecem aos seus clientes sua solução por um período de tempo fixo. No entanto, um test drive também inclui um tour interativo prático dos principais recursos do produto e dos benefícios demonstrados em um cenário de implementação do mundo real.

## <a name="customer-leads"></a>Clientes potenciais

Você deve conectar sua oferta ao seu sistema de gerenciamento de relacionamento com o cliente (CRM) para coletar informações do cliente. Será solicitado ao cliente que forneça permissão para compartilhar as respectivas informações. Esses detalhes do cliente, juntamente com o nome da oferta, a ID e a loja online onde encontraram sua oferta, serão enviados para o sistema CRM que você configurou. O Marketplace comercial dá suporte a uma variedade de sistemas CRM, juntamente com a opção de usar uma tabela do Azure ou configurar um ponto de extremidade HTTPS usando a automatização de energia.

Você pode adicionar ou modificar uma conexão do CRM a qualquer momento durante ou após a criação da oferta. Para obter diretrizes detalhadas, confira [leads do cliente de sua oferta do Marketplace comercial](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Contratos legais

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um contrato padrão que você pode usar para suas ofertas no mercado comercial. Quando você oferece seu software sob o contrato Standard, os clientes precisam apenas lê-lo e aceitá-lo uma vez e você não precisa criar termos e condições personalizados.

Se você optar por usar o contrato padrão, terá a opção de adicionar os termos de emenda universal e até 10 emendas personalizadas ao contrato Standard. Você também pode usar seus próprios termos e condições em vez do contrato padrão. Você gerenciará esses detalhes na página **Propriedades** . Para obter informações detalhadas, consulte [contrato padrão do Microsoft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> Depois de publicar uma oferta usando o contrato padrão para o mercado comercial, você não pode usar seus próprios termos e condições personalizados. Esse é um cenário com duas possibilidades mutuamente exclusivas. Você pode oferecer sua solução sob o contrato Standard ou seus próprios termos e condições. Se você quiser modificar os termos do contrato padrão, poderá fazer isso por meio de emendas de contrato padrão.

## <a name="cloud-solution-providers"></a>Provedores de Soluções na Nuvem

Ao criar sua oferta no Partner Center, você verá a guia **revender por meio de CSPs** . Essa opção permite que os parceiros que fazem parte do programa CSP (provedores de soluções de Microsoft Cloud) revendam sua VM como parte de uma oferta agrupada. Todos os planos BYOL (traga sua própria licença) são automaticamente aceitos para o programa. Você também pode optar por aceitar seus planos não BYOL. Consulte [programa do provedor de soluções na nuvem](cloud-solution-providers.md) para obter mais informações. 

> [!NOTE]
> A aceitação do canal de parceiros do CSP (provedor de soluções na nuvem) já está disponível. Para obter mais informações sobre como comercializar sua oferta por meio de canais de parceiros do Microsoft CSP, consulte [**provedores de soluções de nuvem**](./cloud-solution-providers.md).

## <a name="next-steps"></a>Próximas etapas

- [Criar uma oferta de máquina virtual no Azure Marketplace](azure-vm-create.md)
- [Crie uma máquina virtual usando uma base aprovada](azure-vm-create-using-approved-base.md) ou [crie uma máquina virtual usando sua própria imagem](azure-vm-create-using-own-image.md).
- [Práticas recomendadas de listagem de ofertas](gtm-offer-listing-best-practices.md)