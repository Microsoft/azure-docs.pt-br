---
title: Saiba mais sobre o Azure Image Builder (visualização)
description: Saiba mais sobre o Azure Image Builder para máquinas virtuais no Azure.
author: danielsollondon
ms.author: danis
ms.date: 03/05/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 0e72c35af1f1990527b0154d2ba47a45d3f8b8c9
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425610"
---
# <a name="preview-azure-image-builder-overview"></a>Visualização: visão geral do construtor de imagens do Azure

As imagens de VM (máquina virtual) padronizadas permitem que as organizações migrem para a nuvem e garantam consistência nas implantações. As imagens normalmente incluem definições de segurança e de configuração, bem como o software necessário. Configurar seu próprio pipeline de geração de imagens requer tempo, infraestrutura e configuração, mas com o construtor de imagem de VM do Azure, basta fornecer uma configuração que descreva sua imagem, enviá-la ao serviço, e a imagem seja criada e distribuída.
 
O construtor de imagem de VM do Azure (Construtor de imagens do Azure) permite que você comece com uma imagem do Azure Marketplace baseada em Windows ou Linux, imagens personalizadas existentes e comece a adicionar suas próprias personalizações. Como o Image Builder se baseia no [HashiCorp Packer](https://packer.io/) , você verá algumas semelhanças, mas tem o benefício de um serviço gerenciado. Você também pode especificar onde deseja que suas imagens sejam hospedadas, na [Galeria de Imagens Compartilhadas do Azure](shared-image-galleries.md), como uma imagem gerenciada ou um VHD.

> [!IMPORTANT]
> O Construtor de Imagens do Azure está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Versão prévia dos recursos

Para a versão prévia, os seguintes recursos são compatíveis:

- Criação de imagens de linha de base, que inclui a segurança mínima e as configurações corporativas, e permite que os departamentos a personalizem ainda mais.
- Integração de aplicativos principais, portanto, a VM pode assumir cargas de trabalho após a criação ou adicionar configurações para dar suporte a imagens de área de trabalho virtual do Windows.
- Aplicação de patches em imagens existentes. O Construtor de Imagens permitirá que você aplique patches continuamente em imagens personalizadas existentes.
- Conecte o Construtor de Imagens às suas redes virtuais existentes, para que você possa se conectar a servidores de configuração existentes (DSC, Chef, Puppet etc.), compartilhamentos de arquivo ou outros servidores/serviços roteáveis.
- Integração com a Galeria de Imagens Compartilhadas do Azure. Permite distribuir, apresentar e escalar imagens globalmente e fornece um sistema de gerenciamento de imagens.
- Integração com pipelines de build de imagem existentes. Basta chamar o Construtor de Imagens de seu pipeline ou usar a tarefa simples da versão prévia do Azure DevOps do Construtor de Imagens.
- Migração de um pipeline de personalização de imagem existente para o Azure. Use seus scripts, comandos e processos existentes para personalizar imagens.
- Criação de imagens no formato VHD para dar suporte ao Azure Stack.
 

## <a name="regions"></a>Regiões
O serviço do Construtor de Imagens do Azure estará disponível em versão prévia nessas regiões. As imagens podem ser distribuídas fora dessas regiões.
- Leste dos EUA
- Leste dos EUA 2
- Centro-Oeste dos EUA
- Oeste dos EUA
- Oeste dos EUA 2
- Norte da Europa
- Europa Ocidental

## <a name="os-support"></a>Suporte a SO
O AIB dará suporte a imagens do SO base do Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multisessão/Professional
- Windows 2016
- Windows 2019

## <a name="how-it-works"></a>Como ele funciona

O construtor de imagem de VM do Azure é um serviço do Azure totalmente gerenciado que é acessível por um provedor de recursos do Azure. Forneça uma configuração para o serviço que especifica a imagem de origem, a personalização a ser executada e onde a nova imagem deve ser distribuída, o diagrama abaixo mostra um fluxo de trabalho de alto nível:

![Desenho conceitual do processo do construtor de imagem do Azure mostrando as fontes (Windows/Linux), personalizações (Shell, PowerShell, reinicialização do Windows & atualização, adição de arquivos) e distribuição global com a Galeria de imagens compartilhadas do Azure](./media/image-builder-overview/image-builder-flow.png)

As configurações de modelo podem ser passadas usando o PowerShell, AZ CLI, modelos de ARM e usando a tarefa DevOps do construtor de imagem de VM do Azure, quando você o envia para o serviço, criaremos um recurso de modelo de imagem. Quando o recurso de modelo de imagem for criado, você verá um grupo de recursos de preparo criado em sua assinatura, no formato: IT_ \<DestinationResourceGroup> _\<TemplateName>_ \( GUID). O grupo de recursos de preparo contém arquivos e scripts referenciados no arquivo, Shell, personalização do PowerShell na propriedade ScriptURI.

Para executar a compilação que você invocará `Run` no recurso de modelo de imagem, o serviço implantará recursos adicionais para a compilação, como uma VM, rede, disco, adaptador de rede, etc. Se você criar uma imagem sem usar um construtor de imagem VNET existente também implantará um IP público e NSG, o serviço se conectará à VM de compilação usando SSH ou WinRM. Se você selecionar uma VNET existente, o serviço será implantado usando o link privado do Azure e um endereço IP público não será necessário, para obter mais detalhes sobre a rede do construtor de imagens, examine os [detalhes](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking).

Quando a compilação terminar, todos os recursos serão excluídos, exceto para o grupo de recursos de preparo e a conta de armazenamento, para removê-los, você excluirá o recurso de modelo de imagem ou poderá deixá-los lá para executar a compilação novamente.

Há vários exemplos e guias passo a passo nesta documentação, que referenciam modelos e soluções de configuração no [repositório GitHub do Azure Image Builder](https://github.com/azure/azvmimagebuilder).

### <a name="move-support"></a>Mover suporte
O recurso de modelo de imagem é imutável e contém links para recursos e o grupo de recursos de preparo, portanto, o tipo de recurso não dá suporte à movimentação. Se você quiser mover o recurso de modelo de imagem, verifique se você tem uma cópia do modelo de configuração (Extraia a configuração existente do recurso se não o tiver), crie um novo recurso de modelo de imagem no novo grupo de recursos com um novo nome e exclua o recurso de modelo de imagem anterior. 

## <a name="permissions"></a>Permissões
Quando você se registra no (AIB), o serviço AIB recebe permissão para criar, gerenciar e excluir um grupo de recursos de preparo (IT_*), bem como direitos para adicionar a ele recursos necessários para o build da imagem. Para que isso ocorra, um SPN (nome da entidade de serviço) do AIB é disponibilizado em sua assinatura durante um registro bem-sucedido.

Para permitir que o Construtor de Imagens de VM do Azure distribua imagens para as imagens gerenciadas ou para uma Galeria de Imagens Compartilhadas, você precisará criar uma identidade atribuída pelo usuário do Azure que tenha permissões para ler e gravar imagens. Se você estiver acessando o armazenamento do Azure, isso precisará de permissões para ler contêineres privados e públicos.

As permissões são explicadas em mais detalhes para o [PowerShell](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-permissions-powershell)e [AZ CLI](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-permissions-cli).

## <a name="costs"></a>Custos
Você incorrerá em alguns custos de computação, rede e armazenamento ao criar, compilar e armazenar imagens com o Construtor de Imagens do Azure. Esses custos são semelhantes aos custos incorridos na criação manual de imagens personalizadas. Para os recursos, você será cobrado segundo as suas tarifas do Azure. 

Durante o processo de criação de imagem, os arquivos são baixados e armazenados no grupo de recursos `IT_<DestinationResourceGroup>_<TemplateName>`, o que gera um pequeno custo de armazenamento. Se você não quiser mantê-los, exclua o **modelo de imagem** após o build da imagem.
 
O Construtor de Imagens cria uma VM usando um tamanho de VM D1v2 e o armazenamento e a rede necessários para a VM. Esses recursos terão a mesma duração que o processo de build e serão excluídos assim que o Construtor de Imagens terminar de criar a imagem. 
 
O Construtor de Imagens do Azure distribuirá a imagem para suas regiões escolhidas, o que poderá incorrer em encargos de saída de rede.

## <a name="hyper-v-generation"></a>Geração do Hyper-V
Atualmente, o Image Builder dá suporte apenas para a criação de imagens de geração do Hyper-V (GEN1) 1 para a Galeria de imagens compartilhada do Azure (SIG) ou a imagem gerenciada. 
 
## <a name="next-steps"></a>Próximas etapas 
 
Para experimentar o Construtor de Imagens do Azure, confira os artigos para compilar imagens do [Linux](./linux/image-builder.md) ou do [Windows](./windows/image-builder.md).

