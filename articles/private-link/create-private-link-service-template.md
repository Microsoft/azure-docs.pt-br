---
title: 'Guia de início rápido: criar um serviço de link privado no Link Privado do Azure'
description: Neste guia de início rápido, você usará um modelo do ARM (Azure Resource Manager) para criar um serviço de link privado.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: 34993ad3d3d0494f89bd264a8b7194f52129ad7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555050"
---
# <a name="quickstart-create-a-private-link-service-by-using-an-arm-template"></a>Início Rápido: Criar um serviço de link privado usando um modelo do ARM

Neste guia de início rápido, você usará um modelo do ARM (Azure Resource Manager) para criar um serviço de link privado.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Também é possível concluir este início rápido usando o [portal do Azure](create-private-link-service-portal.md), o [Azure PowerShell](create-private-link-service-powershell.md) ou a [CLI do Azure](create-private-link-service-cli.md).

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Examinar o modelo

Este modelo cria um serviço de link privado.

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-privatelink-service/).

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json":::

Vários recursos do Azure são definidos no modelo:

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): há uma rede virtual para cada máquina virtual.
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): o balanceador de carga que expõe as máquinas virtuais que hospedam o serviço.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): há duas interfaces de rede, uma para cada máquina virtual.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): há duas máquinas virtuais, uma que hospeda o serviço e outra que testa a conexão com o ponto de extremidade privado.
- [**Microsoft.Compute/virtualMachines/extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions): a extensão que instala um servidor Web.
- [**Microsoft.Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices): o serviço de link privado usado para expor o serviço.
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): há dois endereços IP públicos, um para cada máquina virtual.
- [**Microsoft.Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints): o ponto de extremidade privado usado para acessar o serviço.

## <a name="deploy-the-template"></a>Implantar o modelo

Veja como implantar o modelo do ARM no Azure:

1. Para entrar no Azure e abrir o modelo, selecione **Implantar no Azure**. O modelo cria uma máquina virtual, um balanceador de carga padrão, um serviço de link privado, um ponto de extremidade privado, uma rede e uma máquina virtual a ser validada.

   [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. Selecione ou crie o grupo de recursos.
3. Digite o nome de usuário e a senha do administrador da máquina virtual.
4. Leia os termos e condições. Se concordar, selecione **Concordo com os termos e condições declarados acima** > **Comprar**.

## <a name="validate-the-deployment"></a>Validar a implantação

> [!NOTE]
> O modelo do ARM gera um nome exclusivo para o recurso myConsumerVm<b>{uniqueid}</b> da máquina virtual. Substitua o valor gerado por **{uniqueid}** .

### <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Conecte-se à VM _myConsumerVm{uniqueid}_ da Internet da seguinte forma:

1.  Na barra de pesquisa do portal, insira _myConsumerVm{uniqueid}_ .

2.  Selecione **Conectar**. **Conectar-se à máquina virtual** é aberto.

3.  Selecione **Baixar Arquivo RDP**. O Azure cria um arquivo _.rdp_ (protocolo RDP) e ele é baixado no computador.

4.  Abra o arquivo .rdp baixado.

    a. Se solicitado, selecione **Conectar**.

    b. Insira o nome de usuário e a senha que você especificou quando criou a VM.
    
    > [!NOTE]
    > Talvez seja necessário selecionar **Mais opções** > **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.

5.  Selecione **OK**.

6.  Você pode receber um aviso de certificado durante o processo de entrada. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

7.  Depois que a área de trabalho da VM for exibida, minimize-a para voltar para a área de trabalho local.

### <a name="access-the-http-service-privately-from-the-vm"></a>Acessar o serviço http de maneira privada da VM

Veja como se conectar ao servidor http da VM usando o ponto de extremidade privado.

1.  Vá até a Área de Trabalho Remota do _myConsumerVm{uniqueid}_ .
2.  Abra um navegador e insira o endereço do ponto de extremidade privado: `http://10.0.0.5/`.
3.  A página padrão do IIS é exibida.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos criados com o serviço de link privado, exclua o grupo de recursos. Isso remove o serviço de link privado e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Próximas etapas


Para obter mais informações sobre os serviços que dão suporte a um ponto de extremidade privado, confira:
> [!div class="nextstepaction"]
> [Disponibilidade de Link Privado](private-link-overview.md#availability)
