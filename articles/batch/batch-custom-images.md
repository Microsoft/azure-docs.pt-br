---
title: Provisionar um pool de Lote do Azure de uma imagem personalizada | Microsoft Docs
description: Crie um pool de Lote de computação de uma imagem personalizada para provisionar nós que contêm o software e os dados que você precisa para seu aplicativo. Imagens personalizadas são uma maneira eficiente para configurar nós de computação para executar suas cargas de trabalho do Lote.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 04/15/2019
ms.author: lahugh
ms.openlocfilehash: 54456ff48ca7104cc1ba10ddc47cec1bc364ddf6
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323696"
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Usar uma imagem personalizada para criar um pool de máquinas virtuais 

Ao criar um pool no Lote do Azure usando a Configuração de Máquina Virtual, você especifica uma imagem de VM que fornece o sistema operacional para cada nó de computação no pool. Você pode criar um pool de máquinas virtuais com uma imagem do Azure Marketplace compatível ou com uma imagem personalizada (uma imagem de VM que você mesmo criou e configurou). A imagem personalizada deve ser um recurso de *imagem gerenciada* na mesma assinatura e região do Azure que a conta de Lote.

## <a name="benefits-of-custom-images"></a>Benefícios de imagens personalizadas

Quando você fornece uma imagem personalizada, tem controle sobre a configuração do sistema operacional e o tipo de sistema operacional e discos de dados a serem usados. Sua imagem personalizada pode incluir aplicativos e dados de referência ficam disponíveis em todos os nós do pool do Lote assim que eles são provisionados.

Usar uma imagem personalizada economiza tempo ao preparar os nós de computação do seu pool para executar sua carga de trabalho em Lotes. Embora você possa usar uma imagem do Azure Marketplace e instalar o software em cada nó de computação após o provisionamento, talvez seja mais eficiente usar uma imagem personalizada.

Usar uma imagem personalizada configurada para o seu cenário pode fornecer várias vantagens:

- **Configurar o SO (sistema operacional)** . Você pode personalizar a configuração do disco do sistema operacional da imagem. 
- **Pré-instalação aplicativos.** Faça a pré-instalação de aplicativos no disco do SO, o que é mais eficiente e menos propenso a que instalar aplicativos após o provisionamento de nós de computação usando uma tarefa start.
- **Economize tempo de reinicialização em VMs.** A instalação de aplicativo normalmente requer a reinicialização da VM, o que é demorado. Você pode economizar tempo de reinicialização pré-instalando aplicativos. 
- **Copie grandes quantidades de dados de uma só vez.** Torne os dados estáticos parte da imagem personalizada gerenciada copiando-os para os discos de dados de uma imagem gerenciada. Isso só precisa ser feito uma vez e disponibiliza dados para cada nó do pool.
- **Opção de tipos de disco.** Você tem a opção de usar o armazenamento premium para o disco do SO e o disco de dados.
- **Expanda os pools para tamanhos grandes.** Quando você usa uma imagem personalizada gerenciada para criar um pool, ele pode crescer sem que seja necessário que você faça cópias de VHDs do blob de imagem.

## <a name="prerequisites"></a>Pré-requisitos

