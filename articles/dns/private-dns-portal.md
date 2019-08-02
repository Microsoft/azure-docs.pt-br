---
title: Criar uma zona privada do DNS do Azure usando o portal do Azure
description: Neste tutorial, você cria e testa uma zona e um registro DNS privados no DNS do Azure. Este é uma guia passo a passo para criar e gerenciar sua primeira zona e registro DNS privados usando o portal do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 6/15/2019
ms.author: victorh
ms.openlocfilehash: e3772d8f385ed93c96f8aa2f7ee2ded8f46d4e00
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147815"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-the-azure-portal"></a>Tutorial: Criar uma zona privada do DNS do Azure usando o portal do Azure

Este tutorial explica as etapas para criar sua primeira zona e registro DNS privados usando o portal do Azure.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar a hospedagem do seu domínio no DNS do Azure, você precisará criar uma zona DNS para esse nome de domínio. Cada registro DNS para seu domínio é criado dentro dessa zona DNS. Para publicar uma zona de DNS privado em sua rede virtual, você deve especificar a lista de redes virtuais que podem resolver registros na zona.  Elas são chamadas de redes virtuais *vinculadas*. Quando o registro automático estiver habilitado, o DNS do Azure também atualizará os registros de zona sempre que ocorrer a criação de uma máquina virtual, o endereço IP dela mudar ou ela for excluída.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma zona de DNS privado
> * Criar uma rede virtual
> * Vincular a rede virtual
> * Criar máquinas virtuais de teste
> * Criar um registro DNS adicional
> * Testar a zona privada

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se preferir, você pode concluir este tutorial usando o [Azure PowerShell](private-dns-getstarted-powershell.md) ou a [CLI do Azure](private-dns-getstarted-cli.md).

## <a name="create-a-dns-private-zone"></a>Criar uma zona de DNS privado

O exemplo a seguir cria uma zona DNS chamada **private.contoso.com** no grupo de recursos chamado **MyAzureResourceGroup**.

Uma zona DNS contém as entradas DNS para um domínio. Para iniciar a hospedagem do seu domínio no DNS do Azure, crie uma zona DNS para esse nome de domínio.

![Pesquisa de zonas de DNS privado](media/private-dns-portal/search-private-dns.png)

1. Na barra de pesquisa do portal, digite **zonas de dns privado** na caixa de texto de pesquisa e pressione **Enter**.
1. Selecione **Zona de DNS privado**.
2. Selecione **Criar zona de dns privado**.

1. Na página **Criar zona DNS privada**, digite ou selecione os seguintes valores:

   - **Grupo de recursos**: Selecione **Criar novo**, insira *MyAzureResourceGroup* e selecione **OK**. O nome do grupo de recursos deve ser exclusivo na assinatura do Azure. 
   -  **Nome**: Digite *private.contoso.com* para este exemplo.
1. Para **Local do grupo de recursos**, selecione **Centro-oeste dos EUA**.

1. Selecione **Examinar + criar**.

1. Selecione **Criar**.

A criação da zona pode levar alguns minutos.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Na parte superior esquerda da página do portal, selecione **Criar um recurso**, depois **Rede** e **Rede virtual**.
2. Em **Nome**, digite **myAzureVNet**.
3. Em **Grupo de recursos**, selecione **MyAzureResourceGroup**.
4. Para **Localização**, selecione **Centro-oeste dos EUA**.
5. Aceite os outros valores padrão e selecione **Criar**.

## <a name="link-the-virtual-network"></a>Vincular a rede virtual

Para vincular a zona DNS privada a uma rede virtual, crie um link de rede virtual.

