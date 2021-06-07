---
title: Guia de início rápido – Criar um ponto de extremidade privado usando um modelo do ARM
description: Neste guia de início rápido, você usará um modelo do ARM (Azure Resource Manager) para criar um ponto de extremidade privado.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: e80adc16e08e676ac2daabec01a11c10d537c547
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562938"
---
# <a name="quickstart-create-a-private-endpoint-by-using-an-arm-template"></a>Início Rápido: Criar um ponto de extremidade privado usando um modelo do ARM

Neste guia de início rápido, você usará um modelo do ARM (Azure Resource Manager) para criar um ponto de extremidade privado.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Também é possível concluir este início rápido usando o [portal do Azure](create-private-endpoint-portal.md), o [Azure PowerShell](create-private-endpoint-powershell.md) ou a [CLI do Azure](create-private-endpoint-cli.md).

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Examinar o modelo

Este modelo cria um ponto de extremidade privado para uma instância do Banco de Dados SQL do Azure.

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-private-endpoint-sql/).

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json":::

Vários recursos do Azure são definidos no modelo:

- [**Microsoft.Sql/servers**](/azure/templates/microsoft.sql/servers): a instância do Banco de Dados SQL com o banco de dados de exemplo.
- [**Microsoft.Sql/servers/databases**](/azure/templates/microsoft.sql/servers/databases): o banco de dados de exemplo.
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): a rede virtual em que o ponto de extremidade privado é implantado.
- [**Microsoft.Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints): o ponto de extremidade privado para acessar a instância do Banco de Dados SQL.
- [**Microsoft.Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones): a zona usada para resolver o endereço IP do ponto de extremidade privado.
- [**Microsoft.Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft.Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups): o grupo de zonas usado para associar o ponto de extremidade privado a uma zona DNS privada.
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): o endereço IP público usado para acessar a máquina virtual.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): o adaptador de rede da máquina virtual.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): a máquina virtual usada para testar a conexão privada com o ponto de extremidade privado à instância do Banco de Dados SQL.

## <a name="deploy-the-template"></a>Implantar o modelo

Veja como implantar o modelo do ARM no Azure:

1. Para entrar no Azure e abrir o modelo, selecione **Implantar no Azure**. O modelo cria o ponto de extremidade privado, a instância do Banco de Dados SQL, a infraestrutura de rede e uma máquina virtual a ser validada.

   [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Selecione ou crie o grupo de recursos.
3. Digite as credenciais e a senha do Administrador do SQL.
4. Digite o nome de usuário e a senha do administrador da máquina virtual.
5. Leia os termos e condições. Se concordar, selecione **Concordo com os termos e condições declarados acima** > **Comprar**. A implantação pode levar 20 minutos ou mais para ser concluída.

## <a name="validate-the-deployment"></a>Validar a implantação

> [!NOTE]
> O modelo do ARM gera um nome exclusivo para o recurso myVm<b>{uniqueid}</b> da máquina virtual e para o recurso sqlserver<b>{uniqueid}</b> do Banco de Dados SQL. Substitua o valor gerado por **{uniqueid}** .

### <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Conecte-se à VM _myVm{uniqueid}_ da Internet da seguinte forma:

1. Na barra de pesquisa do portal, insira _myVm{uniqueid}_ .

2. Selecione **Conectar**. **Conectar-se à máquina virtual** é aberto.

3. Selecione **Baixar Arquivo RDP**. O Azure cria um arquivo _.rdp_ (protocolo RDP) e ele é baixado no computador.

4. Abra o arquivo .rdp baixado.

   a. Se solicitado, selecione **Conectar**.

   b. Insira o nome de usuário e a senha que você especificou quando criou a VM.

      > [!NOTE]
      > Talvez seja necessário selecionar **Mais opções** > **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.

5. Selecione **OK**.

6. Você pode receber um aviso de certificado durante o processo de entrada. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

7. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para a área de trabalho local.

### <a name="access-the-sql-database-server-privately-from-the-vm"></a>Acessar o servidor do Banco de Dados SQL de maneira privada da VM

Veja como se conectar ao servidor do Banco de Dados SQL da VM usando o ponto de extremidade privado.

1.  Na Área de Trabalho Remota de _myVM{uniqueid}_ , abra o PowerShell.
2.  Insira o seguinte: nslookup sqlserver{uniqueid}.database.windows.net. 
    Você receberá uma mensagem semelhante a esta:

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  Instale o SQL Server Management Studio.
4.  Em  **Conectar-se ao servidor**, insira ou selecione estas informações:
    - **Tipo de servidor**: Selecione **Mecanismo de Banco de Dados**.
    - **Nome do servidor**: Selecione **sqlserver{uniqueid}.database.windows.net**.
    - **Nome de usuário**: Insira um nome de usuário fornecido durante a criação.
    - **Senha**: Insira uma senha fornecida durante a criação.
    - **Lembrar senha**: Selecione  **Sim**.

5.  Selecione **Conectar**.
6.  No menu à esquerda, acesse **Bancos de dados**.
7.  Opcionalmente, crie ou consulte informações no _sample-db_.
8.  Feche a conexão de Área de Trabalho Remota com _myVm{uniqueid}_ .

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos criados com o ponto de extremidade privado, exclua o grupo de recursos. Isso remove o ponto de extremidade privado e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os serviços que dão suporte a um ponto de extremidade privado, confira:
> [!div class="nextstepaction"]
> [Disponibilidade de Link Privado](private-link-overview.md#availability)