- **Um recurso de imagem gerenciada**. Para criar um pool de máquinas virtuais usando uma imagem personalizada, você precisa ter ou criar um recurso de imagem gerenciada na mesma assinatura e região do Azure que a conta do Lote. A imagem deve ser criada de instantâneos de disco do sistema operacional da VM e, opcionalmente, dos discos de dados anexados. Para obter mais informações e etapas para preparar uma imagem gerenciada, veja a seção a seguir.
  - Use uma imagem personalizada exclusiva para cada pool que criar.
  - Para criar um pool com a imagem usando as APIs de Lote, especifique a **ID de recurso** da imagem, que tem a forma `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Para usar o portal, use o **nome** da imagem.  
  - O recurso de imagem gerenciada deve existir pelo tempo de vida do pool para permitir a expansão e pode ser removido após a exclusão do pool.

- **Autenticação do AAD (Azure Active Directory)** . A API do cliente de Lote deve usar a autenticação do AAD. O suporte ao Lote do Azure para AAD está documentado em [Autenticar soluções do serviço de Lote com o Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Preparar uma imagem personalizada

No Azure, você pode preparar uma imagem gerenciada de instantâneos de discos de SO e de dados de uma VM do Azure, de uma VM do Azure generalizada com discos gerenciados ou de um VHD local generalizado carregado por você. Para dimensionar os pools de lote de forma confiável com uma imagem personalizada, é recomendável criar uma imagem gerenciada usando *apenas* o primeiro método: usando instantâneos de discos da VM. Consulte as etapas a seguir para preparar uma VM, gerar um instantâneo e criar uma imagem do instantâneo.

### <a name="prepare-a-vm"></a>Preparar uma VM

Se você estiver criando uma nova VM para a imagem, use uma imagem de terceiros do Azure Marketplace com suporte pelo lote como a imagem base para a imagem gerenciada. Somente imagens de terceiros podem ser usadas como uma imagem de base. Para obter uma lista completa de referências de imagem do Azure Marketplace com suporte pelo lote do Azure, consulte a [lista de SKUs de agente de nó de listas](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) .

> [!NOTE]
> Você não pode usar uma imagem de terceiros que tenham licenças adicionais e termos de compra como sua imagem de base. Para obter informações sobre essas imagens do Marketplace, consulte as diretrizes par máquinas virtuais [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) ou [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
).


* Verifique se a VM é criada com um disco gerenciado. Essa é a configuração de armazenamento padrão quando você cria uma VM.
* Não instale extensões do Azure, tais como a extensão de Script personalizado, na VM. Se a imagem contém uma extensão pré-instalada, o Azure pode ter problemas ao implantar a o pool do Lote.
* Ao usar discos de dados anexados, você precisa montar e formatar os discos de dentro de uma VM para usá-los.
* Verifique se a imagem do sistema operacional base que você forneceu usa unidade temporária padrão. O agente do nó de Lote no momento espera unidade temporária padrão.
* Quando a VM estiver em execução, conecte-se a ela via RDP (para Windows) ou SSH (para Linux). Instale o software necessário ou copie os dados desejados.  

### <a name="create-a-vm-snapshot"></a>Criar um instantâneo da VM

Um instantâneo é uma cópia completa somente leitura de um VHD. Para criar um instantâneo de discos de dados e do SO de uma VM, você pode usar o portal do Azure ou as ferramentas de linha de comando. Para etapas e opções para criar um instantâneo, veja as diretrizes para VMs [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) ou [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md).

### <a name="create-an-image-from-one-or-more-snapshots"></a>Criar uma imagem de um ou mais instantâneos

Para criar uma imagem gerenciada de um instantâneo, use ferramentas de linha de comando do Azure como o comando [az image create](/cli/azure/image). Você pode criar uma imagem especificando um instantâneo do disco do sistema operacional e, opcionalmente, um ou mais instantâneos de disco de dados.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Criar um pool de uma imagem personalizada no portal

Após ter salvo sua imagem personalizada e saber o nome ou a ID do recurso, crie um pool do Lote dessa imagem. As etapas a seguir mostram como criar um pool do portal do Azure.

> [!NOTE]
> Se você estiver criando o pool usando uma das APIs de Lote, verifique se a identidade que você usa para autenticação do AAD tem permissões para o recurso de imagem. Consulte [Autenticar soluções do serviço do Lote no Active Directory](batch-aad-auth.md).
>
> O recurso para a imagem gerenciada deve existir durante o tempo de vida do pool. Se o recurso subjacente for excluído, o pool não poderá ser dimensionado. 

1. Navegue até sua conta do Lote no portal do Azure. Esta conta deve estar na mesma assinatura e na mesma região que o grupo de recursos que contém a imagem personalizada. 
2. Na janela **Configurações** à esquerda, selecione o item de menu **Pools**.
3. Na janela **Pools**, selecione o comando **Adicionar**.
4. Na janela **Adicionar Pool**, selecione **Imagem Personalizada (Windows/Linux)** na lista suspensa **Tipo de Imagem**. Da lista suspensa **Imagem personalizada de VM**, selecione o nome da imagem (forma abreviada da ID de recurso).
5. Selecione o **Editor/Oferta/SKU** correto para sua imagem personalizada.
6. Especifique o restante das configurações necessárias, incluindo o **Tamanho do nó**, os **Nós dedicados de destino** e os **Nós de baixa prioridade**, bem como qualquer configuração opcional desejada.

    Por exemplo, para uma imagem personalizada do Microsoft Windows Server Datacenter 2016, a janela **Adicionar Pool** aparece como mostrado abaixo:

    ![Adicionar pool de uma imagem personalizada do Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Para verificar se um pool existente é baseado em uma imagem personalizada, consulte a propriedade **Sistema Operacional** na seção de resumo de recursos da folha **Pool**. Se o pool tiver sido criado de uma imagem personalizada, ela estará definida como **Imagem de VM personalizada**.

Todas as imagens personalizadas associadas a um pool são exibidas na janela **Propriedades** do pool.

## <a name="considerations-for-large-pools"></a>Considerações sobre pools grandes

Se você planeja criar um pool com centenas de VMs ou mais usando uma imagem personalizada, é importante seguir as diretrizes anteriores para usar uma imagem criada de um instantâneo de VM.

Além disso, observe o seguinte:

- **Limites de tamanho** – o Lote limita o tamanho do pool a 2.500 nós de computação dedicados, ou 1.000 nós de baixa prioridade, quando você usa uma imagem personalizada.

  Se você usar a mesma imagem (ou várias imagens com base no mesmo instantâneo subjacente) para criar vários pools, os nós de computação totais nos pools não poderão exceder os limites anteriores. Não recomendamos o uso de uma imagem ou do respectivo instantâneo subjacente para mais de um único pool.

  Os limites poderão ser reduzidos se você configurar o pool com [pools NAT de entrada](pool-endpoint-configuration.md).

- **Tempo limite de redimensionamento** – se o pool contém um número fixo de nós (não dimensiona automaticamente), aumente propriedade resizeTimeout do pool para um valor como 20 ou 30 minutos. Se o seu pool não alcançar seu tamanho de destino dentro do período de tempo limite, execute outra [operação de redimensionamento](/rest/api/batchservice/pool/resize).

  Se você planejar um pool com mais de 300 nós de computação, você poderá precisar redimensionar o pool várias vezes para alcançar o tamanho de destino.

## <a name="considerations-for-using-packer"></a>Considerações sobre o uso do Packr

Criar um recurso de imagem gerenciada diretamente com o Packer só pode ser feito com contas do lote no modo de assinatura do usuário. Para contas do modo de serviço de lote, você precisa primeiro criar um VHD e, em seguida, importar o VHD para um recurso de imagem gerenciada. Dependendo do modo de alocação do pool (assinatura do usuário ou serviço de lote), suas etapas para criar um recurso de imagem gerenciada irão variar.

Verifique se o recurso usado para criar a imagem gerenciada existe para os tempos de vida de qualquer pool que referencie a imagem personalizada. A falha em fazer isso pode resultar em falhas de alocação de pool e/ou em redimensionamento de falhas. 

Se a imagem ou o recurso subjacente for removido, você poderá receber um erro semelhante a: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Se isso acontecer, verifique se o recurso subjacente não foi removido.

Para obter mais informações sobre como usar o Packer para criar uma VM, consulte [criar uma imagem do Linux com](../virtual-machines/linux/build-image-with-packer.md) o packr ou [criar uma imagem do Windows com o packr](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Próximas etapas

- Para uma visão geral detalhada do Lote, confira [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md).
