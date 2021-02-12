---
title: Isolamento de rede no Azure DevTest Labs
description: Saiba mais sobre o isolamento de rede no Azure DevTest Labs.
ms.topic: article
ms.date: 08/25/2020
ms.openlocfilehash: 28bfa048a8a6666deb58a8ecfff38779c91d95b8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352925"
---
# <a name="network-isolation-in-devtest-labs"></a>Isolamento de rede no DevTest Labs

Uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) atua como um limite de segurança, isolando os recursos do Azure da Internet pública. Você também pode unir uma rede virtual do Azure à sua rede local para poder se conectar com segurança aos seus recursos locais. No DevTest Labs, você pode optar por [isolar todas as máquinas virtuais de laboratório](devtest-lab-configure-vnet.md) e [ambientes na sua rede](connect-environment-lab-virtual-network.md) para garantir que os recursos de laboratório sigam as políticas de rede organizacionais. 

Como proprietário de um laboratório, você também pode optar por isolar completamente o laboratório, ou seja, ao isolar máquinas virtuais e ambientes na rede selecionada, também pode isolar a conta de armazenamento do laboratório e os cofres de chaves criados em sua assinatura. Este artigo orienta você pela criação de um laboratório isolado de rede. 

Examine também os seguintes artigos:

- [Como o DevTest Labs usa a conta de armazenamento do laboratório](encrypt-storage.md)
- [Como o DevTest Labs usa cofres de chaves](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> O isolamento de rede atualmente tem suporte apenas para novas criações de laboratórios.

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>Etapas para habilitar o isolamento de rede durante a criação do laboratório

1. Durante a criação do laboratório, vá para a guia **rede** .
1. Você pode selecionar uma rede **padrão** que será criada pelo laboratório para você ou selecionar uma rede existente na lista suspensa. Você só poderá selecionar redes que estejam na mesma região e assinatura que o do laboratório. 

    > [!div class="mx-imgBorder"]
    > ![Criar laboratório](./media/network-isolation/create-lab.png)
1. Selecione uma sub-rede.

    > [!div class="mx-imgBorder"]
    > ![Criar uma sub-rede](./media/network-isolation/create-lab-subnet.png)
1. Se você optar por isolar os recursos de laboratório (conta de armazenamento de laboratório e cofre de chaves) para a rede padrão, nenhuma ação adicional será necessária e o laboratório tratará do isolamento de recursos no futuro.
 
    > [!div class="mx-imgBorder"]
    > ![Isolamento da rede](./media/network-isolation/isolate-lab-resources.png)
1. Se você optar por isolar os recursos do laboratório (conta de armazenamento do laboratório e cofre de chaves) em uma rede existente selecionada, será necessário concluir as etapas a seguir após a criação do laboratório para garantir que o laboratório continue a funcionar no modo isolado. 
 
    > [!div class="mx-imgBorder"]
    > ![Isolar a origem](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > O proprietário do laboratório precisa concluir essas etapas antes de configurar e/ou criar qualquer recurso no laboratório.

### <a name="steps-to-follow-post-lab-creation"></a>Etapas para seguir a criação do laboratório posterior

1. Na home page do laboratório, selecione o grupo de **recursos** na página **visão geral** . Você deve ver a página do **grupo de recursos** para o grupo de recursos que contém o laboratório. 
 
   > [!div class="mx-imgBorder"]
   > ![Laboratório da contoso](./media/network-isolation/contoso-lab.png)
1. Selecione a conta de armazenamento do Azure do laboratório. A Convenção de nomenclatura para a conta de armazenamento de laboratório é: um<*labNameWithoutInvalidCharacters* > *<número de 4 dígitos*>. Por exemplo, se o nome do laboratório for contosolab, o nome da conta de armazenamento poderá ser acontosolab1234.
 
   > [!div class="mx-imgBorder"]
   > ![Teste da contoso](./media/network-isolation/contoso-test.png)
1. Na conta de armazenamento, vá para firewalls e redes virtuais e verifique se a caixa de seleção "permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento" está marcada. Como o [DevTest Labs é um serviço confiável da Microsoft](../storage/common/storage-network-security.md#trusted-microsoft-services), essa opção permitirá que o laboratório opere normalmente em um modo isolado de rede. 

   > [!div class="mx-imgBorder"]
   > ![Firewalls de laboratório da contoso](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. Em seguida, clique em **+ Adicionar rede virtual existente**, selecione a rede virtual e a sub-rede que você escolheu ao criar o laboratório e clique em **habilitar**. 

   > [!div class="mx-imgBorder"]
   > ![Minha vnet contoso](./media/network-isolation/contoso-lab-my-vnet.png)
5.  Depois que o ponto de extremidade de serviço for habilitado com êxito para a rede virtual selecionada, clique em **Adicionar**. 

   > [!div class="mx-imgBorder"]
   > ![Adicionar](./media/network-isolation/contoso-firewall-add.png)
 
Com isso, o armazenamento do Azure permitirá conexões de entrada da rede virtual adicionada e permitirá que o laboratório opere com êxito em um modo isolado de rede. 

Você também pode optar por automatizar essas etapas para definir essa configuração para vários laboratórios. 

[Saiba mais sobre como gerenciar regras de acesso de rede padrão para o armazenamento do Azure usando o PowerShell e a CLI](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#powershell)

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>Coisas a serem lembradas ao usar um laboratório em um modo isolado de rede

### <a name="accessing-labs-storage-account-outside-the-lab"></a>Acessando a conta de armazenamento do laboratório fora do laboratório 

Em um laboratório isolado de rede, para ações como carregar um VHD na conta de armazenamento do laboratório para criar imagens personalizadas do, o proprietário do laboratório precisará habilitar explicitamente o acesso à conta de armazenamento de um ponto de extremidade permitido. Você pode fazer isso criando uma máquina virtual e acessar com segurança a conta de armazenamento do laboratório dessa máquina virtual. 

[Saiba mais sobre como acessar uma conta de armazenamento de forma privada de uma máquina virtual](../private-link/tutorial-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>Exportando dados de uso do laboratório 

Em um laboratório isolado de rede, para [exportar dados de uso pessoal para o laboratório](personal-data-delete-export.md), o proprietário do laboratório precisará explicitamente fornecer a conta de armazenamento e gerar um blob dentro da conta para armazenar os dados. 

Se uma conta de armazenamento não for fornecida, essa operação falhará no modo isolado da rede, pois a conta de armazenamento do laboratório não poderá ser acessada pelo laboratório para usá-la no caso de nenhuma conta de armazenamento fornecida pelo cliente. 

[Saiba mais sobre como exportar dados de uso do laboratório em uma conta de armazenamento especificada](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>Próximas etapas

[Criar ou modificar laboratórios automaticamente usando modelos do Azure Resource Manager e o PowerShell](devtest-lab-use-arm-and-powershell-for-lab-resources.md)