![Adicionar link de rede virtual](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Abra o grupo de recursos **MyAzureResourceGroup** e selecione a zona privada **private.contoso.com**.
2. No painel esquerdo, selecione **Links de rede virtual**.
3. Selecione **Adicionar**.
4. Digite **myLink** como o **Nome do link**.
5. Para **Rede virtual**, selecione **myAzureVNet**.
6. Marque a caixa de seleção**Habilitar o registro automático**.
7. Selecione **OK**.

## <a name="create-the-test-virtual-machines"></a>Criar as máquinas virtuais de teste

Agora, crie duas máquinas virtuais para que você possa testar a zona DNS privada:

1. No lado superior esquerdo do portal, selecione **Criar um recurso** e depois selecione **Windows Server 2016 Datacenter**.
1. Selecione **MyAzureResourceGroup** para o grupo de recursos.
1. Digite **myVM01** como o nome da máquina virtual.
1. Selecione **Centro-oeste dos EUA** para a **Região**.
1. Digite **azureadmin** para o nome de usuário do administrador.
2. Digite **Azure12345678** para a senha e confirme a senha.

5. Para **Portas de entrada de públicas**, selecione **Permitir portas selecionadas** e depois selecione **RDP (3389)** para **Selecionar portas de entrada**.
10. Aceite as outras opções padrão da página e clique em **Avançar: Discos >** .
11. Aceite as outras opções padrão da página **Discos** e clique em **Avançar: Rede >** .
1. Verifique se **myAzureVNet** está selecionado para a rede virtual.
1. Aceite as outras opções padrão da página e clique em **Avançar: Gerenciamento >** .
2. Para **Diagnóstico de inicialização**, selecione **Desativado**, aceite as outras opções padrão e selecione **Revisar + criar**.
1. Examine as configurações e depois clique em **Criar**.

Repita essas etapas e crie outra máquina virtual chamada **myVM02**.

A conclusão das duas máquinas virtuais vai demorar alguns minutos.

## <a name="create-an-additional-dns-record"></a>Criar um registro DNS adicional

 O exemplo a seguir cria um registro com o nome relativo **db** na Zona DNS **private.contoso.com** no grupo de recursos **MyAzureResourceGroup**. O nome totalmente qualificado do conjunto de registros é **db.private.contoso.com**. O tipo de registro é "A", com o endereço IP de **myVM01**.

1. Abra o grupo de recursos **MyAzureResourceGroup** e selecione a zona privada **private.contoso.com**.
2. Selecione **+ Conjunto de registros**.
3. Em **Nome**, digite **db**.
4. Para **endereço IP**, digite o endereço IP que você vê para **myVM01**. Após a inicialização da máquina virtual, esse registro deve ser automático.
5. Selecione **OK**.

## <a name="test-the-private-zone"></a>Testar a zona privada

Agora você pode testar a resolução de nome para a zona privada **private.contoso.com**.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Configurar VMs para permitir ICMP de entrada

Você pode usar o comando ping para testar a resolução de nome. Portanto, configure o firewall em ambas as máquinas virtuais para permitir pacotes ICMP de entrada.

1. Conecte-se a myVM01 e abra uma janela do Windows PowerShell com privilégios de administrador.
2. Execute o comando a seguir:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Repita para myVM02.

### <a name="ping-the-vms-by-name"></a>Executar ping de VMs por nome

1. No prompt de comando do Windows PowerShell myVM02, execute ping em myVM01 usando o nome de host registrado automaticamente:
   ```
   ping myVM01.private.contoso.com
   ```
   Você deve ver uma saída semelhante a esta:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Execute ping no nome **db** que você criou anteriormente:
   ```
   ping db.private.contoso.com
   ```
   Você deve ver uma saída semelhante a esta:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Excluir todos os recursos

Quando não for mais necessário, exclua o grupo de recursos **MyAzureResourceGroup** para excluir os recursos criados neste tutorial.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou uma zona DNS privada, criou um registro DNS e testou a zona.
Agora você pode aprender mais sobre zonas DNS privadas.

> [!div class="nextstepaction"]
> [Usando o DNS do Azure para domínios privados](private-dns-overview.md)
