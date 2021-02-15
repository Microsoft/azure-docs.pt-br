---
title: Guia de configuração de conta de laboratório acelerado para Azure Lab Services
description: Este guia ajuda os administradores a configurar rapidamente uma conta de laboratório para uso em sua escola.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: aa3e7b586546b3d87f5c6029b284eeb1402ed171
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659855"
---
# <a name="lab-account-setup-guide"></a>Guia de configuração da conta do laboratório
Para configurar seu ambiente de Azure Lab Services, os administradores precisam primeiro configurar uma **conta de laboratório** em sua assinatura do Azure. Uma conta de laboratório é um contêiner para seus laboratórios e leva apenas alguns minutos para ser configurada.

Este guia inclui três seções:
-  A primeira seção concentra-se nos pré-requisitos que precisam ser concluídos *antes* de configurar sua conta de laboratório.
-  A segunda seção fornece orientação sobre como planejar as configurações de conta do laboratório.
-  A terceira seção fornece instruções passo a passo para configurar uma conta de laboratório.

## <a name="prerequisites-for-setting-up-your-lab-account"></a>Pré-requisitos para configurar sua conta de laboratório
Esta seção descreve os pré-requisitos que você precisa concluir para poder configurar uma conta de laboratório.

### <a name="obtain-an-azure-subscription"></a>Obter uma assinatura do Azure
Para criar uma conta de laboratório, você precisa ter acesso a uma assinatura do Azure configurada para sua escola. Sua escola pode ter uma ou mais assinaturas. Você usa uma assinatura para gerenciar a cobrança e a segurança de todos os seus recursos e serviços do Azure, incluindo contas de laboratório.  As assinaturas do Azure são normalmente gerenciadas pelo seu departamento de ti.  Para obter mais informações, leia o tópico a seguir:
 - [Guia do administrador-assinatura](./administrator-guide.md#subscription)

### <a name="estimate-the-number-of-vms-and-vm-sizes-that-you-need"></a>Estimar o número de VMs e os tamanhos de VM necessários
Você precisará estimar o número de VMs (máquinas virtuais) e os [tamanhos de VM](./administrator-guide.md#vm-sizing) de que sua escola precisa.  Leia a seguinte postagem no blog para obter orientação sobre como estruturar seu labs\images.  Esta postagem de blog também ajudará você a decidir o número de VMs e os tamanhos de VM necessários:
- [Mudando de um laboratório físico para Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

Além disso, consulte este artigo que explica orientações adicionais sobre como estruturar laboratórios:
- [Guia do administrador – laboratório](./administrator-guide.md)

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Entender os limites da VM da assinatura e a capacidade da VM regional
Depois de ter uma estimativa do número de VMs e dos tamanhos de VM para seus laboratórios, você precisa:

- Verifique se o limite de capacidade da sua assinatura do Azure permite o número de VMs e o tamanho da VM que você planeja usar em seus laboratórios.

- Crie sua conta de laboratório dentro de uma região que tenha capacidade de VM suficiente disponível.

Leia a postagem de blog a seguir para saber mais: [limites de assinatura de VM e capacidade regional](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553).

### <a name="decide-how-many-lab-accounts-to-create"></a>Decidir quantas contas de laboratório criar

Para começar rapidamente, crie uma única conta de laboratório em seu próprio grupo de recursos.  Posteriormente, você pode criar contas de laboratório adicionais (e grupos de recursos), conforme necessário. Por exemplo, você pode eventualmente ter uma conta de laboratório e um grupo de recursos por departamento como uma maneira de separar claramente os custos.  Leia os artigos a seguir para saber mais sobre as contas de laboratório, os grupos de recursos e a separação dos custos:
- [Guia do administrador – grupo de recursos](./administrator-guide.md#resource-group)
- [Guia do administrador – conta do laboratório](./administrator-guide.md#lab-account) 
- [Gerenciamento de custos para Azure Lab Services](./cost-management-guide.md)

## <a name="planning-your-lab-accounts-settings"></a>Planejando as configurações da sua conta do laboratório

Para planejar as configurações da sua conta do laboratório, você deve considerar as perguntas a seguir.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Quem deve ser proprietários e colaboradores da conta do laboratório?

   Os administradores de ti de sua escola normalmente são os proprietários e os colaboradores de uma conta de laboratório. Eles são responsáveis por gerenciar as políticas que se aplicam a todos os laboratórios contidos na conta do laboratório. A pessoa que cria a conta de laboratório é automaticamente um proprietário. Você pode adicionar outros proprietários e colaboradores do locatário do Azure Active Directory (AD) que está associado à sua assinatura. Para obter mais informações sobre o proprietário da conta do laboratório e as funções de colaborador, leia:
   -  [Guia do administrador-gerenciar identidade](./administrator-guide.md#manage-identity).

   [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

   Os usuários do laboratório veem apenas uma única lista das máquinas virtuais para as quais eles têm acesso entre locatários dentro de Azure Lab Services.

### <a name="who-will-be-allowed-to-create-labs"></a>Quem terá permissão para criar laboratórios?

   Você pode optar por fazer com que seus membros de ti e corpo docente criem laboratórios. Quando um usuário cria um laboratório, ele é atribuído automaticamente como o proprietário do laboratório.  Para criar laboratórios, os usuários (normalmente do locatário do Azure AD associado à sua assinatura) devem ser atribuídos à função de criador de laboratório dentro da conta do laboratório.  Para obter mais informações sobre a função de criador de laboratório, leia:
   -  [Guia do administrador-gerenciar identidade](./administrator-guide.md#manage-identity)

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Quem terá permissão para possuir e gerenciar laboratórios?

   Você também pode optar por ter ti e membros do corpo docente own\manage Labs *sem* dar a eles a capacidade de criar laboratórios.  Nesse caso, os usuários do locatário do Azure AD de sua assinatura são atribuídos ao proprietário ou colaborador para laboratórios existentes.  Para obter mais informações sobre as funções de proprietário e colaborador de um laboratório, leia:
   - [Guia do administrador-gerenciar identidade](./administrator-guide.md#manage-identity)

### <a name="do-you-want-to-save-images-that-can-be-shared-across-labs"></a>Deseja salvar as imagens que podem ser compartilhadas entre os laboratórios?
Uma galeria de imagens compartilhadas é um repositório que você pode usar para salvar e compartilhar imagens. Para classes que precisam da mesma imagem, os criadores de laboratório podem criar a imagem e, em seguida, exportá-la para a Galeria de imagens compartilhadas.  Depois que uma imagem é exportada para a Galeria de imagens compartilhadas, ela pode ser usada para criar novos laboratórios.

Além disso, talvez você queira criar suas imagens em seu ambiente físico e, em seguida, importá-las para a Galeria de imagens compartilhadas.  Para obter mais detalhes sobre esse processo, leia a seguinte postagem no blog: 
- [Importar imagem personalizada para a Galeria de imagens compartilhadas](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)

Se você decidir que precisa usar uma galeria de imagens compartilhadas, precisará criar ou anexar uma galeria de imagens compartilhada à sua conta de laboratório. Além disso, você pode adiar essa decisão até mais tarde, já que ela pode ser anexada a uma conta de laboratório a qualquer momento.  Para obter mais informações sobre a Galeria de imagens compartilhadas, leia:
- [Guia do administrador-Galeria de imagens compartilhadas](./administrator-guide.md#shared-image-gallery)
- [Guia do administrador-preços da Galeria de imagens compartilhadas](./administrator-guide.md#shared-image-gallery-2)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Quais imagens no Azure Marketplace seus laboratórios usarão?
O Azure Marketplace fornece centenas de imagens que você pode habilitar para que os criadores de laboratório possam usar a imagem para criar seu laboratório. Algumas imagens podem incluir tudo o que um laboratório já precisa. Em outros casos, você pode usar uma imagem como um ponto de partida e, em seguida, o criador do laboratório pode personalizá-la Instalando aplicativos ou ferramentas adicionais.

Se você não souber quais imagens são necessárias, poderá voltar mais tarde para habilitá-las. Além disso, a melhor maneira de ver quais imagens estão disponíveis é primeiro criar uma conta de laboratório. Isso lhe dá acesso para que você possa examinar a lista de imagens disponíveis e seu conteúdo.  Para obter mais informações sobre imagens do Marketplace, leia:
- [Especificar imagens do Marketplace disponíveis para os criadores de laboratório](./specify-marketplace-images.md)
  
### <a name="do-the-labs-vms-need-to-have-access-to-other-azure-or-on-premises-resources"></a>As VMs do laboratório precisam ter acesso a outros recursos do Azure ou locais?
Ao configurar uma conta de laboratório, você também pode emparelhar sua conta de laboratório com uma rede virtual (VNet).  Tenha em mente que a sua conta de VNet e de laboratório deve estar localizada na mesma região.  Para decidir se você precisa emparelhar com uma VNet, considere os seguintes cenários:

- **Acesso a um servidor de licenciamento**
  
   Quando você usa imagens do Azure Marketplace, o custo da licença do sistema operacional é agrupado no preço dos serviços de laboratório. No entanto, você não precisa fornecer licenças para o próprio sistema operacional. No entanto, para software adicional e aplicativos que estão instalados, você precisa fornecer uma licença conforme apropriado.  Para acessar um servidor de licenciamento:
   - Você pode optar por se conectar a um servidor de licenciamento local.  Conectar-se a um servidor de licenciamento local requer configuração adicional.
   - Outra opção, que é mais rápida de configurar, é criar um servidor de licenciamento que você hospeda em uma VM do Azure.  A VM do Azure está localizada em uma rede virtual que você emparelha para sua conta de laboratório.

- **Acesso a outros recursos locais, como um compartilhamento de arquivos ou banco de dados**

   Você cria uma VNet para fornecer acesso a recursos locais, normalmente usando um gateway de rede virtual site a site. A configuração desse tipo de ambiente levará mais tempo.

- **Acesso a outros recursos do Azure que estão localizados fora de uma VNet**

   Se você precisar de acesso aos recursos do Azure que *não* estão protegidos em uma VNet, poderá acessar esses recursos por meio da Internet pública, sem fazer nenhum emparelhamento.

Para obter mais informações sobre redes virtuais, leia:
- [Conceitos básicos da arquitetura – rede virtual](./classroom-labs-fundamentals.md#virtual-network)
- [Como se conectar a uma rede virtual](./how-to-connect-peer-virtual-network.md)
- [Como criar um laboratório com um recurso compartilhado no Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>Configurar sua conta de laboratório

Depois de concluir o planejamento, você estará pronto para configurar sua conta de laboratório.  Essas mesmas etapas são aplicáveis para configurar um Azure Lab Services de laboratório [com equipes](./lab-services-within-teams-overview.md).

1. **Crie sua conta de laboratório.** Consulte o tutorial sobre como [criar uma conta de laboratório](./tutorial-setup-lab-account.md#create-a-lab-account) para obter instruções.
   
    Para obter mais diretrizes sobre Nomenclatura, consulte o seguinte artigo:

   - [Diretrizes de nomenclatura para recursos](./administrator-guide.md#naming)

2. **Adicione usuários à função de criador de laboratório.** Para obter instruções, consulte [adicionando usuários à função criador de laboratório](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


3. **Conecte-se a uma rede virtual de mesmo nível.** Para obter instruções, consulte [conectando a rede do seu laboratório a uma rede virtual de mesmo nível](./how-to-connect-peer-virtual-network.md).

   Talvez você também precise consultar as instruções sobre como [Configurar o intervalo de endereços de VMs do laboratório](./how-to-configure-lab-accounts.md).

4. **Habilitar e revisar imagens.** Para obter instruções, consulte [habilitando imagens do Azure Marketplace para criadores de laboratório](./specify-marketplace-images.md).

   Para examinar o conteúdo de cada imagem do Azure Marketplace, selecione o nome da imagem. Por exemplo, a captura de tela a seguir mostra os detalhes da imagem do Ubuntu VM de Ciência de Dados:

   ![Captura de tela de examinar imagens do Azure Marketplace](./media/setup-guide/review-marketplace-images.png)

   Se uma galeria de imagens compartilhada estiver anexada à sua conta de laboratório e você quiser permitir que imagens personalizadas sejam compartilhadas por criadores de laboratório, conclua as etapas semelhantes, conforme mostrado na seguinte captura de tela:

   ![Captura de tela de habilitação de imagens personalizadas em uma galeria de imagens compartilhadas](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Próximas etapas

Próximas etapas que são comuns à configuração de um ambiente de laboratório:

- [Gerenciar contas de laboratório](how-to-manage-lab-accounts.md)
- [Guia de configuração do laboratório](setup-guide.md)