---
author: cynthn
ms.author: cynthn
ms.date: 04/30/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 55d73b2602a9a862375ca3e72dee4597c1d6f1d6
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659742"
---
Imagens padronizadas VM (máquina virtual) permitem que as organizações a migrar para a nuvem e garantir a consistência nas implantações. Imagens normalmente incluem configurações de segurança e configuração predefinidas e o software necessário. Configurar seu próprio pipeline da geração de imagens exige tempo, infraestrutura e configuração, mas com o construtor de imagem de VM do Azure, basta fornecer uma configuração simples que descreve sua imagem, enviá-lo para o serviço e a imagem é criada e distribuída.
 
O construtor de imagens de VM do Azure (construtor de imagens do Azure) permite que você começar com um Windows ou imagem baseado em Linux do Azure Marketplace, imagens personalizadas existentes ou Red Hat Enterprise Linux (RHEL) ISO e começar a adicionar suas próprias personalizações. Como o construtor de imagens se baseia no [HashiCorp Packer](https://packer.io/), você também pode importar seus scripts existentes do Packer shell provisionador. Você também pode especificar onde você deseja que suas imagens hospedadas, o [Galeria de imagens do Azure compartilhado](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), como uma imagem gerenciada ou um VHD.

> [!IMPORTANT]
> Construtor de imagens do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Versão prévia dos recursos

Para a visualização, há suporte para esses recursos:

- Criação de imagens de linha de base dourada, que inclui o mínimo de segurança e configurações corporativas e permitir que os departamentos de TI personalizá-lo ainda mais para suas necessidades.
- Aplicação de patch de imagens existentes, Image Builder permitirá que continuamente aplicar patches em imagens personalizadas existentes.
- Integração com a Galeria de imagens do Azure compartilhado, permite que você distribua, versão, e dimensionar imagens globalmente e lhe dá um sistema de gerenciamento de imagens.
- Integração com a imagem existente criar pipelines, apenas chamar o construtor de imagens do seu pipeline ou usar a visualização de imagem construtor Azure DevOps tarefa simples.
- Migre um pipeline de personalização de imagem existente para o Azure. Use seus scripts existentes, comandos e processos para personalizar as imagens.
- Use o suporte do Red Hat Traga sua própria assinatura. Crie imagens do Red Hat Enterprise para uso com suas assinaturas do Red Hat qualificadas e não utilizadas.
- Criação de imagens no formato VHD.
 

## <a name="regions"></a>Regions
O serviço de construtor de imagem do Azure estará disponível para visualização nessas regiões. Imagens podem ser distribuídas fora essas regiões.
- East US
- Leste dos EUA 2
- Centro-Oeste dos EUA
- Oeste dos EUA
- Oeste dos EUA 2

## <a name="os-support"></a>Suporte do sistema operacional
AIB dará suporte a imagens de sistema operacional base do Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6
- CentOS 7.6
- Windows 2016
- Windows 2019

AIB suportará RHEL ISO, como uma fonte para:
- RHEL 7.3
- RHEL 7.4
- RHEL 7.5

RHEL 7.6 ISOs não têm suporte, mas estão sendo testados.

## <a name="how-it-works"></a>Como ele funciona


![Desenho conceitual do construtor de imagens do Azure](./media/virtual-machines-image-builder-overview/image-builder.png)

O construtor de imagens do Azure é um serviço totalmente gerenciado que pode ser acessado por um provedor de recursos do Azure. O processo do construtor de imagens do Azure tem três partes principais: de origem, personalizar e distribuir, esses são representados em um modelo. O diagrama a seguir mostra os componentes, com algumas de suas propriedades. 
 


**Processo do construtor de imagem** 

![Desenho conceitual do processo de construtor de imagens do Azure](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Crie o modelo de imagem como um arquivo. JSON. Esse arquivo. JSON contém informações sobre a origem da imagem, personalizações e distribuição. Há vários exemplos de [repositório GitHub de construtor de imagem do Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Envie-o para o serviço, isso criará um artefato de modelo de imagem no grupo de recursos que você especificar. Em segundo plano, o Image Builder baixará a imagem de origem ou ISO e scripts, conforme necessário. Eles são armazenados em um grupo de recursos separado é criado automaticamente em sua assinatura, no formato: IT_\<DestinationResourceGroup>_\<TemplateName>. 
1. Depois de criar o modelo de imagem, você pode, em seguida, compile a imagem. Em segundo plano Image Builder usa arquivos de modelo e código-fonte para criar uma VM, rede e armazenamento no IT_\<DestinationResourceGroup > _\<TemplateName > grupo de recursos.
1. Como parte da criação de imagem, o Image builder distribui a imagem de acordo com o modelo, em seguida, exclui os recursos adicionais no IT_\<DestinationResourceGroup > _\<TemplateName > grupo de recursos que foi criado para o processo.


## <a name="permissions"></a>Permissões

Para permitir que o construtor de imagens de VM do Azure distribuir as imagens para as imagens de gerenciado ou para uma galeria de imagens compartilhadas, você precisará fornecer permissões de 'Colaborador' para o serviço "Construtor de imagem de máquina Virtual do Azure" (ID do aplicativo: cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) nos grupos de recursos. 

Se você estiver usando uma imagem gerenciada de personalizada existente ou uma versão de imagem, o construtor de imagens do Azure será necessário um mínimo de acesso 'Reader' a esses grupos de recursos.

Você pode atribuir acesso usando a CLI do Azure:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Se a conta de serviço não for encontrada, isso pode significar que a assinatura onde você está adicionando a atribuição de função ainda não foi registrado para o provedor de recursos.


## <a name="costs"></a>Custos
Você incorrerá em alguma computação, rede e os custos de armazenamento ao criar, compilar e armazenar imagens com o construtor de imagens do Azure. Esses custos são semelhantes dos custos inerentes ao criar manualmente as imagens personalizadas. Para os recursos, você será cobrado a taxas do Azure. 

Durante o processo de criação de imagem, os arquivos são baixados e armazenados no `IT_<DestinationResourceGroup>_<TemplateName>` grupo de recursos que incorrerá em custos de um armazenamento pequeno. f, você não deseja mantê-los, exclua o modelo de imagem após a compilação de imagem.
 
Image Builder cria uma VM usando um tamanho de VM D1v2 e o armazenamento e rede necessários para a VM. Esses recursos vão durar durante o processo de compilação e serão excluídos após Image Builder terminou de criar a imagem. 
 
Construtor de imagens do Azure será distribuir a imagem para as regiões escolhidas, o que pode incorrer em encargos de saída de rede.
 
## <a name="next-steps"></a>Próximas etapas 
 
Para experimentar o construtor de imagens do Azure, consulte os artigos para a construção [Linux](../articles/virtual-machines/linux/image-builder.md) ou [Windows](../articles/virtual-machines/windows/image-builder.md) imagens.
 
 
