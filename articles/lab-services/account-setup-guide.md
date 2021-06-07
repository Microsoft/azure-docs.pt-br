---
title: Guia de configuração de conta de laboratório acelerado para Azure Lab Services
description: Este guia ajuda os administradores a configurar rapidamente uma conta de laboratório para uso em sua escola.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e1f36b6d0983c10926a790d42edef3736e848a59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669382"
---
# <a name="lab-account-setup-guide"></a>Guia de configuração da conta do laboratório
Se você for um administrador, antes de configurar seu ambiente de Azure Lab Services, primeiro você precisará criar uma *conta de laboratório* dentro de sua assinatura do Azure. Uma conta de laboratório é um contêiner para um ou mais laboratórios e leva apenas alguns minutos para ser configurada.

Este guia inclui três seções:
-  Pré-requisitos
-  Planejar as configurações de conta do laboratório
-  Configurar sua conta de laboratório

## <a name="prerequisites"></a>Pré-requisitos
As seções a seguir descrevem o que você precisa fazer antes de configurar uma conta de laboratório.


### <a name="access-your-azure-subscription"></a>Acessar sua assinatura do Azure
Para criar uma conta de laboratório, você precisa ter acesso a uma assinatura do Azure que já está configurada para sua escola. Sua escola pode ter uma ou mais assinaturas. Você usa uma assinatura para gerenciar a cobrança e a segurança de todos os seus recursos e serviços do Azure, incluindo contas de laboratório.  As assinaturas do Azure são geralmente gerenciadas pelo departamento de ti.  Para obter mais informações, consulte a seção "assinatura" do [Guia do Azure Lab Services do administrador](./administrator-guide.md#subscription).

### <a name="estimate-how-many-vms-and-vm-sizes-you-need"></a>Estimar quantas VMs e tamanhos de VM você precisa
É importante saber quantas [máquinas virtuais (VMS) e tamanhos de VM](./administrator-guide.md#vm-sizing) seu laboratório escolar exige. 

Para obter orientação sobre como estruturar seus laboratórios e imagens, consulte a postagem [do blog movendo de um laboratório físico para Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

Para obter diretrizes adicionais sobre como estruturar laboratórios, consulte a seção "laboratório" do [Guia do Azure Lab Services-Administrator](./administrator-guide.md#lab).

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Entender os limites da VM da assinatura e a capacidade da VM regional
Depois de estimar o número de VMs e os tamanhos de VM para seus laboratórios, você precisa:

- Verifique se o limite de capacidade da sua assinatura do Azure permite o número de VMs e o tamanho da VM que você planeja usar em seus laboratórios.
- Crie sua conta de laboratório dentro de uma região que tenha capacidade de VM disponível suficiente.

Para obter mais informações, consulte [limites de assinatura de VM e capacidade regional](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553).

### <a name="decide-how-many-lab-accounts-to-create"></a>Decidir quantas contas de laboratório criar

Para começar rapidamente, crie uma única conta de laboratório em seu próprio grupo de recursos.  Posteriormente, você pode criar contas de laboratório e grupos de recursos adicionais, conforme necessário. Por exemplo, você pode eventualmente ter uma conta de laboratório e um grupo de recursos por departamento como uma maneira de separar claramente os custos. 

Para obter mais informações sobre contas de laboratório, grupos de recursos e separação de custos, consulte:
- A seção "grupo de recursos" do [Guia do Azure Lab Services do administrador](./administrator-guide.md#resource-group)
- A seção "conta de laboratório" do [Guia do Azure Lab Services do administrador](./administrator-guide.md#lab-account) 
- [Gerenciamento de custos para Azure Lab Services](./cost-management-guide.md)

## <a name="plan-your-lab-account-settings"></a>Planejar as configurações de conta do laboratório

Para planejar as configurações de conta do laboratório, considere as perguntas a seguir.

### <a name="who-should-be-the-owners-and-contributors-of-the-lab-account"></a>Quem deve ser os proprietários e os colaboradores da conta do laboratório?

Os administradores de ti de sua escola normalmente assumem as funções de proprietário e colaborador de uma conta de laboratório. Essas funções são responsáveis por gerenciar as políticas que se aplicam a todos os laboratórios na conta do laboratório. A pessoa que cria a conta de laboratório é automaticamente um proprietário. Você pode adicionar outros proprietários e colaboradores do locatário do Azure Active Directory (Azure AD) que está associado à sua assinatura. 

Para obter mais informações sobre as funções de colaborador e proprietário da conta do laboratório, consulte a seção "gerenciar identidade" do [Guia do Azure Lab Services-administrador](./administrator-guide.md#manage-identity).

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

Os usuários do laboratório veem apenas uma única lista das VMs às quais eles têm acesso entre locatários do Azure AD no Azure Lab Services.

### <a name="who-will-be-allowed-to-create-labs"></a>Quem terá permissão para criar laboratórios?

Você pode optar por fazer com que sua equipe de ti ou membros do corpo docente criem laboratórios. Para criar laboratórios, atribua essas pessoas à função de criador de laboratório dentro da conta do laboratório. Você normalmente atribui essa função do locatário do Azure AD que está associado à sua assinatura escolar. Quem cria um laboratório é atribuído automaticamente como o proprietário do laboratório.  

Para obter mais informações sobre a função de criador de laboratório, consulte a seção "gerenciar identidade" do [Guia do Azure Lab Services-administrador](./administrator-guide.md#manage-identity).

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Quem terá permissão para possuir e gerenciar laboratórios?

Você também pode optar por ter ti e membros do corpo docente own\manage Labs *sem* dar a eles a capacidade de criar laboratórios.  Nesse caso, os usuários do locatário do Azure AD de sua assinatura são atribuídos ao proprietário ou colaborador para laboratórios existentes.  

Para obter mais informações sobre o proprietário do laboratório e as funções de colaborador, consulte a seção "gerenciar identidade" do [Guia do Azure Lab Services-administrador](./administrator-guide.md#manage-identity).

### <a name="do-you-want-to-save-images-and-share-them-across-labs"></a>Deseja salvar as imagens e compartilhá-las entre os laboratórios?

A Galeria de imagens compartilhadas é um serviço que você pode usar para salvar e compartilhar imagens. Para classes que precisam usar a mesma imagem, os criadores de laboratório podem criar a imagem e, em seguida, exportá-la para uma galeria de imagens compartilhada.  Depois que uma imagem é exportada para a Galeria de imagens compartilhadas, ela pode ser usada para criar novos laboratórios.

Talvez você queira criar suas imagens em seu ambiente físico e, em seguida, importá-las para uma galeria de imagens compartilhada. Para obter mais informações, consulte a postagem de blog [importar uma imagem personalizada para uma galeria de imagens compartilhadas](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353).

Se você decidir usar o serviço Galeria de imagens compartilhadas, precisará criar ou anexar uma galeria de imagens compartilhada à sua conta de laboratório. Você pode adiar essa decisão por enquanto, porque uma galeria de imagens compartilhadas pode ser anexada a uma conta de laboratório a qualquer momento.  

Para obter mais informações, consulte:
- A seção "Galeria de imagens compartilhadas" do [Guia do Azure Lab Services administrador](./administrator-guide.md#shared-image-gallery)
- A seção "preços" do [Guia do administrador de Azure Lab Services](./administrator-guide.md#pricing)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Quais imagens no Azure Marketplace seus laboratórios usarão?

O Azure Marketplace fornece centenas de imagens que você pode habilitar para que os criadores de laboratório possam usá-las para criar seus laboratórios. Algumas imagens podem incluir tudo o que um laboratório já precisa. Em outros casos, você pode usar uma imagem como um ponto de partida e, em seguida, o criador do laboratório pode personalizá-la Instalando aplicativos ou ferramentas adicionais.

Se você não souber quais imagens são necessárias, poderá voltar mais tarde para habilitá-las. A melhor maneira de ver quais imagens estão disponíveis é primeiro criar uma conta de laboratório. Isso lhe dá acesso para que você possa examinar a lista de imagens disponíveis e seu conteúdo.  

Para obter mais informações, consulte [especificar as imagens do Azure Marketplace que estão disponíveis para os criadores de laboratório](./specify-marketplace-images.md).
  
### <a name="do-the-lab-vms-need-access-to-other-azure-or-on-premises-resources"></a>As VMs de laboratório precisam de acesso a outros recursos do Azure ou locais?

Ao configurar uma conta de laboratório, você também pode emparelhar sua conta de laboratório com uma rede virtual.  Tenha em mente que a sua rede virtual e a conta de laboratório devem estar localizadas na mesma região.  Para decidir se você precisa emparelhar com uma rede virtual, considere os seguintes cenários:

- **Acesso a um servidor de licença**
  
   Quando você usa imagens do Azure Marketplace, o custo da licença do sistema operacional é agrupado no preço dos serviços de laboratório. No entanto, você não precisa fornecer licenças para o próprio sistema operacional. Para software adicional e aplicativos que estão instalados, você precisa fornecer uma licença, conforme apropriado.  Para acessar um servidor de licença:
   - Você pode optar por se conectar a um servidor de licença local.  A conexão a um servidor de licença local requer configuração adicional.
   - Outra opção, que é mais rápida de configurar, é criar um servidor de licença que você hospeda em uma VM do Azure.  A VM do Azure está localizada em uma rede virtual que você emparelha com sua conta de laboratório.

- **Acesso a outros recursos locais, como um compartilhamento de arquivos ou banco de dados**

   Você normalmente cria uma rede virtual para fornecer acesso a recursos locais usando um gateway de rede virtual site a site. A configuração desse tipo de ambiente levará mais tempo.

- **Acesso a outros recursos do Azure que estão localizados fora de uma rede virtual**

   Se precisar de acesso aos recursos do Azure que *não* estão protegidos em uma rede virtual, você poderá acessá-los pela Internet pública, sem precisar fazer nenhum emparelhamento.

   Para obter mais informações sobre redes virtuais, consulte:
   - A seção "rede virtual" de [conceitos básicos de arquitetura no Azure Lab Services](./classroom-labs-fundamentals.md#virtual-network)
   - [Conecte sua rede de laboratório a uma rede virtual de mesmo nível no Azure Lab Services](./how-to-connect-peer-virtual-network.md)
   - [Criar um laboratório com um recurso compartilhado no Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>Configurar sua conta de laboratório

Depois de concluir o planejamento, você estará pronto para configurar sua conta de laboratório. Você pode aplicar as mesmas etapas para configurar [Azure Lab Services em equipes](./lab-services-within-teams-overview.md).

1. **Crie sua conta de laboratório**. Para obter instruções, consulte [criar uma conta de laboratório](./tutorial-setup-lab-account.md#create-a-lab-account).
   
    Para obter informações sobre convenções de nomenclatura, consulte a seção "nomenclatura" do [Guia do Azure Lab Services do administrador](./administrator-guide.md#naming).

1. **Adicione usuários à função de criador de laboratório**. Para obter instruções, consulte [Adicionar usuários à função criador de laboratório](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

1. **Conecte-se a uma rede virtual de mesmo nível**. Para obter instruções, consulte [conectar sua rede de laboratório a um par de rede virtual](./how-to-connect-peer-virtual-network.md).

   Talvez você também precise consultar as instruções para [Configurar o intervalo de endereços de VMs do laboratório](./how-to-configure-lab-accounts.md).

1. **Habilitar e revisar imagens**. Para obter instruções, consulte [especificar quais imagens do Azure Marketplace estão disponíveis para os criadores de laboratório](./specify-marketplace-images.md).

   Para examinar o conteúdo de cada imagem do Azure Marketplace, selecione o nome da imagem. Por exemplo, a captura de tela a seguir mostra os detalhes da imagem do Ubuntu VM de Ciência de Dados:

   ![Captura de tela de uma lista de imagens disponíveis para revisão no Azure Marketplace.](./media/setup-guide/review-marketplace-images.png)

   Se uma galeria de imagens compartilhada estiver anexada à sua conta de laboratório e você quiser permitir que imagens personalizadas sejam compartilhadas por criadores de laboratório, conclua as etapas semelhantes, conforme mostrado na seguinte captura de tela:

   ![Captura de tela de uma lista de imagens personalizadas habilitadas em uma galeria de imagens compartilhada.](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como configurar e gerenciar laboratórios, consulte:

- [Gerenciar contas de laboratório](how-to-manage-lab-accounts.md)  
- [Guia de configuração do laboratório](setup-guide.md)
