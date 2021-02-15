---
title: Saiba mais sobre o Azure Image Builder (visualização)
description: Saiba mais sobre o Azure Image Builder para máquinas virtuais no Azure.
author: danielsollondon
ms.author: danis
ms.date: 05/02/2019
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: imaging
ms.reviewer: cynthn
ms.openlocfilehash: fb596352011bcce3130d22b7277444bc45679f4c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841392"
---
# <a name="preview-azure-image-builder-overview"></a>Visualização: visão geral do construtor de imagens do Azure

As imagens de VM (máquina virtual) padronizadas permitem que as organizações migrem para a nuvem e garantam consistência nas implantações. As imagens normalmente incluem definições de segurança e de configuração, bem como o software necessário. Configurar seu próprio pipeline de geração de imagens requer tempo, infraestrutura e configuração, mas com o Construtor de Imagens de VM do Azure, basta fornecer uma configuração simples que descreva sua imagem e enviá-la ao serviço para que a imagem seja criada e distribuída.
 
O Construtor de Imagens de VM do Azure (Construtor de Imagens do Azure) permite que você comece com uma imagem do Azure Marketplace baseada em Windows ou Linux, imagens personalizadas existentes ou ISO do RHEL (Red Hat Enterprise Linux) e comece a adicionar suas próprias personalizações. Já que o Construtor de Imagens se baseia no [HashiCorp Packer](https://packer.io/), você também pode importar seus scripts de provisionamento de shell do Packer existentes. Você também pode especificar onde deseja que suas imagens sejam hospedadas, na [Galeria de Imagens Compartilhadas do Azure](shared-image-galleries.md), como uma imagem gerenciada ou um VHD.

> [!IMPORTANT]
> O Construtor de Imagens do Azure está atualmente em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Versão prévia dos recursos

Para a versão prévia, os seguintes recursos são compatíveis:

- Criação de imagens de linha de base golden, que inclui suas configurações corporativas e de segurança mínimas e permite que os departamentos o personalizem ainda mais para as necessidades deles.
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

ISOs do RHEL não são mais compatíveis.

## <a name="how-it-works"></a>Como ele funciona

O Construtor de Imagens do Azure é um serviço do Azure totalmente gerenciado que é acessível por um provedor de recursos do Azure. O processo do Construtor de Imagens do Azure tem três partes principais: determinação da origem, personalização e distribuição, que são representadas em um modelo. O diagrama a seguir mostra os componentes, com algumas das respectivas propriedades. 
 

**Processo do Construtor de Imagens** 

![Desenho conceitual do processo do Construtor de Imagens do Azure](./media/image-builder-overview/image-builder-process.png)

1. Crie o Modelo de Imagem como um arquivo .json. Esse arquivo .json contém informações sobre a origem, as personalizações e a distribuição da imagem. Há vários exemplos no [repositório GitHub do Construtor de Imagens do Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Envie-o para o serviço. Isso criará um artefato de Modelo de Imagem no grupo de recursos que você especificar. Em segundo plano, o Construtor de Imagens baixará a ISO ou imagem de origem e os scripts, conforme necessário. Eles são armazenados em um grupo de recursos separado que é criado automaticamente em sua assinatura, no formato: IT_ \<DestinationResourceGroup> _ \<TemplateName> . 
1. Depois que o modelo de imagem for criado, você poderá compilar a imagem. No construtor de imagem de plano de fundo usa o modelo e os arquivos de origem para criar uma VM (tamanho padrão: Standard_D1_v2), rede, IP público, NSG e armazenamento no \<DestinationResourceGroup> grupo de recursos IT_ _ \<TemplateName> .
1. Como parte da criação da imagem, o Image Builder distribui a imagem de acordo com o modelo e, em seguida, exclui os recursos adicionais no \<DestinationResourceGroup> grupo de recursos IT_ _ \<TemplateName> que foi criado para o processo.


## <a name="permissions"></a>Permissões
Quando você se registra no (AIB), o serviço AIB recebe permissão para criar, gerenciar e excluir um grupo de recursos de preparo (IT_*), bem como direitos para adicionar a ele recursos necessários para o build da imagem. Para que isso ocorra, um SPN (nome da entidade de serviço) do AIB é disponibilizado em sua assinatura durante um registro bem-sucedido.

Para permitir que o Construtor de Imagens de VM do Azure distribua imagens para as imagens gerenciadas ou para uma Galeria de Imagens Compartilhadas, você precisará criar uma identidade atribuída pelo usuário do Azure que tenha permissões para ler e gravar imagens. Esse procedimento, caso você esteja acessando o Armazenamento do Azure, precisará de permissões para ler contêineres privados.

Inicialmente, você precisa seguir a documentação intitulada [Criar uma identidade gerenciada atribuída pelo usuário do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) sobre como criar uma identidade.

Depois que você tiver a identidade, precisará conceder permissões a ela. Para fazer isso, use uma definição de função personalizada do Azure e atribua a identidade gerenciada atribuída pelo usuário para usar a definição de função personalizada.

As permissões são explicadas em mais detalhes [aqui](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements) e os exemplos mostram como isso é implementado.

> [!Note]
> Anteriormente, com o AIB, você usaria o SPN do AIB e concederia as permissões de SPN aos grupos de recursos de imagem. Estamos nos distanciando desse modelo para possibilitar futuras funcionalidades. Desde 26 de maio de 2020, o Construtor de Imagens não aceita modelos que não têm uma identidade atribuída pelo usuário. Os modelos existentes precisarão ser reenviados para o serviço com uma [identidade de usuário](./linux/image-builder-json.md). Os exemplos aqui já mostram como você pode criar uma identidade atribuída pelo usuário e adicioná-las a um modelo. Para obter mais informações, examine esta [documentação](https://github.com/danielsollondon/azvmimagebuilder#service-updates-and-latest-release-information) sobre essa alteração e atualizações sobre lançamentos.

## <a name="costs"></a>Custos
Você incorrerá em alguns custos de computação, rede e armazenamento ao criar, compilar e armazenar imagens com o Construtor de Imagens do Azure. Esses custos são semelhantes aos custos incorridos na criação manual de imagens personalizadas. Para os recursos, você será cobrado segundo as suas tarifas do Azure. 

Durante o processo de criação de imagem, os arquivos são baixados e armazenados no grupo de recursos `IT_<DestinationResourceGroup>_<TemplateName>`, o que gera um pequeno custo de armazenamento. Se você não quiser mantê-los, exclua o **modelo de imagem** após o build da imagem.
 
O Construtor de Imagens cria uma VM usando um tamanho de VM D1v2 e o armazenamento e a rede necessários para a VM. Esses recursos terão a mesma duração que o processo de build e serão excluídos assim que o Construtor de Imagens terminar de criar a imagem. 
 
O Construtor de Imagens do Azure distribuirá a imagem para suas regiões escolhidas, o que poderá incorrer em encargos de saída de rede.

## <a name="hyper-v-generation"></a>Geração do Hyper-V
Atualmente, o Image Builder dá suporte apenas para a criação de imagens de geração do Hyper-V (GEN1) 1 para a Galeria de imagens compartilhada do Azure (SIG) ou a imagem gerenciada. Se você quiser criar imagens Gen2, precisará usar uma imagem Gen2 de origem e distribuir para o VHD. Depois, você precisará criar uma imagem gerenciada do VHD e inseri-la no SIG como uma imagem Gen2.
 
## <a name="next-steps"></a>Próximas etapas 
 
Para experimentar o Construtor de Imagens do Azure, confira os artigos para compilar imagens do [Linux](./linux/image-builder.md) ou do [Windows](./windows/image-builder.md).

